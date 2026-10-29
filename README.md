# snake-game
this game devlopnig .in Abhishek .patidar this game 13+ age play 

<!DOCTYPE html>
<html lang="hi">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Neon Snake Game</title>
<style>
*{box-sizing:border-box}
body{
  margin:0;min-height:100vh;display:flex;justify-content:center;
  align-items:center;background:#07111f;color:white;
  font-family:Arial,sans-serif;text-align:center;padding:15px
}
.game{width:min(94vw,430px);background:#102338;padding:18px;
  border-radius:20px;border:1px solid #31516b}
h1{color:#50fa7b;margin:5px}
.score{font-size:20px;margin:12px}
canvas{width:100%;height:auto;background:#06101b;
  border:2px solid #50fa7b;border-radius:10px}
button{padding:12px 18px;margin:8px 4px;border:0;
  border-radius:10px;background:#50fa7b;font-weight:bold;
  cursor:pointer;font-size:16px}
.controls{display:grid;grid-template-columns:repeat(3,60px);
  justify-content:center;gap:5px;margin-top:10px}
.controls button{margin:0;padding:12px}
</style>
</head>
<body>
<div class="game">
  <h1>🐍 NEON SNAKE</h1>
  <div class="score">Score: <span id="score">0</span>
    &nbsp; Best: <span id="best">0</span></div>
  <canvas id="board" width="400" height="400"></canvas>
  <div>
    <button onclick="startGame()">▶ Start / Restart</button>
    <button onclick="togglePause()">Ⅱ Pause</button>
  </div>
  <div class="controls">
    <span></span><button onclick="move(0,-1)">⬆</button><span></span>
    <button onclick="move(-1,0)">⬅</button>
    <button onclick="move(0,1)">⬇</button>
    <button onclick="move(1,0)">➡</button>
  </div>
  <p>Arrow Keys / WASD से खेलें</p>
  <p id="message">Start दबाकर गेम खेलें!</p>
</div>

<script>
const canvas = document.getElementById("board");
const ctx = canvas.getContext("2d");
const cell = 20, total = 20;

let snake, food, dx, dy, score, timer;
let running = false, paused = false, over = false;

let best = Number(localStorage.getItem("snakeBest") || 0);
document.getElementById("best").textContent = best;

function newFood(){
  do {
    food = {
      x: Math.floor(Math.random()*total),
      y: Math.floor(Math.random()*total)
    };
  } while(snake.some(p=>p.x===food.x && p.y===food.y));
}

function startGame(){
  clearInterval(timer);
  snake = [{x:10,y:10},{x:9,y:10},{x:8,y:10}];
  dx=1; dy=0; score=0;
  running=true; paused=false; over=false;
  document.getElementById("score").textContent=score;
  document.getElementById("message").textContent="Good luck!";
  newFood(); draw();
  timer=setInterval(update,130);
}

function move(x,y){
  if(x===-dx && y===-dy) return;
  dx=x; dy=y;
  if(!running && !over) startGame();
}

function togglePause(){
  if(!running || over) return;
  paused=!paused;
  document.getElementById("message").textContent=
    paused ? "Game Paused" : "Keep Playing!";
}

function update(){
  if(!running || paused) return;

  const head={x:snake[0].x+dx,y:snake[0].y+dy};
  const ate=head.x===food.x && head.y===food.y;
  const body=ate ? snake : snake.slice(0,-1);

  if(head.x<0 || head.x>=total ||
     head.y<0 || head.y>=total ||
     body.some(p=>p.x===head.x && p.y===head.y)){
    running=false; over=true; clearInterval(timer);
    document.getElementById("message").textContent=
      "Game Over! Restart करके फिर खेलें।";
    return;
  }

  snake.unshift(head);

  if(ate){
    score++;
    document.getElementById("score").textContent=score;
    if(score>best){
      best=score;
      localStorage.setItem("snakeBest",best);
      document.getElementById("best").textContent=best;
    }
    newFood();
    if(score%5===0){
      clearInterval(timer);
      timer=setInterval(update,Math.max(60,130-score*2));
    }
  }else{
    snake.pop();
  }
  draw();
}

function draw(){
  ctx.fillStyle="#06101b";
  ctx.fillRect(0,0,400,400);

  ctx.strokeStyle="#12283b";
  for(let i=0;i<=400;i+=cell){
    ctx.beginPath();ctx.moveTo(i,0);ctx.lineTo(i,400);ctx.stroke();
    ctx.beginPath();ctx.moveTo(0,i);ctx.lineTo(400,i);ctx.stroke();
  }

  ctx.fillStyle="#ff5577";
  ctx.shadowColor="#ff5577";ctx.shadowBlur=15;
  ctx.beginPath();
  ctx.arc(food.x*cell+10,food.y*cell+10,7,0,Math.PI*2);
  ctx.fill();ctx.shadowBlur=0;

  snake.forEach((p,i)=>{
    ctx.fillStyle=i===0?"#b7ffca":"#50fa7b";
    ctx.shadowColor="#50fa7b";ctx.shadowBlur=i===0?12:0;
    ctx.fillRect(p.x*cell+2,p.y*cell+2,cell-4,cell-4);
  });
  ctx.shadowBlur=0;
}

document.addEventListener("keydown",e=>{
  const keys={
    ArrowUp:[0,-1],w:[0,-1],W:[0,-1],
    ArrowDown:[0,1],s:[0,1],S:[0,1],
    ArrowLeft:[-1,0],a:[-1,0],A:[-1,0],
    ArrowRight:[1,0],d:[1,0],D:[1,0]
  };
  if(keys[e.key]){
    e.preventDefault();move(...keys[e.key]);
  }
  if(e.code==="Space"){
    e.preventDefault();togglePause();
  }
});

draw();
</script>
</body>
</html>
