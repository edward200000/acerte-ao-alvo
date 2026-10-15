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

    #alvo {
      position: absolute;
      width: 90px;
      height: 90px;
      border-radius: 50%;
      background: radial-gradient(
        circle,
        white 0 12%,
        red 13% 30%,
        white 31% 48%,
        red 49% 70%,
        #b91c1c 71%
      );
      border: 2px solid white;
      box-shadow: 0 0 18px #ef4444;
      cursor: crosshair;
      transform: translate(-50%, -50%);
    }

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
