<script>
    import { onMount } from "svelte";
    import { goto } from "$app/navigation";

    // Initialize gride size
    const gridSize = 10;

    // Initialize score
    let score = 0;

    onMount(() => {
        const board = document.getElementById("game-board");

        console.log(board);

        // Define game variables
        let snake = [{
            x: Math.round(gridSize/2),
            y: Math.round(gridSize/2)
        }];
        let food = generateFood();
        let direction = 'right';
        let gameInterval;
        let gameSpeedDelay = 200;
        let gameStarted = false;

        // Initialize game elements
        function draw() {
            board.innerHTML = '';
            drawSnake();
            drawFood();
        }

        // Draw snake
        function drawSnake() {
            snake.forEach((segment) => {
                const snakeElement = createGameElements('div', 'snake');

                // Set color snake
                snakeElement.classList.add('bg-green-600');

                setPosition(snakeElement, segment);
                board.appendChild(snakeElement);
            });
        }

        // Draw food
        function drawFood() {
            const foodElement = createGameElements('div', 'food');

            // Set color food
            foodElement.classList.add('bg-red-600');

            setPosition(foodElement, food);
            board.appendChild(foodElement);
        }

        // Create elements (snake, food)
        function createGameElements(tag, className) {
            const element = document.createElement(tag);
            element.className = className;
            return element;
        }

        // Set the position of elements
        function setPosition(element, position) {
            element.style.gridColumn = position.x;
            element.style.gridRow = position.y;
        }

        // Generate random position for food
        function generateFood() {
            let posX = Math.floor(Math.random() * gridSize + 1);
            let posY = Math.floor(Math.random() * gridSize + 1);

            snake.forEach((segment) => {
                while(segment.x === posX && segment.y === posY) {
                    posX = Math.floor(Math.random() * gridSize + 1);
                    posY = Math.floor(Math.random() * gridSize + 1);
                }
            })

            return { x: posX, y: posY };
        }

        // Moving the snake
        function move() {
            const head = { ...snake[0] };
            switch (direction) {
                case 'up':
                    head.y--;
                    break;

                case 'down':
                    head.y++;
                    break;

                case 'left':
                    head.x--;
                    break;

                case 'right':
                    head.x++;
                    break;
            }

            snake.unshift(head);

            // Test if snake eat the food
            if (head.x === food.x && head.y === food.y) {
                score++;
                food = generateFood();

                increaseSpeed();
                clearInterval(gameInterval);
                gameInterval = setInterval(() => {
                    checkValidation();
                    move();
                    checkCollisions();
                    draw();
                }, gameSpeedDelay);

            } else {
                snake.pop();
            }
        }

        // Increase speed
        function increaseSpeed() {
            if (gameSpeedDelay > 150) {
                gameSpeedDelay -= 5;
            }
        }

        // Start game
        function startGame() {
            gameStarted = true;
            gameInterval = setInterval(() => {
                move();
                checkCollisions();
                draw();
            }, gameSpeedDelay);
        }

        // Game over
        function resetGame() {
            gameStarted = false;
            snake = [{ x: gridSize/2, y: gridSize/2 }];
            food = generateFood();
            score = 0;
            direction = 'right';
            gameSpeedDelay = 200;
        }

        // Validate gametcha
        function checkValidation() {
            if (score === 30) {
                goto('/page-home');
            }
        }

        // Check collisions
        function checkCollisions() {
            const head = snake[0];

            // Collisions with walls
            if (
                (head.x < 0 || head.x > gridSize) ||
                (head.y < 0 || head.y > gridSize)
            ) {
                resetGame();
            }

            // Collisions with snake
            for (let i = 1; i < snake.length; i++) {
                if (head.x === snake[i].x && head.y === snake[i].y) {
                    resetGame();
                }
            }
        }

        // Keypress event listener
        function handleKeyPress(event) {
            if (!gameStarted && event.key === ' ') {
                startGame();

            } else {
                switch (event.key) {
                    case 'ArrowUp':
                        direction = 'up';
                        break;

                    case 'ArrowDown':
                        direction = 'down';
                        break;

                    case 'ArrowLeft':
                        direction = 'left';
                        break;

                    case 'ArrowRight':
                        direction = 'right';
                        break;
                }
            }
        }

        document.addEventListener('keydown', handleKeyPress);
    });
</script>

<p id="snake-score">Score : {score}</p>

<div id="snake-container" class="w-80 h-80">
    <div id="game-board" class="grid grid-cols-{gridSize} grid-rows-{gridSize} w-full h-full border-8 border-blue-700 bg-blue-300">

        <p id="start-menu" class="col-span-full row-start-{gridSize/2} text-center content-center">PRESS SPACE TO START</p>

    </div>
</div>




