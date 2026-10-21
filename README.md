<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Acerte o Alvo 🎯</title>
  <style>
    * {
      box-sizing: border-box;
    }

    body {
      margin: 0;
      font-family: Arial, sans-serif;
      background: #101827;
      color: white;
      text-align: center;
      user-select: none;
    }

    h1 {
      color: #38bdf8;
      margin: 18px 0;
    }

    .painel {
      display: flex;
      justify-content: center;
      flex-wrap: wrap;
      gap: 12px;
      margin: 15px;
    }

    .info {
      background: #243247;
      border-radius: 12px;
      padding: 12px 18px;
      min-width: 105px;
      font-size: 17px;
    }

    .info span {
      display: block;
      color: #facc15;
      font-size: 25px;
      font-weight: bold;
      margin-top: 5px;
    }

    #campo {
      position: relative;
      width: min(92vw, 700px);
      height: min(58vh, 470px);
      min-height: 300px;
      margin: 15px auto;
      background: #1e293b;
      border: 3px solid #38bdf8;
      border-radius: 16px;
      overflow: hidden;
      cursor: crosshair;
      touch-action: manipulation;
    }

   const campo = document.getElementById("campo");
const alvo = document.getElementById("alvo");
const mensagem = document.getElementById("mensagem");

let tamanho = 90;
let tempo = 30;
let pontos = 0;
let jogoAtivo = false;
let intervaloTempo;
let movimento;
let fuga;
let ultimaFuga = 0;

function posicionarAlvo() {
  const margem = tamanho / 2 + 4;

  const x = margem + Math.random() *
    (campo.clientWidth - margem * 2);
  const y = margem + Math.random() *
    (campo.clientHeight - margem * 2);

  alvo.style.left = `${x}px`;
  alvo.style.top = `${y}px`;
}

function iniciarJogo() {
  clearInterval(intervaloTempo);
  clearInterval(movimento);
  clearInterval(fuga);

  tamanho = 90;
  tempo = 30;
  pontos = 0;
  jogoAtivo = true;

  alvo.style.width = `${tamanho}px`;
  alvo.style.height = `${tamanho}px`;
  alvo.style.display = "block";

  posicionarAlvo();
  atualizarPainel();

  // 1. Alvo em movimento
  movimento = setInterval(() => {
    if (jogoAtivo) posicionarAlvo();
  }, 1100);

  // 2. O alvo foge quando o mouse se aproxima
  campo.addEventListener("mousemove", fugirDoMouse);

  // 3. Contagem regressiva
  intervaloTempo = setInterval(() => {
    tempo--;
    atualizarPainel();

    if (tempo <= 0) encerrarJogo();
  }, 1000);

  mensagem.textContent = "Valendo! Acerte o alvo!";
}

function fugirDoMouse(evento) {
  if (!jogoAtivo) return;

  const agora = Date.now();
  if (agora - ultimaFuga < 350) return;

  const caixaCampo = campo.getBoundingClientRect();
  const mouseX = evento.clientX - caixaCampo.left;
  const mouseY = evento.clientY - caixaCampo.top;

  const alvoX = parseFloat(alvo.style.left) || 0;
  const alvoY = parseFloat(alvo.style.top) || 0;

  const distancia = Math.hypot(mouseX - alvoX, mouseY - alvoY);

  if (distancia < tamanho * 1.3) {
    ultimaFuga = agora;
    posicionarAlvo();
  }
}

function acertouAlvo() {
  if (!jogoAtivo) return;

  pontos++;

  // 4. Alvo menor a cada acerto
  tamanho = Math.max(22, tamanho - 7);

  alvo.style.width = `${tamanho}px`;
  alvo.style.height = `${tamanho}px`;

  // Ganha pontos extras conforme o alvo diminui
  pontos += tamanho <= 43 ? 1 : 0;

  posicionarAlvo();
  atualizarPainel();

  mensagem.textContent = "Acertou! Agora ficou mais difícil!";
}

