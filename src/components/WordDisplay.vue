<template>
    <div class="word-display">
      <h2>Введите правильное слово:</h2>
      <div class="word-with-gap">
        <span v-for="(char, index) in wordWithGapArray" :key="index"
              :class="{'highlight': char === '_'}">
          {{ char }}
        </span>
      </div>
    </div>
  </template>

  <script>
  export default {
    props: {
      wordWithGap: {
        type: String,
        required: true
      }
    },
    computed: {
      wordWithGapArray() {
        return this.wordWithGap.split('');
      }
    }
  }
  </script>

  <style scoped>
  .word-display {
    margin: 30px 0;
    padding: 20px;
    border-radius: 12px;
    background-color: #f9f9f9;
    box-shadow: 0 4px 10px rgba(0,0,0,0.05);
    transition: all 0.3s ease;
  }

  .word-display h2 {
    color: #555;
    margin-bottom: 15px;
    font-weight: 600;
    font-size: 1.4rem;
  }

  .word-with-gap {
    font-size: 32px;
    letter-spacing: 4px;
    font-weight: bold;
    color: #444;
    display: flex;
    justify-content: center;
    flex-wrap: wrap;
  }

  .word-with-gap span {
    display: inline-block;
    margin: 0 3px;
    transition: all 0.3s ease;
  }

  .highlight {
    color: #3a7bd5;
    border-bottom: 3px solid #3a7bd5;
    animation: pulse 1.5s infinite;
    padding: 0 5px;
    font-size: 34px;
  }

  @keyframes pulse {
    0% { transform: scale(1); }
    50% { transform: scale(1.1); }
    100% { transform: scale(1); }
  }
  </style>