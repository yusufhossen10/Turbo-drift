# Turbo-drift<!DOCTYPE html>
<html lang="tr">
<head>
<meta charset="UTF-8">
<title>🏎️ TURBO DRIFT</title>
<style>
@import url('https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700;900&family=Exo+2:wght@300;400;600;700&display=swap');
*{margin:0;padding:0;box-sizing:border-box}
body{background:#07070f;display:flex;flex-direction:column;align-items:center;justify-content:center;min-height:100vh;font-family:'Exo 2',sans-serif;overflow:hidden}
h1{font-family:'Orbitron',monospace;font-weight:900;font-size:1.9rem;letter-spacing:.3em;background:linear-gradient(90deg,#ff4e00,#ff9a00,#ffe000);-webkit-background-clip:text;-webkit-text-fill-color:transparent;margin-bottom:10px}
#wrap{display:flex;gap:14px;align-items:flex-start}
#gameContainer{position:relative;border:2px solid #ff4e00;box-shadow:0 0 40px #ff4e0066,0 0 80px #ff4e0022;border-radius:4px;flex-shrink:0}
canvas{display:block}
#hud{position:absolute;top:0;left:0;right:0;display:flex;justify-content:space-between;align-items:flex-start;padding:10px 12px;pointer-events:none}
.hud-box{background:rgba(0,0,0,.78);border:1px solid #ff4e0055;border-radius:4px;padding:4px 10px;font-family:'Orbitron',monospace;color:#fff;font-size:.6rem;letter-spacing:.08em;min-width:65px;text-align:center}
.hud-box span{display:block;font-size:.95rem;font-weight:700;color:#ff9a00}
#boostBars{position:absolute;bottom:6px;left:12px;right:12px;display:flex;gap:6px;pointer-events:none}
.bbar-wrap{flex:1;display:none;align-items:center;gap:5px}
.bbar-wrap.active{display:flex}
.bbar-label{font-family:'Orbitron',monospace;font-size:.5rem;color:#aaa;white-space:nowrap}
.bbar-bg{flex:1;height:5px;background:#ffffff11;border-radius:3px;overflow:hidden}
.bbar-fill{height:100%;border-radius:3px;transition:width .1s}
#shieldFill{background:#00cfff;box-shadow:0 0 6px #00cfff}
#turboFill{background:#ff4eee;box-shadow:0 0 6px #ff4eee}
#overlay{position:absolute;inset:0;display:flex;flex-direction:column;align-items:center;justify-content:center;background:rgba(0,0,0,.9);z-index:20;padding:18px}
#overlay h2{font-family:'Orbitron',monospace;font-size:1.5rem;font-weight:900;color:#ff4e00;letter-spacing:.2em;margin-bottom:8px;text-align:center}
#overlay p{color:#888;font-size:.82rem;margin-bottom:4px;text-align:center}
.score-display{font-family:'Orbitron',monospace;font-size:1.7rem;color:#ffe000;margin:8px 0 4px}
.coin-display{font-family:'Orbitron',monospace;font-size:.95rem;color:#ffd700;margin-bottom:14px}
.btn{background:linear-gradient(90deg,#ff4e00,#ff9a00);color:#000;border:none;padding:10px 26px;font-family:'Orbitron',monospace;font-size:.8rem;font-weight:700;letter-spacing:.15em;cursor:pointer;border-radius:3px;transition:transform .15s,box-shadow .15s;box-shadow:0 0 20px #ff4e0099;margin:3px}
.btn:hover{transform:scale(1.05);box-shadow:0 0 28px #ff4e00cc}
#legendRow{display:flex;gap:6px;flex-wrap:wrap;justify-content:center;margin:10px 0 4px}
.leg{font-size:.64rem;color:#666;background:rgba(255,255,255,.04);border-radius:4px;padding:3px 7px}
#controls{margin-top:6px;color:#333;font-size:.7rem;text-align:center}
#controls em{color:#ff9a0066;font-style:normal}
/* ── GARAGE ── */
#garage{width:188px;display:flex;flex-direction:column;gap:8px}
#garageTitle{font-family:'Orbitron',monospace;font-size:.75rem;color:#ff9a00;letter-spacing:.15em;border-bottom:1px solid #ff4e0033;padding-bottom:6px}
#walletBox{background:#0e0e1a;border:1px solid #ffd70033;border-radius:5px;padding:7px 10px;font-family:'Orbitron',monospace;font-size:.72rem;color:#ffd700;text-align:center}
.car-card{background:#0e0e1a;border:1px solid #ff4e0022;border-radius:6px;padding:9px;cursor:pointer;transition:border-color .2s,box-shadow .2s;position:relative;overflow:hidden}
.car-card:hover{border-color:#ff9a0088}
.car-card.selected{border-color:#00cfff;box-shadow:0 0 14px #00cfff33}
.car-card.locked{opacity:.55;cursor:not-allowed}
.car-card canvas{display:block;margin:0 auto}
.car-name{font-family:'Orbitron',monospace;font-size:.6rem;color:#fff;text-align:center;margin-top:5px;letter-spacing:.1em}
.car-stats{font-size:.55rem;color:#555;text-align:center;margin-top:2px}
.car-price{font-size:.6rem;color:#ffd700;text-align:center;margin-top:4px;font-weight:700}
.car-owned{font-size:.6rem;color:#00ff88;text-align:center;margin-top:4px;font-weight:700}
.lock-icon{position:absolute;top:5px;right:7px;font-size:.8rem}
.buy-btn{background:linear-gradient(90deg,#cc8800,#ffaa00);color:#000;border:none;padding:4px 10px;font-family:'Orbitron',monospace;font-size:.55rem;font-weight:700;cursor:pointer;border-radius:3px;margin-top:5px;width:100%;display:block}
.buy-btn:hover{background:linear-gradient(90deg,#ffaa00,#ffe000)}
</style>
</head>
<body>
<h1>⚡ TURBO DRIFT</h1>
<div id="wrap">

  <!-- GARAGE PANEL -->
  <div id="garage">
    <div id="garageTitle">🏪 GARAJ</div>
    <div id="walletBox">💰 <span id="walletDisplay">0</span> JETON</div>
    <div id="carCards"></div>
  </div>

  <!-- GAME -->
  <div id="gameContainer">
    <canvas id="gameCanvas" width="400" height="600"></canvas>

    <div id="hud">
      <div class="hud-box">SKOR<span id="scoreDisplay">0</span></div>
      <div class="hud-box">💰<span id="coinDisplay">0</span></div>
      <div class="hud-box">EN İYİ<span id="bestDisplay">0</span></div>
    </div>

    <div id="boostBars">
      <div class="bbar-wrap" id="shieldWrap">
        <div class="bbar-label">🛡</div>
        <div class="bbar-bg"><div class="bbar-fill" id="shieldFill" style="width:100%"></div></div>
      </div>
      <div class="bbar-wrap" id="turboWrap">
        <div class="bbar-label">🚀</div>
        <div class="bbar-bg"><div class="bbar-fill" id="turboFill" style="width:100%"></div></div>
      </div>
    </div>

    <div id="overlay">
      <h2>TURBO DRIFT</h2>
      <p>Araçlardan kaçın, ödülleri toplayın!</p>
      <p>Jetonla yeni arabalar satın alın!</p>
      <div id="finalScore" class="score-display" style="display:none"></div>
      <div id="finalCoins" class="coin-display" style="display:none"></div>
      <button class="btn" id="startBtn">BAŞLAT</button>
      <div id="legendRow">
        <div class="leg">⭐ +100 Puan</div>
        <div class="leg">💰 +1 Jeton</div>
        <div class="leg">💎 +5 Jeton</div>
        <div class="leg">🛡️ Kalkan</div>
        <div class="leg">🚀 Turbo</div>
        <div class="leg">✖2 X2 Puan</div>
      </div>
      <div id="controls">
        <em>←→ / A D</em> Şerit &nbsp;|&nbsp; <em>↑↓ / W S</em> Hız
      </div>
    </div>
  </div>

</div>
<script>
// ═══════════════════════════ CAR DEFS ════════════════════════════
const CAR_DEFS=[
  {id:'basic',  name:'STANDART', color:'#00d4ff',accent:'#0088bb',price:0,   owned:true, maxSpeed:8,  accel:.18,desc:'Dengeli başlangıç'},
  {id:'muscle', name:'MUSCLE',   color:'#ff3333',accent:'#aa0000',price:120,  owned:false,maxSpeed:9,  accel:.15,desc:'Güçlü ağır motor'},
  {id:'sport',  name:'SPORT GT', color:'#00ff88',accent:'#00aa55',price:280,  owned:false,maxSpeed:11, accel:.23,desc:'Hızlı ve çevik'},
  {id:'neon',   name:'NEON GT',  color:'#ff00ff',accent:'#8800cc',price:500,  owned:false,maxSpeed:10, accel:.21,desc:'Neon ışıklı efsane'},
  {id:'golden', name:'GOLDEN',   color:'#ffd700',accent:'#cc8800',price:900,  owned:false,maxSpeed:13, accel:.26,desc:'En iyi araba!'},
];

// ═══════════════════════════ SAVE / LOAD ══════════════════════════
let wallet=0, best=0, selectedCarId='basic';
function save(){
  try{localStorage.setItem('td2',JSON.stringify({wallet,best,owned:CAR_DEFS.map(c=>c.owned),sel:selectedCarId}));}catch(e){}
}
function load(){
  try{
    const d=JSON.parse(localStorage.getItem('td2')||'{}');
    if(d.wallet!=null) wallet=d.wallet;
    if(d.best!=null)   best=d.best;
    if(d.owned) d.owned.forEach((v,i)=>{if(CAR_DEFS[i])CAR_DEFS[i].owned=v;});
    if(d.sel)   selectedCarId=d.sel;
  }catch(e){}
}
load();

// ═══════════════════════════ CANVAS ══════════════════════════════
const canvas=document.getElementById('gameCanvas');
const ctx=canvas.getContext('2d');
const W=canvas.width,H=canvas.height;

// HUD refs
const scoreEl=document.getElementById('scoreDisplay');
const coinEl=document.getElementById('coinDisplay');
const bestEl=document.getElementById('bestDisplay');
const walletEl=document.getElementById('walletDisplay');
const overlay=document.getElementById('overlay');
const startBtn=document.getElementById('startBtn');
const finalScoreEl=document.getElementById('finalScore');
const finalCoinsEl=document.getElementById('finalCoins');
const shieldWrap=document.getElementById('shieldWrap');
const turboWrap=document.getElementById('turboWrap');
const shieldFill=document.getElementById('shieldFill');
const turboFill=document.getElementById('turboFill');

// ═══════════════════════════ ROAD ════════════════════════════════
const LANE_COUNT=3;
const RL=48,RR=W-48;
const RW=RR-RL,LW=RW/LANE_COUNT;
function laneX(l){return RL+l*LW+LW/2;}

// ═══════════════════════════ STATE ═══════════════════════════════
let state='menu',score=0,coins=0,frame=0;
let roadSpeed=4;
let multiplier=1,multTimer=0;
let shieldActive=false,shieldTimer=0;
const SHIELD_MAX=360;
let turboActive=false,turboTimer=0;
const TURBO_MAX=220;

const player={
  x:W/2,y:H-110,w:36,h:60,
  lane:1,targetX:laneX(1),
  speed:0,sway:0,
  get maxSpeed(){return(getCar().maxSpeed||8)+(turboActive?5:0);},
  get accel(){return getCar().accel||.18;}
};
function getCar(){return CAR_DEFS.find(c=>c.id===selectedCarId)||CAR_DEFS[0];}

let enemies=[],items=[],floats=[],particles=[];
const ENEMY_COLORS=['#e63946','#457b9d','#2a9d8f','#f4a261','#8ecae6','#c77dff','#ffb703'];

const ITYPES=[
  {type:'star',  emoji:'⭐',color:'#ffe000',glow:'#ffcc00',label:'+100 PUAN!',w:20,h:20,weight:3},
  {type:'coin',  emoji:'💰',color:'#ffd700',glow:'#cc9900',label:'+1 JETON!', w:20,h:20,weight:4},
  {type:'gem',   emoji:'💎',color:'#00ffee',glow:'#00aacc',label:'+5 JETON!', w:22,h:22,weight:1},
  {type:'shield',emoji:'🛡',color:'#00cfff',glow:'#0099cc',label:'KALKAN!',   w:24,h:24,weight:1},
  {type:'turbo', emoji:'🚀',color:'#ff4eee',glow:'#cc00aa',label:'TURBO!',    w:24,h:24,weight:1},
  {type:'x2',    emoji:'✖2',color:'#ffaa00',glow:'#ff7700',label:'X2 PUAN!',  w:22,h:22,weight:1},
];
const TOTAL_W=ITYPES.reduce((a,b)=>a+b.weight,0);

// ═══════════════════════════ INPUT ═══════════════════════════════
const keys={};
document.addEventListener('keydown',e=>{
  if(keys[e.key]) return; keys[e.key]=true;
  if(state!=='playing') return;
  if((e.key==='ArrowLeft'||e.key==='a'||e.key==='A')&&player.lane>0){
    player.lane--;player.targetX=laneX(player.lane);
  }
  if((e.key==='ArrowRight'||e.key==='d'||e.key==='D')&&player.lane<LANE_COUNT-1){
    player.lane++;player.targetX=laneX(player.lane);
  }
});
document.addEventListener('keyup',e=>{delete keys[e.key];});

// ═══════════════════════════ HELPERS ═════════════════════════════
function hex2rgb(h){return{r:parseInt(h.slice(1,3),16),g:parseInt(h.slice(3,5),16),b:parseInt(h.slice(5,7),16)};}
function shade(col,amt){const{r,g,b}=hex2rgb(col);return`rgb(${Math.min(255,Math.max(0,r+amt))},${Math.min(255,Math.max(0,g+amt))},${Math.min(255,Math.max(0,b+amt))})`;}
function rr(c,x,y,w,h,r){c.beginPath();c.moveTo(x+r,y);c.lineTo(x+w-r,y);c.quadraticCurveTo(x+w,y,x+w,y+r);c.lineTo(x+w,y+h-r);c.quadraticCurveTo(x+w,y+h,x+w-r,y+h);c.lineTo(x+r,y+h);c.quadraticCurveTo(x,y+h,x,y+h-r);c.lineTo(x,y+r);c.quadraticCurveTo(x,y,x+r,y);}
function overlap(ax,ay,aw,ah,bx,by,bw,bh){return ax-aw/2<bx+bw/2&&ax+aw/2>bx-bw/2&&ay-ah/2<by+bh/2&&ay+ah/2>by-bh/2;}

// ═══════════════════════════ DIFFICULTY ══════════════════════════
function diff(){
  // level increases every 400 score
  const level=1+Math.floor(score/400);
  return {
    level,
    roadSpeed:3.5+level*0.55,
    enemySpeed:1.5+level*0.38,
    spawnRate:Math.max(18,80-level*4),  // frames between spawns
    itemRate:Math.max(45,120-level*4),
    doubleEnemy:level>=4&&Math.random()<0.15+(level-4)*0.04,
    wobbly:level>=3,
  };
}

// ═══════════════════════════ SPAWN ═══════════════════════════════
function spawnEnemy(){
  const d=diff();
  const lane=Math.floor(Math.random()*LANE_COUNT);
  const lanes=d.doubleEnemy?[lane,(lane+1)%LANE_COUNT]:[lane];
  lanes.forEach(l=>{
    enemies.push({x:laneX(l),y:-90,w:34,h:58,
      color:ENEMY_COLORS[Math.floor(Math.random()*ENEMY_COLORS.length)],
      speed:1.5+Math.random()*2.5,lane:l,
      wobble:d.wobbly?(Math.random()-.5)*.55:0,dead:false});
  });
}
function spawnItem(){
  const lane=Math.floor(Math.random()*LANE_COUNT);
  let r=Math.random()*TOTAL_W;
  let def=ITYPES[0];
  for(const t of ITYPES){r-=t.weight;if(r<=0){def=t;break;}}
  items.push({...def,x:laneX(lane),y:-50,lane,pulse:Math.random()*Math.PI*2,age:0,dead:false});
}
function spawnFloat(x,y,text,color){floats.push({x,y:y-15,text,color,life:1,vy:-2.2});}
function spawnParts(x,y,cols){
  const c=cols||['#ff4e00','#ff9a00','#ffe000'];
  for(let i=0;i<28;i++){const a=Math.random()*Math.PI*2,s=1+Math.random()*5;particles.push({x,y,vx:Math.cos(a)*s,vy:Math.sin(a)*s,life:1,color:c[Math.floor(Math.random()*c.length)],r:2+Math.random()*4});}
}

// ═══════════════════════════ DRAW ════════════════════════════════
function drawRoad(rs){
  // asphalt
  ctx.fillStyle='#1a1a2e';ctx.fillRect(RL,0,RW,H);
  // grass
  ctx.fillStyle='#0d1a0d';ctx.fillRect(0,0,RL,H);ctx.fillRect(RR,0,W-RR,H);
  // kerb stripes
  const kW=12,sH=38;
  for(let i=0;i<H/sH+2;i++){
    const yy=(i*sH+(frame*rs*.65)%sH)-sH;
    ctx.fillStyle=i%2===0?'#cc2200':'#eeeeee';
    ctx.fillRect(RL-kW,yy,kW,sH);ctx.fillRect(RR,yy,kW,sH);
  }
  // lane dashes
  ctx.save();ctx.strokeStyle='#ffffff15';ctx.lineWidth=2;ctx.setLineDash([26,18]);
  for(let l=1;l<LANE_COUNT;l++){const lx=RL+l*LW;ctx.beginPath();ctx.moveTo(lx,0);ctx.lineTo(lx,H);ctx.stroke();}
  ctx.setLineDash([]);ctx.restore();
  // edges
  ctx.strokeStyle='#ffffff44';ctx.lineWidth=2;
  ctx.beginPath();ctx.moveTo(RL,0);ctx.lineTo(RL,H);ctx.stroke();
  ctx.beginPath();ctx.moveTo(RR,0);ctx.lineTo(RR,H);ctx.stroke();
}

function drawCar(x,y,w,h,color,accent,flip,sway,shielded){
  ctx.save();ctx.translate(x,y);ctx.rotate(sway||0);
  // shadow
  ctx.fillStyle='rgba(0,0,0,.32)';ctx.beginPath();ctx.ellipse(3,h*.47,w*.43,h*.17,0,0,Math.PI*2);ctx.fill();
  // body grad
  const g=ctx.createLinearGradient(-w/2,0,w/2,0);
  g.addColorStop(0,shade(color,-45));g.addColorStop(.4,color);g.addColorStop(1,shade(color,-35));
  ctx.fillStyle=g;ctx.beginPath();rr(ctx,-w/2,-h/2,w,h,9);ctx.fill();
  // cabin
  ctx.fillStyle=flip?'#1a3c5e':'#0d2137';ctx.beginPath();rr(ctx,-w/2+6,-h/2+10,w-12,h*.43,5);ctx.fill();
  // shine
  ctx.fillStyle='rgba(255,255,255,.11)';ctx.beginPath();rr(ctx,-w/2+8,-h/2+12,w-16,h*.15,3);ctx.fill();
  // accent stripe
  ctx.fillStyle=accent;ctx.fillRect(-w/2+3,flip?h*.08:-h*.12,w-6,4);
  // lights
  const lc=flip?'#ff4444':'#ffffaa';ctx.fillStyle=lc;ctx.shadowColor=lc;ctx.shadowBlur=9;
  ctx.fillRect(-w/2+3,flip?-h/2+4:h/2-8,8,5);ctx.fillRect(w/2-11,flip?-h/2+4:h/2-8,8,5);
  ctx.shadowBlur=0;
  // wheels
  ctx.fillStyle='#111';
  [[-w/2-3,-h/2+9],[w/2-5,-h/2+9],[-w/2-3,h/2-15],[w/2-5,h/2-15]].forEach(([wx,wy])=>ctx.fillRect(wx,wy,8,13));
  // shield
  if(shielded){
    ctx.strokeStyle=`rgba(0,207,255,${.45+Math.sin(frame*.15)*.3})`;
    ctx.lineWidth=3;ctx.shadowColor='#00cfff';ctx.shadowBlur=20;
    ctx.beginPath();ctx.ellipse(0,0,w*.72,h*.56,0,0,Math.PI*2);ctx.stroke();
    ctx.shadowBlur=0;
  }
  ctx.restore();
}

function drawItem(it){
  it.age++;
  const bob=Math.sin(it.age*.07+it.pulse)*3.5;
  const sc=1+Math.sin(it.age*.1+it.pulse)*.07;
  ctx.save();ctx.translate(it.x,it.y+bob);ctx.scale(sc,sc);
  // glow
  ctx.shadowColor=it.glow||it.color;ctx.shadowBlur=14;
  const rg=ctx.createRadialGradient(0,0,0,0,0,it.w);
  rg.addColorStop(0,it.color+'55');rg.addColorStop(1,'transparent');
  ctx.fillStyle=rg;ctx.beginPath();ctx.arc(0,0,it.w,0,Math.PI*2);ctx.fill();
  ctx.shadowBlur=0;
  ctx.font=`${it.w+4}px serif`;ctx.textAlign='center';ctx.textBaseline='middle';
  ctx.fillText(it.emoji,0,2);
  ctx.restore();
}

function drawParticles(){
  particles.forEach(p=>{ctx.globalAlpha=p.life;ctx.fillStyle=p.color;ctx.beginPath();ctx.arc(p.x,p.y,p.r*p.life,0,Math.PI*2);ctx.fill();});
  ctx.globalAlpha=1;
}
function drawFloats(){
  floats.forEach(f=>{
    ctx.globalAlpha=f.life;
    ctx.font='bold 14px Orbitron,monospace';ctx.textAlign='center';
    ctx.fillStyle=f.color;ctx.shadowColor=f.color;ctx.shadowBlur=10;
    ctx.fillText(f.text,f.x,f.y);ctx.shadowBlur=0;
  });
  ctx.globalAlpha=1;
}
function drawLevelBanner(level){
  if(score>0&&score%400<6&&frame%3===0){
    ctx.save();ctx.globalAlpha=.9;
    ctx.font='bold 13px Orbitron,monospace';ctx.textAlign='center';
    ctx.fillStyle='#ff4e00';ctx.shadowColor='#ff4e00';ctx.shadowBlur=12;
    ctx.fillText('⚡ SEVİYE '+level+' — HIZLANIYOR!',W/2,H/2-20);
    ctx.shadowBlur=0;ctx.restore();
  }
}

// ═══════════════════════════ COLLECT ════════════════════════════
function collect(it){
  switch(it.type){
    case'star': score+=100*multiplier; spawnFloat(it.x,it.y,it.label,'#ffe000'); spawnParts(it.x,it.y,['#ffe000','#ffcc00','#fff']); break;
    case'coin': coins+=1;wallet+=1; spawnFloat(it.x,it.y,'+1 JETON','#ffd700'); spawnParts(it.x,it.y,['#ffd700','#ffaa00']); break;
    case'gem':  coins+=5;wallet+=5; spawnFloat(it.x,it.y,'+5 JETON','#00ffee'); spawnParts(it.x,it.y,['#00ffee','#00ccbb']); break;
    case'shield': shieldActive=true;shieldTimer=SHIELD_MAX; spawnFloat(it.x,it.y,'KALKAN!','#00cfff'); break;
    case'turbo':  turboActive=true; turboTimer=TURBO_MAX;  spawnFloat(it.x,it.y,'TURBO!','#ff4eee');  break;
    case'x2':     multiplier=2;multTimer=450; spawnFloat(it.x,it.y,'X2 PUAN!','#ffaa00'); break;
  }
  save();updateGarage();walletEl.textContent=wallet;
}

// ═══════════════════════════ RESET ═══════════════════════════════
function reset(){
  score=0;coins=0;frame=0;multiplier=1;multTimer=0;
  shieldActive=false;shieldTimer=0;turboActive=false;turboTimer=0;
  player.x=laneX(1);player.targetX=laneX(1);player.lane=1;player.speed=0;player.sway=0;
  enemies=[];items=[];floats=[];particles=[];
}

// ═══════════════════════════ GAME OVER ═══════════════════════════
function gameover(){
  if(score>best) best=score;
  wallet+=coins; save();
  state='dead';
  document.getElementById('overlay').querySelector('h2').textContent='💥 ÇARPTINIZ!';
  document.getElementById('overlay').querySelector('p').textContent='Araçtan kaçamadınız.';
  finalScoreEl.style.display='block'; finalScoreEl.textContent=score+' PUAN';
  finalCoinsEl.style.display='block'; finalCoinsEl.textContent='💰 +'+coins+' jeton kazandınız! (Toplam: '+wallet+')';
  startBtn.textContent='YENİDEN BAŞLAT';
  overlay.style.display='flex';
  updateGarage();walletEl.textContent=wallet;bestEl.textContent=best;
  (function boom(){
    if(!particles.length) return;
    ctx.fillStyle='rgba(7,7,15,.45)';ctx.fillRect(0,0,W,H);
    drawParticles();
    particles.forEach(p=>{p.x+=p.vx;p.y+=p.vy;p.vx*=.92;p.vy*=.92;p.life-=.022;});
    particles=particles.filter(p=>p.life>0);
    requestAnimationFrame(boom);
  })();
}

// ═══════════════════════════ MAIN LOOP ═══════════════════════════
function loop(){
  if(state!=='playing') return;
  frame++;
  const d=diff();
  roadSpeed=d.roadSpeed;

  // speed input
  const up=keys['ArrowUp']||keys['w']||keys['W'];
  const dn=keys['ArrowDown']||keys['s']||keys['S'];
  if(up)   player.speed=Math.min(player.speed+player.accel,player.maxSpeed);
  else if(dn) player.speed=Math.max(player.speed-player.accel*2,-player.maxSpeed*.35);
  else { player.speed*=.97; if(Math.abs(player.speed)<.05) player.speed=0; }
  player.x+=(player.targetX-player.x)*.16;
  player.sway=(player.targetX-player.x)*.005;

  // timers
  if(shieldActive){shieldTimer--;if(shieldTimer<=0)shieldActive=false;}
  if(turboActive){turboTimer--;if(turboTimer<=0)turboActive=false;}
  if(multTimer>0){multTimer--;if(multTimer<=0)multiplier=1;}

  // spawning
  if(frame%d.spawnRate===0) spawnEnemy();
  if(frame%d.itemRate===0)  spawnItem();

  // move enemies
  enemies.forEach(e=>{e.y+=(roadSpeed+d.enemySpeed+e.speed);e.x+=e.wobble;if(e.y>H+100)e.dead=true;});
  enemies=enemies.filter(e=>!e.dead);

  // move items
  items.forEach(it=>{it.y+=roadSpeed*.75;if(it.y>H+60)it.dead=true;});

  // score per frame
  score+=Math.round(.5*d.level*multiplier);

  // item collision
  items.forEach(it=>{
    if(!it.dead&&overlap(player.x,player.y,player.w-6,player.h-10,it.x,it.y,it.w+12,it.h+12)){
      it.dead=true;collect(it);
    }
  });
  items=items.filter(it=>!it.dead);

  // enemy collision
  for(const e of enemies){
    if(overlap(player.x,player.y,player.w-10,player.h-12,e.x,e.y,e.w-8,e.h-12)){
      if(shieldActive){
        shieldActive=false;shieldTimer=0;
        spawnFloat(player.x,player.y,'KALKAN KIRILD!','#00cfff');
        spawnParts(player.x,player.y,['#00cfff','#ffffff']);
        e.dead=true;enemies=enemies.filter(x=>!x.dead);
      } else {
        spawnParts(player.x,player.y);
        gameover();return;
      }
    }
  }

  // update particles / floats
  particles.forEach(p=>{p.x+=p.vx;p.y+=p.vy;p.vx*=.93;p.vy*=.93;p.life-=.026;});
  particles=particles.filter(p=>p.life>0);
  floats.forEach(f=>{f.y+=f.vy;f.life-=.02;});
  floats=floats.filter(f=>f.life>0);

  // ─── DRAW ─────────────────────────────────────────────
  ctx.fillStyle='#07070f';ctx.fillRect(0,0,W,H);
  drawRoad(roadSpeed);

  // exhaust
  const exhaustN=turboActive?5:Math.abs(player.speed)>.3?2:0;
  for(let i=0;i<exhaustN;i++){
    ctx.fillStyle=`rgba(${turboActive?'255,78,238':'140,190,255'},${.05+Math.random()*.1})`;
    ctx.beginPath();ctx.arc(player.x+(Math.random()-.5)*10,player.y+player.h/2+Math.random()*22,3+Math.random()*5,0,Math.PI*2);ctx.fill();
  }

  enemies.forEach(e=>drawCar(e.x,e.y,e.w,e.h,e.color,shade(e.color,30),true,e.wobble*.5,false));
  items.forEach(it=>drawItem(it));
  drawCar(player.x,player.y,player.w,player.h,getCar().color,getCar().accent,false,player.sway,shieldActive);
  drawParticles();drawFloats();
  drawLevelBanner(d.level);

  // multiplier banner
  if(multiplier>1){
    ctx.save();ctx.globalAlpha=.85;ctx.font='bold 11px Orbitron,monospace';ctx.textAlign='center';
    ctx.fillStyle='#ffaa00';ctx.shadowColor='#ffaa00';ctx.shadowBlur=8;
    ctx.fillText('✖2 PUAN AKTİF — '+Math.ceil(multTimer/60)+'s',W/2,H-12);
    ctx.shadowBlur=0;ctx.restore();
  }

  // HUD
  scoreEl.textContent=score;coinEl.textContent=coins;bestEl.textContent=best;
  shieldWrap.className='bbar-wrap'+(shieldActive?' active':'');
  turboWrap.className='bbar-wrap'+(turboActive?' active':'');
  shieldFill.style.width=((shieldTimer/SHIELD_MAX)*100)+'%';
  turboFill.style.width=((turboTimer/TURBO_MAX)*100)+'%';

  requestAnimationFrame(loop);
}

// ═══════════════════════════ GARAGE UI ═══════════════════════════
function buildPreview(car,size){
  const pc=document.createElement('canvas');pc.width=size;pc.height=size+16;
  const px=pc.getContext('2d');
  const w=size*.54,h=size*.88,cx=size/2,cy=(size+16)/2;
  const g=px.createLinearGradient(cx-w/2,0,cx+w/2,0);
  g.addColorStop(0,shade(car.color,-45));g.addColorStop(.4,car.color);g.addColorStop(1,shade(car.color,-35));
  px.fillStyle=g;px.beginPath();rr(px,cx-w/2,cy-h/2,w,h,8);px.fill();
  px.fillStyle='#0d2137';px.beginPath();rr(px,cx-w/2+5,cy-h/2+9,w-10,h*.42,4);px.fill();
  px.fillStyle=car.accent;px.fillRect(cx-w/2+3,cy-h*.12,w-6,4);
  px.fillStyle='#ffffaa';px.shadowColor='#ffffaa';px.shadowBlur=8;
  px.fillRect(cx-w/2+3,cy+h/2-7,7,4);px.fillRect(cx+w/2-10,cy+h/2-7,7,4);px.shadowBlur=0;
  return pc;
}

function updateGarage(){
  const c=document.getElementById('carCards');c.innerHTML='';
  walletEl.textContent=wallet;
  CAR_DEFS.forEach(car=>{
    const card=document.createElement('div');
    card.className='car-card'+(!car.owned?' locked':'')+(car.id===selectedCarId?' selected':'');
    card.appendChild(buildPreview(car,56));
    const nm=document.createElement('div');nm.className='car-name';nm.textContent=car.name;card.appendChild(nm);
    const st=document.createElement('div');st.className='car-stats';st.textContent='HIZ:'+car.maxSpeed+' | İVME:'+Math.round(car.accel*100);card.appendChild(st);
    if(!car.owned){
      const lk=document.createElement('span');lk.className='lock-icon';lk.textContent='🔒';card.appendChild(lk);
      const pr=document.createElement('div');pr.className='car-price';pr.textContent='💰 '+car.price+' JETON';card.appendChild(pr);
      if(wallet>=car.price){
        const bb=document.createElement('button');bb.className='buy-btn';bb.textContent='SATIN AL';
        bb.onclick=ev=>{ev.stopPropagation();buyCar(car.id);};card.appendChild(bb);
      }
    } else {
      const ow=document.createElement('div');ow.className='car-owned';ow.textContent=car.id===selectedCarId?'✅ SEÇİLİ':'✔ SAHİBİNSİN';card.appendChild(ow);
    }
    if(car.owned) card.onclick=()=>{selectedCarId=car.id;save();updateGarage();};
    c.appendChild(card);
  });
}

function buyCar(id){
  const car=CAR_DEFS.find(c=>c.id===id);
  if(!car||car.owned||wallet<car.price) return;
  wallet-=car.price;car.owned=true;selectedCarId=id;save();updateGarage();
}

// ═══════════════════════════ START BUTTON ════════════════════════
startBtn.addEventListener('click',()=>{
  reset();state='playing';
  finalScoreEl.style.display='none';finalCoinsEl.style.display='none';
  overlay.querySelector('h2').textContent='TURBO DRIFT';
  overlay.querySelector('p').textContent='Araçlardan kaçın, ödülleri toplayın!';
  startBtn.textContent='BAŞLAT';
  overlay.style.display='none';
  requestAnimationFrame(loop);
});

// ═══════════════════════════ INIT ════════════════════════════════
updateGarage();bestEl.textContent=best;walletEl.textContent=wallet;
ctx.fillStyle='#07070f';ctx.fillRect(0,0,W,H);
drawRoad(4);
drawCar(W/2,H-110,36,60,getCar().color,getCar().accent,false,0,false);
</script>
</body>
</html>
