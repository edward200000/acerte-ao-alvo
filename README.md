<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Jogo de Tiro ao Alvo</title>
  <style>
    body {
      background: #101827;
      color: white;
      font-family: Arial, sans-serif;
      text-align: center;
      padding: 20px;
    }

    button {
      padding: 14px 24px;
      background: #22c55e;
      color: white;
      border: none;
      border-radius: 8px;
      font-size: 18px;
      cursor: pointer;
    }

    #campo {
      width: 100%;
      max-width: 600px;
      height: 350px;
      background: #1e293b;
      border: 2px solid #38bdf8;
      margin: 20px auto;
      position: relative;
      overflow: hidden;
    }

    #alvo {
      width: 60px;
      height: 60px;
      border-radius: 50%;
      background: radial-gradient(white 15%, red 16% 45%, white 46% 65%, red 66%);
      position: absolute;
      display: none;
      transform: translate(-50%, -50%);
      cursor: crosshair;
    }
  </style>
</head>
<body>
  <h1>🎯 Tiro ao Alvo</h1>
  <p>Pontos: <span id="pontos">0</span></p>
  <p id="mensagem">Clique para começar!</p>
  <button id="botao">Começar jogo</button>

  <div id="campo">
    <div id="alvo"></div>
  </div>

  <script>
    const botao = document.getElementById("botao");
    const campo = document.getElementById("campo");
    const alvo = document.getElementById("alvo");
    const mensagem = document.getElementById("mensagem");
    const placar = document.getElementById("pontos");

    let pontos = 0;
    let jogando = false;

    function moverAlvo() {
      const x = 35 + Math.random() * (campo.clientWidth - 70);
      const y = 35 + Math.random() * (campo.clientHeight - 70);
      alvo.style.left = x + "px";
      alvo.style.top = y + "px";
    }

    botao.addEventListener("click", function() {
      pontos = 0;
      placar.textContent = pontos;
      jogando = true;
      alvo.style.display = "block";
      moverAlvo();
      mensagem.textContent = "Jogo iniciado! Clique no alvo!";
      botao.textContent = "Jogo iniciado";
    });

    alvo.addEventListener("click", function() {
      if (!jogando) return;
      pontos += 10;
      placar.textContent = pontos;
      moverAlvo();
      mensagem.textContent = "Acertou! +10 pontos!";
    });
  </script>
</body>
</html>