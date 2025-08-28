<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Snake — Classic Style</title>
  <style>
    body {
      margin: 0;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      background: #0b1020;
      font-family: Arial, sans-serif;
      color: white;
    }
    canvas {
      background: #111a2b;
      border: 2px solid #7ef36a;
    }
    #overlay {
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      background: rgba(0,0,0,0.85);
      padding: 20px 40px;
      border-radius: 12px;
      text-align: center;
    }
    #overlay h1 {
      margin: 0 0 20px 0;
      font-size: 28px;
    }
    #overlay select, #overlay button {
      margin-top: 10px;
      padding: 10px 20px;
      border: none;
      border-radius: 8px;
      font-size: 16px;
      cursor: pointer;
    }
    #overlay select {
      background: #222;
      color: white;
    }
    #overlay button {
      background: #7ef36a;
      font-weight: bold;
    }
  </style>
</head>
<body>
  <canvas id="gameCanvas" width="400" height="400"></canvas>

  <div id="overlay">
    <h1>Snake Game</h1>
    <label for="speed">Choose Speed:</label><br>
    <select id="speed">
      <option value="200">Easy</option>
      <option value="120" selected>Normal</option>
      <option value="80">Hard</option>
      <option value="50">Extreme</option>
    </select><br>
    <button onclick="startGame()">Play</button>
  </div>

  <script>
    const canvas = document.getElementById("gameCanvas");
    const ctx = canvas.getContext("2d");
    const overlay = document.getElementById("overlay");
    const speedSelect = document.getElementById("speed");

    const gridSize = 20;
    let snake, dx, dy, apple, score, gameInterval, running, speed;

    function initGame() {
      snake = [{ x: 200, y: 200 }, { x: 180, y: 200 }, { x: 160, y: 200 }];
      dx = gridSize;
      dy = 0;
      score = 0;
      apple = randomApple();
      running = true;
    }

    function startGame() {
      speed = parseInt(speedSelect.value);
      overlay.style.display = "none";
      initGame();
      clearInterval(gameInterval);
      gameInterval = setInterval(gameLoop, speed);
    }

    function endGame() {
      running = false;
      clearInterval(gameInterval);
      overlay.style.display = "block";
      overlay.querySelector("h1").innerText = "Game Over! Score: " + score;
    }

    function randomApple() {
      return {
        x: Math.floor(Math.random() * (canvas.width / gridSize)) * gridSize,
        y: Math.floor(Math.random() * (canvas.height / gridSize)) * gridSize
      };
    }

    function gameLoop() {
      if (!running) return;

      const head = { x: snake[0].x + dx, y: snake[0].y + dy };

      // Wraparound
      if (head.x < 0) head.x = canvas.width - gridSize;
      if (head.x >= canvas.width) head.x = 0;
      if (head.y < 0) head.y = canvas.height - gridSize;
      if (head.y >= canvas.height) head.y = 0;

      // Collision with self
      for (let part of snake) {
        if (part.x === head.x && part.y === head.y) {
          endGame();
          return;
        }
      }

      snake.unshift(head);

      if (head.x === apple.x && head.y === apple.y) {
        score++;
        apple = randomApple();
      } else {
        snake.pop();
      }

      // Draw
      ctx.fillStyle = "#111a2b";
      ctx.fillRect(0, 0, canvas.width, canvas.height);

      ctx.fillStyle = "red";
      ctx.fillRect(apple.x, apple.y, gridSize, gridSize);

      ctx.fillStyle = "#7ef36a";
      for (let part of snake) {
        ctx.fillRect(part.x, part.y, gridSize, gridSize);
      }

      ctx.fillStyle = "white";
      ctx.font = "16px Arial";
      ctx.fillText("Score: " + score, 10, 20);
    }

    // Controls
    document.addEventListener("keydown", e => {
      if (e.key === "ArrowUp" && dy === 0) { dx = 0; dy = -gridSize; }
      else if (e.key === "ArrowDown" && dy === 0) { dx = 0; dy = gridSize; }
      else if (e.key === "ArrowLeft" && dx === 0) { dx = -gridSize; dy = 0; }
      else if (e.key === "ArrowRight" && dx === 0) { dx = gridSize; dy = 0; }
      // If opposite direction is pressed, nothing happens
    });
  </script>
</body>
</html>
