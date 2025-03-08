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
    }
  },
  methods: {
    isVowel(char) {
      return 'аеёиоуыэюя'.includes(char.toLowerCase());
    },
    checkAccent(index) {
      this.$emit('attempt');

      // Проверяем, если буква является правильным ударением (в слове с большой буквы)
      if (this.word[index] === this.word[index].toUpperCase() && this.isVowel(this.word[index])) {
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
  gap: 5px;
  margin-bottom: 15px;
}

button {
  min-width: 40px;
  height: 40px;
  font-size: 18px;
  border-radius: 5px;
  border: 1px solid #ddd;
  background-color: #f9f9f9;
  cursor: pointer;
  transition: all 0.3s ease;
  font-family: 'Montserrat', sans-serif;
}

button.vowel {
  background: linear-gradient(135deg, #f9f9f9, #e8f4ff);
  border-color: #cce0ff;
}

button.vowel:hover {
  background: linear-gradient(135deg, #e8f4ff, #cce0ff);
  transform: translateY(-2px);
}

button:disabled {
  opacity: 0.6;
  cursor: not-allowed;
  background: #eee;
  transform: none;
}

.error {
  color: #e74c3c;
  margin-top: 10px;
  font-weight: 500;
  padding: 5px;
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
    min-width: 35px;
    height: 35px;
    font-size: 16px;
  }
}

@media (max-width: 400px) {
  .buttons-wrapper {
    gap: 3px;
  }

  button {
    min-width: 30px;
    height: 30px;
    font-size: 14px;
  }
}
</style>