<template>
  <div>
    <h1>vue-hangman</h1>
    <p>Find the Country - Enter a letter</p>
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

    <div class="popup-container" :class="{ show: popup }">
      <div class="popup">
        <h2 id="final-message">{{ finalMessage }}</h2>
        <h3>{{ this.selectedWord }}</h3>
        <button id="play-button" @click="playAgain">Play Again</button>
      </div>
    </div>

    <div class="notification-container" :class="{ show: notification }">
      <p>You have already entered this letter</p>
    </div>
  </div>
</template>

<script>
import svgHangman from './svgHangman.vue';
import countries from '../countries.json';
export default {
  name: 'Game',
  data() {
    return {
      words: countries,
      selectedWord: '',
      correctLetters: [],
      wrongLetters: [],
      letters: [],
      finalMessage: '',
      wrongTitle: '',
      notification: false,
      popup: false,
      finished: false,
    };
  },
  computed: {
    displayedWord() {
      return this.letters.join('');
    },
  },
  components: {
    svgHangman,
  },
  created() {
    window.addEventListener('keydown', e => {
      if ((e.keyCode >= 65 && e.keyCode <= 90) || e.keyCode === 32) {
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
      } else if (e.keyCode === 13) {
        // Enter key pressed
        if (this.finished) {
          this.playAgain();
        }
      }
    });
  },
  mounted() {
    this.selectWord();
    this.displayWord();
  },
  methods: {
    // choose a random word
    selectWord() {
      this.selectedWord = this.words[Math.floor(Math.random() * this.words.length)].name.toLowerCase();
    },
    // Display the word / letters
    displayWord() {
      this.letters = this.selectedWord.split('').map(letter => (this.correctLetters.includes(letter) ? letter : ''));
      // If user won
      if (this.displayedWord === this.selectedWord) {
        this.finished = true;
        this.popup = true;
        this.finalMessage = '💪';
      }
    },
    // If user types the same letter again
    showNotification() {
      this.notification = true;
      setTimeout(() => {
        this.notification = false;
      }, 2000);
    },
    // if user enters a wrong letter
    updateWrongLettersEl() {
      if (this.wrongLetters.length > 0) {
        this.wrongTitle = 'Wrong:';
      } else {
        this.wrongTitle = '';
      }
      // Display parts
      const figureParts = document.querySelectorAll('.figure-part');
      figureParts.forEach((part, index) => {
        const errors = this.wrongLetters.length;
        if (index < errors) {
          part.style.display = 'block';
        } else {
          part.style.display = 'none';
        }
      });

      // Check if lost
      if (this.wrongLetters.length === figureParts.length) {
        this.finished = true;
        this.finalMessage = '👎';
        this.popup = true;
      }
    },
    // Reset the game
    playAgain() {
      this.correctLetters.splice(0);
      this.wrongLetters.splice(0);
      this.finished = false;
      this.selectWord();
      this.displayWord();
      this.updateWrongLettersEl();
      this.popup = false;
    },
  },
};
</script>

<style scoped lang="scss">
.game-container {
  padding: 20px 30px;
  position: relative;
  margin: auto;
  height: 350px;
  width: 450px;
}

.wrong-letters-container {
  position: absolute;
  top: 20px;
  right: 20px;
  display: flex;
  flex-direction: column;
  text-align: right;
}

.wrong-letters-container p {
  margin: 0 0 5px;
}

.wrong-letters-container span {
  font-size: 24px;
}

.word {
  display: flex;
  position: absolute;
  bottom: 10px;
  left: 50%;
  transform: translateX(-50%);
}

.letter {
  border-bottom: 3px solid #2980b9;
  display: inline-flex;
  font-size: 30px;
  align-items: center;
  justify-content: center;
  margin: 0 3px;
  height: 50px;
  width: 20px;
}

.popup-container {
  background-color: rgba(0, 0, 0, 0.3);
  position: fixed;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  display: none;
  align-items: center;
  justify-content: center;
  &.show {
    display: flex;
  }
}

.popup {
  background: #2980b9;
  min-width: 250px;
  border-radius: 5px;
  box-shadow: 0 15px 10px 3px rgba(0, 0, 0, 0.1);
  padding: 20px;
  text-align: center;
}

.popup button {
  cursor: pointer;
  background-color: #fff;
  color: #2980b9;
  border: 0;
  margin-top: 20px;
  padding: 12px 20px;
  font-size: 16px;
}

.popup button:active {
  transform: scale(0.98);
}

.popup button:focus {
  outline: 0;
}

.notification-container {
  background-color: rgba(0, 0, 0, 0.3);
  border-radius: 10px 10px 0 0;
  padding: 15px 20px;
  position: absolute;
  bottom: -50px;
  transition: transform 0.3s ease-in-out;
}

.notification-container p {
  margin: 0;
}

.notification-container.show {
  transform: translateY(-50px);
}
</style>
