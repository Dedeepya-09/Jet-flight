# Jet-flight
<!DOCTYPE html>
<html>
<head>
    <title>Solar Jet Fighter</title>
    <style>
        canvas {
            border: 1px solid black;
        }
        body {
            margin: 0;
            padding: 0;
            background: url('https://images.unsplash.com/photo-1447433589675-4aaa569f3e05?ixlib=rb-4.0.3&auto=format&fit=crop&w=1350&q=80') no-repeat center center fixed;
            background-size: cover;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
        }
    </style>
</head>
<body>
    <canvas id="gameCanvas" width="800" height="600"></canvas>

    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');

        // Player (Jet Fighter)
        const player = {
            x: canvas.width / 2,
            y: canvas.height - 50,
            width: 40,
            height: 20,
            speed: 5,
            dx: 0
        };

        // Enemies (Alien Spacecraft)
        let enemies = [];
        const enemySpeed = 2;
        const enemySpawnRate = 50;
        let frameCount = 0;

        // Bullets
        let bullets = [];
        const bulletSpeed = 7;

        // Game controls
        let rightPressed = false;
        let leftPressed = false;
        let spacePressed = false;

        // Event Listeners
        document.addEventListener('keydown', keyDownHandler);
        document.addEventListener('keyup', keyUpHandler);

        function keyDownHandler(e) {
            if (e.key === 'Right' || e.key === 'ArrowRight') rightPressed = true;
            if (e.key === 'Left' || e.key === 'ArrowLeft') leftPressed = true;
            if (e.key === ' ') spacePressed = true;
        }

        function keyUpHandler(e) {
            if (e.key === 'Right' || e.key === 'ArrowRight') rightPressed = false;
            if (e.key === 'Left' || e.key === 'ArrowLeft') leftPressed = false;
            if (e.key === ' ') spacePressed = false;
        }

        // Draw player (simple jet shape)
        function drawPlayer() {
            ctx.beginPath();
            ctx.moveTo(player.x, player.y);
            ctx.lineTo(player.x + player.width/2, player.y + player.height);
            ctx.lineTo(player.x - player.width/2, player.y + player.height);
            ctx.closePath();
            ctx.fillStyle = 'silver';
            ctx.fill();
        }

        // Draw enemy (alien spacecraft shape)
        function drawEnemy(enemy) {
            ctx.beginPath();
            ctx.arc(enemy.x, enemy.y, enemy.radius, 0, Math.PI * 2);
            ctx.lineTo(enemy.x - 15, enemy.y + 15);
            ctx.lineTo(enemy.x + 15, enemy.y + 15);
            ctx.closePath();
            ctx.fillStyle = 'green';
            ctx.fill();
        }

        // Draw bullet
        function drawBullet(bullet) {
            ctx.fillStyle = 'red';
            ctx.fillRect(bullet.x, bullet.y, bullet.width, bullet.height);
        }

        // Spawn enemies
        function spawnEnemy() {
            if (frameCount % enemySpawnRate === 0) {
                const radius = 20;
                const x = Math.random() * (canvas.width - radius * 2) + radius;
                enemies.push({ x, y: -radius, radius });
            }
        }

        // Update game objects
        function update() {
            // Move player
            if (rightPressed && player.x < canvas.width - player.width/2) player.x += player.speed;
            if (leftPressed && player.x > player.width/2) player.x -= player.speed;

            // Shoot bullets
            if (spacePressed && frameCount % 10 === 0) {
                bullets.push({
                    x: player.x,
                    y: player.y - 5,
                    width: 4,
                    height: 10
                });
            }

            // Move bullets
            bullets.forEach((bullet, index) => {
                bullet.y -= bulletSpeed;
                if (bullet.y < 0) bullets.splice(index, 1);
            });

            // Move enemies
            enemies.forEach((enemy, index) => {
                enemy.y += enemySpeed;
                if (enemy.y > canvas.height) enemies.splice(index, 1);

                // Collision detection with bullets
                bullets.forEach((bullet, bulletIndex) => {
                    if (
                        bullet.x > enemy.x - enemy.radius &&
                        bullet.x < enemy.x + enemy.radius &&
                        bullet.y > enemy.y - enemy.radius &&
                        bullet.y < enemy.y + enemy.radius
                    ) {
                        enemies.splice(index, 1);
                        bullets.splice(bulletIndex, 1);
                    }
                });
            });

            frameCount++;
        }

        // Main game loop
        function draw() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            
            drawPlayer();
            bullets.forEach(drawBullet);
            enemies.forEach(drawEnemy);
            
            spawnEnemy();
            update();
            
            requestAnimationFrame(draw);
        }

        draw();
    </script>
</body>
</html>
