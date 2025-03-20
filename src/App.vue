<template>
  <div id="app" :class="{ 'dark-theme': isDarkTheme }">
    <!-- Переключатель темы -->
    <div class="top-bar">
      <button v-if="selectedGame" class="back-button" @click="backToMenu">
        <span class="back-icon">←</span>
        <span>В меню</span>
      </button>
      <div class="theme-toggle">
        <label class="theme-switch">
          <input type="checkbox" v-model="isDarkTheme" @change="saveThemePreference">
          <span class="toggle-track">
            <span class="toggle-icon">{{ isDarkTheme ? '🌙' : '☀️' }}</span>
          </span>
        </label>
      </div>
    </div>

    <h1 v-if="!selectedGame" class="app-title">Игры для подготовки к ЕГЭ</h1>

    <!-- Меню выбора игры -->
    <div v-if="!selectedGame" class="game-selection">
      <h2 class="section-title">Выберите игру</h2>
      <div class="games-container">
        <div class="game-card" @click="selectGame('words')">
          <div class="card-content">
            <h3>Угадай слово</h3>
            <p>Вставьте пропущенную букву в слове</p>
            <div class="card-action">Начать игру</div>
          </div>
        </div>
        <div class="game-card" @click="selectGame('accent')">
          <div class="card-content">
            <h3>Угадай ударение</h3>
            <p>Укажите правильное ударение в слове</p>
            <div class="card-action">Начать игру</div>
          </div>
        </div>
      </div>
    </div>

    <!-- Игра "Угадай слово" -->
    <div v-if="selectedGame === 'words'" class="game-container">

      <h2 class="game-title">Угадай слово</h2>

      <div class="mode-switch">
        <label class="mode-option">
          <input type="radio" value="text" v-model="inputMode" />
          <span class="mode-label">Ввод текста</span>
        </label>
        <label class="mode-option">
          <input type="radio" value="letter" v-model="inputMode" />
          <span class="mode-label">Выбор буквы</span>
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
        <div v-if="showSuccess" class="success-message">
          <div class="success-icon">✓</div>
          <div class="success-text">Правильно!</div>
        </div>
      </transition>
    </div>

    <!-- Игра "Угадай ударение" -->
    <div v-if="selectedGame === 'accent'" class="game-container">

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
        <div v-if="showSuccess" class="success-message">
          <div class="success-icon">✓</div>
          <div class="success-text">Правильно!</div>
        </div>
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
      inputMode: 'text',
      isDarkTheme: false
    }
  },
  watch: {
    isDarkTheme: {
      immediate: true,
      handler(newVal) {
        // Применяем класс темы к body
        if (newVal) {
          document.body.classList.add('dark-theme');
          document.documentElement.classList.add('dark-theme');
        } else {
          document.body.classList.remove('dark-theme');
          document.documentElement.classList.remove('dark-theme');
        }
      }
    }
  },
  methods: {
    saveThemePreference() {
      localStorage.setItem('darkTheme', this.isDarkTheme);
    },
    loadThemePreference() {
      const savedTheme = localStorage.getItem('darkTheme');
      if (savedTheme !== null) {
        this.isDarkTheme = savedTheme === 'true';
      } else {
        const prefersDark = window.matchMedia('(prefers-color-scheme: dark)').matches;
        this.isDarkTheme = prefersDark;
      }
    },
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
  },
  mounted() {
    this.loadThemePreference();
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Montserrat:wght@400;500;600;700&display=swap');

/* Переменные темы (светлая тема по умолчанию) */
:root {
  /* Основные цвета */
  --bg-color: #ffffff;
  --surface-color: #ffffff;
  --primary-color: #0070f3;
  --primary-gradient: linear-gradient(135deg, #0070f3, #00c3ff);
  --secondary-color: #6c5ce7;
  --text-primary: #111827;
  --text-secondary: #4b5563;
  --text-tertiary: #6b7280;

  /* Элементы интерфейса */
  --card-bg: #ffffff;
  --card-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
  --card-hover-shadow: 0 15px 35px rgba(0, 0, 0, 0.15);
  --button-bg: var(--primary-gradient);
  --button-shadow: 0 4px 15px rgba(0, 112, 243, 0.25);
  --input-bg: #f7fafc;
  --input-border: #e2e8f0;
  --input-focus-border: #0070f3;

  /* Акценты */
  --success-color: #10b981;
  --error-color: #ef4444;
  --warning-color: #f59e0b;

  /* Эффекты */
  --radius-sm: 8px;
  --radius-md: 12px;
  --radius-lg: 20px;
  --transition-fast: 0.2s ease;
  --transition-normal: 0.3s ease;

  /* Системные */
  --backdrop-filter: blur(10px);
  --container-padding: clamp(16px, 5vw, 30px);
}

/* Темная OLED тема */
:root.dark-theme, .dark-theme {
  --bg-color: #000000;
  --surface-color: #121212;
  --primary-color: #0091ff;
  --primary-gradient: linear-gradient(135deg, #0091ff, #6d00ff);
  --secondary-color: #8a5cf7;
  --text-primary: #f8fafc;
  --text-secondary: #cbd5e1;
  --text-tertiary: #94a3b8;

  --card-bg: #121212;
  --card-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
  --card-hover-shadow: 0 15px 35px rgba(0, 0, 0, 0.35);
  --button-bg: var(--primary-gradient);
  --button-shadow: 0 4px 15px rgba(0, 145, 255, 0.25);
  --input-bg: #1e1e1e;
  --input-border: #2a2a2a;
  --input-focus-border: #0091ff;

  --success-color: #00d160;
  --error-color: #ff4d4d;
  --warning-color: #ffbb00;
}

/* Базовые стили */
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

html, body {
  font-family: 'Montserrat', sans-serif;
  background-color: var(--bg-color);
  color: var(--text-primary);
  transition: background-color 0.3s ease, color 0.3s ease;
  min-height: 100vh;
  padding: 0;
  margin: 0;
  overflow-x: hidden;
}

#app {
  width: 100%;
  max-width: 600px;
  margin: 0 auto;
  padding: var(--container-padding);
  transition: all var(--transition-normal);
}

/* Типография */
.app-title {
  font-size: clamp(1.5rem, 6vw, 2.5rem);
  font-weight: 700;
  text-align: center;
  margin-bottom: 2rem;
  background: var(--primary-gradient);
  -webkit-background-clip: text;
  background-clip: text;
  color: transparent;
  padding-bottom: 0.5rem;
  position: relative;
}

.app-title::after {
  content: "";
  position: absolute;
  bottom: 0;
  left: 50%;
  transform: translateX(-50%);
  height: 4px;
  width: 60px;
  background: var(--primary-gradient);
  border-radius: 2px;
}

.section-title {
  font-size: clamp(1.25rem, 4vw, 1.75rem);
  font-weight: 600;
  color: var(--text-primary);
  text-align: center;
  margin-bottom: 1.5rem;
}

.game-title {
  font-size: clamp(1.25rem, 5vw, 2rem);
  font-weight: 600;
  color: var(--primary-color);
  text-align: center;
  margin-bottom: 1.5rem;
}

/* Переключатель темы */
.theme-toggle {
  display: flex;
  justify-content: flex-end;
  margin-bottom: 1rem;
}

.theme-switch {
  position: relative;
  display: inline-block;
  width: 60px;
  height: 34px;
  margin-left: auto;
}

.theme-switch input {
  opacity: 0;
  width: 0;
  height: 0;
}

.toggle-track {
  position: absolute;
  cursor: pointer;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: var(--primary-gradient);
  border-radius: 34px;
  transition: all 0.4s;
  overflow: hidden;
}

.toggle-icon {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.4s;
  font-size: 18px;
}

input:checked + .toggle-track .toggle-icon {
  left: calc(100% - 30px);
}

input:not(:checked) + .toggle-track .toggle-icon {
  left: 10px;
}

/* Карточки игр */
.games-container {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 20px;
  margin-top: 2rem;
}

.game-card {
  position: relative;
  background: var(--card-bg);
  border-radius: var(--radius-lg);
  box-shadow: var(--card-shadow);
  transition: all var(--transition-normal);
  overflow: hidden;
  cursor: pointer;
  height: 100%;
}

.game-card::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: var(--primary-gradient);
  opacity: 0;
  transition: opacity var(--transition-normal);
  z-index: 0;
}

.game-card:hover {
  transform: translateY(-8px);
  box-shadow: var(--card-hover-shadow);
}

.game-card:hover::before {
  opacity: 0.05;
}

.card-content {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 2rem 1.5rem;
  position: relative;
  z-index: 1;
  height: 100%;
}

.card-icon {
  font-size: 3rem;
  margin-bottom: 1rem;
}

.game-card h3 {
  font-size: 1.25rem;
  font-weight: 600;
  color: var(--text-primary);
  margin-bottom: 1rem;
}

.game-card p {
  font-size: 0.9rem;
  color: var(--text-secondary);
  text-align: center;
  margin-bottom: 1.5rem;
  flex-grow: 1;
}

.card-action {
  background: var(--primary-gradient);
  color: white;
  padding: 0.75rem 1.5rem;
  border-radius: var(--radius-sm);
  font-weight: 600;
  font-size: 0.9rem;
  box-shadow: var(--button-shadow);
  transition: all var(--transition-fast);
}

.game-card:hover .card-action {
  transform: scale(1.05);
}

/* Кнопка назад */
.back-button {
  display: flex;
  align-items: center;
  background: transparent;
  color: var(--primary-color);
  border: 2px solid var(--primary-color);
  padding: 0.5rem 1rem;
  border-radius: var(--radius-sm);
  font-weight: 600;
  margin-bottom: 2rem;
  cursor: pointer;
  transition: all var(--transition-fast);
}

.back-button:hover {
  background-color: var(--primary-color);
  color: white;
}

.back-icon {
  margin-right: 0.5rem;
  font-size: 1.1rem;
}

/* Режим игры */
.mode-switch {
  display: flex;
  justify-content: center;
  gap: 1rem;
  margin-bottom: 2rem;
}

.mode-option {
  position: relative;
  cursor: pointer;
}

.mode-option input {
  position: absolute;
  opacity: 0;
}

.mode-label {
  display: inline-block;
  padding: 0.5rem 1.25rem;
  border-radius: var(--radius-sm);
  background-color: var(--input-bg);
  color: var(--text-secondary);
  font-weight: 500;
  transition: all var(--transition-fast);
}

.mode-option input:checked + .mode-label {
  background: var(--primary-gradient);
  color: white;
  box-shadow: var(--button-shadow);
}

.success-message {
  position: fixed;
  bottom: 30px; /* Было: top: 50% */
  left: 50%;
  transform: translateX(-50%); /* Было: translate(-50%, -50%) */
  display: flex;
  flex-direction: column;
  align-items: center;
  background-color: var(--success-color);
  color: white;
  padding: 1rem 2rem; /* Немного компактнее */
  border-radius: var(--radius-lg);
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
  z-index: 1000;
}

@keyframes bounce-in {
  0% { transform: translateX(-50%) scale(0); } /* Обновлено */
  50% { transform: translateX(-50%) scale(1.2); } /* Обновлено */
  100% { transform: translateX(-50%) scale(1); } /* Обновлено */
}

@keyframes bounce-out {
  0% { transform: translateX(-50%) scale(1); } /* Обновлено */
  100% { transform: translateX(-50%) scale(0); } /* Обновлено */
}

.success-icon {
  font-size: 2.5rem;
  margin-bottom: 0.5rem;
}

.success-text {
  font-weight: 600;
  font-size: 1.25rem;
}

/* Контейнер игры */
.game-container {
  background: var(--surface-color);
  border-radius: var(--radius-lg);
  padding: 2rem;
  box-shadow: var(--card-shadow);
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

/* Стили для дочерних компонентов */
button, input, select, textarea {
  font-family: 'Montserrat', sans-serif;
  color: var(--text-primary);
  border-color: var(--input-border);
  background-color: var(--input-bg);
  transition: all var(--transition-fast);
}

button {
  cursor: pointer;
}

/* Стили для компонента WordInput */
.buttons-wrapper {
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  gap: 10px;
  margin: 0 auto;
  max-width: 400px;
}

.buttons-wrapper button {
  background: var(--primary-gradient);
  color: white;
  border: none;
  font-weight: 500;
  transition: all var(--transition-fast);
}

.buttons-wrapper button:hover {
  transform: translateY(-2px);
  box-shadow: var(--button-shadow);
}

.error {
  color: var(--error-color);
  font-weight: 500;
  margin-top: 10px;
}

/* Адаптивные стили */
@media (max-width: 768px) {
  .game-container {
    padding: 1.5rem;
  }
}

@media (max-width: 480px) {
  #app {
    padding: 1rem;
  }

  .game-container {
    padding: 1.25rem;
  }

  .mode-switch {
    flex-direction: column;
    gap: 0.75rem;
  }

  .mode-label {
    width: 100%;
    text-align: center;
  }

  .success-message {
    padding: 1.5rem;
  }

  .success-icon {
    font-size: 2rem;
  }

  .success-text {
    font-size: 1.1rem;
  }

  .buttons-wrapper {
    grid-template-columns: repeat(5, 1fr);
    gap: 6px;
  }
}

/* Верхняя панель */
.top-bar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1.5rem;
}

/* Обновляем стили переключателя, убираем margin-bottom */
.theme-toggle {
  display: flex;
  justify-content: flex-end;
  margin-bottom: 0;
}

/* Обновляем стили кнопки, убираем margin-bottom */
.back-button {
  display: flex;
  align-items: center;
  background: transparent;
  color: var(--primary-color);
  border: 2px solid var(--primary-color);
  padding: 0.5rem 1rem;
  border-radius: var(--radius-sm);
  font-weight: 600;
  cursor: pointer;
  transition: all var(--transition-fast);
  margin-bottom: 0;
}

/* Если нет кнопки назад, смещаем переключатель темы вправо */
.top-bar:not(:has(.back-button)) .theme-toggle {
  margin-left: auto;
}
</style>