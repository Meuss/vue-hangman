<template>
  <div>
    <h3>vue-hangman, updated with vite / vue 3</h3>
    <h2>Find the Country - Enter a letter</h2>
    <div class="game-container">
      <svg height="250" width="200" class="figure-container">
        <!-- Rod -->
        <line x1="60" y1="20" x2="140" y2="20" />
        <line x1="140" y1="20" x2="140" y2="50" />
        <line x1="60" y1="20" x2="60" y2="230" />
        <line x1="20" y1="230" x2="100" y2="230" />
        <!-- Head -->
        <circle cx="140" cy="70" r="20" class="figure-part" />
        <!-- Body -->
        <line x1="140" y1="90" x2="140" y2="150" class="figure-part" />
        <!-- Arms -->
        <line x1="140" y1="120" x2="120" y2="100" class="figure-part" />
        <line x1="140" y1="120" x2="160" y2="100" class="figure-part" />
        <!-- Legs -->
        <line x1="140" y1="150" x2="120" y2="180" class="figure-part" />
        <line x1="140" y1="150" x2="160" y2="180" class="figure-part" />
      </svg>

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
import { ref, computed, onMounted } from 'vue';
import countries from '../assets/countries.json';
export default {
  name: 'Game',
  setup() {
    // =====================================================
    // Data
    // =====================================================
    const words = ref(countries);
    const selectedWord = ref('');
    const correctLetters = ref([]);
    const wrongLetters = ref([]);
    const letters = ref([]);
    const finalMessage = ref('');
    const wrongTitle = ref('');
    const notification = ref(false);
    const popup = ref(false);
    const finished = ref(false);
    // =====================================================
    // Computed
    // =====================================================
    const displayedWord = computed(function () {
      return letters.value.join('');
    });
    // =====================================================
    // Methods
    // =====================================================
    // choose a random word
    function selectWord() {
      selectedWord.value = words.value[Math.floor(Math.random() * words.value.length)].name.toLowerCase();
    }
    // Display the word / letters
    function displayWord() {
      letters.value = selectedWord.value.split('').map((letter) => (correctLetters.value.includes(letter) ? letter : ''));
      // If user won
      if (displayedWord.value === selectedWord.value) {
        finished.value = true;
        popup.value = true;
        finalMessage.value = '💪';
      }
    }
    // If user types the same letter again
    function showNotification() {
      notification.value = true;
      setTimeout(() => {
        notification.value = false;
      }, 2000);
    }
    // if user enters a wrong letter
    function updateWrongLettersEl() {
      if (wrongLetters.value.length > 0) {
        wrongTitle.value = 'Wrong:';
      } else {
        wrongTitle.value = '';
      }
      // Display parts
      const figureParts = document.querySelectorAll('.figure-part');
      figureParts.forEach((part, index) => {
        const errors = wrongLetters.value.length;
        if (index < errors) {
          part.style.display = 'block';
        } else {
          part.style.display = 'none';
        }
      });

      // Check if lost
      if (wrongLetters.value.length === figureParts.length) {
        finished.value = true;
        finalMessage.value = '👎';
        popup.value = true;
      }
    }
    // Reset the game
    function playAgain() {
      correctLetters.value.splice(0);
      wrongLetters.value.splice(0);
      finished.value = false;
      selectWord();
      displayWord();
      updateWrongLettersEl();
      popup.value = false;
    }
    // =====================================================
    // previously "created" hook
    // =====================================================
    window.addEventListener('keydown', (e) => {
      if ((e.keyCode >= 65 && e.keyCode <= 90) || e.keyCode === 32) {
        const letter = e.key.toLowerCase();
        if (finished.value != true) {
          if (selectedWord.value.includes(letter)) {
            if (!correctLetters.value.includes(letter)) {
              correctLetters.value.push(letter);
              displayWord();
            } else {
              showNotification();
            }
          } else {
            if (!wrongLetters.value.includes(letter)) {
              wrongLetters.value.push(letter);
              updateWrongLettersEl();
            } else {
              showNotification();
            }
          }
        }
      } else if (e.keyCode === 13) {
        // Enter key pressed
        if (finished.value) {
          playAgain();
        }
      }
    });
    // =====================================================
    // previously "created" hook
    // =====================================================
    onMounted(() => {
      selectWord();
      displayWord();
    });

    return {
      words,
      selectedWord,
      correctLetters,
      wrongLetters,
      letters,
      finalMessage,
      wrongTitle,
      notification,
      popup,
      finished,
      selectWord,
      displayedWord,
      displayWord,
      showNotification,
      updateWrongLettersEl,
      playAgain,
    };
  },
};
</script>

<style scoped>
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
}
.popup-container.show {
  display: flex;
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
