# brick-break-game
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Brick Breaker</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        canvas {
            border: 2px solid #4a5568; /* Gray-700 */
            background-color: #edf2f7; /* Gray-200 */
        }
    </style>
</head>
<body class="bg-gray-100 flex items-center justify-center min-h-screen">
    <div class="text-center">
        <h1 class="text-3xl font-bold text-gray-800 mb-4">Brick Breaker</h1>
        <canvas id="gameCanvas" width="480" height="320"></canvas>
        <p class="mt-4 text-gray-600">Use ← and → keys to move the paddle. Break all bricks to win!</p>
    </div>

    <script>
        // Get canvas and context
        const canvas = document.getElementById("gameCanvas");
        const ctx = canvas.getContext("2d");

        // Game variables
        const ballRadius = 10;
        let x = canvas.width / 2;
        let y = canvas.height - 30;
        let dx = 2;
        let dy = -2;

        const paddleHeight = 10;
        const paddleWidth = 75;
        let paddleX = (canvas.width - paddleWidth) / 2;

        const brickRowCount = 3;
        const brickColumnCount = 5;
        const brickWidth = 75;
        const brickHeight = 20;
        const brickPadding = 10;
        const brickOffsetTop = 30;
        const brickOffsetLeft = 30;

        let rightPressed = false;
        let leftPressed = false;

        // Initialize bricks
        const bricks = [];
        for (let c = 0; c < brickColumnCount; c++) {
            bricks[c] = [];
            for (let r = 0; r < brickRowCount; r++) {
                bricks[c][r] = { x: 0, y: 0, status: 1 };
            }
        }

        // Event listeners for paddle movement
        document.addEventListener("keydown", keyDownHandler);
        document.addEventListener("keyup", keyUpHandler);

        function keyDownHandler(e) {
            if (e.key === "Right" || e.key === "ArrowRight") rightPressed = true;
            else if (e.key === "Left" || e.key === "ArrowLeft") leftPressed = true;
        }

        function keyUpHandler(e) {
            if (e.key === "Right" || e.key === "ArrowRight") rightPressed = false;
            else if (e.key === "Left" || e.key === "ArrowLeft") leftPressed = false;
        }

        // Draw functions
        function drawBall() {
            ctx.beginPath();
            ctx.arc(x, y, ballRadius, 0, Math.PI * 2);
            ctx.fillStyle = "#e53e3e"; // Red-600
            ctx.fill();
            ctx.closePath();
        }

        function drawPaddle() {
            ctx.beginPath();
            ctx.rect(paddleX, canvas.height - paddleHeight, paddleWidth, paddleHeight);
            ctx.fillStyle = "#2b6cb0"; // Blue-700
            ctx.fill();
            ctx.closePath();
        }

        function drawBricks() {
            for (let c = 0; c < brickColumnCount; c++) {
                for (let r = 0; r < brickRowCount; r++) {
                    if (bricks[c][r].status === 1) {
                        const brickX = c * (brickWidth + brickPadding) + brickOffsetLeft;
                        const brickY = r * (brickHeight + brickPadding) + brickOffsetTop;
                        bricks[c][r].x = brickX;
                        bricks[c][r].y = brickY;
                        ctx.beginPath();
                        ctx.rect(brickX, brickY, brickWidth, brickHeight);
                        ctx.fillStyle = "#48bb78"; // Green-500
                        ctx.fill();
                        ctx.closePath();
                    }
                }
            }
        }

        // Collision detection
        function collisionDetection() {
            for (let c = 0; c < brickColumnCount; c++) {
                for (let r = 0; r < brickRowCount; r++) {
                    const b = bricks[c][r];
                    if (b.status === 1) {
                        if (x > b.x && x < b.x + brickWidth && y > b.y && y < b.y + brickHeight) {
                            dy = -dy;
                            b.status = 0;
                        }
                    }
                }
            }
        }

        // Game loop
        function draw() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            drawBricks();
            drawBall();
            drawPaddle();
            collisionDetection();

            // Ball movement and wall collision
            if (x + dx > canvas.width - ballRadius || x + dx < ballRadius) dx = -dx;
            if (y + dy < ballRadius) dy = -dy;
            else if (y + dy > canvas.height - ballRadius) {
                if (x > paddleX && x < paddleX + paddleWidth) dy = -dy;
                else {
                    alert("Game Over! Reload to play again.");
                    document.location.reload();
                    return;
                }
            }

            // Paddle movement
            if (rightPressed && paddleX < canvas.width - paddleWidth) paddleX += 7;
            else if (leftPressed && paddleX > 0) paddleX -= 7;

            x += dx;
            y += dy;

            // Check win condition
            const remainingBricks = bricks.flat().filter(b => b.status === 1).length;
            if (remainingBricks === 0) {
                alert("You Win! Reload to play again.");
                document.location.reload();
                return;
            }

            requestAnimationFrame(draw);
        }

        // Start the game
        draw();
    </script>
</body>
</html>
