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
          <button v-for="(choice, index) in currentScene.choices" :key="index" @click="navigateToScene(choice.nextScene)"
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

const SCENES = {

  //INÍCIO-----------------------------------------------------------------------------
  start: {
    title: 'A Vila dos Condenados',
    text: 'Você acorda em uma pequena vila. É noite, e a lua brilha alto no céu. Você se levanta e observa a paisagem. As casas são pequenas, sua grande maioria feitas de madeira. Todas parecem abandonadas há muito tempo. Nos seus bolsos há apenas uma lanterna. Não tem ninguém andando pelas ruas, e não tem nenhuma iluminação. Existe uma grande rua de terra à frente, uma placa próxima e uma pequena carroça. O que você faz?',
    background: '/Imagens_Caminho_Introdução-Frente/Rua_vila.jpg',
    choices: [
      { text: 'Seguir a rua em frente', nextScene: 'ruaPrincipal' },
      { text: 'Olhar a placa', nextScene: 'placa' },
      { text: 'Vasculhar a carroça', nextScene: 'carroça' }
    ]
  },
  placa: {
    title: 'Placa da vila',
    text: 'A placa de madeira está quebrada, com "Elowen" gravado em sangue coagulado, pingando lentamente. Um vento gélido sopra, carregando sussurros que dizem: "Você não pertence aqui…" Algo invisível toca sua mão, e você ouve risadas distorcidas. Você corre para a rua principal, sentindo olhos famintos te perseguirem.',
    background: '/Imagens_Caminho_Introdução-Frente/Placa_ensanguentada.jpg',
    choices: [{ text: 'Voltar para a rua', nextScene: 'ruaPrincipal' }]
  },
  carroça: {
    title: 'Carroça da vila',
    text: 'Você vasculha a carroça, e dentro dela você encontra uma faca de caça e uma pequena bolsa. Após pegar o que encontrou, você decide seguir a rua principal em frente.',
    background: '/Imagens_Caminho_Introdução-Frente/Imagem_carroça.png',
    choices: [{ text: 'Voltar para a rua', nextScene: 'ruaPrincipal' }]
  },
  ruaPrincipal: {
    title: 'Rua Principal',
    text: 'Você seguiu a rua e se deparou com duas casas: uma à esquerda, e outra à direita. A da direita parece mais conservada, enquanto a da esquerda está muito deteriorada. O que você faz?',
    background: '/Imagens_Caminho_Introdução-Frente/Cruzamento.png',
    choices: [
      { text: 'Explorar a casa da direita', nextScene: 'casaDireita' },
      { text: 'Explorar a casa da esquerda', nextScene: 'casaEsquerda' },
      { text: 'Seguir em frente', nextScene: 'cruzamento' }
    ]
  },
  //INÍCIO-----------------------------------------------------------------------------

  //CASA DA DIREITA---------------------------------------------------------------------
  casaDireita: {
    title: 'Casa da Direita - O Abismo Silencioso',
    text: 'Você se aproxima da casa, e ela está com a porta ligeiramente aberta. Você liga a lanterna, entra nela e se depara com três cômodos: Quarto, banheiro e cozinha. Em qual você quer ir primeiro?',
    background: '/Imagens_Caminho_Introdução-Frente/Casa_direita.png',
    choices: [
      { text: 'Ir para o quarto', nextScene: 'quartoCasaDireita' },
      { text: 'Ir para a cozinha', nextScene: 'cozinhaCasaDireita' },
      { text: 'Ir para o banheiro', nextScene: 'banheiroCasaDireita' }
    ]
  },
  quartoCasaDireita: {
    title: ' O quarto',
    text: 'Você adentra o quarto e se depara com poucos móveis: um armário e uma cama. A princípio não há nada à vista que possa lhe auxiliar em sua jornada ou ajudar a compreender onde está. Porém ao abrir o armário você se depara com um bilhete, com uma escrita em sangue, que diz: "Você achou que podia fugir disso? Que ninguém descobriria? A culpa não desaparece só porque você a esconde!" Assustado, você sai e volta para o hall de entrada, pensando no que o bilhete queria dizer. Gostaria de explorar mais?',
    background: '/Imagens_Caminho_Introdução-Frente/Bilhete_sangue_quarto.png',
    choices: [
      { text: 'Sim, continuar', nextScene: 'continuarCasaDireita' },
      { text: 'Voltar para a rua', nextScene: 'cruzamento' }
    ],
    back: true
  },
  cozinhaCasaDireita: {
    title: 'A cozinha',
    text: 'Você entra na cozinha e se depara com uma mesa, uma pia e alguns armários. Não há nada na mesa, e dentro dos armários você encontra apenas alguns artigos de porcelana e louça. Porém em cima da pia tem um bilhete, com apenas alguns fragmentos de texto legíveis, e o resto manchado por sangue. Nele consta os seguintes escritos: "___o amul______não___prote___eu achei___presente___mas___sussurros___noite___cada vez mais alto___eles___dentro das paredes___não me deix___dormir. eu___sinto___olhos___atrás de mim___sangue___tanto sang______se encontrar___n___toque." Ao lado dele tem um amuleto, cujo formato lembra um losango, com um cordão amarrado a ele. Você quer pegá-lo?',
    background: '/Imagens_Caminho_Introdução-Frente/Imagem_bilhete_cozinha.png',
    choices: [
      { text: 'Sim, pegar o amuleto', nextScene: 'cozinhaCasaDireitaAmuleto' },
      { text: 'Não, ignorar e voltar para o hall', nextScene: 'continuarCasaDireita' }
    ],
    back: true
  },
  cozinhaCasaDireitaAmuleto: {
    title: 'Amuleto do Inferno',
    text: 'Ao tocar o amuleto, uma sombra grotesca se ergue, com garras que rasgam o ar! Ela grita: "Você é meu!" Você cai no chão, mas se levanta rapidamente e foge para o hall. Explorar mais?',
    background: '/Imagens_Caminho_Introdução-Frente/Amuleto_maligno.jpg',
    choices: [
      { text: 'Sim, continuar', nextScene: 'continuarCasaDireita' },
      { text: 'Fugir para a rua', nextScene: 'cruzamento' }
    ],
    back: true
  },
  banheiroCasaDireita: {
    title: 'O banheiro',
    text: 'Você entra no banheiro e se depara apenas com uma privada e uma pia, nada demais. Não há nada de bom aqui. Você decide voltar para o hall de entrada. Gostaria de explorar mais?',
    background: '/Imagens_Caminho_Introdução-Frente/Banheiro_casa_direita.png',
    choices: [
      { text: 'Sim, continuar', nextScene: 'continuarCasaDireita' },
      { text: 'Fugir para a rua', nextScene: 'cruzamento' }
    ],
    back: true
  },
  continuarCasaDireita: {
    title: 'Casa da Direita - O Tormento Persiste',
    text: 'De volta ao hall, você pode explorar mais cômodos ou sair para a rua. Para onde você vai?',
    background: '/Imagens_Caminho_Introdução-Frente/Casa_direita.png',
    choices: [
      { text: 'O quarto', nextScene: 'quartoCasaDireita' },
      { text: 'A cozinha', nextScene: 'cozinhaCasaDireita' },
      { text: 'Banheiro sangrento', nextScene: 'banheiroCasaDireita' },
      { text: 'Rua principal', nextScene: 'cruzamento' }
    ],
    back: true
  },
  //CASA DA DIREITA---------------------------------------------------------------------

  //CASA DA ESQUERDA--------------------------------------------------------------------
  casaEsquerda: {
    title: 'Casa da esquerda',
    text: 'Você se aproxima da casa, desprovida de porta, e percebe que a qualquer momento tudo pode vir abaixo, mas mesmo assim decide entrar. O interior está todo retorcido e destruído, e quase todos os locais estão bloqueados por escombros. O único local que é possível entrar é em um quarto. Você quer entrar nesse quarto?',
    background: '/Imagens_Caminho_Introdução-Frente/Casa_esquerda.png',
    choices: [
      { text: 'Sim, entrar no quarto', nextScene: 'quartoCasaEsquerda' },
      { text: 'Fugir para a rua', nextScene: 'cruzamento' }
    ]
  },
  quartoCasaEsquerda: {
    title: 'O quarto escuro',
    text: 'Você liga a lanterna, avança para o quarto, e assim que entra é tomado pelo terror: há um cadáver de uma mulher em cima da cama, e acima dele, na parede, há uma palavra,escrita em sangue:  N"YARAH! Aparentemente não há mais nada no quarto além dessa cena horrenda, com exceção de um pequeno abajur ao lado da cama. Tomado pelo medo, você se pergunta quem era aquela mulher, e se a escrita na parede seria o nome dela. Você não consegue reconhecê-la, mas sente que a conhece de algum lugar. Talvez ela possa ter algo que explique aquele nome. Gostaria de vasculhar o cadáver?',
    background: '/Imagens_Caminho_Introdução-Frente/Cena_cadaver_quarto.png',
    choices: [
      { text: 'Sim, vasculhar', nextScene: 'flashbackCasaEsquerda' },
      { text: 'Fugir para a rua', nextScene: 'cruzamento' }
    ]
  },
  flashbackCasaEsquerda: {
    title: 'Visão Infernal',
    text: 'Você avança e toca no cadáver. No mesmo instante, você fica tonto e desmaia no chão, e começa a ter um sonho. "Você está na mesma casa, mas ela parece totalmente intacta. Observando os arredores, percebe que há um relógio na parede, marcando 21h47. Abaixo dele, sentado à mesa, um homem lê seu jornal. Ele se levanta e lhe chama: -  Querida, estou saindo para meu turno de vigia. A troca da guarda ocorrerá logo. Não abra a porta para ninguém, e em último caso, se tranque no quarto. O homem sai, e você fica sozinho. Vendo como está tarde da noite, decide ir dormir em seu quarto. Porém, logo após deitar, escuta um barulho estranho vindo de onde estava anteriormente, algo parecido com passos, e decide ir investigar. Você retorna, vasculha o local, e não encontra o que poderia ter sido a fonte do barulho. Pensando ter sido coisa da sua cabeça, você volta ao quarto e se deita novamente, e fecha os olhos. De repente, o ar começa a ficar pesado, e você sente algo te observando. Estendendo a mão, acende o pequeno abajur ao lado da cama, e quando abre os olhos... Uma enorme criatura negra pula em você, com mãos em formas de garras e rosto pálido. Seus olhos são vazios, e sua boca demonstra dentes pontiagudos". Você acorda assustado e aterrorizado, suando frio e com taquicardia. Você rapidamente sai do quarto e da casa, voltando para a rua principal. Sua mente está acelerada, sem entender o que está acontecendo.',
    background: '/Imagens_Caminho_Introdução-Frente/N\'YARAH.png',
    choices: [
      { text: 'Seguir a rua', nextScene: 'cruzamento' },
    ]
  },
  //CASA DA ESQUERDA--------------------------------------------------------------------

  cruzamento: {
    title: 'O cruzamento',
    text: 'Alguns metros à frente, você chega em um cruzamento, e é possível seguir para três direções: para a esquerda, para a direita e em frente. Qual caminho você escolhe?',
    background: '/Imagens_Caminho_Introdução-Frente/Cruzamento.png',
    choices: [
      { text: 'Esquerdo', nextScene: 'caminhoEsquerda' },
      { text: 'Direito', nextScene: 'caminhoDireita' },
      { text: 'Frente', nextScene: 'caminhoFrente' }
    ]
  },

  //CAMINHO FRENTE-------------------------------------------------------------------------
  caminhoFrente: {
    title: 'O caminho amaldiçoado',
    text: 'Você decidiu seguir pelo caminho em frente, sempre alerta aos arredores sombrios e misteriosos daquela pequena vila. Tudo ao redor parece abandonado há muitos anos, a maior parte das casas destruídas, sem sinal claro do que possa ter acontecido. Após algumas dezenas de metros, você percebe que há algumas coisas diferentes no cenário: há uma farmácia em um dos lados da rua, com uma guarita à frente dela. Além disso, você consegue ver um carro batido entrando na parede de madeira de uma das casas. Onde você gostaria de explorar?',
    background: '/Imagens_Caminho_Introdução-Frente/Caminho com a farmácia_guarita_carro.png',
    choices: [
      { text: 'Guarita', nextScene: 'guarita'},
      { text: 'Farmácia', nextScene: 'farmacia' },
      { text: 'Carro batido', nextScene: 'carroBatido' }
    ]
  },

  guarita: {
    title: 'Guarita',
    text: 'Você decidiu olhar na guarita para verificar se havia algo que pudesse ser de alguma ajuda nessa vila desolada. Ao entrar la, você nota alguns relatórios escritos logo acima da pequena bancada que deveria ser onde o vigia ficava. Você começa a lê-los: "18/06/1991, 01h53 - Tem algo me observando. Sinto um arrepio na espinha, e estou suando frio. Já olhei para todos os lados, mas não consigo ver o que ou quem está fazendo isso". "18/06/1991, 02h37 - Vi um vulto perto de uma das casas. Ele não parecia um humano, e tinha uma forma grotesca. Quando pisquei, ele desapareceu, sem deixar rastros". "18/06/1991, 03h17 - Ele apareceu de novo, e agora está me encarando. não consigo ver seu olho, e sua forma é horrenda e retorcida demais para ser descrita. Estou trêmulo, e meu desespero está aumentando cada vez mais". "18/06/1991, 03h34 - Ele está vindo em minha direção, não sei o que fazer além de me agarrar a pequena faca que trouxe comigo. Não posso morrer aqui, minha irmã precisa de mim". A parede está respingada de sangue, e é possível perceber a palavra "SOCORRO" escrita de forma desesperada, numa tentativa de pedir por ajuda, provavelmente feita com o sangue do vigia que fora brutalmente morto ali. Porém o corpo dele não está em lugar algum. Você não encontra mais nada ali, e decide voltar para a rua, assustado com o que leu e pensando quem poderia ser o tal vulto. Cada vez mais perguntas e menos respostas. Você pode explorar os outros locais ou seguir em frente, o que vai fazer? ',
    background: '/Imagens_Caminho_Introdução-Frente/Guarita.jpg',
    choices: [
      { text: 'Continuar explorando', nextScene: 'caminhoFrente' },
      { text: 'Seguir em frente', nextScene: 'trechoFinal'}
    ]
  },
  
  farmacia: {
    title: 'A farmácia',
    text: 'Você decidiu averiguar a farmácia, para procurar por qualquer coisa que pudesse ser útil. Ao entrar, você liga a lanterna e encontra a farmácia toda bagunçada, com prateleiras caídas, remédios pelo chão e algumas manchas de sangue. Você vasculha os remédios e pega alguns que julga serem úteis, além de algumas bandagens. Logo à frente há uma porta que parece levar ao depósito, e há um rastro de sangue no chão levando a ele. Você quer entrar?',
    background: '/Imagens_Caminho_Introdução-Frente/Interior_farmácia.jpg',
    choices: [
      { text: 'Sim, entrar no depósito', nextScene: 'estoqueFarmacia' },
      { text: 'Não, voltar', nextScene: 'caminhoFrente' }
    ]
  },
  estoqueFarmacia: {
  title: 'Depósito do Tormento',
  text: 'Você decidiu entrar no depósito, apesar do medo que sentia. Ao abrir a porta, o rastro de sangue continuava até uma pequena caixa, tampada com uma espécie de tábua, em um canto. Além da caixa, há algumas medicações espalhadas pelas prateleiras, mas nada que seja útil no momento. Percebendo que a única coisa que parece ser importante ali é a caixa, você decide abri-la. Chegando perto, um cheiro pungente atinge suas narinas, causando ânsia e quase te fazendo vomitar. Você retira a tábua e, apesar de não vomitar antes, dessa vez não consegue segurar-se e derrama o que quer que estivesse em seu estômago no chão ao lado da caixa. Dentro dela, membros humanos cortados, braços e pernas, olhos e orelhas, órgãos internos e externos, tudo junto em uma mistura sanguinolenta e putrefata. O cheiro fica muito pior, e você acaba desmaiando. Durante esse desmaio, você tem um sonho...',
  background: '/Imagens_Caminho_Introdução-Frente/Caixa_pedaços.jpg',
  choices: [
    { text: 'Continuar', nextScene: 'ritual' }
  ]
},
  ritual: {
    title: 'O ritual',
    text: '"Nesse sonho você está carregando uma caixa, seguindo uma trilha no meio da floresta. O nervosismo é forte, e você está sempre olhando para trás para se certificar que não está sendo seguido. Havia chovido, e o caminho estava enlameado. Depois de um breve tempo andando você chega em uma pequena clareira, e nela já tem algumas coisas preparadas e devidamente posicionadas: no centro estão posicionados os corpos de duas mulheres, em cima de um altar de pedra, uma mais nova e uma mais velha. Ao redor, três círculos de sangue, com círculos menores feitos ao redor do comprimento de cada um. Você abre a caixa, e dentro dela estão, ainda frescos, membros decepados de diversas pessoas, além de órgãos internos, todos banhados em muito sangue. Você começa a colocar os membros e órgãos nos círculos, lentamente manchando a vegetação com ainda mais sangue. Após terminar, você pega ainda uma bolsa de sangue que tinha guardada em sua mochila, se encaminha para o altar de pedra, e derrama em cima dos dois corpos. Com tudo isso feito, você pega um livro empoeirado de dentro da mochila, e começa a recitar algumas palavras em uma língua estranha, e conforme vai falando o círculo começa a brilhar, nuvens negras começam a se formar no céu, e o sangue na pedra é consumido, dando início ao ritual. Acima dos corpos surge uma entidade sombria, e ela se dirige a você: - Quem perturba N\'yarah, o atormentador das almas, ladrão de memórias e guardião do submundo? Apesar do medo intenso, você a encara e responde a ele: - Meu nome é Elias, e lhe ofereço este sacrifício de sangue em troca de um pedido. Quero que traga essas duas moças de volta à vida. Ele lhe encara, como se avaliando você, e fala: - Realizarei seu pedido. Mas acho que não irei consumir apenas esses pedaços de corpos. . . IREI CONSUMIR A VILA TODA COMO PREÇO! Ele tenta escapar do círculo ritualístico, porém é mantido dentro dele devido ao sangue e aos pedaços de corpos que estão presentes neles. Ele berra: - HUMANO ESTÚPIDO! COMO OUSA ME PRENDER!? ASSIM QUE CONSEGUIR ME LIBERTAR, TE FAREI SOFRER DE UMA MANEIRA INIMAGINÁVEL!! Apesar de amedrontado, você continua a barganha com ele: - Faça o que lhe peço, e esses pedaços de corpos serão seus. Enquanto não fizer o que peço, não deixarei que vá! Porém, para seu horror, você percebe uma falha em um dos círculos, ocorrido devido a sua pegada cheia de lama. Assim que percebe, imediatamente tenta completar o círculo com o pouco sangue que restou na caixa. Mas já é tarde demais, N\'yarah percebe a falha e avança furiosamente em direção a ela. Você observa horrorizado enquanto ele avança em direção a vila..."',
    background: '/Imagens_Caminho_Introdução-Frente/Altar de pedra.jpg',
    choices:[
      { text: 'Acordar', nextScene: 'estoqueFarmacia2'}
    ]
  },
  estoqueFarmacia2:{
    title: 'Medo',
    text: 'Você acorda do sonho todo molhado de suor, com seu coração batendo de maneira muito rápida e uma dor de cabeça explosiva. Você entende o que é aquela caixa. São os membros e sangue utilizados nesse ritual, feito por você mesmo. Você percebe que está começando a ter uma crise nervosa, e resolve tomar um dos calmantes que achou. Tremendo, você sai do depósito, e vai em direção a rua. Você começa a compreender algumas coisas, como o porquê da vila estar deserta, alguns locais manchados de sangue e a maioria dos lugares bagunçados e destruídos. Você entende que o culpado pela vila estar deserta é você, que libertou o monstro que matou a todos... Você quer explorar mais ou seguir em frente?',  
    background: '/Imagens_Caminho_Introdução-Frente/Caixa_pedaços.jpg',
    choices: [
      { text: 'Explorar mais', nextScene: 'caminhoFrente' },
      { text: 'Seguir em frente', nextScene: 'trechoFinal' }
    ]
  },
  carroBatido: {
    title: 'Carro do Pesadelo',
    text: 'Você se aproxima do carro, vendo que ele está entrando na casa devido ao acidente, e a parte da frente dele está muito danificada, com pedaços de madeira em toda parte e com ferragens se introduzindo para dentro, provavelmente devido a força do impacto. A lateral também está danificada, como se houvesse uma colisão com outro veículo. Você olha dentro do carro pelo vidro, e consegue ver uma coisa peculiar: apesar de não haver corpos, tem um ursinho de pelúcia e algumas manchas de sangue nos bancos da frente. Você tenta abrir as portas, mas estão todas trancadas. Você decide quebrar o vidro do carro. Após alguns golpes, o vidro se estilhaça e você consegue destrancá-lo. Entrando no carro, é possível ver alguns itens de maquiagem, além do ursinho de pelúcia. A julgar por esses itens, provavelmente eram uma mulher e uma criança dentro do veículo. Decidido a verificar tudo, você abre o porta-luvas e verifica as portas, mas não encontra nada de importante. Porém, ao tocar no ursinho para averiguar se havia algo que pudesse dar alguma pista, você tem uma visão, um flashback... "Você está dentro do mesmo carro de antes, no banco de trás. A sua frente estão uma mulher ao volante e uma menina no banco do passageiro. Elas estão tranquilas, cantando enquanto a mulher dirige pelas ruas da vila. O vilarejo está movimentado, com pessoas indo e vindo de todos os lados. Enquanto o carro se desloca, ao passar por um cruzamento, outro carro invade a pista, atingindo a lateral do carro. O impacto é muito forte, e o veículo que você está é arremessado contra a parede de madeira de uma das casas. O impacto é forte, e uma parte das ferragens do carro é empurrada para dentro, transpassando ambas as mulheres. Você olha horrorizado para essa cena, e decide tentar ajudá-las da melhor forma. Ao ver seus rostos, porém, o terror toma conta de você, pois as reconhece logo de cara. . . Sua esposa e sua filha. . ." Você retorna ao presente, suando frio e tremendo. Algumas coisas começavam a se encaixar, fragmentos de memória se juntando para montar um quebra cabeça aterrorizante, que poderia finalmente explicar o que estava acontecendo ali. Mas porque tudo isso estava acontecendo? Porque estava vendo e vivendo isso? Você é tomado pela dor da perda, e apenas fica ali parado por alguns minutos. Após essa experiência traumática, você volta para a rua. Deseja explorar mais?',
    background: '/Imagens_Caminho_Introdução-Frente/Carro_batido.jpg',
    choices: [
      { text: 'Explorar mais', nextScene: 'caminhoFrente' },
      { text: 'Seguir em frente', nextScene: 'trechoFinal' }
    ]
  },
  trechoFinal: {
    title: 'A sua ruína',
    text: 'Você segue em frente, e a rua se acaba em uma trilha. Essa trilha parece familiar, e você segue por ela. Após caminhar por um bom tempo, tendo se machucado muito devido a baixa visibilidade, você chega a uma clareira, e no centro dela, um altar de pedra, com os corpos de duas mulheres que você conhece muito bem... Sua esposa e sua filha. Você avança até lá, com lágrimas escorrendo por suas bochechas, e finalmente compreendendo o que tinha acontecido naquela vila. Você toca no corpo frio delas, e no mesmo instante, você ouve um susurro perto de você... - Nada que você fez adiantou, apenas causou dor e sofrimento. Agora sofra as consequências de seus atos no sofrimento eterno. Após isso, N"YARAH te mata, dilacerando violentamente o seu corpo...' ,
    background: '/Imagens_Caminho_Introdução-Frente/Altar de pedra.jpg',
    choices:[
      { text: 'GameOver', nextScene: 'start'}
    ]
  },
  //CAMINHO FRENTE------------------------------------------------------------------------
//CAMINHO ESQUERDA----------------------------------------------------------------------
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
  //CAMINHO ESQUERDA----------------------------------------------------------------------
  //CAMINHO DIREITA-----------------------------------------------------------------------
  caminhoDireita: {
    title: 'O Caminho da Sombra',
    text: 'Você avança pela Rua, estranhamente com partes destruídas, outras quase inteiras. As casas carcomidas exibem vãos escuros onde antes havia portas e janelas. Você enxerga a rua graças à lua e o silêncio pesa. O que você faz?',
    background: '/Imagens_Caminho_Direita/caminhoDireita.jpg',
    choices: [
      { text: 'Marcenaria de Dois Andares', nextScene: 'marcenaria' },
      { text: 'Santuário', nextScene: 'santuario' },
      { text: 'Caçamba do Carro', nextScene: 'cacamba' },
      { text: 'Avançar pela rua', nextScene: 'ruaFinal' }
    ],
    back: true
  },
  marcenaria: {
    title: 'Marcenaria de Dois Andares',
    text: 'Fachada de madeira com uma pequena placa acima pendurada escrita "Marcenaria". Dentro, máquinas e ferramentas cobertas de poeira e serragem no chão.',
    background: '/Imagens_Caminho_Direita/marcenaria.jpg',
    choices: [
      { text: 'Ir mais adentro', nextScene: 'decisoes' },
      { text: 'Armário empoeirado', nextScene: 'armario' },
      { text: 'Voltar para o caminho', nextScene: 'caminhoDireita' }
    ],
  },
  decisoes: {
    title: 'Escolhas',
    text: 'Você se vê frente a duas portas de madeira fechadas, e à esquerda uma escada para o sótão',
    background: '/Imagens_Caminho_Direita/decisoes.jpg',
    choices: [
      { text: 'Porta à esquerda (banheiro)', nextScene: 'banheiro' },
      { text: 'Escada à esquerda sótão', nextScene: 'sotao' },
      { text: 'Porta à direita despensa', nextScene: 'despensa' },
      { text: 'Voltar', nextScene: 'marcenaria' },
    ]
  },
  banheiro: {
    title: 'Banheiro da Marcenaria',
    text: 'Um banheiro sujo e com um líquido preto estranho no chão próximo à banheira. O que você faz?',
    background: '/Imagens_Caminho_Direita/banheiro.jpg',
    choices: [
      { text: 'Ir à pia', nextScene: 'piaBanheiro' },
      { text: 'Voltar', nextScene: 'decisoes' }
    ]
  },
  piaBanheiro: {
    title: 'Frente à pia do banheiro',
    text: 'Um espelho que estranhamente não reflete, e sobre a pia, uma fita cassete. O que você faz?',
    background: '/Imagens_Caminho_Direita/piaBanheiro.jpg',
    choices: [
      { text: 'Analisar etiqueta da fita', nextScene: 'analiseFita' },
      { text: 'Tocar fita (se tiver gravador)', nextScene: 'tocarFita' },
      { text: 'Voltar', nextScene: 'banheiro' }
    ]
  },
  analiseFita: {
    title: 'Etiqueta da Fita',
    text: 'Você lê: "Eles estão sempre aqui." Uma pontada de memória surge, mas nada acontece além disso.',
    background: '/Imagens_Caminho_Direita/analiseFita.jpg',
    choices: [
      { text: 'Voltar', nextScene: 'piaBanheiro' }
    ]
  },
  tocarFita: {
    title: 'Gravação na Fita',
    text: 'A voz sussurra: "Você precisa lembrar…" O eco corta o silêncio.',
    background: '/Imagens_Caminho_Direita/banheiro.jpg',
    choices: [
      { text: 'Voltar', nextScene: 'piaBanheiro' }
    ]
  },
  sotao: {
    title: 'Sótão da Marcenaria',
    text: 'Feixe de luz entra por uma janela. E um baú enferrujado ao fundo e muitos destroços do que já fora um sótão amplo.',
    background: '/Imagens_Caminho_Direita/sotao.jpg',
    choices: [
      { text: 'Vasculhar o baú', nextScene: 'bauVelho' },
      { text: 'Voltar', nextScene: 'decisoes' }
    ]
  },
  bauVelho: {
    title: 'Baú Suspeito',
    text: 'Você olha dentro e tem três itens. Qual sua escolha?',
    background: '/Imagens_Caminho_Direita/bauVelho.jpg',
    choices: [
      { text: 'Pegar Moeda Velha', nextScene: 'pegouMoeda' },
      { text: 'Pegar Fotografia', nextScene: 'pegouFoto' },
      { text: 'Pegar Gravador', nextScene: 'pegouGravador' },
      { text: 'Voltar', nextScene: 'sotao' }
    ]
  },
  pegouMoeda: {
    title: 'Moeda Velha',
    text: 'Você guarda a Moeda Velha.',
    background: '/Imagens_Caminho_Direita/bauVelho.jpg',
    choices: [
      { text: 'Voltar', nextScene: 'bauVelho' }
    ]
  },
  pegouFoto: {
    title: 'Fotografia Não Revelada',
    text: 'Você vê seu rosto ao lado de uma mulher e uma criança. Uma memória vaga surge.',
    background: '/Imagens_Caminho_Direita/pegouFoto.jpg',
    choices: [
      { text: 'Voltar', nextScene: 'bauVelho' }
    ]
  },
  pegouGravador: {
    title: 'Gravador de Fita',
    text: 'Você guarda o Gravador.',
    background: '/Imagens_Caminho_Direita/pegouGravador.jpg',
    choices: [
      { text: 'Voltar', nextScene: 'bauVelho' }
    ]
  },
  despensa: {
    title: 'Despensa',
    text: 'Você abre a porta e vê um vazio, logo sente um puxão invisível que acaba sugando você para a escuridão. Você morreu!',
    background: '/Imagens_Caminho_Direita/despensa.png',
    choices: [
      { text: 'Voltar', nextScene: 'decisoes' }
    ]
  },
  armario: {
    title: 'Armário Empoeirado',
    text: 'Prateleiras vazias e mofo. Nada de útil além de um porta-retrato.',
    background: '/Imagens_Caminho_Direita/armario.jpg',
    choices: [
      { text: 'Analisar retrato', nextScene: 'retrato' },
      { text: 'Voltar', nextScene: 'marcenaria' }
    ]
  },
  retrato: {
    title: 'Porta-retrato',
    text: 'Um retrato do marceneiro que você conhecia e usava técnicas avançadas para moldar tudo que via.',
    background: '/Imagens_Caminho_Direita/armario.jpg',
    choices: [
      { text: 'Voltar', nextScene: 'armario' }
    ]
  },
  cacamba: {
    title: 'Caçamba da caminhonete',
    text: 'Carro abandonado e sujo com duas lanternas quebradas, poeira e folhas secas. Aparentemente nada de útil para pegar.',
    background: '/Imagens_Caminho_Direita/cacamba.jpg',
    choices: [
      { text: 'Voltar', nextScene: 'caminhoDireita' }
    ]
  },
  santuario: {
    title: 'Santuário',
    text: 'Bancos a esquerda e direita com um altar no fundo. E um ser humanoide magro e pálido ajoelhado de costas para você.',
    background: '/Imagens_Caminho_Direita/santuario.jpg',
    choices: [
      { text: 'Aproximar-se no escuro', nextScene: 'fim2' },
      { text: 'Atacar com faca (se tiver)', nextScene: 'fugaSantuario' }
    ]
  },
  fim2: {
    title: 'Morto pela criatura',
    text: 'A criatura te ataca assim que se aproxima. Você acaba morto pela criatura!',
    background: '/Imagens_Caminho_Direita/fim 2.jpg',
    choices: [
      { text: 'Voltar para o início do caminho da direita', nextScene: 'caminhoDireita' }
    ]
  },
  fugaSantuario: {
    title: 'Fuga do Santuário',
    text: 'Você fere a criatura e foge para fora do santuário.',
    background: '/Imagens_Caminho_Direita/santuario.jpg',
    choices: [
      { text: 'Voltar para o início do caminho da direita', nextScene: 'caminhoDireita' }
    ]
  },
  ruaFinal: {
    title: 'Rua Final',
    text: 'A névoa se adensa formando um muro branco.',
    background: '/Imagens_Caminho_Direita/ruaFinal.jpg',
    choices: [
      { text: 'Avançar pela névoa', nextScene: 'fim3' }
    ]
  },
  fim3: {
    title: 'Fim do Esquecimento',
    text: 'N\'yarah o envolve em trevas; sua mente é consumida.',
    background: '/Imagens_Caminho_Direita/ruaFinal.jpg',
    choices: [
      { text: 'Voltar para o início do caminho da direita', nextScene: 'caminhoDireita' }
    ]
  }
};
  //CAMINHO DIREITA-----------------------------------------------------------------------


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
  from { opacity: 0; }
  to { opacity: 1; }
}
</style>