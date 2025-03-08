<template>
    <div id="app">
      <h1 class="title">Игра "Угадай слово"</h1>

      <transition name="fade" mode="out-in">
        <WordDisplay :key="currentWord" :wordWithGap="wordWithGap" />
      </transition>

      <WordInput
        :correctWord="currentWord"
        @correct-answer="getNextWord"
        @attempt="incrementAttempts"
      />

      <GameStats
        :correctAnswers="correctAnswers"
        :totalAttempts="totalAttempts"
      />

      <transition name="bounce">
        <div v-if="showSuccess" class="success-message">Правильно!</div>
      </transition>
    </div>
  </template>

  <script>
  import WordDisplay from './components/WordDisplay.vue';
  import WordInput from './components/WordInput.vue';
  import GameStats from './components/GameStats.vue';
  import { words } from './data/words.js';

  export default {
    components: {
      WordDisplay,
      WordInput,
      GameStats
    },
    data() {
      return {
        words: words,
        currentWord: '',
        wordWithGap: '',
        correctAnswers: 0,
        totalAttempts: 0,
        showSuccess: false
      }
    },
    methods: {
      getRandomWord() {
        return this.words[Math.floor(Math.random() * this.words.length)];
      },

      createWordWithGap(word) {
        const vowels = 'аеёиоуыэюя';
        const vowelPositions = [];

        for (let i = 0; i < word.length; i++) {
          if (vowels.includes(word[i].toLowerCase())) {
            vowelPositions.push(i);
          }
        }

        if (vowelPositions.length > 0) {
          const randomIndex = vowelPositions[Math.floor(Math.random() * vowelPositions.length)];
          return word.substring(0, randomIndex) + '_' + word.substring(randomIndex + 1);
        }

        return word;
      },

      getNextWord() {
        this.showSuccessAnimation();
        this.correctAnswers++;

        // Задержка для анимации
        setTimeout(() => {
          this.currentWord = this.getRandomWord();
          this.wordWithGap = this.createWordWithGap(this.currentWord);
        }, 600);
      },

      incrementAttempts() {
        this.totalAttempts++;
      },

      showSuccessAnimation() {
        this.showSuccess = true;
        setTimeout(() => {
          this.showSuccess = false;
        }, 1500);
      }
    },
    created() {
      this.currentWord = this.getRandomWord();
      this.wordWithGap = this.createWordWithGap(this.currentWord);
    }
  }
  </script>

  <style>
  @import url('https://fonts.googleapis.com/css2?family=Montserrat:wght@400;600;700&display=swap');

  #app {
    font-family: 'Montserrat', sans-serif;
    text-align: center;
    margin: 40px auto;
    max-width: 550px;
    padding: 30px;
    box-shadow: 0 8px 25px rgba(0,0,0,0.1);
    border-radius: 15px;
    background: linear-gradient(145deg, #ffffff, #f5f8ff);
  }

  .title {
    color: #3a7bd5;
    margin-bottom: 25px;
    font-weight: 700;
    letter-spacing: 0.5px;
    text-transform: uppercase;
    position: relative;
  }

  .title:after {
    content: '';
    display: block;
    width: 70px;
    height: 3px;
    background-color: #3a7bd5;
    margin: 10px auto 0;
    border-radius: 2px;
  }

  .success-message {
    position: fixed;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    background-color: rgba(46, 204, 113, 0.9);
    color: white;
    padding: 15px 30px;
    border-radius: 30px;
    font-size: 24px;
    font-weight: bold;
    z-index: 100;
    box-shadow: 0 5px 15px rgba(0,0,0,0.2);
  }

  /* Анимации */
  .fade-enter-active,
  .fade-leave-active {
    transition: opacity 0.5s ease, transform 0.5s ease;
  }

  .fade-enter-from,
  .fade-leave-to {
    opacity: 0;
    transform: translateY(10px);
  }

  .bounce-enter-active {
    animation: bounce-in 0.5s;
  }

  .bounce-leave-active {
    animation: bounce-out 0.5s;
  }

  @keyframes bounce-in {
    0% { transform: translate(-50%, -50%) scale(0); }
    50% { transform: translate(-50%, -50%) scale(1.2); }
    100% { transform: translate(-50%, -50%) scale(1); }
  }

  @keyframes bounce-out {
    0% { transform: translate(-50%, -50%) scale(1); }
    100% { transform: translate(-50%, -50%) scale(0); }
  }
  </style>