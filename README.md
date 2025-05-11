# Space---shooter---game
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Space Shooter Game</title>
  <link rel="stylesheet" href="style.css" />
</head>
<body>
  <canvas id="gameCanvas"></canvas>
  <script src="script.js"></script>
</body>
</html>
body {
  margin: 0;
  overflow: hidden;
  background: black;
}

canvas {
  display: block;
}
const canvas = document.getElementById("gameCanvas");
const ctx = canvas.getContext("2d");

canvas.width = 400;
canvas.height = 600;

let keys = {};
let bullets = [];
let enemies = [];

const player = {
  x: canvas.width / 2 - 15,
  y: canvas.height - 60,
  width: 30,
  height: 30,
  speed: 5,
  color: "lime"
};

function drawPlayer() {
  ctx.fillStyle = player.color;
  ctx.fillRect(player.x, player.y, player.width, player.height);
}

function drawBullets() {
  ctx.fillStyle = "white";
  bullets.forEach((bullet, index) => {
    bullet.y -= 7;
    ctx.fillRect(bullet.x, bullet.y, bullet.width, bullet.height);

    // Remove off-screen bullets
    if (bullet.y < 0) bullets.splice(index, 1);
  });
}

function drawEnemies() {
  enemies.forEach((enemy, eIndex) => {
    enemy.y += 2;
    ctx.fillStyle = "red";
    ctx.fillRect(enemy.x, enemy.y, enemy.width, enemy.height);

    // Collision check with bullets
    bullets.forEach((bullet, bIndex) => {
      if (
        bullet.x < enemy.x + enemy.width &&
        bullet.x + bullet.width > enemy.x &&
        bullet.y < enemy.y + enemy.height &&
        bullet.y + bullet.height > enemy.y
      ) {
        enemies.splice(eIndex, 1);
        bullets.splice(bIndex, 1);
      }
    });

    // Game over condition
    if (enemy.y + enemy.height > canvas.height) {
      alert("Game Over!");
      document.location.reload();
    }
  });
}

function spawnEnemy() {
  const x = Math.random() * (canvas.width - 30);
  enemies.push({ x: x, y: -30, width: 30, height: 30 });
}

function update() {
  if (keys["ArrowLeft"] && player.x > 0) player.x -= player.speed;
  if (keys["ArrowRight"] && player.x < canvas.width - player.width)
    player.x += player.speed;
}

function gameLoop() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);

  update();
  drawPlayer();
  drawBullets();
  drawEnemies();

  requestAnimationFrame(gameLoop);
}

setInterval(spawnEnemy, 1000);

document.addEventListener("keydown", (e) => {
  keys[e.key] = true;
  if (e.key === " ") {
    bullets.push({
      x: player.x + player.width / 2 - 2.5,
      y: player.y,
      width: 5,
      height: 10
    });
  }
});

document.addEventListener("keyup", (e) => {
  keys[e.key] = false;
});

gameLoop();
