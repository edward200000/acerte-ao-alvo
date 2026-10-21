<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Acerte ao Alvo — Inferno Colorido</title>
<style>
:root{--fire:#ff8733;--glow:#ff4a16;--rgb:255,90,20;--bright:#ffd078;--hue:0deg}
*{box-sizing:border-box}
body{margin:0;background:radial-gradient(ellipse at 50% 60%,#3a0d12,#100307 72%);color:#fff3ef;font-family:Arial,sans-serif}
main{max-width:1120px;margin:auto;padding:18px}
header{border-bottom:1px solid #7b3030;padding-bottom:12px;margin-bottom:20px}
h1{margin:0;color:#0789ff;text-shadow:0 0 15px #ff382d;font-size:clamp(25px,4vw,34px)}
.top{display:flex;justify-content:space-between;align-items:stretch;gap:14px;flex-wrap:wrap}
.panel,.stat,.phase-panel{background:#19090e;border:1px solid #a82a2a;border-radius:12px}
.panel{padding:16px;min-width:210px}
.panel h2{color:#ff623b;font-size:28px;margin:12px 0}
.panel p{font-size:12px;color:#ffb5a4;margin:6px 0}
.stats{display:flex;gap:7px}
.stat{width:76px;min-height:90px;text-align:center;padding:13px 4px}
.stat small{display:block;color:#ffb5a4;font-size:10px;margin-bottom:10px}
.stat strong{font-size:21px}
.phase-panel{padding:14px;margin:18px 0}
.phase-heading{display:flex;justify-content:space-between;gap:10px;flex-wrap:wrap;margin-bottom:12px}
.phase-heading strong{color:#ff9b72}
.phase-heading span{font-size:12px;color:#ffb5a4}
.phase-list{display:grid;grid-template-columns:repeat(4,minmax(0,1fr));gap:10px}
.phase-card{border:1px solid #663033;border-radius:10px;background:#10070a;padding:12px;text-align:left;color:#fff3ef;cursor:pointer;min-width:0}
.phase-card strong,.phase-card .difficulty,.phase-card small,.phase-card .state{display:block}
.phase-card strong{font-size:15px;margin-bottom:5px}
.phase-card .difficulty{font-weight:bold;color:var(--fire);margin-bottom:8px}
.phase-card small{color:#d7aaa3;line-height:1.4}
.phase-card .state{margin-top:9px;font-size:12px;color:#ddd}
.phase-card.selected{border-color:var(--fire);background:#351117;box-shadow:0 0 12px var(--glow)}
.phase-card:disabled{opacity:.55;cursor:not-allowed}
.arena{height:min(48vh,440px);min-height:300px;position:relative;overflow:hidden;margin:18px 0 14px;border:1px solid #612027;border-radius:14px;background:radial-gradient(ellipse at 50% 100%,#4b1111,#1b070c 68%);touch-action:manipulation}
.arena:after{content:"";position:absolute;pointer-events:none;inset:auto 0 0;height:50px;background:linear-gradient(transparent,#090306)}
.message{position:absolute;z-index:1;inset:0;display:flex;align-items:center;justify-content:center;text-align:center;padding:20px;color:#ffd5c9;font-size:18px;line-height:1.6}
.target{position:absolute;z-index:3;display:none;align-items:center;justify-content:center;padding:0;border:0;background:transparent;cursor:pointer;line-height:1;user-select:none;touch-action:manipulation;filter:hue-rotate(var(--hue)) drop-shadow(0 0 12px var(--glow))}
.controls{display:flex;align-items:center;gap:10px;flex-wrap:wrap}
.action{border:1px solid #ff5946;border-radius:9px;padding:12px 18px;background:#9b2024;color:white;font-size:15px;font-weight:bold;cursor:pointer}
.action:hover{background:#c32b2b}
#hint{color:#ffb5a4;font-size:13px}
footer{color:#a97878;font-size:12px;margin-top:16px;line-height:1.5}
.arena.fire-flash{animation:flash .55s ease-out}
@keyframes flash{20%{box-shadow:inset 0 0 90px rgba(var(--rgb),.55),0 0 35px rgba(var(--rgb),.9);border-color:var(--fire)}}
.fire-burst{position:absolute;z-index:5;pointer-events:none;transform:translate(-50%,-50%);width:12px;height:12px;border:3px solid var(--bright);border-radius:50%;box-shadow:0 0 16px var(--glow),inset 0 0 12px var(--fire);animation:ring .5s ease-out forwards}
@keyframes ring{to{opacity:0;width:145px;height:145px;border-width:1px}}
.particle{position:absolute;z-index:6;pointer-events:none;left:0;top:0;transform:translate(-50%,-50%);font-size:var(--sz);filter:hue-rotate(var(--hue)) drop-shadow(0 0 8px var(--glow));animation:fly .7s ease-out forwards}
@keyframes fly{to{opacity:0;transform:translate(calc(-50% + var(--dx)),calc(-50% + var(--dy))) rotate(var(--rot)) scale(1.4)}}
.edge-flame{position:absolute;z-index:4;pointer-events:none;font-size:clamp(24px,4vw,42px);filter:hue-rotate(var(--hue)) drop-shadow(0 0 10px var(--glow));animation:edge .7s ease-out forwards}
@keyframes edge{0%{opacity:0;transform:scale(.3) translateY(12px)}25%{opacity:1;transform:scale(1.1)}100%{opacity:0;transform:scale(.7) translateY(-22px)}}
.points{position:absolute;z-index:7;pointer-events:none;color:var(--bright);font-weight:900;font-size:22px;text-shadow:0 0 10px var(--glow);transform:translate(-50%,-50%);animation:points .65s ease-out forwards}
@keyframes points{0%{opacity:0}20%{opacity:1;transform:translate(-50%,-70%) scale(1.2)}100%{opacity:0;transform:translate(-50%,-160%)}}
@media(max-width:720px){.phase-list{grid-template-columns:repeat(2,minmax(0,1fr))}}
@media(max-width:560px){main{padding:12px}.panel{width:100%}.stats{width:100%}.stat{flex:1;width:auto}.arena{height:360px}.phase-card{padding:10px}}
</style>
</head>
<body>
<main>
<header><h1>acerte-ao-alvo</h1></header>
<section class="top">
  <div class="panel">
    <h2>🔥 INFERNO</h2>
    <hr style="border:0;border-top:1px solid #854040">
    <p id="mode">MODO FÁCIL</p>
    <p id="phase">FASE 1 • FÁCIL</p>
  </div>
  <div class="stats" aria-live="polite">
    <div class="stat"><small>PONTOS</small><strong id="score">0</strong></div>
    <div class="stat"><small>COMBO</small><strong id="combo">x0</strong></div>
    <div class="stat"><small>VIDAS</small><strong id="lives">♥♥♥</strong></div>
    <div class="stat"><small>RECORDE</small><strong id="best">0</strong></div>
  </div>
</section>

<section class="phase-panel">
  <div class="phase-heading">
    <strong>🗺️ ESCOLHA SUA FASE</strong>
    <span id="unlockedText">Fases desbloqueadas: 1/4</span>
  </div>
  <div class="phase-list" id="phaseList"></div>
</section>

<section class="arena" id="arena" aria-label="Área do jogo">
  <div class="message" id="message">
    Escolha uma fase e pressione <b>Começar jogo</b>.<br>
    Faça 100 pontos para desbloquear a próxima fase!
  </div>
  <button class="target" id="target" aria-label="Acertar alvo">🎯</button>
</section>

<div class="controls">
  <button class="action" id="start">▶ Começar jogo</button>
  <button class="action" id="restart" hidden>↻ Jogar novamente</button>
  <span id="hint">Alvos normais: 10 pontos • Alvos de fogo: 25 pontos</span>
</div>

<footer>
  🔥 Acerte os alvos antes que encolham e desapareçam.
  Cada fase tem fogo de uma cor diferente.
  O progresso e o recorde ficam salvos neste navegador.
</footer>
</main>

<script>
(() => {
  const $ = id => document.getElementById(id);
  const arena = $('arena');
  const target = $('target');
  const message = $('message');

  const phases = [
    {
      id:1, name:'FÁCIL', title:'FASE 1 • FÁCIL',
      size:76, visible:1800, pause:300,
      mode:'MODO FÁCIL',
      fire:'#ff8a35', rgb:'255,80,0',
      glow:'#ff5a18', bright:'#ffbe41',
      hue:'0deg', colorLabel:'Laranja'
    },
    {
      id:2, name:'NORMAL', title:'FASE 2 • NORMAL',
      size:64, visible:1300, pause:220,
      mode:'MODO NORMAL',
      fire:'#4dff82', rgb:'30,255,90',
      glow:'#14f75a', bright:'#bcffcb',
      hue:'82deg', colorLabel:'Verde'
    },
    {
      id:3, name:'DIFÍCIL', title:'FASE 3 • DIFÍCIL',
      size:52, visible:900, pause:150,
      mode:'MODO DIFÍCIL',
      fire:'#39aaff', rgb:'25,120,255',
      glow:'#178cff', bright:'#a8e4ff',
      hue:'185deg', colorLabel:'Azul'
    },
    {
      id:4, name:'EXTREMA', title:'FASE 4 • EXTREMA',
      size:42, visible:650, pause:100,
      mode:'MODO EXTREMO',
      fire:'#ff58d1', rgb:'255,45,180',
      glow:'#ff28b4', bright:'#ffc0f0',
      hue:'285deg', colorLabel:'Rosa'
    }
  ];

  let score=0, combo=0, lives=3, timeLeft=30;
  let active=false, timer=null, spawnTimer=null;
  let bonus=false, best=0, selected=1, unlocked=1;
  let targetToken=0;

  try {
    best=Number(localStorage.getItem('acerteAoAlvoBest')||0);
    unlocked=Math.max(1,Math.min(4,Number(
      localStorage.getItem('acerteAoAlvoUnlockedPhase')||1
    )));
    selected=Math.max(1,Math.min(unlocked,Number(
      localStorage.getItem('acerteAoAlvoSelectedPhase')||1
    )));
  } catch(e) {}

  $('best').textContent=best;

  const cfg=()=>phases[selected-1];

  function save(){
    try {
      localStorage.setItem('acerteAoAlvoBest',String(best));
      localStorage.setItem('acerteAoAlvoUnlockedPhase',String(unlocked));
      localStorage.setItem('acerteAoAlvoSelectedPhase',String(selected));
    } catch(e) {}
  }

  function renderPhases(){
    const list=$('phaseList');
    list.replaceChildren();

    phases.forEach(p=>{
      const open=p.id<=unlocked;
      const b=document.createElement('button');

      b.type='button';
      b.className='phase-card'+(p.id===selected?' selected':'');
      b.disabled=!open||active;

      const title=document.createElement('strong');
      title.textContent='FASE '+p.id;

      const diff=document.createElement('span');
      diff.className='difficulty';
      diff.textContent=p.name+' • FOGO '+p.colorLabel.toUpperCase();
      diff.style.color=p.fire;

      const desc=document.createElement('small');
      desc.textContent='Alvo: '+(
        p.visible>=1000
          ? (p.visible/1000).toFixed(1).replace('.',',')+' s'
          : p.visible+' ms'
      )+' • '+p.size+' px';

      const state=document.createElement('span');
      state.className='state';
      state.textContent=!open
        ? '🔒 Bloqueada'
        : p.id===selected
          ? '✓ Selecionada'
          : '▶ Escolher fase';

      b.append(title,diff,desc,state);

      b.addEventListener('click',()=>{
        if(!open||active)return;
        selected=p.id;
        save();
        applyPhase();
        renderPhases();
        readyMessage();
      });

      list.appendChild(b);
    });

    $('unlockedText').textContent='Fases desbloqueadas: '+unlocked+'/4';
  }

  function applyPhase(){
    const p=cfg();

    [
      ['--fire',p.fire],
      ['--rgb',p.rgb],
      ['--glow',p.glow],
      ['--bright',p.bright],
      ['--hue',p.hue]
    ].forEach(([k,v])=>arena.style.setProperty(k,v));

    $('phase').textContent=p.title;
    $('mode').textContent=p.mode;
    target.style.filter=
      `hue-rotate(${p.hue}) drop-shadow(0 0 12px ${p.glow})`;

    $('score').textContent=score;
    $('combo').textContent='x'+combo;
    $('lives').textContent=
      '♥'.repeat(lives)+'♡'.repeat(3-lives);
    $('best').textContent=best;

    $('hint').textContent=active
      ? `Tempo: ${timeLeft}s • Meta: ${score}/100 • Fogo ${p.colorLabel}`
      : `Fogo ${p.colorLabel} • alvo normal 10 pontos • alvo bônus 25 pontos`;

    $('start').hidden=active;
    $('restart').hidden=active;
  }

  function readyMessage(){
    message.style.display='flex';
    message.innerHTML=
      `Você escolheu <b>${cfg().title}</b>.<br>`+
      `Fogo ${cfg().colorLabel.toLowerCase()}! `+
      'Faça 100 pontos para desbloquear a próxima fase.';
  }

  function endGame(text,completed=false){
    active=false;
    clearInterval(timer);
    clearTimeout(spawnTimer);
    targetToken++;
    target.style.display='none';

    if(score>best)best=score;
    save();
    applyPhase();
    renderPhases();

    $('start').hidden=false;
    $('restart').hidden=false;
    $('start').textContent='▶ Jogar novamente';

    message.style.display='flex';
    message.innerHTML=
      `<b>${text}</b><br>Pontuação: ${score}<br>`+
      (completed&&selected<4
        ? `🎉 Fase ${selected+1} desbloqueada!`
        : completed
          ? '🏆 Você completou todas as fases!'
          : 'Tente novamente e faça 100 pontos!');
  }

  function effects(x,y,points){
    arena.classList.remove('fire-flash');
    void arena.offsetWidth;
    arena.classList.add('fire-flash');
    setTimeout(()=>arena.classList.remove('fire-flash'),600);

    const ring=document.createElement('span');
    ring.className='fire-burst';
    ring.style.left=x+'px';
    ring.style.top=y+'px';
    arena.appendChild(ring);
    setTimeout(()=>ring.remove(),600);

    const glyphs=['🔥','✨','✦','💥','🔥','🟠'];

    for(let i=0;i<20;i++){
      const el=document.createElement('span');
      el.className='particle';
      el.textContent=glyphs[Math.floor(Math.random()*glyphs.length)];

      const a=2*Math.PI*i/20+(Math.random()-.5)*.4;
      const d=45+Math.random()*105;

      el.style.left=x+'px';
      el.style.top=y+'px';
      el.style.setProperty('--dx',Math.cos(a)*d+'px');
      el.style.setProperty('--dy',(Math.sin(a)*d-20)+'px');
      el.style.setProperty('--rot',(Math.random()*180-90)+'deg');
      el.style.setProperty('--sz',(16+Math.random()*18)+'px');

      arena.appendChild(el);
      setTimeout(()=>el.remove(),750);
    }

    const edges=[
      ['2%','10%'],['20%','3%'],['48%','3%'],
      ['77%','4%'],['92%','20%'],['2%','60%'],
      ['92%','58%'],['15%','84%'],['51%','86%'],
      ['84%','82%']
    ];

    edges.forEach(([left,top])=>{
      if(Math.random()<.48)return;

      const el=document.createElement('span');
      el.className='edge-flame';
      el.textContent='🔥';
      el.style.left=left;
      el.style.top=top;

      arena.appendChild(el);
      setTimeout(()=>el.remove(),750);
    });

    const label=document.createElement('span');
    label.className='points';
    label.textContent='+'+points;
    label.style.left=x+'px';
    label.style.top=(y-15)+'px';

    arena.appendChild(label);
    setTimeout(()=>label.remove(),700);
  }

  function spawn(){
    if(!active)return;

    const p=cfg();
    const token=++targetToken;
    const size=p.size;
    const pad=8;

    const maxX=Math.max(pad,arena.clientWidth-size-pad);
    const maxY=Math.max(pad,arena.clientHeight-size-pad);

    target.style.transition='none';
    target.style.width=size+'px';
    target.style.height=size+'px';
    target.style.fontSize=Math.round(size*.72)+'px';

    target.style.left=
      (pad+Math.random()*(maxX-pad))+'px';
    target.style.top=
      (pad+Math.random()*(maxY-pad))+'px';

    bonus=Math.random()<.2;
    target.textContent=bonus?'🔥':'🎯';
    target.style.display='flex';

    requestAnimationFrame(()=>{
      if(!active||token!==targetToken)return;

      target.style.transition=
        `width ${p.visible}ms linear,`+
        `height ${p.visible}ms linear,`+
        `font-size ${p.visible}ms linear`;

      target.style.width='28px';
      target.style.height='28px';
      target.style.fontSize='22px';
    });

    clearTimeout(spawnTimer);

    spawnTimer=setTimeout(()=>{
      if(!active||token!==targetToken)return;

      target.style.display='none';
      combo=0;
      lives--;
      applyPhase();

      if(lives<=0){
        endGame('Fim de jogo! Suas vidas acabaram.');
      } else {
        spawnTimer=setTimeout(spawn,p.pause);
      }
    },p.visible);
  }

  function start(){
    clearInterval(timer);
    clearTimeout(spawnTimer);
    targetToken++;

    score=0;
    combo=0;
    lives=3;
    timeLeft=30;
    active=true;

    message.style.display='none';
    $('start').hidden=true;
    $('restart').hidden=true;

    applyPhase();
    renderPhases();
    spawn();

    timer=setInterval(()=>{
      timeLeft--;
      applyPhase();

      if(timeLeft<=0){
        endGame('Tempo esgotado!');
      }
    },1000);
  }

  target.addEventListener('click',e=>{
    e.preventDefault();
    e.stopPropagation();

    if(!active)return;

    clearTimeout(spawnTimer);

    const r=target.getBoundingClientRect();
    const a=arena.getBoundingClientRect();

    const x=r.left-a.left+r.width/2;
    const y=r.top-a.top+r.height/2;

    const earned=(bonus?25:10)+Math.min(combo,10)*2;

    effects(x,y,earned);
    score+=earned;
    combo++;
    target.style.display='none';
    targetToken++;

    if(score>=100){
      if(selected<4){
        unlocked=Math.max(unlocked,selected+1);
      }

      save();
      endGame('FASE '+selected+' CONCLUÍDA!',true);
      return;
    }

    applyPhase();
    spawnTimer=setTimeout(spawn,cfg().pause);
  });

  arena.addEventListener('click',e=>{
    if(active&&e.target!==target){
      combo=0;
      applyPhase();
    }
  });

  $('start').addEventListener('click',start);
  $('restart').addEventListener('click',start);

  applyPhase();
  renderPhases();
  readyMessage();
})();
</script>
</body>
</html>
