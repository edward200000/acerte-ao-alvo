<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Acerte o Alvo Extremo 🔥</title>
<style>
*{box-sizing:border-box}
body{margin:0;background:#100307;color:white;font-family:Arial,sans-serif;overflow:hidden}
#game{position:relative;width:100vw;height:100vh;background:radial-gradient(ellipse at bottom,#6b1609,#180308 55%,#050205)}
canvas{position:absolute;width:100%;height:100%;inset:0;cursor:crosshair}
#hud{position:absolute;top:15px;left:15px;right:15px;display:flex;justify-content:space-between;gap:10px;pointer-events:none}
.panel{background:#16090de8;border:1px solid #a42b26;border-radius:10px;padding:12px;box-shadow:0 0 20px #ff280622}
h1{margin:0;color:#ff6236;font-size:clamp(18px,3vw,30px);text-shadow:0 0 15px red}
.stat{text-align:center;min-width:75px}
.label{font-size:10px;color:#ffb49c}
.value{font-size:22px;font-weight:bold;margin-top:5px}
#stats{display:flex;gap:7px}
#bossBox{display:none;position:absolute;top:105px;left:20%;width:60%;text-align:center;color:#ff6747;font-weight:bold}
#bossTrack{height:14px;background:#321018;border:1px solid #ff6545;border-radius:10px;overflow:hidden;margin-top:6px}
#bossHealth{height:100%;width:100%;background:linear-gradient(90deg,#ff172b,#ff8a25)}
#powers{position:absolute;bottom:18px;left:18px;display:flex;gap:10px}
button{padding:12px 15px;color:white;font-weight:bold;background:#85151c;border:1px solid #ff593d;border-radius:8px;cursor:pointer}
button:hover{filter:brightness(1.3)}
button:disabled{opacity:.4}
#help{position:absolute;bottom:20px;right:20px;text-align:right;color:#ffc0a6;font-size:12px;line-height:1.7}
#overlay{position:absolute;inset:0;background:#080204dd;display:flex;justify-content:center;align-items:center;padding:20px;z-index:3}
.card{max-width:520px;width:100%;text-align:center;padding:30px;background:linear-gradient(145deg,#30090f,#100408);border:1px solid #c63831;border-radius:18px;box-shadow:0 0 55px #f20d2425}
.card h2{font-size:clamp(35px,8vw,62px);margin:15px 0;color:#ff6038;text-shadow:0 0 20px #f30}
.card p{line-height:1.6;color:#e6b9b0}
.actions{display:flex;justify-content:center;flex-wrap:wrap;gap:10px;margin:18px 0}
#message{position:absolute;top:40%;width:100%;text-align:center;font-size:clamp(25px,6vw,50px);font-weight:bold;color:#ffe2a0;text-shadow:0 0 20px red;pointer-events:none}
#toast{position:absolute;top:27%;width:100%;text-align:center;color:#ffcf70;font-weight:bold;pointer-events:none}
@media(max-width:600px){#hud{top:7px;left:7px;right:7px}.panel{padding:7px}.stat{min-width:53px}.value{font-size:17px}#powers{left:7px;bottom:8px}button{padding:9px;font-size:11px}#help{display:none}}
</style>
</head>
<body>
<div id="game">
<canvas id="canvas"></canvas>
<div id="hud">
 <div class="panel">
  <h1>🔥 INFERNO</h1>
  <div class="label" id="mode">MODO INFERNO</div>
  <div class="label">FASE <b id="level">1</b></div>
 </div>
 <div id="stats">
  <div class="panel stat"><div class="label">PONTOS</div><div class="value" id="score">0</div></div>
  <div class="panel stat"><div class="label">COMBO</div><div class="value" id="combo">x0</div></div>
  <div class="panel stat"><div class="label">VIDAS</div><div class="value" id="lives">♥♥♥</div></div>
  <div class="panel stat"><div class="label">RECORDE</div><div class="value" id="record">0</div></div>
 </div>
</div
