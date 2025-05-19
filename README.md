<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Catch the Hearts 💖</title>
  <style>
    * { margin: 0; padding: 0; box-sizing: border-box; }
    body {
      font-family: 'Segoe UI', sans-serif;
      overflow: hidden;
      background: linear-gradient(135deg, #f9a8d4, #f472b6);
      color: white;
      touch-action: none;
    }
    #gameArea {
      position: relative;
      width: 100vw;
      height: 100vh;
      background-color: #fff0f5;
    }
    #player {
      width: 80px;
      height: 80px;
      background: url('https://emoji.slack-edge.com/T02MH7ZHC/heartpulse/1f7314f06c6a16d8.png') no-repeat center center / contain;
      position: absolute;
      bottom: 20px;
      left: 50%;
      transform: translateX(-50%);
    }
    .heart {
      width: 40px;
      height: 40px;
      background: url('https://i.imgur.com/KZsmUi2.png') no-repeat center center / contain;
      position: absolute;
    }
    #scoreBoard {
      position: absolute;
      top: 10px;
      left: 10px;
      font-size: 20px;
      color: black;
    }
    #lives {
      position: absolute;
      top: 10px;
      right: 10px;
      font-size: 20px;
      color: black;
    }
    #gameOver {
      position: absolute;
      top: 40%;
      left: 50%;
      transform: translate(-50%, -50%);
      font-size: 36px;
      color: #c0392b;
      display: none;
      text-align: center;
    }
    #compliment {
      position: absolute;
      bottom: 100px;
      left: 50%;
      transform: translateX(-50%);
      font-size: 20px;
      color: #2c3e50;
      background: #ffdde1;
      padding: 10px 20px;
      border-radius: 20px;
      display: none;
    }
    #startBtn {
      position: absolute;
      top: 45%;
      left: 50%;
      transform: translate(-50%, -50%);
      font-size: 24px;
      padding: 15px 40px;
      border-radius: 12px;
      border: none;
      background: #f472b6;
      color: white;
      cursor: pointer;
      z-index: 1000;
    }
  </style>
</head>
<body>

<div id="gameArea">
  <div id="player"></div>
  <div id="scoreBoard">Score: 0 | High Score: 0</div>
  <div id="lives">Lives: ❤️❤️❤️</div>
  <div id="gameOver">
    <strong>Game Over 💔</strong><br>
    Refresh to play again!
  </div>
  <div id="compliment">You're amazing! 🌟</div>

  <button id="startBtn">Start Game</button>

  <!-- Background Music -->
  <audio id="bgMusic" loop>
    <source src="aashiqtera.mp3" type="audio/mpeg" />
    Your browser does not support the audio element.
  </audio>
</div>

<script>
  const gameArea = document.getElementById("gameArea");
  const player = document.getElementById("player");
  const scoreBoard = document.getElementById("scoreBoard");
  const livesDisplay = document.getElementById("lives");
  const gameOver = document.getElementById("gameOver");
  const compliment = document.getElementById("compliment");
  const startBtn = document.getElementById("startBtn");
  const bgMusic = document.getElementById("bgMusic");

  let score = 0;
  let highScore = 0;
  let lives = 3;
  let heartFallSpeed = 2;

  const compliments = [
    "You're amazing! 🌟",
    "Keep going, superstar! ✨",
    "You're doing great! 💖",
    "You're the best! 💯",
    "You're a heart catcher! 💘",
    "So proud of you! 😍"
  ];

  let heartSpawner;
  let speedIncreaser;

  // Player movement handlers
  function movePlayer(x) {
    let posX = x - 40;
    if (posX < 0) posX = 0;
    if (posX > window.innerWidth - 80) posX = window.innerWidth - 80;
    player.style.left = posX + "px";
  }

  window.addEventListener("mousemove", e => movePlayer(e.clientX));
  window.addEventListener("touchmove", e => {
    if (e.touches.length > 0) movePlayer(e.touches[0].clientX);
  });

  function dropHeart() {
    const heart = document.createElement("div");
    heart.classList.add("heart");
    heart.style.left = `${Math.random() * (window.innerWidth - 40)}px`;
    heart.style.top = `0px`;
    gameArea.appendChild(heart);

    const fallInterval = setInterval(() => {
      let top = parseFloat(heart.style.top);
      heart.style.top = `${top + heartFallSpeed}px`;

      const heartRect = heart.getBoundingClientRect();
      const playerRect = player.getBoundingClientRect();

      if (
        heartRect.bottom >= playerRect.top &&
        heartRect.left < playerRect.right &&
        heartRect.right > playerRect.left
      ) {
        gameArea.removeChild(heart);
        score++;
        updateScore();
        clearInterval(fallInterval);

        if (score > highScore) {
          highScore = score;
          showCompliment();
        }

      } else if (top > window.innerHeight) {
        gameArea.removeChild(heart);
        loseLife();
        clearInterval(fallInterval);
      }
    }, 16);
  }

  function loseLife() {
    lives--;
    updateLives();
    if (lives === 0) {
      endGame();
    }
  }

  function updateScore() {
    scoreBoard.innerText = `Score: ${score} | High Score: ${highScore}`;
  }

  function updateLives() {
    let hearts = "";
    for (let i = 0; i < lives; i++) {
      hearts += "❤️";
    }
    livesDisplay.innerText = `Lives: ${hearts}`;
  }

  function endGame() {
    clearInterval(heartSpawner);
    clearInterval(speedIncreaser);
    gameOver.style.display = "block";
  }

  function showCompliment() {
    compliment.innerText = compliments[Math.floor(Math.random() * compliments.length)];
    compliment.style.display = "block";
    setTimeout(() => {
      compliment.style.display = "none";
    }, 3000);
  }

  function startGame() {
    score = 0;
    lives = 3;
    heartFallSpeed = 2;
    updateScore();
    updateLives();
    gameOver.style.display = "none";

    heartSpawner = setInterval(dropHeart, 1000);
    speedIncreaser = setInterval(() => {
      heartFallSpeed += 0.3;
    }, 5000);
  }

  startBtn.addEventListener('click', () => {
    bgMusic.volume = 0.3;
    bgMusic.play().catch(e => console.log("Audio play error:", e));
    startBtn.style.display = "none";
    startGame();
  });
</script>

</body>
</html>
