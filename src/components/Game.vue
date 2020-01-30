<template>
  <div>
    <h1>vue-hangman</h1>
    <p>Find the hidden word - Enter a letter</p>
    <div class="game-container">
      <svgHangman />

      <div class="wrong-letters-container">
        <div id="wrong-letters">
          <div class="wrongtitle">
            <p>{{ wrongTitle }}</p>
          </div>
          <span v-for="(letter, index) in wrongLetters" :key="index">
            {{ letter }}
          </span>
        </div>
      </div>

      <div class="word" id="word">
        <div v-for="(letter, index) in letters" :key="index">
          <span class="letter">{{ letter }}</span>
        </div>
      </div>
    </div>

    <div class="popup-container" id="popup-container">
      <div class="popup">
        <h2 id="final-message">{{ finalMessage }}</h2>
        <button id="play-button" @click="playAgain">Play Again</button>
      </div>
    </div>

    <div class="notification-container" id="notification-container">
      <p>You have already entered this letter</p>
    </div>
  </div>
</template>

<script>
import svgHangman from "./svgHangman.vue";
import words from "word-list-json";
export default {
  name: "Game",
  data() {
    return {
      words: words,
      selectedWord: "",
      correctLetters: [],
      wrongLetters: [],
      letters: [],
      finalMessage: "",
      wrongTitle: "",
      finished: false
    };
  },
  computed: {
    displayedWord() {
      return this.letters.join("");
    }
  },
  components: {
    svgHangman
  },
  created() {
    window.addEventListener("keydown", e => {
      if (e.keyCode >= 65 && e.keyCode <= 90) {
        const letter = e.key.toLowerCase();
        if (this.finished != true) {
          if (this.selectedWord.includes(letter)) {
            if (!this.correctLetters.includes(letter)) {
              this.correctLetters.push(letter);
              this.displayWord();
            } else {
              this.showNotification();
            }
          } else {
            if (!this.wrongLetters.includes(letter)) {
              this.wrongLetters.push(letter);
              this.updateWrongLettersEl();
            } else {
              this.showNotification();
            }
          }
        }
      }
    });
  },
  mounted() {
    this.selectWord();
    this.displayWord();
  },
  methods: {
    selectWord() {
      this.selectedWord = this.words[
        Math.floor(Math.random() * this.words.length)
      ];
    },
    displayWord() {
      this.letters = this.selectedWord
        .split("")
        .map(letter => (this.correctLetters.includes(letter) ? letter : ""));
      if (this.displayedWord === this.selectedWord) {
        this.finished = true;
        document.getElementById("popup-container").style.display = "flex";
        this.finalMessage = `You won! ${this.selectedWord}`;
      }
    },
    showNotification() {
      const notification = document.getElementById("notification-container");
      notification.classList.add("show");

      setTimeout(() => {
        notification.classList.remove("show");
      }, 2000);
    },
    updateWrongLettersEl() {
      if (this.wrongLetters.length > 0) {
        this.wrongTitle = "Wrong:";
      } else {
        this.wrongTitle = "";
      }

      // Display parts
      const figureParts = document.querySelectorAll(".figure-part");
      figureParts.forEach((part, index) => {
        const errors = this.wrongLetters.length;

        if (index < errors) {
          part.style.display = "block";
        } else {
          part.style.display = "none";
        }
      });

      // Check if lost
      if (this.wrongLetters.length === figureParts.length) {
        this.finished = true;
        this.finalMessage = `You lost. 😕 ${this.selectedWord}`;
        document.getElementById("popup-container").style.display = "flex";
      }
    },
    playAgain() {
      this.correctLetters.splice(0);
      this.wrongLetters.splice(0);
      this.finished = false;
      this.selectWord();
      this.displayWord();
      this.updateWrongLettersEl();
      document.getElementById("popup-container").style.display = "none";
    }
  }
};
</script>

<style scoped lang="scss">
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