function atualizarPainel() {
  const pontuacao = document.getElementById("pontuacao");
  const cronometro = document.getElementById("tempo");

  if (pontuacao) pontuacao.textContent = pontos;
  if (cronometro) cronometro.textContent = tempo;
}

function encerrarJogo() {
  jogoAtivo = false;

  clearInterval(intervaloTempo);
  clearInterval(movimento);
  clearInterval(fuga);

  campo.removeEventListener("mousemove", fugirDoMouse);
  alvo.style.display = "none";

  mensagem.textContent = `Fim de jogo! Você fez ${pontos} pontos.`;
}

// Clique no alvo conta como acerto

alvo.addEventListener("click", (evento) => {
  evento.stopPropagation();
  acertouAlvo();
});
    #mensagem {
      min-height: 26px;
      color: #facc15;
      font-weight: bold;
      padding: 5px;
    }

    button {
      background: #0ea5e9;
      color: white;
      border: none;
      padding: 14px 28px;
      border-radius: 10px;
      font-size: 18px;
      font-weight: bold;
      cursor: pointer;
    }

    button:hover {
      background: #0284c7;
    }

    #resultado {
      font-size: 22px;
      font-weight: bold;
      margin: 16px;
    }

    .dica {
      color: #cbd5e1;
      font-size: 14px;
      padding: 0 12px 18px;
    }
  </style>
</head>
<body>
  <h1>🎯 Acerte o Alvo!</h1>

  <div class="painel">
    <div class="info">Pontos<span id="pontos">0</span></div>
    <div class="info">Tempo<span id="tempo">30</span></div>
    <div class="info">Acertos<span id="acertos">0</span></div>
  </div>

  <div id="campo">
    <div id="alvo" hidden></div>
  </div>

  <div id="mensagem">Clique em começar para jogar!</div>
  <button id="iniciar">Começar jogo</button>
  <div id="resultado"></div>
  <p class="dica">
    Acerte o alvo antes de 2 segundos!<br>
    Acerto: +10 pontos | Erro ou alvo perdido: -5 pontos
  </p>

  <script>
    const campo = document.getElementById("campo");
    const alvo = document.getElementById("alvo");
    const pontosTexto = document.getElementById("pontos");
    const tempoTexto = document.getElementById("tempo");
    const acertosTexto = document.getElementById("acertos");
    const mensagem = document.getElementById("mensagem");
    const resultado = document.getElementById("resultado");
    const iniciar = document.getElementById("iniciar");

    let pontos = 0;
    let acertos = 0;
    let tempo = 30;
    let tamanho = 90;
    let jogando = false;
    let alvoCriadoEm = 0;
    let relogio = null;
    let verificarAlvo = null;
    let rodada = 0;

    function atualizarPainel() {
      pontosTexto.textContent = pontos;
      tempoTexto.textContent = tempo;
      acertosTexto.textContent = acertos;
    }

    function criarAlvo() {
      if (!jogando) return;

      rodada++;
      const rodadaAtual = rodada;
      const limiteX = campo.clientWidth - tamanho;
      const limiteY = campo.clientHeight - tamanho;

      const x = tamanho / 2 +
        Math.random() * Math.max(0, limiteX);
      const y = tamanho / 2 +
        Math.random() * Math.max(0, limiteY);

      alvo.style.width = tamanho + "px";
      alvo.style.height = tamanho + "px";
      alvo.style.left = x + "px";
      alvo.style.top = y + "px";
      alvo.hidden = false;

      alvoCriadoEm = Date.now();
      mensagem.textContent = "Rápido! Você tem 2 segundos!";

      clearTimeout(verificarAlvo);
      verificarAlvo = setTimeout(() => {
        if (!jogando || rodada !== rodadaAtual) return;

        alvo.hidden = true;
        pontos -= 5;
        atualizarPainel();
        mensagem.textContent = "⏰ O alvo sumiu! -5 pontos";
        criarProximoAlvo();
      }, 2000);
    }

    function criarProximoAlvo() {
      clearTimeout(verificarAlvo);
      if (!jogando) return;

      alvo.hidden = true;
      setTimeout(() => {
        if (jogando) criarAlvo();
      }, 250);
    }

    alvo.addEventListener("pointerdown", (evento) => {
      evento.stopPropagation();
      if (!jogando || alvo.hidden) return;

      clearTimeout(verificarAlvo);
      rodada++;

      pontos += 10;
      acertos++;
      tamanho = Math.max(18, tamanho * 0.90);

      atualizarPainel();
      mensagem.textContent = "🎯 Acertou! +10 pontos";
      criarProximoAlvo();
    });

    campo.addEventListener("pointerdown", (evento) => {
      if (!jogando) return;
      if (evento.target === alvo) return;

      pontos -= 5;
      atualizarPainel();
      mensagem.textContent = "❌ Errou! -5 pontos";
    });

    function terminarJogo() {
      jogando = false;
      clearInterval(relogio);
      clearTimeout(verificarAlvo);
      alvo.hidden = true;
      iniciar.disabled = false;
      iniciar.textContent = "Jogar novamente";
      mensagem.textContent = "Fim de jogo!";
      resultado.textContent =
        `🏆 Pontuação final: ${pontos} pontos | 🎯 ${acertos} acertos`;
    }

    iniciar.addEventListener("click", () => {
      clearInterval(relogio);
      clearTimeout(verificarAlvo);

      pontos = 0;
      acertos = 0;
      tempo = 30;
      tamanho = 90;
      rodada = 0;
      jogando = true;

      resultado.textContent = "";
      iniciar.disabled = true;
      iniciar.textContent = "Jogando...";
      atualizarPainel();
      criarAlvo();

      relogio = setInterval(() => {
        tempo--;
        atualizarPainel();

        if (tempo <= 0) {
          terminarJogo();
        }
      }, 1000);
    });
  </script>
