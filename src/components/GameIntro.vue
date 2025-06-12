<template>
  <div v-if="showIntro" class="intro-screen">
    <div class="content">
      <h1 class="game-title">Elowen: Ecos de uma Última Lembrança</h1>
      <button 
        @click="startGame" 
        class="enter-button"
        :class="{ 'pressed': isPressed }"
      >
        Pressione ENTER para iniciar
      </button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'GameIntro',
  data() {
    return {
      showIntro: true,
      isPressed: false
    }
  },
  methods: {
    startGame() {
      this.isPressed = true;
      setTimeout(() => {
        this.showIntro = false;
        this.$emit('game-started');
      }, 500);
    }
  },
  mounted() {
    window.addEventListener('keydown', (e) => {
      if (e.key === 'Enter' && this.showIntro) {
        this.startGame();
      }
    });
  },
  beforeUnmount() {
    window.removeEventListener('keydown', (e) => {
      if (e.key === 'Enter' && this.showIntro) {
        this.startGame();
      }
    });
  }
}
</script>

<style scoped>
.intro-screen {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: #000;
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 9999;
  animation: fadeIn 1s ease;
}

.content {
  text-align: center;
  color: white;
}

.game-title {
  font-size: 3.5rem;
  font-weight: bold;
  margin-bottom: 2rem;
  text-shadow: 2px 2px 4px rgba(255, 0, 0, 0.5);
  animation: flicker 4s infinite;
}

.enter-button {
  background: transparent;
  border: 2px solid #ff0000;
  color: #ff0000;
  padding: 1rem 2rem;
  font-size: 1.5rem;
  cursor: pointer;
  transition: all 0.3s ease;
  text-shadow: 1px 1px 2px rgba(255, 0, 0, 0.3);
}

.enter-button:hover {
  background: rgba(255, 0, 0, 0.1);
  transform: scale(1.05);
}

.enter-button.pressed {
  transform: scale(0.95);
  background: rgba(255, 0, 0, 0.2);
}

@keyframes flicker {
  0%, 100% { opacity: 1; }
  50% { opacity: 0.8; }
}

@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

@keyframes fadeOut {
  from { opacity: 1; }
  to { opacity: 0; }
}
</style> 