<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>Happy babii-versary ❤️</title>
<style>
body {
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  background: linear-gradient(to bottom right, ivory, #8b0000);
  margin: 0; padding: 20px;
  display: flex; flex-direction: column;
  align-items: center; justify-content: flex-start;
  min-height: 100vh; text-align: center;
  color: #2c2c2c;
}
h1 {
  font-size: 2.5rem; margin: 20px 0; color: #8b0000;
  animation: fadeIn 2s ease-in-out;
}
@keyframes fadeIn {
  from { opacity: 0; transform: translateY(20px); }
  to { opacity: 1; transform: translateY(0); }
}
/* Floating hearts */
.heart {
  position: fixed; bottom: -50px; font-size: 2rem;
  color: #d4af37; animation: floatUp 6s linear infinite;
  opacity: 0.8;
}
@keyframes floatUp {
  0% { transform: translateY(0) scale(1); opacity: 1; }
  100% { transform: translateY(-100vh) scale(1.5); opacity: 0; }
}
/* Progress Tracker */
#progressTracker {
  font-size: 1.3rem; margin-bottom: 15px; color: #8b0000;
}
.beat { display: inline-block; animation: heartbeat 0.6s ease; }
@keyframes heartbeat {
  0% { transform: scale(1); }
  25% { transform: scale(1.4); }
  50% { transform: scale(1); }
  75% { transform: scale(1.2); }
  100% { transform: scale(1); }
}
/* QR Code glowing */
.qr-section { margin-top: 30px; display: none; }
.qr-section img {
  width: 200px; cursor: pointer; animation: glow 1.5s infinite alternate;
}
@keyframes glow {
  from { filter: drop-shadow(0 0 5px red); }
  to { filter: drop-shadow(0 0 20px red); }
}
/* Game containers */
.game-container { display: none; margin-top: 20px; }
.win-message {
  font-size: 2rem; color: #8b0000; margin-top: 20px;
  display: none; animation: fadeIn 2s ease-in-out;
}
button {
  margin: 10px; padding: 10px 20px; font-size: 1rem;
  border: none; border-radius: 8px; background: #d4af37;
  color: #fff; cursor: pointer; transition: transform 0.2s;
}
button:hover { transform: scale(1.05); }
@media (max-width: 600px) {
  h1 { font-size: 2rem; }
  .qr-section img { width: 150px; }
}
/* Diagnostics (optional) */
#diag { margin-top: 8px; font-size: 0.9rem; color: #5a0000; }
#diag details { user-select: none; }
#diag code { background: #fff8f0; padding: 2px 4px; border-radius: 6px; }
</style>
</head>
<body>
<h1>Happy babii-versary ❤️</h1>

<div id="progressTracker">Progress: Memory Match 🖤 | Maze 🖤</div>

<div id="gameSelection">
  <button onclick="showGame('memoryGame')">Play Memory Match 🎴</button>
  <button onclick="showGame('mazeGame')">Play Maze 🌀</button>
</div>

<div id="memoryGame" class="game-container">
  <p>Find all the matching pairs! 🐱🐱</p>
  <div id="memoryBoard" style="display:grid;grid-template-columns:repeat(4,80px);gap:10px;justify-content:center;"></div>
  <button onclick="backToMenu()">Back to Game Selection</button>
</div>

<div id="mazeGame" class="game-container">
  <p>Guide the emoji to the 🏁! Use arrow keys. Watch out for traps and enemies! ✨</p>
  <canvas id="mazeCanvas" width="320" height="320" style="border:2px solid #d4af37"></canvas>
  <button onclick="backToMenu()">Back to Game Selection</button>
</div>

<div class="win-message" id="winMessage">🎉 You Win! 🎉</div>

<div class="qr-section" id="qrSection">
  <a href="https://gifft.me/o/l/qgf095t59rq4mwtxghx2dzuk" target="_blank">
    <img src="qrchimpX1024.png" alt="Scan or Click the QR Code">
  </a>
</div>

<div id="diag">
  <details>
    <summary>Diagnostics (click to expand)</summary>
    <div>Emoji image status: <strong id="emojiStatus">loading…</strong></div>
    <div>Expected emoji path: <code>emoji.png</code></div>
    <div>Maze grid: <code>8 × 8</code>, canvas: <code>320 × 320</code></div>
    <div>Memory cards: <code>8</code> tiles / 4 pairs</div>
  </details>
</div>

<audio autoplay loop>
  <source src="music.mp3" type="audio/mpeg">
</audio>

<script>
// Floating hearts
function createHeart() {
  const heart = document.createElement('div');
  heart.classList.add('heart'); heart.innerHTML = '💖';
  heart.style.left = Math.random() * 100 + 'vw';
  heart.style.animationDuration = (4 + Math.random() * 3) + 's';
  document.body.appendChild(heart);
  setTimeout(() => heart.remove(), 7000);
}
setInterval(createHeart, 800);

