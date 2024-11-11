<template>
  <div class="hangman-background">
    <h1 class="main-title">Hangman Game</h1>
    <div class="hangman-game-wrapper">
      <div v-show="!startGameState && !finishGameState" class="welcome">
        <h1 class="title">Welcome to the Game</h1>
        <span class="text">Please, click "Start Game" to start playing.</span>
      </div>
      <div v-show="isGameOver" class="hangman-game-final">
        <h1 class="title">Congratulations! You guessed the word {{currentWord}} correctly</h1>
        <span class="text">It took you {{guesserCount}} guesses to complete the game.</span>
      </div>
      <div v-show="hasAlertMessage" class="alertMessage">
        <img class="alertSVG" src="./components/icons/alert.svg" alt="alert"/>
        <span class="text-alert">{{alertMessage}}</span>
      </div>
      <div v-show="startGameState && !finishGameState && !isGameOver" class="hangman-game">
        <span class="title">This word has {{wordLength}} letters </span>
        <span class="title">{{displayWord}}</span>
        <div class="guessed-box">
          <span class="title"> Right guesses {{ rightGuesses }}</span>
          <span class="title"> Wrong guesses {{ wrongGuesses }}</span>
        </div>
        <span class="title guessed-letters">Guessed Letters: {{displayGuessedLetters}}</span>
        <div class="guess-box">
          <input type="text" v-model="currentGuess" maxlength="1" @keyup.enter="makeGuess" placeholder="A-Z">
          <button class="guess-button" @click="makeGuess">Make a Guess</button>
        </div>
      </div>
      <div v-show="!startGameState && finishGameState" class="hangman-game-thank-you">
        <h1 class="title">Thanks for playing</h1>
      </div>
    </div>
    <button class="outer-buttons" @click="Restart" v-if="!startGameState && finishGameState && isGameOver">Continue Game!</button>
    <button class="outer-buttons" @click="startGame" v-if="!startGameState && !finishGameState && !isGameOver">Start Game</button>
    <button class="outer-buttons" @click="Restart" v-if="startGameState && !finishGameState && !isGameOver">Finish Game</button>
  </div>
</template>

<script>
export default {
  name: "HangmanApp",
  data() {
    return {
      wordsToGuess: [
        {
          index: 1,
          word: "Brazil",
        }, {
          index: 2,
          word: "Apple",
        }, {
          index: 3,
          word: "House",
        }, {
          index: 4,
          word: "River",
        }, {
          index: 5,
          word: "Rocket",
        }, {
          index: 6,
          word: "Forest",
        }, {
          index: 7,
          word: "Galaxy",
        }, {
          index: 8,
          word: "Purple",
        }, {
          index: 9,
          word: "Garden",
        }, {
          index: 10,
          word: "Table",
        }
      ],
      guessedLetters: [],
      guesserCount: 0,
      currentGuess: "",
      maxGuesses: 100,
      wrongGuesses: 0,
      rightGuesses: 0,
      startGameState: false,
      finishGameState: false,
      currentWord: "",
      isGameOver: false,
      alertMessage: "",
      hasAlertMessage: false,
    };
  },
  methods:{
    Restart(){
      this.isGameOver = false;
      this.startGameState = false;
      this.finishGameState = false;
    },
    startGame(){
      this.startGameState = true;
      this.finishGameState = false;
      this.rightGuesses = 0;
      this.wrongGuesses = 0;
      this.guessedLetters = [];
      this.currentWord = this.wordsToGuess[Math.floor(Math.random() * this.wordsToGuess.length)].word;
    },
    finishGame(){
      this.finishGameState = true;
      this.startGameState = false;
    },
    makeGuess() {
      if (!this.isGameOver && this.currentGuess === "") {
        this.showTemporaryMessage("You must enter a letter to make a guess");
        return;
      }
      const letter = this.currentGuess.toLowerCase();
      this.guesserCount++;
      if (this.guessedLetters.length === 0 || !this.guessedLetters.includes(letter)) {
        this.guessedLetters.push(letter);
        if (!this.currentWord.toLowerCase().includes(letter)) {
          this.wrongGuesses++;
        }
        else{
          this.rightGuesses++;
        }
      }
      else{
        this.showTemporaryMessage("You already guessed this letter");
        this.wrongGuesses++;
      }
      this.checkGame();
      this.currentGuess = "";
    },
    checkGame(){
      if (!this.displayWord.includes("_")) {
        this.startGameState = false;
        this.finishGameState = true;
        this.isGameOver = true;
      }
    },
    showTemporaryMessage(message){
      this.alertMessage = message;
      this.hasAlertMessage = true;
      setTimeout(() => {
        this.alertMessage = "";
        this.hasAlertMessage = false;
      }, 2000);
    }
  },
  computed:{
    wordLength(){
      return this.currentWord.length;
    },
    displayWord() {
      return this.currentWord
          .split("")
          .map(letter => (this.guessedLetters.includes(letter.toLowerCase()) ? letter : "_"))
          .join(" ");
    },
    displayGuessedLetters(){
      return this.guessedLetters
          .filter(it => !this.currentWord.toLowerCase().includes(it.toLowerCase()))
          .join(", ")
          .toUpperCase();
    }
  }
};

