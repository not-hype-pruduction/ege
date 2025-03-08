<template>
        <div class="game-stats">
          <div class="stat-item">
            <div class="stat-label">Правильных ответов:</div>
            <div class="stat-value">
              <transition name="count" mode="out-in">
                <span :key="correctAnswers">{{ correctAnswers }}</span>
              </transition>
            </div>
          </div>
          <div class="stat-item">
            <div class="stat-label">Всего попыток:</div>
            <div class="stat-value">
              <transition name="count" mode="out-in">
                <span :key="totalAttempts">{{ totalAttempts }}</span>
              </transition>
            </div>
          </div>
          <div class="stat-item" v-if="totalAttempts > 0">
            <div class="stat-label">Точность:</div>
            <div class="stat-value">
              <div class="accuracy-bar">
                <div class="accuracy-progress" :style="{ width: accuracy + '%' }"></div>
              </div>
              <span>{{ accuracy }}%</span>
            </div>
          </div>
        </div>
      </template>

      <script>
      export default {
        props: {
          correctAnswers: {
            type: Number,
            default: 0
          },
          totalAttempts: {
            type: Number,
            default: 0
          }
        },
        computed: {
          accuracy() {
            if (this.totalAttempts === 0) return 0;
            return Math.round((this.correctAnswers / this.totalAttempts) * 100);
          }
        }
      }
      </script>

      <style scoped>
      .game-stats {
        margin: 30px 0 10px;
        padding: 20px;
        background: linear-gradient(145deg, #f5f8ff, #ffffff);
        border-radius: 12px;
        box-shadow: 0 4px 15px rgba(0,0,0,0.05);
        transition: all 0.3s ease;
      }

      .stat-item {
        display: flex;
        justify-content: space-between;
        align-items: center;
        margin-bottom: 12px;
        padding: 8px 0;
        border-bottom: 1px solid rgba(0,0,0,0.05);
      }

      .stat-item:last-child {
        border-bottom: none;
        margin-bottom: 0;
      }

      .stat-label {
        font-weight: 600;
        color: #555;
      }

      .stat-value {
        font-size: 20px;
        font-weight: bold;
        color: #3a7bd5;
        display: flex;
        align-items: center;
        gap: 10px;
      }

      .accuracy-bar {
        width: 100px;
        height: 8px;
        background-color: #e0e0e0;
        border-radius: 4px;
        overflow: hidden;
      }

      .accuracy-progress {
        height: 100%;
        background: linear-gradient(90deg, #00d2ff, #3a7bd5);
        border-radius: 4px;
        transition: width 0.7s cubic-bezier(0.19, 1, 0.22, 1);
      }

      .count-enter-active,
      .count-leave-active {
        transition: all 0.5s ease;
      }

      .count-enter-from {
        opacity: 0;
        transform: translateY(-20px);
      }

      .count-leave-to {
        opacity: 0;
        transform: translateY(20px);
      }
      @media (max-width: 600px) {
        .game-stats {
          margin: 20px 0;
          padding: 15px;
        }

        .stat-item {
          margin-bottom: 8px;
          padding: 6px 0;
          flex-direction: column;
          align-items: flex-start;
        }

        .stat-label {
          margin-bottom: 5px;
        }

        .stat-value {
          font-size: 18px;
        }

        .accuracy-bar {
          width: 80px;
          height: 6px;
        }
      }
      </style>