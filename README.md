# old-man-game
<!DOCTYPE html>
<html lang="zh">
<head>
  <meta charset="UTF-8">
  <title>小恐龙游戏</title>
  <style>
    body {
      margin: 0;
      overflow: hidden;
      background: white;
    }
    canvas {
      display: block;
      margin: auto;
      background: #f5f5f5;
    }
  </style>
</head>
<body>
  <canvas id="gameCanvas" width="600" height="200"></canvas>
  <script>
    const canvas = document.getElementById("gameCanvas");
    const ctx = canvas.getContext("2d");

    // 配置
    const groundY = 150;
    const dinoSize = 30;
    const jumpPower = 15;
    const gravity = 1.2;
    const obstacleWidth = 20;
    const obstacleHeight = 40;
    let obstacleSpeed = 6;

    // 状态
    let dinoY = groundY;
    let velocityY = 0;
    let isJumping = false;
    let obstacleX = canvas.width;
    let score = 0;
    let gameOver = false;

    // 按键事件
    document.addEventListener("keydown", e => {
      if (e.code === "Space" && !isJumping && !gameOver) {
        velocityY = -jumpPower;
        isJumping = true;
      }
      if (e.code === "Space" && gameOver) {
        restart();
      }
    });

    function restart() {
      dinoY = groundY;
      velocityY = 0;
      isJumping = false;
      obstacleX = canvas.width;
      score = 0;
      obstacleSpeed = 6;
      gameOver = false;
      loop();
    }

    function update() {
      if (gameOver) return;

      // 跳跃物理
      dinoY += velocityY;
      velocityY += gravity;
      if (dinoY >= groundY) {
        dinoY = groundY;
        velocityY = 0;
        isJumping = false;
      }

      // 障碍移动
      obstacleX -= obstacleSpeed;
      if (obstacleX + obstacleWidth < 0) {
        obstacleX = canvas.width;
        score++;
        // 难度递增
        if (score % 5 === 0) obstacleSpeed += 1;
      }

      // 碰撞检测
      if (50 < obstacleX + obstacleWidth &&
          50 + dinoSize > obstacleX &&
          dinoY + dinoSize > groundY - obstacleHeight) {
        gameOver = true;
      }
    }

    function draw() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);

      // 地面
      ctx.fillStyle = "#888";
      ctx.fillRect(0, groundY, canvas.width, 2);

      // 恐龙
      ctx.fillStyle = "black";
      ctx.fillRect(50, dinoY - dinoSize, dinoSize, dinoSize);

      // 障碍物
      ctx.fillStyle = "red";
      ctx.fillRect(obstacleX, groundY - obstacleHeight, obstacleWidth, obstacleHeight);

      // 分数
      ctx.fillStyle = "black";
      ctx.font = "16px Arial";
      ctx.fillText("分数: " + score, 10, 20);

      if (gameOver) {
        ctx.fillStyle = "red";
        ctx.font = "24px Arial";
        ctx.fillText("游戏结束！按空格重新开始", 120, 100);
      }
    }

    function loop() {
      update();
      draw();
      if (!gameOver) {
        requestAnimationFrame(loop);
      }
    }

    loop();
  </script>
</body>
</html>
