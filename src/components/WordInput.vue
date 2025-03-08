<template>
        <div class="word-input">
          <div class="input-wrapper">
            <input
              type="text"
              v-model="userInput"
              placeholder="Введите слово"
              @keyup.enter="checkAnswer"
              :class="{ shake: showError }"
            />
            <button @click="checkAnswer">Проверить</button>
          </div>
          <transition name="slide-fade">
            <p v-if="errorMessage" class="error">{{ errorMessage }}</p>
          </transition>
        </div>
      </template>

  <script>
  export default {
    props: {
      correctWord: {
        type: String,
        required: true
      }
    },
    data() {
      return {
        userInput: '',
        errorMessage: '',
        showError: false
      }
    },
    methods: {
      checkAnswer() {
        this.$emit('attempt');

        // Используем trim() для удаления пробелов в начале и конце строки
        if (this.userInput.trim().toLowerCase() === this.correctWord.toLowerCase()) {
          this.$emit('correct-answer');
          this.userInput = '';
          this.errorMessage = '';
        } else {
          this.errorMessage = 'Неверно. Попробуйте еще раз.';
          this.showErrorAnimation();
        }
      },
      showErrorAnimation() {
        this.showError = true;
        setTimeout(() => {
          this.showError = false;
        }, 500);
      }
    },
    watch: {
      correctWord() {
        this.errorMessage = '';
      }
    }
  }
  </script>

      <style scoped>
      .word-input {
        margin: 30px 0;
        width: 100%;
      }

      .input-wrapper {
        display: flex;
        justify-content: center;
        gap: 10px;
        max-width: 100%;
      }

      input {
        padding: 10px 15px;
        font-size: 18px;
        border-radius: 8px;
        border: 2px solid #ddd;
        width: 60%;
        max-width: 300px;
        transition: all 0.3s ease;
        font-family: 'Montserrat', sans-serif;
        box-sizing: border-box;
      }

      input:focus {
        outline: none;
        border-color: #3a7bd5;
        box-shadow: 0 0 0 2px rgba(58, 123, 213, 0.2);
      }

      input.shake {
        animation: shake 0.5s cubic-bezier(0.36, 0.07, 0.19, 0.97) both;
        transform: translate3d(0, 0, 0);
        border-color: #e74c3c;
      }

      button {
        padding: 10px 20px;
        font-size: 18px;
        border-radius: 8px;
        border: none;
        background: linear-gradient(135deg, #3a7bd5, #00d2ff);
        color: white;
        cursor: pointer;
        transition: all 0.3s ease;
        font-family: 'Montserrat', sans-serif;
        font-weight: 600;
        white-space: nowrap;
      }

      button:hover {
        transform: translateY(-2px);
        box-shadow: 0 5px 15px rgba(0, 210, 255, 0.3);
      }

      button:active {
        transform: translateY(0);
      }

      .error {
        color: #e74c3c;
        margin-top: 10px;
        font-weight: 500;
        padding: 5px;
      }

      @keyframes shake {
        10%, 90% { transform: translate3d(-1px, 0, 0); }
        20%, 80% { transform: translate3d(2px, 0, 0); }
        30%, 50%, 70% { transform: translate3d(-4px, 0, 0); }
        40%, 60% { transform: translate3d(4px, 0, 0); }
      }

      .slide-fade-enter-active, .slide-fade-leave-active {
        transition: all 0.3s ease;
      }

      .slide-fade-enter-from, .slide-fade-leave-to {
        transform: translateY(-10px);
        opacity: 0;
      }
      @media (max-width: 600px) {
        .input-wrapper {
          flex-direction: column;
          gap: 15px;
          width: 100%;
        }

        input {
          width: 100%;
          max-width: 100%;
          font-size: 16px;
          padding: 12px;
        }

        button {
          width: 100%;
          padding: 12px;
          font-size: 16px;
        }
      }
      </style>