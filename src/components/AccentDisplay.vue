<template>
  <div class="accent-display">
    <h2>Укажите букву с ударением:</h2>
    <div class="accent-word">
      <span v-for="(char, index) in normalizedWord" :key="index" class="letter">
        {{ char }}
      </span>
    </div>
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
  computed: {
    normalizedWord() {
      return this.word.toLowerCase().split('');
    }
  }
}
</script>

<style scoped>
.accent-display {
  margin: 20px 0;
  padding: 15px;
  border-radius: 12px;
  background-color: #f9f9f9;
  box-shadow: 0 4px 10px rgba(0,0,0,0.05);
  transition: all 0.3s ease;
}

.accent-display h2 {
  color: #555;
  margin-bottom: 15px;
  font-weight: 600;
  font-size: 1.4rem;
}

.accent-word {
  font-size: 32px;
  letter-spacing: 4px;
  font-weight: bold;
  color: #444;
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
}

.letter {
  display: inline-block;
  margin: 0 3px;
  transition: all 0.3s ease;
  min-width: 20px;
}

@media (max-width: 600px) {
  .accent-display {
    margin: 15px 0;
    padding: 12px;
  }

  .accent-display h2 {
    font-size: 1.2rem;
    margin-bottom: 10px;
  }

  .accent-word {
    font-size: 26px;
    letter-spacing: 2px;
  }
}

@media (max-width: 400px) {
  .accent-word {
    font-size: 22px;
    letter-spacing: 1px;
  }
}
</style>