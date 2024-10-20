<html lang="ky">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Тогуз Коргоол</title>
    <style>
        body {
            font-family: 'Arial', sans-serif;
            text-align: center;
            background-color: blueviolet;
            color: #fff;
            margin: 0;
            padding: 0;
        }
        h1 {
            color: white;
        }
        .board {
            display: grid;
            grid-template-columns: repeat(9, 70px);
            gap: 10px;
            justify-content: center;
            margin-bottom: 20px;
        }
        .hole {
            width: 70px;
            height: 70px;
            background-color: #add8e6;
            display: flex;
            align-items: center;
            justify-content: center;
            border-radius: 50%;
            font-size: 20px;
            cursor: pointer;
            position: relative;
            transition: background-color 0.3s, transform 0.3s;
        }
        .hole:hover {
            background-color: #87cefa;
            transform: scale(1.1);
        }
        .hole img {
            width: 45px;
            height: 45px;
        }
        .scores {
            margin-bottom: 20px;
            font-size: 18px;
        }
        .hole .stones {
            position: absolute;
            bottom: 5px;
            right: 5px;
            font-size: 14px;
            background-color: black;
            border-radius: 50%;
            padding: 3px 7px;
            border: 1px solid #ccc;
        }
        button {
            padding: 10px 20px;
            font-size: 16px;
            background-color: #0056b3;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            transition: background-color 0.3s;
        }
        button:hover {
            background-color: #004494;
        }
        .game-mode, .skins, .statistics {
            margin: 20px 0;
        }
        .container {
            padding: 20px;
        }
        .timer {
            font-size: 18px;
            margin-bottom: 20px;
        }
        .player-info {
            display: flex;
            align-items: center;
            justify-content: center;
            margin-bottom: 20px;
        }
        .avatar {
            width: 50px;
            height: 50px;
            border-radius: 50%;
            margin-right: 10px;
        }
        @media only screen and (max-width: 600px) {
            .board {
                transform: scale(0.8);
                justify-content: center;
            }
        }
    </style>
</head>
<body>
    <h1>Тогуз Коргоол</h1>
     <div class="player-info">
        <img src="photo_2024-09-16_21-46-27.jpg" alt="Player 1 Avatar" class="avatar">
        <span id="player1-name">Оюнчу 1</span>
    </div> 
    <div class="scores">
        <span id="player1-score">Очко Оюнчу 1: 0</span> |
        <span id="player2-score">Очко Оюнчу 2: 0</span>
    </div>
 <div id="board" class="board"></div>
 <div class="timer">Оюн убактысы: <span id="time">30</span> сек</div>
 <div class="game-mode">
        <label for="gameMode">Оюн режими:</label>
        <select id="gameMode">
            <option value="bot">Компьютер менен</option>
            <option value="two-players">Эки оюнчу</option>
        </select>
    </div>
 <div class="skins">
        <label for="skin-select">Доска стилин тандаңыз:</label>
        <select id="skin-select">
            <option value="classic">Классикалык</option>
            <option value="modern">Заманбап</option>
            <option value="nature">Табият</option>
        </select>
    </div>
<div>
       <button onclick="restartGame()">Жаңыртуу</button>
    </div>
 <div class="statistics">
        <h3>Оюнчу статистикасы:</h3>
        <p>Оюндар: <span id="games-played">0</span></p>
        <p>Жеңиштер: <span id="games-won">0</span></p>
    </div>
