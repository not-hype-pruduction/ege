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
  margin: 20px 0;
  padding: 15px;
  border-radius: 12px;
  background-color: var(--game-bg);
  box-shadow: 0 4px 10px var(--shadow);
  transition: all 0.3s ease;
}

.word-display h2 {
  color: var(--text-color);
  margin-bottom: 15px;
  font-weight: 600;
  font-size: 1.4rem;
}

.word-with-gap {
  font-size: 32px;
  letter-spacing: 4px;
  font-weight: bold;
  color: var(--text-color);
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
}

.highlight {
  color: var(--accent-color);
  border-bottom: 3px solid var(--accent-color);
}

@keyframes pulse {
  0% { transform: scale(1); }
  50% { transform: scale(1.1); }
  100% { transform: scale(1); }
}

/* Медиа-запросы для мобильных устройств */
@media (max-width: 600px) {
  .word-display {
    margin: 15px 0;
    padding: 12px;
  }

  .word-display h2 {
    font-size: 1.2rem;
    margin-bottom: 10px;
  }

  .word-with-gap {
    font-size: 26px;
    letter-spacing: 2px;
  }

  .highlight {
    font-size: 28px;
  }
}

@media (max-width: 400px) {
  .word-with-gap {
    font-size: 22px;
    letter-spacing: 1px;
  }

  .highlight {
    font-size: 24px;
    padding: 0 3px;
  }
}
</style>