</script>

<style scoped>
.hangman-background{
  display: flex;
  flex-direction: column;
  justify-content: center;
  row-gap: 20px;
  font-family: Arial, sans-serif;
  background: linear-gradient(to bottom right, #83a4d4, #b6fbff);
  align-items: center;
  height: 100vw;
  width: 100vw;
  .main-title{
    font-size: 2rem;
    font-weight: bold;
    color: black;
  }
  .title{
    font-size: 1rem;
    font-weight: bold;
    color: black;
  }
  .text-alert{
    font-size: 1rem;
    font-weight: bold;
    color: black;
  }
  .text{
    font-size: 1rem;
    color: black;
  }
  .hangman-game-wrapper{
    display: flex;
    flex-direction: column;
    row-gap: 20px;
    background-color: white;
    padding: 20px;
    border-radius: 10px;
    width: 450px;
    height: fit-content;
  }
  .alertMessage{
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: center;
    column-gap: 5px;
    background-color: white;
    color: black;
    border: 1px solid red;
    border-radius: 10px;
    padding: 10px;
    width: 100%;
    height: fit-content;
    .alertSVG{
      width: 30px;
      height: 30px;
    }
  }
  .welcome{
    display: flex;
    flex-direction: column;
    row-gap: 10px;
    align-items: center;
    justify-content: center;
    width: 100%;
    height: 100%;
  }
  .hangman-game{
    display: flex;
    flex-direction: column;
    row-gap: 20px;
    align-items: center;
    justify-content: center;
    width: 100%;
    height: 100%;
    .guessed-box{
      display: flex;
      flex-direction: row;
      column-gap: 20px;
      align-items: center;
      justify-content: center;
    }
    .hangman-game-final{
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      width: 100%;
      height: 100%;
    }
    .guessed-letters{
      display: flex;
      border: 1px solid black;
      border-radius: 10px;
      padding: 10px;
      width: 100%;
      text-wrap: wrap;
    }
    .guess-box{
      display: flex;
      flex-direction: column;
      row-gap: 20px;
      align-items: center;
      justify-content: center;
      input{
        padding: 10px;
        border-radius: 10px;
        border: 1px solid black;
        width: 100px;
        text-align: center;
      }
      .guess-button{
        background-color: darkslategray;
        color: white;
        text-align: center;
        font-size: 14px;
        border: none;
        border-radius: 10px;
        padding: 10px 20px;
      }
    }
  }
  .hangman-game-thank-you{
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    width: 100%;
    height: 100%;
  }
  .outer-buttons{
    display: flex;
    background-color: green;
    color: white;
    text-align: center;
    font-size: 16px;
    cursor: pointer;
    border: none;
    border-radius: 10px;
    padding: 10px 20px;
  }
}

</style>
