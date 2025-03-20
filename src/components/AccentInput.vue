<template>
  <div class="accent-input">
    <div class="buttons-wrapper">
      <button
          v-for="(char, index) in wordChars"
          :key="index"
          @click="checkAccent(index)"
          :class="{ 'vowel': isVowel(char) }"
          :disabled="!isVowel(char)"
      >
        {{ char }}
      </button>
    </div>
    <transition name="slide-fade">
      <p v-if="errorMessage" class="error">{{ errorMessage }}</p>
    </transition>
  </div>
</template>

<script>
export default {
  props: {
    word: {
      type: String,
      required: true
    }
  },
  data() {
    return {
      errorMessage: '',
      correctIndex: -1
    }
  },
  computed: {
    wordChars() {
      return this.word.toLowerCase().split('');
    },
    // Найти индекс буквы с ударением (с заглавной буквы)
    accentIndex() {
      for (let i = 0; i < this.word.length; i++) {
        if (this.word[i] === this.word[i].toUpperCase() && this.isVowel(this.word[i])) {
          return i;
        }
      }
      return -1;
    }
  },
  methods: {
    isVowel(char) {
      return 'аеёиоуыэюя'.includes(char.toLowerCase());
    },
    checkAccent(index) {
      this.$emit('attempt');

      // Сравниваем с правильным индексом ударной гласной
      if (index === this.accentIndex) {
        this.$emit('correct-answer');
        this.errorMessage = '';
      } else {
        this.errorMessage = 'Неверно. Попробуйте еще раз.';
      }
    }
  },
  watch: {
    word() {
      this.errorMessage = '';
    }
  }
}
</script>

<style scoped>
.accent-input {
  margin: 30px 0;
  width: 100%;
}

.buttons-wrapper {
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
  gap: 8px;
  margin-bottom: 15px;
}

button {
  min-width: 45px;
  height: 45px;
  font-size: 20px;
  border-radius: 8px;
  border: none;
  background-color: #f0f0f0;
  cursor: pointer;
  transition: all 0.3s ease;
  font-family: 'Montserrat', sans-serif;
  box-shadow: 0 2px 5px rgba(0,0,0,0.1);
}

button.vowel {
  background: linear-gradient(135deg, #f8f9ff, #e1e9ff);
  border: 1px solid #d4e0ff;
  color: #4a6baf;
  font-weight: 600;
}

button.vowel:hover {
  background: linear-gradient(135deg, #e1e9ff, #c8d8ff);
  transform: translateY(-3px);
  box-shadow: 0 4px 8px rgba(0,0,0,0.15);
}

button:disabled {
  opacity: 0.6;
  cursor: not-allowed;
  background: #eee;
  transform: none;
  box-shadow: none;
}

.error {
  color: #e74c3c;
  margin-top: 10px;
  font-weight: 500;
  padding: 8px 15px;
  background-color: #ffebee;
  border-radius: 6px;
  animation: pulse 1.5s infinite;
}

@keyframes pulse {
  0% { opacity: 0.8; }
  50% { opacity: 1; }
  100% { opacity: 0.8; }
}

.slide-fade-enter-active, .slide-fade-leave-active {
  transition: all 0.3s ease;
}

.slide-fade-enter-from, .slide-fade-leave-to {
  transform: translateY(-10px);
  opacity: 0;
}

@media (max-width: 600px) {
  button {
    min-width: 38px;
    height: 38px;
    font-size: 17px;
  }
}

@media (max-width: 400px) {
  .buttons-wrapper {
    gap: 4px;
  }

  button {
    min-width: 32px;
    height: 32px;
    font-size: 15px;
  }
}
</style>