let memoryWon = false; let mazeWon = false;
function updateProgress() {
  const tracker = document.getElementById('progressTracker');
  let memHeart = memoryWon ? '<span class="beat">❤️</span>' : '🖤';
  let mazeHeart = mazeWon ? '<span class="beat">❤️</span>' : '🖤';
  tracker.innerHTML = `Progress: Memory Match ${memHeart} | Maze ${mazeHeart}`;
  if (memoryWon && mazeWon) {
    document.getElementById('winMessage').style.display = 'block';
    document.getElementById('qrSection').style.display = 'block';
    startFireworks();
  }
}

function showGame(gameId) {
  document.getElementById('gameSelection').style.display = 'none';
  document.getElementById(gameId).style.display = 'block';
}
function backToMenu() {
  document.querySelectorAll('.game-container').forEach(div => div.style.display = 'none');
  document.getElementById('gameSelection').style.display = 'block';
}

/* ---------------- Memory Match ---------------- */
const memoryBoard = document.getElementById('memoryBoard');
let cards = ['🐱','🐱','🐶','🐶','🐰','🐰','🐼','🐼'];
cards = cards.sort(() => 0.5 - Math.random());
let firstCard = null; let lockBoard = false;
cards.forEach(symbol => {
  const card = document.createElement('div');
  card.style.width='80px'; card.style.height='80px';
  card.style.display='flex'; card.style.alignItems='center'; card.style.justifyContent='center';
  card.style.background='#fff8f0'; card.style.border='2px solid #d4af37';
  card.style.fontSize='2rem'; card.style.borderRadius='10px'; card.style.cursor='pointer';
  card.innerText=''; card.dataset.symbol = symbol;
  card.addEventListener('click', () => {
    if (lockBoard || card.innerText!=='') return;
    card.innerText = symbol;
    if (!firstCard) { firstCard = card; }
    else {
      if (firstCard.dataset.symbol === card.dataset.symbol) { firstCard = null;
        if ([...memoryBoard.children].every(c=>c.innerText!=='')) { memoryWon=true; updateProgress(); }
      } else {
        lockBoard = true;
        setTimeout(()=>{firstCard.innerText=''; card.innerText=''; firstCard=null; lockBoard=false;},800);
      }
    }
  });
  memoryBoard.appendChild(card);
});

/* ---------------- Maze Game (random start, traps, enemies) ---------------- */
const mazeCanvas = document.getElementById('mazeCanvas');
const ctx = mazeCanvas.getContext('2d');
const sizeMaze=8, cellSizeMaze=40;
const mazeLayout=[
  [0,0,1,0,2,0,1,0],
  [1,1,1,0,1,0,1,0],
  [0,0,0,2,1,0,0,0],
  [1,1,1,0,1,1,1,0],
  [0,2,0,0,0,0,1,0],
  [0,1,1,1,1,0,1,0],
  [0,0,2,1,0,0,2,0],
  [1,1,0,1,0,1,1,0]
];
function randomStartMaze(){
  const openCells=[]; for(let y=0;y<sizeMaze;y++){for(let x=0;x<sizeMaze;x++){if(mazeLayout[y][x]===0) openCells.push({x,y});}}
  return openCells[Math.floor(Math.random()*openCells.length)];
}
let player={x:0,y:0}, goal={x:7,y:7}, moving=false, flagGlow=false;
player=randomStartMaze();

// player image
const playerImg=new Image();
playerImg.src='emoji.png';
const playerSize=40;
let playerImgReady=false;
playerImg.onload=()=>{if(playerImg.naturalWidth>0) playerImgReady=true;}

// enemies
const enemies=[
  {x:3,y:0},{x:6,y:7},{x:0,y:5}
];

