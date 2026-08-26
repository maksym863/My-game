#000000
<!DOCTYPE html>
<html lang="uk">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Котяча пригода 😺</title>

  <style>
    * {
      box-sizing: border-box
    }

    body {
      margin: 0;
      overflow: hidden;
      font-family: Arial, sans-serif;
      background: #87ceeb;
      touch-action: none;
    }

    #game {
      position: relative;
      width: 100vw;
      height: 100vh;
      overflow: hidden;
      background: linear-gradient(#87ceeb 0 55%, #63bd52 55%);
    }

    #info {
      position: absolute;
      top: 10px;
      left: 10px;
      z-index: 30;
      background: white;
      padding: 10px 14px;
      border-radius: 15px;
      font-weight: bold;
      line-height: 1.5;
    }

    #restart {
      position: absolute;
      top: 10px;
      right: 10px;
      z-index: 40;
      padding: 10px 13px;
      border: 3px solid #333;
      border-radius: 14px;
      background: white;
      font-size: 16px;
      font-weight: bold;
    }

    #message {
      position: absolute;
      top: 95px;
      left: 50%;
      transform: translateX(-50%);
      z-index: 30;
      background: white;
      padding: 8px 14px;
      border-radius: 15px;
      font-weight: bold;
      white-space: nowrap;
    }

    #cat {
      position: absolute;
      width: 55px;
      height: 55px;
      font-size: 48px;
      z-index: 10;
    }

    .fish {
      position: absolute;
      font-size: 35px;
      z-index: 5;
    }

    .dog {
      position: absolute;
      font-size: 42px;
      z-index: 9;
    }

    #controls {
      position: absolute;
      bottom: 20px;
      left: 20px;
      z-index: 50;
      display: grid;
      grid-template-columns: 70px 70px 70px;
      grid-template-rows: 70px 70px;
      gap: 8px;
    }

    .control {
      border: 3px solid #333;
      border-radius: 20px;
      background: white;
      font-size: 30px;
      box-shadow: 0 5px 0 #555;
      user-select: none;
      touch-action: none;
    }

    .control:active {
      transform: translateY(5px);
      box-shadow: 0 0 0 #555;
      background: #ddd;
    }

    #up {
      grid-column: 2
    }

    #left {
      grid-column: 1;
      grid-row: 2
    }

    #down {
      grid-column: 2;
      grid-row: 2
    }

    #right {
      grid-column: 3;
      grid-row: 2
    }

    #gameOver {
      display: none;
      position: absolute;
      inset: 0;
      z-index: 100;
      background: rgba(0, 0, 0, .7);
      color: white;
      align-items: center;
      justify-content: center;
      flex-direction: column;
      text-align: center;
    }

    #gameOver h1 {
      font-size: 42px;
    }

    #again {
      padding: 15px 25px;
      border: 0;
      border-radius: 15px;
      font-size: 20px;
    }
  </style>
</head>

<body>

  <div id="game">

    <div id="info">
      🐟 Риба: <span id="score">0</span>/<span id="total">0</span><br>
      ⭐ Рівень: <span id="level">1</span><br>
      ❤️ Життя: <span id="lives">5</span>
    </div>

    <button id="restart">🔄 Перезапустити</button>

    <div id="message">Збери всю рибу! 😺</div>

    <div id="cat">😺</div>

    <div id="controls">
      <button class="control" id="up">⬆️</button>
      <button class="control" id="left">⬅️</button>
      <button class="control" id="down">⬇️</button>
      <button class="control" id="right">➡️</button>
    </div>

    <div id="gameOver">
      <h1>😿 Гру закінчено!</h1>
      <p id="gameOverText"></p>
      <button id="again">🔄 Грати знову</button>
    </div>

  </div>

  <script>

const game=document.getElementById("game");
const cat=document.getElementById("cat");

const scoreText=document.getElementById("score");
const totalText=document.getElementById("total");
const levelText=document.getElementById("level");
const livesText=document.getElementById("lives");
const message=document.getElementById("message");

let x=100;
let y=150;

let level=1;
let score=0;
let totalFish=5;

let lives=5;

let speed=5;

let dogs=[];

let moving={
  up:false,
  down:false,
  left:false,
  right:false
};


/* ЗАПУСК РІВНЯ */

function startLevel(){

  document.querySelectorAll(".fish").forEach(e=>e.remove());
  document.querySelectorAll(".dog").forEach(e=>e.remove());

  score=0;

  x=100;
  y=150;

  speed=5+level*0.35;

  totalFish=4+level;

  scoreText.textContent=score;
  totalText.textContent=totalFish;
  levelText.textContent=level;
  livesText.textContent=lives;

  message.textContent=
    "Рівень "+level+"! Збери рибу 😺";

  createFish();

  /* На 15 рівні рівно ДВІ собаки */
  if(level===15){
    createDogs();
    message.textContent=
      "⚠️ РІВЕНЬ 15! Дві собаки полюють на кота! 🐶🐶";
  }

  updateCat();
}


/* РИБА */

function createFish(){

  for(let i=0;i<totalFish;i++){

    const fish=document.createElement("div");

    fish.className="fish";
    fish.textContent="🐟";

    fish.style.left=
      (70+Math.random()*
      (game.clientWidth-130))+"px";

    fish.style.top=
      (130+Math.random()*
      (game.clientHeight-230))+"px";

    game.appendChild(fish);
  }
}


/* ДВІ СОБАКИ */