<audio id="move-sound" src="move.mp3" preload="auto"></audio>
    <audio id="win-sound" src="win.mp3" preload="auto"></audio>
 <script>
        let board = [
            [9, 9, 9, 9, 9, 9, 9, 9, 9],
            [9, 9, 9, 9, 9, 9, 9, 9, 9]
        ];
        let scores = [0, 0];
        let currentPlayer = 0;
        let gameMode = 'bot';
        let difficulty = 'easy';
        let gamesPlayed = 0;
        let gamesWon = [0, 0];
        let moveTime = 30; // 30 секунд на ход
        let timer;
  // Функция для создания доски
        function createBoard() {
            const boardElement = document.getElementById('board');
            boardElement.innerHTML = '';
            for (let player = 0; player < 2; player++) {
                for (let i = 0; i < 9; i++) {
                    const hole = document.createElement('div');
                    hole.className = 'hole';
                    hole.innerHTML = `<div class="stones">${board[player][i]}</div>`;
                    if (player === 0) {
                        hole.onclick = () => makeMove(currentPlayer, i);
                    }
                    boardElement.appendChild(hole);
                }
            }
        }
 // Таймер для хода
        function startTimer() {
            clearInterval(timer);
            moveTime = 30;
            document.getElementById('time').textContent = moveTime;
            timer = setInterval(function() {
                moveTime--;
                document.getElementById('time').textContent = moveTime;
                if (moveTime <= 0) {
                    clearInterval(timer);
                    alert("Убакыт бүттү! Кийинки оюнчу.");
                    currentPlayer = 1 - currentPlayer;
                    updateBoard();
                }
            }, 1000);
        }
   // Функция для выполнения хода
        function makeMove(player, holeIndex) {
            if (player !== currentPlayer) {
                alert("Азыр сиздин кезегиңиз эмес!");
                return;
            }
            let stones = board[player][holeIndex];
            if (stones === 0) {
                alert("Бул тешик бош!");
                return;
            }
            board[player][holeIndex] = 0;
            let side = player;
            let hole = holeIndex;
            while (stones > 0) {
                hole++;
                if (hole > 8) {
                    hole = 0;
                    side = 1 - side;
                }
                board[side][hole]++;
                stones--;
            }
            if (board[side][hole] % 2 === 0 && side !== currentPlayer) {
                scores[currentPlayer] += board[side][hole];
                board[side][hole] = 0;
            }
            if (scores[currentPlayer] >= 81) {
                alert(`Оюнчу ${currentPlayer + 1} жеңди!`);
                playSound('win-sound');
                gamesPlayed++;
                gamesWon[currentPlayer]++;
                restartGame();
                return;
            }
            currentPlayer = 1 - currentPlayer;
            updateBoard();
            playSound('move-sound');
            if (gameMode === 'bot' && currentPlayer === 1) {
                setTimeout(botMove, 1000);
            }
            startTimer();
            checkGameOver();
        }
 // Функция для хода бота
        function botMove() {
            let bestMove = -1;
            if (difficulty === 'easy') {
                do {
                    bestMove = Math.floor(Math.random() * 9);
                } while (board[1][bestMove] === 0);
            }
            makeMove(1, bestMove);
        }
     // Обновление доски и счета
        function updateBoard() {
            createBoard();
            document.getElementById('player1-score').textContent = `Очко Оюнчу 1: ${scores[0]}`;
            document.getElementById('player2-score').textContent = `Очко Оюнчу 2: ${scores[1]}`;
            document.getElementById('games-played').textContent = gamesPlayed;
            document.getElementById('games-won').textContent = gamesWon[currentPlayer];
        }
  // Проверка на конец игры
        function checkGameOver() {
            if (scores[0] >= 81 || scores[1] >= 81) {
                clearInterval(timer);
                alert(`Оюн бүткөндө, ${scores[0] >= 81 ? 'Оюнчу 1' : 'Оюнчу 2'} жеңди!`);
                playSound('win-sound');
                restartGame();
            }
        }
  // Функция для перезапуска игры
        function restartGame() {
            board = [
                [9, 9, 9, 9, 9, 9, 9, 9, 9],
                [9, 9, 9, 9, 9, 9, 9, 9, 9]
            ];
            scores = [0, 0];
            currentPlayer = 0;
            gamesPlayed = 0;
            gamesWon = [0, 0];
            createBoard();
            updateBoard();
            startTimer();
        }
 // Воспроизведение звука
        function playSound(soundId) {
            document.getElementById(soundId).play();
        }
// Смена скина доски
        document.getElementById('skin-select').addEventListener('change', function() {
            const selectedSkin = this.value;
            document.body.style.backgroundImage = `url(${selectedSkin}.jpg)`;
        });
  // Запуск игры
        createBoard();
        startTimer();
    </script>
</body>
</html>
