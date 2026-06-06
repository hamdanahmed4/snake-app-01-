# snake-app-01-
made by hamdan first try
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Snake — Fruit Edition</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Space+Mono:wght@400;700&family=Syne:wght@400;700;800&display=swap" rel="stylesheet">
<style>
  *{box-sizing:border-box;margin:0;padding:0}
  body{background:#0d0d14;min-height:100vh;display:flex;align-items:center;justify-content:center;font-family:'Space Mono',monospace;color:#e2e8f0}
  #layout{display:flex;gap:16px;align-items:flex-start;justify-content:center;padding:24px}
  .panel{width:140px;flex-shrink:0}
  #game-wrap{display:flex;flex-direction:column;align-items:center;gap:12px}
  #canvas{border:1.5px solid #334155;border-radius:8px;display:block;background:#0a0a0f}
  .panel-card{background:#161622;border:0.5px solid #2a2a3e;border-radius:12px;padding:14px 10px;margin-bottom:10px}
  .panel-label{font-size:10px;letter-spacing:0.12em;text-transform:uppercase;color:#64748b;margin-bottom:6px;font-family:'Syne',sans-serif}
  .score-val{font-size:28px;font-weight:700;color:#e2e8f0;line-height:1}
  .fruit-display{font-size:28px;text-align:center;line-height:1.2}
  .fruit-name{font-size:10px;color:#94a3b8;text-align:center;margin-top:4px;font-family:'Syne',sans-serif}
  .hiscore-row{display:flex;justify-content:space-between;align-items:center;padding:5px 0;border-bottom:0.5px solid #1e293b;font-size:11px}
  .hiscore-row:last-child{border-bottom:none}
  .hiscore-rank{color:#475569;font-size:10px}
  .hiscore-score{font-weight:700;color:#e2e8f0}
  #status{font-family:'Syne',sans-serif;font-size:13px;color:#64748b;letter-spacing:0.05em;text-align:center}
  #start-btn{font-family:'Space Mono',monospace;font-size:12px;padding:10px 24px;border:1.5px solid #334155;border-radius:6px;background:transparent;color:#e2e8f0;cursor:pointer;letter-spacing:0.08em;transition:background .15s}
  #start-btn:hover{background:#161622}
  .streak-badge{font-size:10px;color:#f59e0b;letter-spacing:0.05em;height:18px;text-align:center;font-family:'Syne',sans-serif}
  .next-label{font-size:10px;color:#475569;text-align:center;font-family:'Syne',sans-serif;margin-top:8px;margin-bottom:3px;letter-spacing:0.08em;text-transform:uppercase}
  .next-fruit{font-size:20px;text-align:center}
  h1{font-family:'Syne',sans-serif;font-size:13px;letter-spacing:0.15em;color:#2dd4bf;text-align:center;text-transform:uppercase;margin-bottom:8px}
</style>
</head>
<body>
<div id="layout">
  <div class="panel">
    <div class="panel-card">
      <div class="panel-label">Score</div>
      <div class="score-val" id="score-disp">0</div>
    </div>
    <div class="panel-card">
      <div class="panel-label">Current fruit</div>
      <div class="fruit-display" id="fruit-emoji">🍎</div>
      <div class="fruit-name" id="fruit-name">Apple</div>
    </div>
    <div class="panel-card" style="text-align:center">
      <div class="next-label">Next up</div>
      <div class="next-fruit" id="next-fruit-emoji">🍊</div>
      <div class="streak-badge" id="streak-disp"></div>
    </div>
  </div>

  <div id="game-wrap">
    <h1>🐍 Snake — Fruit Edition</h1>
    <canvas id="canvas" width="320" height="320"></canvas>
    <div id="status">press start to play</div>
    <button id="start-btn">▶ START</button>
  </div>

  <div class="panel">
    <div class="panel-card">
      <div class="panel-label">High Scores</div>
      <div id="hiscore-list">
        <div class="hiscore-row"><span class="hiscore-rank">#1</span><span class="hiscore-score">—</span></div>
        <div class="hiscore-row"><span class="hiscore-rank">#2</span><span class="hiscore-score">—</span></div>
        <div class="hiscore-row"><span class="hiscore-rank">#3</span><span class="hiscore-score">—</span></div>
        <div class="hiscore-row"><span class="hiscore-rank">#4</span><span class="hiscore-score">—</span></div>
        <div class="hiscore-row"><span class="hiscore-rank">#5</span><span class="hiscore-score">—</span></div>
      </div>
    </div>
    <div class="panel-card">
      <div class="panel-label">Best streak</div>
      <div class="score-val" id="best-streak">0</div>
      <div style="font-size:10px;color:#475569;margin-top:4px;font-family:'Syne',sans-serif">fruits in a row</div>
    </div>
    <div class="panel-card">
      <div class="panel-label">Fruits eaten</div>
      <div class="score-val" id="total-eaten">0</div>
    </div>
  </div>
</div>

<script>
const FRUITS=[
  {e:'🍎',n:'Apple',pts:10},{e:'🍊',n:'Orange',pts:15},{e:'🍋',n:'Lemon',pts:20},
  {e:'🍇',n:'Grapes',pts:25},{e:'🍓',n:'Strawberry',pts:30},{e:'🍉',n:'Watermelon',pts:35},
  {e:'🍑',n:'Peach',pts:40},{e:'🍍',n:'Pineapple',pts:50},{e:'🥝',n:'Kiwi',pts:60},
  {e:'🫐',n:'Blueberry',pts:75},{e:'🍒',n:'Cherry',pts:90},{e:'🥭',n:'Mango',pts:100},
  {e:'🍈',n:'Melon',pts:120},{e:'🫒',n:'Olive',pts:150},{e:'🍌',n:'Banana',pts:80},
];
const GRID=16,CELL=20,W=320,H=320;
const canvas=document.getElementById('canvas');
const ctx=canvas.getContext('2d');
let snake,dir,nextDir,fruit,fruitIdx,nextFruitIdx,score,running,loop,streak,bestStreak,totalEaten;
let hiScores=JSON.parse(localStorage.getItem('snakeHiScores')||'[]');
let fruitCanvasEl=null;

function initGame(){
  snake=[{x:8,y:8},{x:7,y:8},{x:6,y:8}];
  dir={x:1,y:0};nextDir={x:1,y:0};
  fruitIdx=0;nextFruitIdx=1;
  score=0;streak=0;totalEaten=0;
  placeFruit();updateUI();preloadEmoji();
}

function preloadEmoji(){
  fruitCanvasEl=document.createElement('canvas');
  fruitCanvasEl.width=CELL;fruitCanvasEl.height=CELL;
  let fc=fruitCanvasEl.getContext('2d');
  fc.font=`${CELL-4}px serif`;fc.textAlign='center';fc.textBaseline='middle';
  fc.clearRect(0,0,CELL,CELL);fc.fillText(FRUITS[fruitIdx].e,CELL/2,CELL/2+1);
}

function placeFruit(){
  let pos;
  do{pos={x:Math.floor(Math.random()*GRID),y:Math.floor(Math.random()*GRID)};}
  while(snake.some(s=>s.x===pos.x&&s.y===pos.y));
  fruit=pos;
}

function updateUI(){
  document.getElementById('score-disp').textContent=score;
  document.getElementById('fruit-emoji').textContent=FRUITS[fruitIdx].e;
  document.getElementById('fruit-name').textContent=FRUITS[fruitIdx].n;
  document.getElementById('next-fruit-emoji').textContent=FRUITS[nextFruitIdx].e;
  document.getElementById('total-eaten').textContent=totalEaten;
  document.getElementById('best-streak').textContent=Math.max(bestStreak||0,streak);
  let sd=document.getElementById('streak-disp');
  sd.textContent=streak>=2?`🔥 ${streak} streak`:'';
}

function tick(){
  dir={x:nextDir.x,y:nextDir.y};
  let head={x:snake[0].x+dir.x,y:snake[0].y+dir.y};
  if(head.x<0||head.x>=GRID||head.y<0||head.y>=GRID||snake.some(s=>s.x===head.x&&s.y===head.y)){
    endGame();return;
  }
  snake.unshift(head);
  if(head.x===fruit.x&&head.y===fruit.y){
    let bonus=1+Math.floor(streak/3)*0.5;
    score+=Math.floor(FRUITS[fruitIdx].pts*bonus);
    streak++;totalEaten++;
    if(streak>(bestStreak||0))bestStreak=streak;
    fruitIdx=nextFruitIdx;
    nextFruitIdx=(nextFruitIdx+1)%FRUITS.length;
    placeFruit();preloadEmoji();updateUI();
  }else{
    snake.pop();
  }
  draw();
}

function draw(){
  ctx.fillStyle='#0a0a0f';ctx.fillRect(0,0,W,H);
  ctx.strokeStyle='#1a1a2e';ctx.lineWidth=0.5;
  for(let i=0;i<=GRID;i++){
    ctx.beginPath();ctx.moveTo(i*CELL,0);ctx.lineTo(i*CELL,H);ctx.stroke();
    ctx.beginPath();ctx.moveTo(0,i*CELL);ctx.lineTo(W,i*CELL);ctx.stroke();
  }
  snake.forEach((s,i)=>{
    let t=i/snake.length;
    let r=Math.round(46+t*(20-46)),g=Math.round(213+t*(180-213)),b=Math.round(115+t*(60-115));
    ctx.fillStyle=`rgb(${r},${g},${b})`;
    ctx.beginPath();
    let px=s.x*CELL,py=s.y*CELL;
    if(i===0){ctx.roundRect(px+1,py+1,CELL-2,CELL-2,5);}
    else{ctx.roundRect(px+2,py+2,CELL-4,CELL-4,3);}
    ctx.fill();
    if(i===0){
      let ex=dir.x,ey=dir.y;
      ctx.fillStyle='#0a0a0f';
      let ow1={x:-ey*3,y:ex*3},ow2={x:ey*3,y:-ex*3};
      ctx.beginPath();ctx.arc(px+CELL/2+ex*4+ow1.x,py+CELL/2+ey*4+ow1.y,2,0,Math.PI*2);ctx.fill();
      ctx.beginPath();ctx.arc(px+CELL/2+ex*4+ow2.x,py+CELL/2+ey*4+ow2.y,2,0,Math.PI*2);ctx.fill();
    }
  });
  if(fruitCanvasEl)ctx.drawImage(fruitCanvasEl,fruit.x*CELL,fruit.y*CELL);
}

function endGame(){
  running=false;clearInterval(loop);
  hiScores.push(score);
  hiScores.sort((a,b)=>b-a);
  hiScores=hiScores.slice(0,5);
  localStorage.setItem('snakeHiScores',JSON.stringify(hiScores));
  updateHiScores();
  document.getElementById('status').textContent=`game over — scored ${score}`;
  document.getElementById('start-btn').textContent='▶ PLAY AGAIN';
  document.getElementById('start-btn').style.display='block';
  ctx.fillStyle='rgba(10,10,15,0.75)';ctx.fillRect(0,0,W,H);
  ctx.fillStyle='#2dd4bf';ctx.font='bold 18px Space Mono,monospace';
  ctx.textAlign='center';ctx.textBaseline='middle';
  ctx.fillText('GAME OVER',W/2,H/2-18);
  ctx.fillStyle='#94a3b8';ctx.font='13px Space Mono,monospace';
  ctx.fillText(`score: ${score}`,W/2,H/2+10);
}

function updateHiScores(){
  document.getElementById('hiscore-list').innerHTML=
    Array.from({length:5},(_,i)=>`<div class="hiscore-row"><span class="hiscore-rank">#${i+1}</span><span class="hiscore-score">${hiScores[i]??'—'}</span></div>`).join('');
}

document.addEventListener('keydown',e=>{
  if(['ArrowUp','ArrowDown','ArrowLeft','ArrowRight'].includes(e.key))e.preventDefault();
  if(e.key==='ArrowUp'||e.key==='w'){if(dir.y!==1)nextDir={x:0,y:-1};}
  if(e.key==='ArrowDown'||e.key==='s'){if(dir.y!==-1)nextDir={x:0,y:1};}
  if(e.key==='ArrowLeft'||e.key==='a'){if(dir.x!==1)nextDir={x:-1,y:0};}
  if(e.key==='ArrowRight'||e.key==='d'){if(dir.x!==-1)nextDir={x:1,y:0};}
});

let touchStartX=null,touchStartY=null;
canvas.addEventListener('touchstart',e=>{touchStartX=e.touches[0].clientX;touchStartY=e.touches[0].clientY;},{passive:true});
canvas.addEventListener('touchend',e=>{
  if(touchStartX===null)return;
  let dx=e.changedTouches[0].clientX-touchStartX,dy=e.changedTouches[0].clientY-touchStartY;
  if(Math.abs(dx)>Math.abs(dy)){if(dx>0&&dir.x!==-1)nextDir={x:1,y:0};else if(dx<0&&dir.x!==1)nextDir={x:-1,y:0};}
  else{if(dy>0&&dir.y!==-1)nextDir={x:0,y:1};else if(dy<0&&dir.y!==1)nextDir={x:0,y:-1};}
  touchStartX=touchStartY=null;
},{passive:true});

document.getElementById('start-btn').addEventListener('click',()=>{
  if(running)return;
  running=true;bestStreak=bestStreak||0;
  initGame();draw();
  document.getElementById('status').textContent='arrow keys or WASD to move';
  document.getElementById('start-btn').style.display='none';
  let speed=180;
  function startLoop(){
    loop=setInterval(()=>{
      tick();
      let ns=Math.max(80,180-Math.floor(score/100)*10);
      if(ns!==speed){speed=ns;clearInterval(loop);if(running)startLoop();}
    },speed);
  }
  startLoop();
});

updateHiScores();
ctx.fillStyle='#0a0a0f';ctx.fillRect(0,0,W,H);
ctx.fillStyle='#2dd4bf';ctx.font='bold 14px Space Mono,monospace';ctx.textAlign='center';ctx.textBaseline='middle';
ctx.fillText('SNAKE',W/2,H/2-12);
ctx.fillStyle='#475569';ctx.font='11px Space Mono,monospace';
ctx.fillText('eat every fruit in order',W/2,H/2+10);
</script>
</body>
</html>
