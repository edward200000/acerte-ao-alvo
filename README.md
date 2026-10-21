<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Acerte ao Alvo — Inferno</title>

<style>
:root {
  --bg: #100307;
  --panel: #19090e;
  --red: #ff3d35;
  --orange: #ff623b;
  --text: #fff3ef;
  --muted: #ffb5a4;
}

* {
  box-sizing: border-box;
}

body {
  margin: 0;
  min-height: 100vh;
  background: radial-gradient(
    ellipse at 50% 70%, #30090d 0%, var(--bg) 65%
  );
  color: var(--text);
  font-family: Arial, sans-serif;
}

main {
  max-width: 1120px;
  margin: auto;
  padding: 18px;
}

header {
  border-bottom: 1px solid #7b3030;
  padding-bottom: 12px;
  margin-bottom: 20px;
}

h1 {
  margin: 0;
  color: #0789ff;
  text-shadow: 0 0 15px #ff382d;
  font-size: clamp(25px, 4vw, 34px);
}

.top {
  display: flex;
  align-items: stretch;
  justify-content: space-between;
  gap: 16px;
  flex-wrap: wrap;
}

.panel, .stat {
  border: 1px solid #a82a2a;
  border-radius: 12px;
  background: #17080d;
}

.panel {
  padding: 16px;
  min-width: 210px;
}

.panel h2 {
  color: var(--orange);
  font-size: 28px;
  margin: 18px 0 10px;
}

.panel p {
  font-size: 11px;
  color: var(--muted);
  margin: 5px 0;
}

.stats {
  display: flex;
  gap: 7px;
}

.stat {
  width: 76px;
  min-height: 90px;
  text-align: center;
  padding: 13px 4px;
}

.stat small {
  display: block;
  color: var(--muted);
  font-size: 10px;
  margin-bottom: 10px;
}

.stat strong {
  font-size: 21px;
}

.arena {
  margin: 20px 0 14px;
  border: 1px solid #612027;
  border-radius: 14px;
  min-height: 300px;
  height: min(48vh, 440px);
  position: relative;
  overflow: hidden;
  background: radial-gradient(
    ellipse at 50% 100%, #4b1111 0%, #1b070c 65%
  );
  touch-action: manipulation;
}

