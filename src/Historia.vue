<template>
  <div class="min-h-screen flex items-center justify-center p-4 bg-black relative" tabindex="0"
    @keyup.enter="advanceTextPart">
    <GameIntro @game-started="startGame" />
    <div v-if="gameStarted" class="game-content fixed inset-0 flex items-center justify-center">
      <div class="absolute inset-0 bg-grainy opacity-30 pointer-events-none"></div>
      <div v-if="currentScene"
        class="max-w-2xl w-full bg-black bg-opacity-95 p-8 rounded-lg shadow-2xl border-2 border-red-950 relative overflow-hidden"
        :style="{ backgroundImage: `url(${currentScene.background})` }">
        <div class="absolute top-0 left-0 w-full h-4 bg-red-950 animate-drip"></div>
        <h1
          class="text-4xl font-extrabold mb-6 text-red-700 animate-flicker tracking-widest drop-shadow-[0_2px_2px_rgba(255,0,0,0.5)]">
          {{ currentScene.title }}
        </h1>
        <p class="mb-8 text-gray-400 leading-relaxed italic text-lg drop-shadow-[0_1px_1px_rgba(0,0,0,0.8)]">
          {{ currentTextPart }}
        </p>
        <div v-if="isLastTextPart && hasChoices" class="space-y-5">
          <button v-for="(choice, index) in currentScene.choices" :key="index"
            @click="navigateToScene(choice.nextScene)"
            class="w-full bg-red-900 hover:bg-red-800 text-gray-200 font-bold py-3 px-5 rounded-lg transition duration-300 ease-in-out transform hover:scale-105 hover:shadow-[0_0_15px_rgba(255,0,0,0.5)] hover:skew-x-1">
            {{ choice.text }}
          </button>
        </div>
        <button v-if="isLastTextPart && currentScene.back" @click="navigateBack"
          class="mt-6 w-full bg-gray-900 hover:bg-gray-800 text-gray-500 font-bold py-3 px-5 rounded-lg transition duration-300 ease-in-out hover:shadow-[0_0_10px_rgba(255,255,255,0.1)]">
          Voltar
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import GameIntro from './components/GameIntro.vue'

export default {
  name: 'Game',
  components: {
    GameIntro
  },
  data() {
    return {
      currentSceneId: 'start',
      sceneHistory: [],
      currentTextPartIndex: 0,
      textParts: [],
      gameStarted: false
    };
  },
  computed: {
    currentScene() {
      return SCENES[this.currentSceneId] || null;
    },
    hasChoices() {
      return this.currentScene.choices && this.currentScene.choices.length > 0;
    },
    currentTextPart() {
      return this.textParts[this.currentTextPartIndex] || '';
    },
    isLastTextPart() {
      return this.currentTextPartIndex >= this.textParts.length - 1;
    }
  },
  methods: {
    startGame() {
      this.gameStarted = true
      this.textParts = this.splitText(this.currentScene.text)
      this.$el.focus()
    },
    splitText(text) {
      const sentences = text.match(/[^.!?]+[.!?]+(?:\s|$)|[^.!?]+$/g) || [text];
      const parts = [];
      let currentPart = '';
      let charCount = 0;
      const maxChars = 200;

      sentences.forEach(sentence => {
        if (charCount + sentence.length <= maxChars) {
          currentPart += sentence + ' ';
          charCount += sentence.length + 1;
        } else {
          if (currentPart) parts.push(currentPart.trim());
          currentPart = sentence + ' ';
          charCount = sentence.length + 1;
        }
      });
      if (currentPart) parts.push(currentPart.trim());
      return parts.length > 0 ? parts : [text];
    },
    navigateToScene(nextScene) {
      this.sceneHistory.push(this.currentSceneId);
      this.currentSceneId = nextScene;
      this.currentTextPartIndex = 0;
      this.textParts = this.splitText(SCENES[nextScene].text);
    },
    navigateBack() {
      const previousSceneId = this.sceneHistory.pop() || 'start';
      this.currentSceneId = previousSceneId;
      this.currentTextPartIndex = 0;
      this.textParts = this.splitText(SCENES[previousSceneId].text);
    },
    advanceTextPart() {
      if (!this.gameStarted) return
      if (this.currentTextPartIndex < this.textParts.length - 1) {
        this.currentTextPartIndex++;
      }
    }
  },
  mounted() {
    this.textParts = this.splitText(this.currentScene.text);
    this.$el.focus();
  },
  updated() {
    this.$el.focus();
  }
};
</script>

<style scoped>
.min-h-screen {
  background-color: #000000;
}

.max-w-2xl {
  background-size: cover;
  background-position: center;
}

.bg-grainy {
  background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 200 200'%3E%3Cfilter id='noiseFilter'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.65' numOctaves='3' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%' height='100%' filter='url(%23noiseFilter)'/%3E%3C/svg%3E");
  background-size: 100px 100px;
}

@keyframes flicker {

  0%,
  19%,
  21%,
  23%,
  25%,
  54%,
  56%,
  100% {
    opacity: 1;
  }

  20%,
  24%,
  55% {
    opacity: 0.3;
  }
}

.animate-flicker {
  animation: flicker 1.5s infinite;
}

@keyframes drip {
  0% {
    transform: translateY(-100%);
  }

  100% {
    transform: translateY(100%);
  }
}

.animate-drip {
  animation: drip 5s infinite linear;
}

.game-content {
  animation: fadeIn 0.5s ease;
}

@keyframes fadeIn {
  from {
    opacity: 0;
  }

  to {
    opacity: 1;
  }
}
</style>