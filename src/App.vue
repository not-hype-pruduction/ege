<template>
            <div id="app">
              <h1 class="title">Игры для подготовки к ЕГЭ</h1>

              <!-- Меню выбора игры -->
              <div v-if="!selectedGame" class="game-selection">
                <h2>Выберите игру:</h2>
                <div class="games-container">
                  <div class="game-card" @click="selectGame('words')">
                    <h3>Угадай слово</h3>
                    <p>Вставьте пропущенную букву в слове</p>
                  </div>
                  <div class="game-card" @click="selectGame('accent')">
                    <h3>Угадай ударение</h3>
                    <p>Укажите правильное ударение в слове</p>
                  </div>
                </div>
                <button class="back-button" v-if="selectedGame" @click="backToMenu">Вернуться в меню</button>
              </div>

              <!-- Игра "Угадай слово" -->
              <div v-if="selectedGame === 'words'" class="game-container">
                <button class="back-button" @click="backToMenu">← В меню</button>
                <h2 class="game-title">Угадай слово</h2>

                <div class="mode-switch">
                  <label>
                    <input type="radio" value="text" v-model="inputMode" /> Ввод текста
                  </label>
                  <label>
                    <input type="radio" value="letter" v-model="inputMode" /> Выбор буквы
                  </label>
                </div>

                <transition name="fade" mode="out-in">
                  <WordDisplay :key="currentWord" :wordWithGap="wordWithGap" />
                </transition>

                <WordInput
                  :correctWord="currentWord"
                  :inputMode="inputMode"
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

              <!-- Игра "Угадай ударение" -->
              <div v-if="selectedGame === 'accent'" class="game-container">
                <button class="back-button" @click="backToMenu">← В меню</button>
                <h2 class="game-title">Угадай ударение</h2>

                <AccentDisplay :word="currentAccentWord" />
                <AccentInput
                  :word="currentAccentWord"
                  @correct-answer="getNextAccentWord"
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
            </div>
          </template>

          <script>
          import WordDisplay from './components/WordDisplay.vue';
          import WordInput from './components/WordInput.vue';
          import GameStats from './components/GameStats.vue';
          import AccentDisplay from './components/AccentDisplay.vue';
          import AccentInput from './components/AccentInput.vue';
          import { words } from './data/words.js';
          import { accentWords } from './data/accentWords.js';

          export default {
            components: {
              WordDisplay,
              WordInput,
              GameStats,
              AccentDisplay,
              AccentInput
            },
            data() {
              return {
                selectedGame: null,
                words: words,
                accentWords: accentWords,
                currentWord: '',
                wordWithGap: '',
                currentAccentWord: '',
                correctAnswers: 0,
                totalAttempts: 0,
                showSuccess: false,
                inputMode: 'text' // Добавляем режим ввода
              }
            },
            methods: {
              selectGame(game) {
                this.selectedGame = game;
                this.correctAnswers = 0;
                this.totalAttempts = 0;

                if (game === 'words') {
                  this.currentWord = this.getRandomWord();
                  this.wordWithGap = this.createWordWithGap(this.currentWord);
                } else if (game === 'accent') {
                  this.currentAccentWord = this.getRandomAccentWord();
                }
              },
              backToMenu() {
                this.selectedGame = null;
              },
              getRandomWord() {
                return this.words[Math.floor(Math.random() * this.words.length)];
              },
              getRandomAccentWord() {
                return this.accentWords[Math.floor(Math.random() * this.accentWords.length)];
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

                setTimeout(() => {
                  this.currentWord = this.getRandomWord();
                  this.wordWithGap = this.createWordWithGap(this.currentWord);
                }, 600);
              },
              getNextAccentWord() {
                this.showSuccessAnimation();
                this.correctAnswers++;

                setTimeout(() => {
                  this.currentAccentWord = this.getRandomAccentWord();
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
            box-sizing: border-box;
            overflow: hidden;
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

          .game-selection {
            margin: 20px 0;
          }

          .game-selection h2 {
            color: #555;
            margin-bottom: 20px;
          }

          .games-container {
            display: flex;
            justify-content: center;
            gap: 20px;
            flex-wrap: wrap;
          }

          .game-card {
            background: linear-gradient(145deg, #ffffff, #f0f7ff);
            padding: 25px 20px;
            border-radius: 15px;
            width: 200px;
            cursor: pointer;
            transition: all 0.3s ease;
            box-shadow: 0 5px 15px rgba(0,0,0,0.05);
          }

          .game-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 8px 20px rgba(58, 123, 213, 0.2);
          }

          .game-card h3 {
            color: #3a7bd5;
            margin-bottom: 10px;
          }

          .game-card p {
            color: #666;
            font-size: 14px;
          }

          .back-button {
            background: transparent;
            color: #3a7bd5;
            border: 2px solid #3a7bd5;
            padding: 8px 15px;
            margin: 10px 0 20px;
            border-radius: 8px;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s ease;
          }

          .back-button:hover {
            background-color: #3a7bd5;
            color: white;
          }

          .game-title {
            color: #555;
            margin-bottom: 20px;
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

          @media (max-width: 768px) {
            #app {
              margin: 20px auto;
              padding: 20px;
              max-width: 95%;
              width: 95%;
            }

            .title {
              font-size: 1.5rem;
            }

            .game-card {
              width: 100%;
              max-width: 250px;
            }

            .success-message {
              font-size: 20px;
              padding: 12px 24px;
            }
          }

          @media (max-width: 400px) {
            #app {
              padding: 15px;
              margin: 10px auto;
            }

            .title {
              font-size: 1.3rem;
            }

            .game-card {
              padding: 15px;
            }
          }

          .mode-switch {
            margin-bottom: 20px;
            display: flex;
            justify-content: center;
            gap: 20px;
          }

          .mode-switch label {
            font-weight: 600;
            color: #555;
            cursor: pointer;
          }

          .mode-switch input {
            margin-right: 5px;
          }
          </style>