.arena::before {
  content: "";
  position: absolute;
  inset: auto 0 0;
  height: 50px;
  background: linear-gradient(transparent, #090306);
  pointer-events: none;
}

.target {
  position: absolute;
  width: 76px;
  height: 76px;
  border-radius: 50%;
  display: none;
  align-items: center;
  justify-content: center;
  cursor: crosshair;
  user-select: none;
  filter: drop-shadow(0 0 10px #ff3b21);
  font-size: 56px;
  line-height: 1;
  z-index: 2;
}

.message {
  position: absolute;
  inset: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  text-align: center;
  padding: 20px;
  color: #ffd5c9;
  font-size: 18px;
  line-height: 1.6;
}

.controls {
  display: flex;
  gap: 10px;
  flex-wrap: wrap;
  align-items: center;
}

button {
  border: 1px solid #ff5946;
  border-radius: 9px;
  padding: 12px 18px;
  background: #8e1d20;
  color: white;
  font-weight: bold;
  font-size: 15px;
  cursor: pointer;
}

button:hover {
  background: #c32b2b;
}

#hint {
  color: var(--muted);
  font-size: 13px;
}

footer {
  color: #a97878;
  font-size: 12px;
  margin-top: 16px;
}

@media (max-width: 560px) {
  main { padding: 12px; }
  .top { gap: 10px; }
  .panel { width: 100%; }
  .stats { width: 100%; }
  .stat { flex: 1; width: auto; }
  .arena { height: 360px; }
}
</style>
</head>

<body>
<main>
  <header>
    <h1>acerte-ao-alvo</h1>
  </header>

  <section class="top">
    <div class="panel">
      <h2>🔥 INFERNO</h2>
      <hr style="border:0;border-top:1px solid #854040">
      <p id="mode">MODO INFERNO</p>
      <p id="phase">FASE 1</p>
    </div>

    <div class="stats" aria-live="polite">
      <div class="stat">
        <small>PONTOS</small>
        <strong id="score">0</strong>
      </div>
      <div class="stat">
        <small>COMBO</small>
        <strong id="combo">x0</strong>
      </div>
      <div class="stat">
        <small>VIDAS</small>
        <strong id="lives">♥♥♥</strong>
      </div>
      <div class="stat">
        <small>RECORDE</small>
        <strong id="best">0</strong>
      </div>
    </div>
  </section>

  <section class="arena" id="arena" aria-label="Área do jogo">
    <div class="message" id="message">
      Acerte o alvo para ganhar pontos!<br>
      Pressione “Começar jogo” para jogar.
    </div>
    <button class="target" id="target" aria-label="Acertar alvo">🎯</button>
  </section>

  <div class="controls">
    <button id="start">▶ Começar jogo</button>
    <button id="restart" hidden>↻ Jogar novamente</button>
    <span id="hint">Você tem 30 segundos e 3 vidas.</span>
  </div>

  <footer>
    Dica: toque ou clique no alvo assim que ele aparecer.
  </footer>
</main>

<script>
(() => {
  const $ = id => document.getElementById(id);

  const arena = $('arena');
  const target = $('target');
  const message = $('message');

  let score = 0;
  let combo = 0;
  let lives = 3;
  let timeLeft = 30;
  let active = false;
  let timer = null;
  let spawnTimer = null;
  let best = 0;

  try {
    best = Number(
      localStorage.getItem('acerteAoAlvoBest') || 0
    );
  } catch (e) {}

  $('best').textContent = best;

  function update() {
    $('score').textContent = score;
    $('combo').textContent = 'x' + combo;

    $('lives').textContent =
      '♥'.repeat(lives) + '♡'.repeat(3 - lives);

    $('phase').textContent =
      'FASE ' + (1 + Math.floor(score / 100));

    $('hint').textContent = active
      ? `Tempo: ${timeLeft}s — acerte o alvo!`
      : 'Você tem 30 segundos e 3 vidas.';
  }

  function placeTarget() {
    if (!active) return;

    const pad = 8;

    const maxX = Math.max(
      pad,
      arena.clientWidth - target.offsetWidth - pad
    );

    const maxY = Math.max(
      pad,
      arena.clientHeight - target.offsetHeight - pad
    );

    target.style.left =
      (pad + Math.random() * (maxX - pad)) + 'px';

    target.style.top =
      (pad + Math.random() * (maxY - pad)) + 'px';

    target.style.display = 'flex';

    target.textContent =
      Math.random() < 0.18 ? '🔥' : '🎯';

    clearTimeout(spawnTimer);

    spawnTimer = setTimeout(() => {
      if (!active) return;

      target.style.display = 'none';
      combo = 0;
      lives--;

      update();

      if (lives <= 0) {
        endGame('Fim de jogo! Suas 3 vidas acabaram.');
      } else {
        spawnTimer = setTimeout(placeTarget, 350);
      }
    }, 1500);
  }

  function endGame(text) {
    active = false;

    clearInterval(timer);
    clearTimeout(spawnTimer);

    target.style.display = 'none';
    message.style.display = 'flex';

    message.innerHTML =
      `${text}<br><strong>Pontuação: ${score}</strong>`;

    $('start').hidden = true;
    $('restart').hidden = false;

    if (score > best) {
      best = score;
      $('best').textContent = best;

      try {
        localStorage.setItem(
          'acerteAoAlvoBest',
          String(best)
        );
      } catch (e) {}
    }

    update();
  }

  function startGame() {
    clearInterval(timer);
    clearTimeout(spawnTimer);

    score = 0;
    combo = 0;
    lives = 3;
    timeLeft = 30;
    active = true;

    message.style.display = 'none';
    $('start').hidden = true;
    $('restart').hidden = true;

    update();
    placeTarget();

    timer = setInterval(() => {
      timeLeft--;
      update();

      if (timeLeft <= 0) {
        endGame('Tempo esgotado!');
      }
    }, 1000);
  }

  target.addEventListener('click', e => {
    e.stopPropagation();

    if (!active) return;

    clearTimeout(spawnTimer);

    score += 10 + Math.min(combo, 10) * 2;
    combo++;

    target.style.display = 'none';

    update();

    spawnTimer = setTimeout(placeTarget, 220);
  });

  arena.addEventListener('click', e => {
    if (!active || e.target === target) return;

    combo = 0;
    update();
  });

  $('start').addEventListener('click', startGame);
  $('restart').addEventListener('click', startGame);

  update();
})();
</script>
</body>
</html>
<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Acerte ao Alvo — 1 Segundo</title>
<style>
:root{--bg:#100307;--panel:#19090e;--red:#ff3d35;--orange:#ff623b;--text:#fff3ef;--muted:#ffb5a4}
*{box-sizing:border-box}
body{margin:0;min-height:100vh;background:radial-gradient(ellipse at 50% 70%,#30090d 0%,var(--bg) 65%);color:var(--text);font-family:Arial,sans-serif}
main{max-width:1120px;margin:auto;padding:18px}
header{border-bottom:1px solid #7b3030;padding:0 0 12px;margin-bottom:20px}
h1{margin:0;color:#0789ff;text-shadow:0 0 15px #ff382d;font-size:clamp(25px,4vw,34px)}
.top{display:flex;align-items:stretch;justify-content:space-between;gap:16px;flex-wrap:wrap}
.panel,.stat{border:1px solid #a82a2a;border-radius:12px;background:#17080d}
.panel{padding:16px;min-width:210px}
.panel h2{color:var(--orange);font-size:28px;margin:18px 0 10px}
.panel p{font-size:11px;color:var(--muted);margin:5px 0}
.stats{display:flex;gap:7px}
.stat{width:76px;min-height:90px;text-align:center;padding:13px 4px}
.stat small{display:block;color:var(--muted);font-size:10px;margin-bottom:10px}
.stat strong{font-size:21px}
.arena{margin:20px 0 14px;border:1px solid #612027;border-radius:14px;min-height:300px;height:min(48vh,440px);position:relative;overflow:hidden;background:radial-gradient(ellipse at 50% 100%,#4b1111 0%,#1b070c 65%);touch-action:manipulation}
.arena:before{content:"";position:absolute;inset:auto 0 0;height:50px;background:linear-gradient(transparent,#090306);pointer-events:none}
.target{position:absolute;width:68px;height:68px;border-radius:50%;display:none;align-items:center;justify-content:center;cursor:crosshair;user-select:none;filter:drop-shadow(0 0 10px #ff3b21);font-size:49px;line-height:1;z-index:2;transition:filter .1s}
.message{position:absolute;inset:0;display:flex;align-items:center;justify-content:center;text-align:center;padding:20px;color:#ffd5c9;font-size:18px;line-height:1.6}
.controls{display:flex;gap:10px;flex-wrap:wrap;align-items:center}
button{border:1px solid #ff5946;border-radius:9px;padding:12px 18px;background:#8e1d20;color:white;font-weight:bold;font-size:15px;cursor:pointer}
button:hover{background:#c32b2b}
button:disabled{opacity:.5;cursor:not-allowed}
#hint{color:var(--muted);font-size:13px}
footer{color:#a97878;font-size:12px;margin-top:16px}
@media(max-width:560px){main{padding:12px}.top{gap:10px}.panel{width:100%}.stats{width:100%}.stat{flex:1;width:auto}.arena{height:360px}}
</style>
</head>
<body>
<main>
<header><h1>acerte-ao-alvo</h1></header>
<section class="top">
  <div class="panel">
    <h2>🔥 INFERNO</h2>
    <hr style="border:0;border-top:1px solid #854040">
    <p id="mode">MODO INFERNO: DIFÍCIL</p>
    <p id="phase">FASE 1</p>
  </div>
  <div class="stats" aria-live="polite">
    <div class="stat"><small>PONTOS</small><strong id="score">0</strong></div>
    <div class="stat"><small>COMBO</small><strong id="combo">x0</strong></div>
    <div class="stat"><small>VIDAS</small><strong id="lives">♥♥♥</strong></div>
    <div class="stat"><small>RECORDE</small><strong id="best">0</strong></div>
  </div>
</section>

<section class="arena" id="arena" aria-label="Área do jogo">
  <div class="message" id="message">
    Acerte o alvo para ganhar pontos!<br>
    Pressione “Começar jogo” para jogar.
  </div>
  <button class="target" id="target" aria-label="Acertar alvo">🎯</button>
</section>

<div class="controls">
  <button id="start">▶ Começar jogo</button>
  <button id="restart" hidden>↻ Jogar novamente</button>
  <span id="hint">30 segundos • 3 vidas • o alvo encolhe e some em 1 segundo!</span>
</div>

<footer>
  Dica: 🎯 vale pontos normais; 🔥 dá bônus!
  A cada 50 pontos, os alvos ficam menores e desaparecem mais rápido.
</footer>
</main>

<script>
(() => {
  const $ = id => document.getElementById(id);
  const arena = $('arena');
  const target = $('target');
  const message = $('message');

  let score = 0;
  let combo = 0;
  let lives = 3;
  let timeLeft = 30;
  let active = false;
  let timer = null;
  let spawnTimer = null;
  let targetIsBonus = false;
  let best = 0;

  try {
    best = Number(localStorage.getItem('acerteAoAlvoBest') || 0);
  } catch(e) {}

  $('best').textContent = best;

  function difficultyLevel() {
    return Math.floor(score / 25);
  }

  function update() {
    $('score').textContent = score;
    $('combo').textContent = 'x' + combo;
    $('lives').textContent =
      '♥'.repeat(lives) + '♡'.repeat(3 - lives);

    $('phase').textContent =
      'FASE ' + (1 + difficultyLevel());

    $('hint').textContent = active
      ? `Tempo: ${timeLeft}s — fase ${1 + difficultyLevel()} — mire rápido!`
      : '30 segundos • 3 vidas • o alvo encolhe e some em 1 segundo!';
  }

  function requestShrink(size) {
    target.style.transition = 'none';
    target.style.width = size + 'px';
    target.style.height = size + 'px';
    target.style.fontSize = Math.round(size * 0.72) + 'px';

    requestAnimationFrame(() => {
      if (!active) return;

      target.style.transition =
        'width 1000ms linear, height 1000ms linear, font-size 1000ms linear';

      target.style.width = '16px';
      target.style.height = '16px';
      target.style.fontSize = '12px';
    });
  }

  function placeTarget() {
    if (!active) return;

    const level = difficultyLevel();
    const size = Math.max(24, 68 - level * 6);

    target.style.width = size + 'px';
    target.style.height = size + 'px';
    target.style.fontSize = Math.round(size * 0.72) + 'px';

    const pad = 8;
    const maxX = Math.max(
      pad, arena.clientWidth - size - pad
    );
    const maxY = Math.max(
      pad, arena.clientHeight - size - pad
    );

    target.style.left =
      (pad + Math.random() * (maxX - pad)) + 'px';
    target.style.top =
      (pad + Math.random() * (maxY - pad)) + 'px';

    targetIsBonus = Math.random() < 0.20;
    target.textContent = targetIsBonus ? '🔥' : '🎯';

    target.setAttribute(
      'aria-label',
      targetIsBonus ? 'Alvo bônus de fogo' : 'Acertar alvo'
    );

    target.style.display = 'flex';

    requestShrink(size);

    clearTimeout(spawnTimer);

    const visibleTime = 1000;

    spawnTimer = setTimeout(() => {
      if (!active) return;

      target.style.display = 'none';
      combo = 0;
      lives--;
      update();

      if (lives <= 0) {
        endGame('Fim de jogo! Suas 3 vidas acabaram.');
      } else {
        spawnTimer = setTimeout(
          placeTarget,
          Math.max(60, 260 - level * 20)
        );
      }
    }, visibleTime);
  }

  function endGame(text) {
    active = false;
    clearInterval(timer);
    clearTimeout(spawnTimer);

    target.style.display = 'none';
    message.style.display = 'flex';

    message.innerHTML =
      `${text}<br><strong>Pontuação: ${score}</strong>` +
      `<br><small>Você chegou à fase ${1 + difficultyLevel()}.</small>`;

    $('start').hidden = true;
    $('restart').hidden = false;

    if (score > best) {
      best = score;
      $('best').textContent = best;

      try {
        localStorage.setItem('acerteAoAlvoBest', String(best));
      } catch(e) {}
    }

    update();
  }

  function startGame() {
    clearInterval(timer);
    clearTimeout(spawnTimer);

    score = 0;
    combo = 0;
    lives = 3;
    timeLeft = 30;
    active = true;

    target.style.width = '68px';
    target.style.height = '68px';
    target.style.fontSize = '49px';

    message.style.display = 'none';
    $('start').hidden = true;
    $('restart').hidden = true;

    update();
    placeTarget();

    timer = setInterval(() => {
      timeLeft--;
      update();

      if (timeLeft <= 0) {
        endGame('Tempo esgotado!');
      }
    }, 1000);
  }

  target.addEventListener('click', e => {
    e.stopPropagation();

    if (!active) return;

    clearTimeout(spawnTimer);

    const points = targetIsBonus ? 25 : 10;
    score += points + Math.min(combo, 10) * 2;
    combo++;

    target.style.display = 'none';
    update();

    spawnTimer = setTimeout(
      placeTarget,
      Math.max(60, 260 - difficultyLevel() * 20)
    );
  });

  arena.addEventListener('click', e => {
    if (!active || e.target === target) return;

    combo = 0;
    update();
  });

  $('start').addEventListener('click', startGame);
  $('restart').addEventListener('click', startGame);

  update();
})();
</script>
</body>
</html>