function createDogs(){

  dogs=[];

  for(let i=0;i<2;i++){

    const dog=document.createElement("div");

    dog.className="dog";
    dog.textContent="🐶";

    let dogData={
      element:dog,
      x:game.clientWidth-100-i*80,
      y:game.clientHeight-180-i*80
    };

    dog.style.left=dogData.x+"px";
    dog.style.top=dogData.y+"px";

    game.appendChild(dog);

    dogs.push(dogData);
  }
}


/* РУХ КОТА */

function updateCat(){

  const maxX=game.clientWidth-cat.offsetWidth;
  const maxY=game.clientHeight-cat.offsetHeight;

  x=Math.max(0,Math.min(x,maxX));
  y=Math.max(0,Math.min(y,maxY));

  cat.style.left=x+"px";
  cat.style.top=y+"px";

  collectFish();
}


/* ЗБИРАННЯ РИБИ */

function collectFish(){

  document.querySelectorAll(".fish").forEach(fish=>{

    const fx=fish.offsetLeft;
    const fy=fish.offsetTop;

    const distance=Math.hypot(x-fx,y-fy);

    if(distance<55){

      fish.remove();

      score++;

      scoreText.textContent=score;

      if(score>=totalFish){

        if(level<15){

          message.textContent=
            "🎉 Рівень пройдено!";

          setTimeout(()=>{

            level++;

            startLevel();

          },1000);

        }else{

          message.textContent=
            "🏆 ТИ ПРОЙШОВ УСІ 15 РІВНІВ! 😺🎉";

        }
      }
    }
  });
}


/* СОБАКИ БІЖАТЬ ЗА КОТОМ */

function updateDogs(){

  if(level!==15) return;

  dogs.forEach(dog=>{

    let dx=x-dog.x;
    let dy=y-dog.y;

    let distance=Math.hypot(dx,dy);

    if(distance>1){

      /*
       * Собака рухається до кота.
       * Чим ближче — тим небезпечніше!
       */

      let dogSpeed=2.2;

      dog.x+=(dx/distance)*dogSpeed;
      dog.y+=(dy/distance)*dogSpeed;

      dog.element.style.left=
        dog.x+"px";

      dog.element.style.top=
        dog.y+"px";
    }

    /* Собака зловила кота */

    if(distance<45){

      loseLife(dog);

    }

  });
}


/* ВТРАТА ЖИТТЯ */

let hitCooldown=false;

function loseLife(dog){

  if(hitCooldown) return;

  hitCooldown=true;

  lives--;

  livesText.textContent=lives;

  message.textContent=
    "🐶 Гав! Кіт втратив життя! ❤️";

  /*
   * Відкидаємо собаку назад,
   * щоб вона не забрала всі життя одразу.
   */

  dog.x=game.clientWidth-100;
  dog.y=game.clientHeight-150;

  if(lives<=0){

    gameOver();

  }else{

    setTimeout(()=>{
      hitCooldown=false;
    },1500);

  }
}


/* GAME OVER */

function gameOver(){

  moving.up=false;
  moving.down=false;
  moving.left=false;
  moving.right=false;

  document.getElementById("gameOver").style.display="flex";

  document.getElementById("gameOverText").textContent=
    "Ти дійшов до рівня "+level+". Риба: "+score;
}


/* КНОПКА ЗАТИСКАННЯ */

function holdButton(button,direction){

  button.addEventListener("pointerdown",e=>{

    e.preventDefault();

    moving[direction]=true;

  });

  button.addEventListener("pointerup",()=>{

    moving[direction]=false;

  });

  button.addEventListener("pointercancel",()=>{

    moving[direction]=false;

  });

  button.addEventListener("pointerleave",()=>{

    moving[direction]=false;

  });
}

holdButton(
  document.getElementById("up"),
  "up"
);

holdButton(
  document.getElementById("down"),
  "down"
);

holdButton(
  document.getElementById("left"),
  "left"
);

holdButton(
  document.getElementById("right"),
  "right"
);


/* КЛАВІАТУРА */

document.addEventListener("keydown",e=>{

  if(e.key==="ArrowUp"||e.key==="w")
    moving.up=true;

  if(e.key==="ArrowDown"||e.key==="s")
    moving.down=true;

  if(e.key==="ArrowLeft"||e.key==="a")
    moving.left=true;

  if(e.key==="ArrowRight"||e.key==="d")
    moving.right=true;

});

document.addEventListener("keyup",e=>{

  if(e.key==="ArrowUp"||e.key==="w")
    moving.up=false;

  if(e.key==="ArrowDown"||e.key==="s")
    moving.down=false;

  if(e.key==="ArrowLeft"||e.key==="a")
    moving.left=false;

  if(e.key==="ArrowRight"||e.key==="d")
    moving.right=false;

});


/* ПЕРЕЗАПУСК */

function restartGame(){

  level=1;
  lives=5;

  document.getElementById("gameOver")
    .style.display="none";

  startLevel();
}

document.getElementById("restart")
  .addEventListener("click",restartGame);

document.getElementById("again")
  .addEventListener("click",restartGame);


/* ГОЛОВНИЙ ЦИКЛ */

function gameLoop(){

  if(moving.up) y-=speed;
  if(moving.down) y+=speed;
  if(moving.left) x-=speed;
  if(moving.right) x+=speed;

  updateCat();
  updateDogs();

  requestAnimationFrame(gameLoop);
}

startLevel();
gameLoop();

</script>

</body>

</html>