function drawMaze(){
  ctx.clearRect(0,0,mazeCanvas.width,mazeCanvas.height);
  for(let y=0;y<sizeMaze;y++){
    for(let x=0;x<sizeMaze;x++){
      if(mazeLayout[y][x]===1){ctx.fillStyle='#8b0000';ctx.fillRect(x*cellSizeMaze,y*cellSizeMaze,cellSizeMaze,cellSizeMaze);}
      else if(mazeLayout[y][x]===2){ctx.fillStyle='#ff8c00';ctx.fillRect(x*cellSizeMaze,y*cellSizeMaze,cellSizeMaze,cellSizeMaze);}
    }
  }
  // goal
  const gx=goal.x*cellSizeMaze+cellSizeMaze/2, gy=goal.y*cellSizeMaze+cellSizeMaze/2;
  if(flagGlow){const t=Date.now()/200; const r=14+(Math.sin(t)*6+6); ctx.save(); ctx.globalAlpha=0.6; ctx.beginPath(); ctx.arc(gx,gy,r,0,Math.PI*2); ctx.fillStyle='rgba(255,0,0,0.35)'; ctx.fill(); ctx.restore();}
  ctx.font='24px Arial'; ctx.textAlign='center'; ctx.textBaseline='middle'; ctx.fillText('🏁',gx,gy);
  // player
  const bounce=moving?Math.sin(Date.now()/90)*8:0;
  const px=player.x*cellSizeMaze, py=player.y*cellSizeMaze+bounce;
  if(playerImgReady) ctx.drawImage(playerImg,px,py,playerSize,playerSize); else { ctx.font='28px "Apple Color Emoji","Segoe UI Emoji","Noto Color Emoji",Arial'; ctx.textAlign='left'; ctx.textBaseline='top'; ctx.fillText('😺', px+6, py+6);}
  // enemies
  enemies.forEach(e=>{ctx.fillStyle='purple'; ctx.beginPath(); ctx.arc(e.x*cellSizeMaze+cellSizeMaze/2,e.y*cellSizeMaze+cellSizeMaze/2,cellSizeMaze/2-4,0,Math.PI*2); ctx.fill();});
}

function tryMove(dx,dy){
  const newX=player.x+dx, newY=player.y+dy;
  if(newX>=0&&newY>=0&&newX<sizeMaze&&newY<sizeMaze&&mazeLayout[newY][newX]!==1){
    player.x=newX; player.y=newY;
    if(mazeLayout[newY][newX]===2) player=randomStartMaze();
    if(enemies.some(e=>e.x===player.x&&e.y===player.y)) player=randomStartMaze();
    if(player.x===goal.x&&player.y===goal.y){flagGlow=true; mazeWon=true; startFireworks(); updateProgress();}
  }
  drawMaze();
}

function moveEnemies(){
  enemies.forEach(e=>{
    const dirs=[{dx:0,dy:-1},{dx:0,dy:1},{dx:-1,dy:0},{dx:1,dy:0},{dx:0,dy:0}];
    const shuffled=dirs.sort(()=>0.5-Math.random());
    for(let i=0;i<shuffled.length;i++){
      const nx=e.x+shuffled[i].dx, ny=e.y+shuffled[i].dy;
      if(nx>=0&&ny>=0&&nx<sizeMaze&&ny<sizeMaze&&mazeLayout[ny][nx]===0){e.x=nx;e.y=ny;break;}
    }
    if(e.x===player.x&&e.y===player.y) player=randomStartMaze();
  });
}

document.addEventListener('keydown',e=>{if(["ArrowUp","ArrowDown","ArrowLeft","ArrowRight"].includes(e.key)){e.preventDefault();moving=true;if(e.key==='ArrowUp') tryMove(0,-1); if(e.key==='ArrowDown') tryMove(0,1); if(e.key==='ArrowLeft') tryMove(-1,0); if(e.key==='ArrowRight') tryMove(1,0);}});
document.addEventListener('keyup',e=>{if(["ArrowUp","ArrowDown","ArrowLeft","ArrowRight"].includes(e.key)) moving=false;});

function animateMaze(){ moveEnemies(); drawMaze(); requestAnimationFrame(animateMaze); }
animateMaze();

/* ---------------- Fireworks ---------------- */
function startFireworks(){
  const fwCanvas=document.createElement('canvas'); fwCanvas.width=window.innerWidth; fwCanvas.height=window.innerHeight;
  fwCanvas.style.position='fixed'; fwCanvas.style.top=0; fwCanvas.style.left=0; fwCanvas.style.pointerEvents='none';
  document.body.appendChild(fwCanvas);
  const fctx=fwCanvas.getContext('2d'); const particles=[];
  function createFirework(x,y){for(let i=0;i<50;i++){particles.push({x:x,y:y,angle:Math.random()*2*Math.PI,speed:Math.random()*5+2,life:100});}}
  setInterval(()=>createFirework(Math.random()*fwCanvas.width,Math.random()*fwCanvas.height),800);
  function animateFW(){
    fctx.clearRect(0,0,fwCanvas.width,fwCanvas.height);
    for(let i=0;i<particles.length;i++){
      const p=particles[i]; p.x+=Math.cos(p.angle)*p.speed; p.y+=Math.sin(p.angle)*p.speed; p.life--;
      fctx.fillStyle='hsl('+Math.random()*360+',100%,50%)'; fctx.fillRect(p.x,p.y,3,3);
      if(p.life<=0){particles.splice(i,1); i--;}
    }
    requestAnimationFrame(animateFW);
  }
  animateFW();
}
</script>
</body>
</html>