</body>
</html>

<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Target Arena</title>
<style>
* { box-sizing: border-box; }

body {
  margin: 0;
  font-family: Arial, sans-serif;
  background: #101323;
  color: white;
  text-align: center;
}

header {
  padding: 18px;
  background: #1c2140;
  box-shadow: 0 3px 15px #0005;
}

h1 { margin: 5px; color: #65f7ff; }

.hud {
  display: flex;
  justify-content: center;
  gap: 20px;
  flex-wrap: wrap;
  padding: 14px;
  font-size: 18px;
}

button {
  cursor: pointer;
  border: 0;
  border-radius: 9px;
  padding: 11px 16px;
  font-weight: bold;
  background: #65f7ff;
  color: #101323;
}

button:hover { filter: brightness(1.15); }
button:disabled { opacity: .45; cursor: not-allowed; }

#arena {
  height: 360px;
  max-width: 850px;
  margin: 0 auto;
  position: relative;
  overflow: hidden;
  background:
    radial-gradient(circle, #26345b, #13182d 75%);
  border: 2px solid #65f7ff;
  border-radius: 14px;
  cursor: crosshair;
  touch-action: manipulation;
}

#target {
  width: 70px;
  height: 70px;
  position: absolute;
  border-radius: 50%;
  background: radial-gradient(circle,
    white 0 10%,
    #ff4242 11% 30%,
    white 31% 48%,
    #ff4242 49% 70%,
    #8d111d 71%);
  box-shadow: 0 0 18px #ff4242;
  transform: translate(-50%, -50%);
  cursor: crosshair;
}

#target.hidden { display: none; }

#message {
  min-height: 28px;
  padding: 12px;
  color: #ffe66d;
  font-weight: bold;
}

.shop {
  max-width: 850px;
  margin: 20px auto;
  padding: 16px;
}

.items {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(145px, 1fr));
  gap: 12px;
}

.item {
  padding: 15px;
  border: 1px solid #414b70;
  border-radius: 12px;
  background: #1c2140;
}

.item .preview {
  font-size: 35px;
  margin: 8px;
}

