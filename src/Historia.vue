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
  caminhoEsquerda: {
    title: 'Caminho da esquerda',
    text: 'Você decidiu seguir pelo caminho da esquerda, este caminho leva para uma grande rua da vila, o único movimento que você vê são os ratos correndo pela rua, um cheiro ruim toma conta do ar, a lua é tampada por uma nuvem, deixando o ambiente ainda mais desesperador. Nesta rua se localiza o que parece ser o mercado da vila e a rua termina em uma velha estação de trem. Você deseja explorar mais a rua?',
    background: '/Imagens_Caminho_Esquerda/Caminho_esquerda.png', 
    choices: [
      { text: 'Explorar o mercado', nextScene: 'mercado' },
      { text: 'Explorar a estação de trem', nextScene: 'estacaoTrem' }
    ]
  },
  mercado: {
    title: 'Mercado',
    text: 'Ao entrar no mercado, você encontra tudo bagunçado, prateleiras caídas no chão, caixas jogadas para todos os lados. o que você deseja explorar:',
    background: '/Imagens_Caminho_Esquerda/Mercado.png', 
    choices: [
      { text: 'Explorar os fundos da loja', nextScene: 'fundosLoja'},
      { text: 'Explorar o depósito', nextScene: 'deposito' }
    ],
    back: true
  },
  fundosLoja: {
    title: 'Fundos da loja',
    text: 'Você caminha para os fundos da loja, encontra uma bagunça e uns ratos, como não tem nada de interessante, você volta para o interior da loja',
    background: '/Imagens_Caminho_Esquerda/Mercado_interno.png', 
    back: true
  },
  deposito: {
    title: 'Depósito da loja',
    text: 'Você entra dentro do depósito, assim como o restante do mercado, tudo está uma bagunça, andando mais um pouco, você encontra algumas caixas fechadas,ao abrir uma das caixas, um rato acaba pulando em cima de você, após o susto, você abre outra caixa e encontra alguns suprimentos, após você coletar os itens, você volta para o centro do mercado.',
    background: '/Imagens_Caminho_Esquerda/Estoque_mercado.png', 
    back: true
  },
  estacaoTrem: {
    title: 'Estação de trem',
    text: 'Ao entrar na estação, é possível observar, que conforme a cidade, ela está completamente abandonada, como se fosse desativada há décadas, no chão você pode encontrar várias passagens de trens espalhadas, para os mais diversos lugares, o que te leva a se perguntar, o que aconteceu nesta vila, porque uma vila que parecia ser tão movimentada, agora está deserta. Conforme você segue explorando a estação, encontra a sala de segurança, a porta está aberta, deseja entrar na sala de segurança?',
    background: '/Imagens_Caminho_Esquerda/Estação_de_trem.png', 
    choices: [
      { text: 'Sim', nextScene: 'salaSeguranca' },
      { text: 'Seguir investigando a estação', nextScene: 'meioEstacao' }
    ],
    back: true
  },
  salaSeguranca: {
    title: 'Sala de segurança',
    text: 'Você entra na sala e encontra a mesa do segurança, ao lado da mesa, na parede, você encontra um quadro de chaves, nele você observa que falta várias chaves, ao lado do quadro, você encontra um armário, dentro dele encontra algumas fotos, do que parece ser do guarda da estação, como você não encontra mais nada, você sai da sala',
    background: '/Imagens_Caminho_Esquerda/SalaSegurança_estação.png', 
    choices: [
      {text: 'Seguir adiante' ,nextScene: 'meioEstacao'}
    ]
  },
  meioEstacao: {
    title: 'Trem abandonado',
    text: 'Você avança mais um pouco para dentro da estação, chegando nos trilhos, você encontra um trem abandonado. Você entra no trem, dentro dele você encontra várias malas jogadas pelos assentos, como se todos tivessem saído correndo de dentro do trem, em um dos assentos você encontra uma jaleco, aparentemente pertencia ao médico da vila, e como você não encontra mais nada no trem, você sai do trem',
    background: '/Imagens_Caminho_Esquerda/Interior_trem.png', 
    choices: [
      {text: 'Seguir para os trilhos',nextScene: 'trilhos' }
    ],
    back: true
  },
  trilhos: {
    title: 'Trilhos do trem',
    text: 'Ao sair do trem, você começa seguir a linha do trem a fim de poder encontrar alguns vestígios do o que pode ter ocorrido para que toda a vila fosse abandonada. Após uma grande caminhada, começando a perder as esperanças, o cansaço começa a dominar seu corpo, mais adiante, completamente exausto, você decide consumir os recursos que tem a disposição. Agora revigorado, você retoma sua caminhada, e bem mais adiante, ao olhar para trás, você sente como se não tivesse se distanciado da vila, como se por mais que tenha andado muito, permaneceu no mesmo lugar.',
    background: '/Imagens_Caminho_Esquerda/Trilho_trem.png', 
    choices: [
      {text: 'Seguir adiante',nextScene: 'parte2Trilhos' }
    ],
    back: true
  },
  parte2Trilhos: {
    title: 'Trilhos do trem',
    text: 'Depois de muito tempo caminhando, já muito confuso, adiante você avista um túnel, chegando na entrada, percebe-se que está muito escuro, você liga sua lanterna e segue adiante. Já no meio do túnel, sua lanterna começa a falhar, ao iluminar as paredes é possível ver algumas escritas estranhas nas paredes, mas tem uma que te chama a atenção, escrito em sangue na parede, de forma agressiva, como se fosse um aviso a alguém: " VÁ EMBORA, VOCÊ SABE O QUE FEZ", ao ler, sua mente entra em colapso, memórias estranhas passam vagamente em sua cabeça, você começa a ouvir vozes sussurrando em seu ouvido dizendo "CULPADO". Desnorteado com tudo que aconteceu, ainda assim decide continuar a caminhada, mesmo com medo.',
    background: '/Imagens_Caminho_Esquerda/Túnel.png', 
    choices: [
      {text: 'Seguir adiante',nextScene: 'parte3Trilhos' }
    ]
  },
  parte3Trilhos: {
    title: 'Trilhos do trem',
    text: 'Conforme você avança, mais e mais escritas aparecem, as vozes aumentam e a sua noção da realidade já está abalada, você já não sabe mais se tudo que está vendo é real, até que de repente, você escuta passos, como se algo estivesse correndo em sua direção, quando menos se espera, um lobo negro de olhos brancos brilhantes pula em cima de você, te derrubando, rapidamente em desespero, você tenta correr, mas acaba tropeçando devido ao medo, o que decide fazer?',
    background: '/Imagens_Caminho_Esquerda/Tunel_lobo.png', 
    choices: [
      { text: 'Enfrentar o lobo', nextScene: 'lutaLobo' },
      { text: 'Tentar fugir', nextScene: 'fugaLobo' }
    ]
  },
  lutaLobo:{
    title:'Luta contra o lobo',
    text: 'Rapidamente você pega sua mochila, tira a faca que encontrou e arremessa a mochila para tentar distrair o lobo, te salvando da mordida dele, o lobo te derruba, arrancado a faca de sua mão e agarra o seu braço, lutando ferozmente, você consegue alcançar a faca e o esfaqueia na garganta, o matando na hora. Devido aos seus ferimentos e a perda de sangue, você acaba desmaiando, e após um tempo que pareceram horas, você acorda.',
    background: '/Imagens_Caminho_Esquerda/Tunel_lobo.png', 
    choices: [
      { text: 'Seguir em frente', nextScene: 'luzFimTunel'} 
    ]
  },
  fugaLobo:{
    title: 'Fuga do lobo',
    text: 'Você começa a correr desesperadamente, mas como o lobo é mais rápido logo a frente ele te alcança e salta em suas costas te derrubando, o lobo então, começa a te atacar diversas vezes, arrancando suas pernas, depois os braços, até ele dilacerar sua barriga, fazendo com que você tenha uma morte extremanente dolorosa e lenta.',
    background: '/Imagens_Caminho_Esquerda/Tunel_lobo.png',
    choices:[
      {text: 'GAME OVER', nextScene:'start'}
    ]
  },
  luzFimTunel:{
    title: 'A luz no final do túnel',
    text: 'Mesmo ferido, você cambaleia até o final do túnel, onde uma luz brilha intensamente. Querendo sair logo do túnel, você anda o mais rápido que pode em direção ao final dele. Ao chegar lá, a claridade afeta sua visão fortemente enquanto caminha para fora daquele túnel. Ao abrir os olhos, você percebe que está deitado numa cama, olhando para um teto familiar. Você estava na sua casa, e tudo foi apenas um sonho... Ou será que não?',
    background: '/Imagens_Caminho_Esquerda/Túnel.png',
    choices:[
      {text: 'Fim?', nextScene: 'fim'}
    ]
  },
  fim:{
    title: 'O fim?',
    text: 'Não parece que aquilo foi um sonho... Você sente que há algo faltando nessa história...',
    background: '/Imagens_Caminho_Esquerda/Túnel.png',
    choices:[
      {text: 'GAME OVER', nextScene:'start'}
    ]
  },

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