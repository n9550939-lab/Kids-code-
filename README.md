# Kids-code-
“Kids Code لعبة تعليمية للأطفال لتعلم البرمجة”.
<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Kids Code - النسخة النهائية</title>
<style>
body{margin:0;font-family:sans-serif;background:linear-gradient(135deg,#6EC6FF,#A66CFF);text-align:center;color:white;}
h2,h3{margin:10px;}
button{cursor:pointer;}
#login-box,#dashboard,#game{margin:20px auto;}
#login-box{background:white;color:#333;padding:30px;border-radius:20px;width:350px;margin-top:50px;box-shadow:0 8px 20px rgba(0,0,0,0.2);}
#login-box input{width:90%;padding:10px;margin:8px 0;border-radius:10px;border:1px solid #ccc;font-size:16px;}
#login-box button{background:#FF8C42;color:white;border:none;padding:10px;width:95%;border-radius:15px;font-size:16px;}
#dashboard{display:none;}
.world{background:white;color:#333;margin:15px auto;padding:15px;width:300px;border-radius:20px;cursor:pointer;transition:0.3s;}
.world:hover{transform:translateY(-5px);box-shadow:0 5px 15px rgba(0,0,0,0.2);}
#game{display:none;}
#game h2{margin-top:0;}
#game-area{width:400px;height:400px;background:#eef5ff;margin:20px auto;border-radius:15px;position:relative;overflow:hidden;border:3px solid #6C63FF;}
#robot{width:40px;height:40px;background:url('https://i.imgur.com/OoH1c7T.png') no-repeat center/contain;position:absolute;bottom:0;left:0;transition:0.3s;}
.goal{width:40px;height:40px;background:url('https://i.imgur.com/5tO7SQt.png') no-repeat center/contain;position:absolute;}
.door{width:50px;height:50px;background:url('https://i.imgur.com/YJv9mKu.png') no-repeat center/contain;position:absolute;border:3px solid #FFD700;border-radius:12px;box-shadow:0 0 15px 5px rgba(255,215,0,0.7);animation:glow 1.5s infinite alternate;}
@keyframes glow{0%{box-shadow:0 0 10px 2px rgba(255,215,0,0.5);}50%{box-shadow:0 0 20px 8px rgba(255,215,0,0.9);}100%{box-shadow:0 0 15px 5px rgba(255,215,0,0.7);}}
.obstacle{width:40px;height:40px;background:url('https://i.imgur.com/0r0xRfi.png') no-repeat center/contain;position:absolute;border-radius:5px;}
#controls button{margin:5px;padding:10px 15px;border:none;border-radius:10px;background:#FF8C42;color:white;font-size:16px;}
#controls .loopBtn{background:#FFD700;}
#stats{margin:10px;}
.star-effect{width:15px;height:15px;background:yellow;border-radius:50%;position:absolute;animation:floatStars 2s infinite;}
.tip-box{background:rgba(0,0,0,0.6);color:white;padding:8px;border-radius:10px;margin:10px;width:90%;display:inline-block;font-size:14px;}
.code-box{background:#fff;color:#333;padding:12px;border-radius:10px;margin:10px auto;width:90%;text-align:left;font-family:monospace;font-size:14px;height:120px;overflow:auto;}
</style>
</head>
<body>

<div id="login-box">
<h2>🚀 Kids Code</h2>
<input type="text" id="player-name" placeholder="اسم البطل"><br>
<input type="password" placeholder="كلمة السر"><br>
<button onclick="showDashboard()">ابدأ</button>
</div>

<div id="dashboard">
<h2>🌍 خريطة العوالم</h2>
<div class="world" onclick="startGame('commands')">🌳 غابة الأوامر</div>
<div class="world" onclick="startGame('loops')">❄️ جزيرة التكرار</div>
<div class="world" onclick="startGame('if')">🚀 عالم الشروط</div>
</div>

<div id="game">
<h2 id="game-title">اللعبة</h2>
<div id="stats">
<span id="level">المرحلة: 1</span> | 
<span id="points">نقاط: 0</span> | 
<span id="coins">💰: 0</span> | 
<span id="xp">XP: 0</span>
</div>
<div class="tip-box" id="tip"></div>
<div class="code-box" id="code">الكود التعليمي سيظهر هنا لكل مرحلة</div>
<div id="game-area">
<div id="robot"></div>
<div id="goal" class="goal"></div>
<div id="door" class="door"></div>
</div>
<div id="controls">
<button onclick="move('up')">⬆️ فوق</button>
<button onclick="move('down')">⬇️ تحت</button>
<button onclick="move('left')">⬅️ يسار</button>
<button onclick="move('right')">➡️ يمين</button>
<button class="loopBtn" onclick="loopMove()">🔁 Loop</button>
</div>
</div>

<script>
const loginBox=document.getElementById('login-box');
const dashboard=document.getElementById('dashboard');
const game=document.getElementById('game');
const robot=document.getElementById('robot');
const goal=document.getElementById('goal');
const door=document.getElementById('door');
const gameArea=document.getElementById('game-area');
const gameTitle=document.getElementById('game-title');
const levelSpan=document.getElementById('level');
const pointsSpan=document.getElementById('points');
const coinsSpan=document.getElementById('coins');
const xpSpan=document.getElementById('xp');
const tipBox=document.getElementById('tip');
const codeBox=document.getElementById('code');

let topPos=0,leftPos=0;
let currentGame='';
let currentLevel=1;
let points=0,coins=0,xp=0;
let obstacles=[];

function showDashboard(){loginBox.style.display='none';dashboard.style.display='block';}

function startGame(level){
dashboard.style.display='none';
game.style.display='block';
robot.style.top='360px';
robot.style.left='0px';
topPos=0; leftPos=0;
currentGame=level;
currentLevel=1;
points=0; coins=0; xp=0;
setupLevel();
}

const codes={
commands:[
"moveRight();",
"moveLeft();",
"moveUp();",
"moveDown();",
"moveRight(); moveUp();",
"moveLeft(); moveUp();",
"moveRight(); moveDown();",
"moveLeft(); moveDown();",
"moveRight(); moveUp(); moveRight();",
"moveLeft(); moveUp(); moveLeft();"
],
loops:[
"repeat(3){ moveRight(); }",
"repeat(2){ moveUp(); moveRight(); }",
"repeat(4){ moveDown(); moveLeft(); }",
"repeat(3){ moveRight(); moveUp(); }",
"repeat(5){ moveLeft(); moveDown(); }",
"repeat(2){ moveRight(); moveDown(); }",
"repeat(3){ moveUp(); moveLeft(); }",
"repeat(4){ moveRight(); moveDown(); }",
"repeat(5){ moveUp(); moveRight(); }",
"repeat(6){ moveDown(); moveLeft(); moveRight(); }"
],
ifStatements:[
"if(obstacle){ moveLeft(); }",
"if(obstacle){ moveRight(); }",
"if(obstacle){ moveUp(); }",
"if(obstacle){ moveDown(); }",
"if(obstacle){ moveUp(); moveRight(); }",
"if(obstacle){ moveDown(); moveLeft(); }",
"if(obstacle){ moveRight(); moveUp(); }",
"if(obstacle){ moveLeft(); moveDown(); }",
"if(obstacle){ moveRight(); moveDown(); }",
"if(obstacle){ moveLeft(); moveUp(); }"
]
};

function setupLevel(){
levelSpan.textContent=`المرحلة: ${currentLevel}/10`;
pointsSpan.textContent=`نقاط: ${points}`;
coinsSpan.textContent=`💰: ${coins}`;
xpSpan.textContent=`XP: ${xp}`;
robot.style.top='360px';
robot.style.left='0px';
topPos=0; leftPos=0;
obstacles.forEach(o=>gameArea.removeChild(o));
obstacles=[];
goal.style.display='none';
door.style.display='none';

let codeText='';
if(currentGame==='commands'){
gameTitle.textContent=`🌳 غابة الأوامر - المرحلة ${currentLevel}/10`;
goal.style.display='block';
goal.style.top=`${30*currentLevel}px`;
goal.style.left=`${30*currentLevel}px`;
addObstacles(currentLevel+2);
addStars(3);
showTip("💡 استخدم الأوامر لتحريك الروبوت!");
codeText=codes.commands[currentLevel-1];
}else if(currentGame==='loops'){
gameTitle.textContent=`❄️ جزيرة التكرار - المرحلة ${currentLevel}/10`;
goal.style.display='block';
goal.style.top=`${20*currentLevel}px`;
goal.style.left=`${20*currentLevel}px`;
addObstacles(currentLevel+3);
addStars(4);
showTip("💡 استخدم Loop لتكرار الحركة!");
codeText=codes.loops[currentLevel-1];
}else if(currentGame==='if'){
gameTitle.textContent=`🚀 عالم الشروط - المرحلة ${currentLevel}/10`;
goal.style.display='block';
goal.style.top=`${20*currentLevel}px`;
goal.style.left=`${30*currentLevel}px`;
door.style.display='block';
door.style.top=`${50 + Math.random()*300}px`;
door.style.left=`${50 + Math.random()*300}px`;
addObstacles(currentLevel+4);
addStars(5);
showTip("💡 استخدم If لتجنب العقبات!");
codeText=codes.ifStatements[currentLevel-1];
}
codeBox.textContent=codeText;
}

function move(dir){
const step=20;
const areaSize=400-40;
if(dir==='up' && topPos<areaSize) topPos+=step;
if(dir==='down' && topPos>0) topPos-=step;
if(dir==='right' && leftPos<areaSize) leftPos+=step;
if(dir==='left' && leftPos>0) leftPos-=step;
robot.style.top=(400-40-topPos)+'px';
robot.style.left=leftPos+'px';
checkCollisions();
}

function loopMove(){
if(currentGame==='loops'){
for(let i=0;i<currentLevel;i++){
setTimeout(()=>{ move('right'); }, i*300);
setTimeout(()=>{ move('up'); }, i*300+150);
}
}
}

function addObstacles(num){
for(let i=0;i<num;i++){
let ob=document.createElement('div');
ob.className='obstacle';
ob.style.top=`${Math.random()*300}px`;
ob.style.left=`${Math.random()*300}px`;
gameArea.appendChild(ob);
obstacles.push(ob);
}
}

function addStars(num){
for(let i=0;i<num;i++){
let s=document.createElement('div');
s.className='star-effect';
s.style.top=`${Math.random()*350}px`;
s.style.left=`${Math.random()*350}px`;
gameArea.appendChild(s);
setTimeout(()=>{gameArea.removeChild(s)},5000);
}
}

function hit(a,b){
const r=a.getBoundingClientRect();
const g=b.getBoundingClientRect();
return !(r.right < g.left || r.left > g.right || r.bottom < g.top || r.top > g.bottom);
}

function checkCollisions(){
for(let o of obstacles){
if(hit(robot,o)){
alert('💥 اصطدمت بعقبة! حاول مرة أخرى');
setupLevel();
return;
}
}
if(currentGame==='commands'||currentGame==='loops'){
if(hit(robot,goal)){points+=10; coins+=5; xp+=5; nextLevel();}
}else if(currentGame==='if'){
if(hit(robot,goal)) door.style.background='yellow';
if(hit(robot,door)){points+=15; coins+=10; xp+=10; nextLevel();}
}
}

function nextLevel(){
if(currentLevel<10){
alert(`🎉 أكملت المرحلة ${currentLevel} بنجاح!`);
currentLevel++;
setupLevel();
}else{
alert(`🏆 أكملت جميع المراحل في Kids Code!\nالنقاط: ${points}\n💰 الكوينز: ${coins}\nXP: ${xp}`);
dashboard.style.display='block';
game.style.display='none';
}
}

function showTip(text){tipBox.textContent=text;}
</script>
</body>
</html>