.item h3 { margin: 7px 0; }
.item p { color: #c5cbe0; font-size: 14px; }

.particle {
  position: absolute;
  width: 8px;
  height: 8px;
  border-radius: 50%;
  pointer-events: none;
  animation: burst .6s ease-out forwards;
}

@keyframes burst {
  to {
    transform: translate(var(--dx), var(--dy)) scale(0);
    opacity: 0;
  }
}

@keyframes flash {
  50% { filter: brightness(2.5); }
}

#arena.hit { animation: flash .18s; }

.crosshair {
  position: absolute;
  z-index: 2;
  pointer-events: none;
  font-size: 28px;
  transform: translate(-50%, -50%);
}

footer {
  padding: 20px;
  color: #929bbd;
  font-size: 13px;
}
</style>
</head>

<body>
<header>
  <h1>🎯 TARGET ARENA</h1>
  <p>Faça pontos, compre melhorias e acerte com estilo!</p>
</header>

<div class="hud">
  <div>💰 Moedas: <b id="coins">0</b></div>
  <div>🎯 Acertos: <b id="score">0</b></div>
  <div>✨ Efeito: <b id="effectName">Básico</b></div>
</div>

<div id="arena">
  <div id="target" title="Acerte o alvo!"></div>
  <div id="crosshair" class="crosshair">⊕</div>
</div>

<div id="message">Clique no alvo para ganhar moedas!</div>

<section class="shop">
  <h2>🛒 Loja de melhorias</h2>
  <p>Compre efeitos e miras usando as moedas que ganhar.</p>
  <div class="items" id="items"></div>
</section>

<footer>Seu progresso é salvo neste navegador.</footer>

<script>
const arena = document.getElementById("arena");
const target = document.getElementById("target");
const coinsEl = document.getElementById("coins");
const scoreEl = document.getElementById("score");
const effectName = document.getElementById("effectName");
const message = document.getElementById("message");
const crosshair = document.getElementById("crosshair");

const upgrades = [
  {
    id: "fire",
    name: "🔥 Explosão de fogo",
    price: 30,
    description: "Partículas laranja e vermelhas.",
    preview: "🔥"
  },
  {
    id: "ice",
    name: "❄️ Explosão de gelo",
    price: 50,
    description: "Efeito de partículas azuis.",
    preview: "❄️"
  },
  {
    id: "neon",
    name: "⚡ Explosão neon",
    price: 80,
    description: "Partículas coloridas brilhantes.",
    preview: "⚡"
  },
  {
    id: "laser",
    name: "🔴 Mira laser",
    price: 40,
    description: "Troca o visual da mira.",
    preview: "🔴"
  },
  {
    id: "star",
    name: "🌟 Mira estelar",
    price: 60,
    description: "Uma mira em formato de estrela.",
    preview: "🌟"
  }
];

let saved = {};
try {
  saved = JSON.parse(localStorage.getItem("targetArenaSave")) || {};
} catch {}

let coins = Number.isFinite(saved.coins) ? saved.coins : 0;
let score = Number.isFinite(saved.score) ? saved.score : 0;
let owned = Array.isArray(saved.owned) ? saved.owned : [];
let equipped = saved.equipped || "";
let aim = saved.aim || "basic";

function save() {
  localStorage.setItem("targetArenaSave", JSON.stringify({
    coins, score, owned, equipped, aim
  }));
}

function updateHUD() {
  coinsEl.textContent = coins;
  scoreEl.textContent = score;

  const active = upgrades.find(item => item.id === equipped);
  effectName.textContent = active
    ? active.name
    : aim === "laser" ? "Mira laser"
    : aim === "star" ? "Mira estelar"
    : "Básico";
}

function renderShop() {
  const items = document.getElementById("items");
  items.innerHTML = "";

  upgrades.forEach(item => {
    const isOwned = owned.includes(item.id);
    const isEffect = ["fire", "ice", "neon"].includes(item.id);
    const isEquipped = isEffect
      ? equipped === item.id
      : aim === item.id;

    const card = document.createElement("div");
    card.className = "item";

    const preview = document.createElement("div");
    preview.className = "preview";
    preview.textContent = item.preview;

    const title = document.createElement("h3");
    title.textContent = item.name;

    const desc = document.createElement("p");
    desc.textContent = item.description;

    const btn = document.createElement("button");
    btn.textContent = isEquipped
      ? "Equipado"
      : isOwned ? "Equipar" : `Comprar (${item.price} 🪙)`;
    btn.disabled = isEquipped || (!isOwned && coins < item.price);

    btn.addEventListener("click", () => {
      if (!owned.includes(item.id)) {
        if (coins < item.price) return;
        coins -= item.price;
        owned.push(item.id);
      }

      if (isEffect) {
        equipped = item.id;
      } else {
        aim = item.id;
      }

      save();
      updateHUD();
      renderShop();
      message.textContent = "Melhoria equipada! ✨";
    });

    card.append(preview, title, desc, btn);
    items.appendChild(card);
  });

  updateHUD();
}

function moveTarget() {
  const padding = 40;
  const maxX = Math.max(padding, arena.clientWidth - padding);
  const maxY = Math.max(padding, arena.clientHeight - padding);

  target.style.left =
    (padding + Math.random() * (maxX - padding)) + "px";
  target.style.top =
    (padding + Math.random() * (maxY - padding)) + "px";
}

function createParticles(x, y) {
  let colors = ["#ffffff", "#ff4242", "#ffb347"];

  if (equipped === "fire") {
    colors = ["#ff3b20", "#ff9f1c", "#ffe66d"];
  } else if (equipped === "ice") {
    colors = ["#72f1ff", "#ffffff", "#4385ff"];
  } else if (equipped === "neon") {
    colors = ["#ff4dff", "#65f7ff", "#b7ff45", "#ffe66d"];
  }

  const count = equipped ? 28 : 12;

  for (let i = 0; i < count; i++) {
    const particle = document.createElement("div");
    particle.className = "particle";
    particle.style.left = x + "px";
    particle.style.top = y + "px";
    particle.style.background =
      colors[Math.floor(Math.random() * colors.length)];

    const angle = Math.random() * Math.PI * 2;
    const distance = 30 + Math.random() * 75;

    particle.style.setProperty("--dx",
      Math.cos(angle) * distance + "px");
    particle.style.setProperty("--dy",
      Math.sin(angle) * distance + "px");

    arena.appendChild(particle);
    particle.addEventListener("animationend", () => particle.remove());
  }

  arena.classList.remove("hit");
  void arena.offsetWidth;
  arena.classList.add("hit");
}

target.addEventListener("click", event => {
  event.stopPropagation();

  const rect = arena.getBoundingClientRect();
  const x = event.clientX - rect.left;
  const y = event.clientY - rect.top;

  createParticles(x, y);

  score++;
  coins += 5;

  message.textContent = "+5 moedas! 🎉";
  moveTarget();
  save();
  updateHUD();
  renderShop();
});

arena.addEventListener("pointermove", event => {
  const rect = arena.getBoundingClientRect();
  crosshair.style.left = (event.clientX - rect.left) + "px";
  crosshair.style.top = (event.clientY - rect.top) + "px";
});

arena.addEventListener("click", event => {
  if (event.target === target) return;
  message.textContent = "Você errou! Tente acertar o alvo.";
});

function updateAim() {
  crosshair.textContent =
    aim === "laser" ? "✛" :
    aim === "star" ? "✦" : "⊕";

  crosshair.style.color =
    aim === "laser" ? "#ff4242" :
    aim === "star" ? "#ffe66d" : "#65f7ff";
}

const oldRenderShop = renderShop;
renderShop = function() {
  oldRenderShop();
  updateAim();
};

moveTarget();
renderShop();
</script>
</body>
</html>

<div class="hud">
  <div>⏱️ Tempo: <b id="timer">60</b>s</div>
  <div>📏 Tamanho: <b id="targetSize">70</b>px</div>
</div>
