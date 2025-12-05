<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>type=</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    
    <style>
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-height: 100vh;
            background-color: #1e1e1e;
            color: white;
            margin: 0;
            transition: background-color 0.5s;
        }

        .container {
            text-align: center;
            padding: 40px 60px;
            border-radius: 12px;
            box-shadow: 0 8px 30px rgba(0, 0, 0, 0.4);
            transition: all 0.5s ease-in-out;
            min-width: 450px;
            background-color: #282c34; /* Нейтральный фон контейнера */
        }
        
        /* Стили для Главного Меню */
        #main-menu {
            display: flex;
            flex-direction: column;
            align-items: center;
        }

        #main-menu h1 {
            font-size: 2.5em;
            margin-bottom: 20px;
            color: #61dafb;
        }

        #main-menu p {
            font-size: 1.1em;
            color: #ccc;
            margin-bottom: 30px;
        }

        /* --- Стилизация по уровням сложности (Контейнер) --- */
        .wuss { background-color: #38a169; box-shadow: 0 0 20px #38a169; }
        .easy { background-color: #4c78a8; box-shadow: 0 0 20px #4c78a8; }
        .medium { background-color: #f6ad55; box-shadow: 0 0 20px #f6ad55; }
        .hard { background-color: #e53e3e; box-shadow: 0 0 20px #e53e3e; }
        .insane { background-color: #9f7aea; box-shadow: 0 0 20px #9f7aea; }
        .challenge { background-color: #d53f8c; box-shadow: 0 0 20px #d53f8c; }
        .x-tra { 
            background-color: #000000; 
            box-shadow: 0 0 30px #ffffff;
            border: 2px solid #ffffff;
        }

        /* --- Общие элементы Игры --- */

        #level-display {
            font-size: 1.4em;
            margin-bottom: 25px;
            font-weight: 500;
            color: #d0d0d0;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        #level-display i { margin-right: 10px; }

        #key-display {
            font-size: 4.5em;
            font-weight: 700;
            margin-bottom: 20px;
            padding: 15px 25px;
            border-radius: 10px;
            min-height: 80px;
            display: flex;
            flex-wrap: wrap;
            align-items: center;
            justify-content: center;
            gap: 10px;
        }
        
        #key-display span {
            padding: 5px;
            border-radius: 5px;
            line-height: 1;
        }

        /* Стиль для самой текущей клавиши */
        #key-display .current-key {
            color: #ffeb3b !important;
            text-shadow: 0 0 10px rgba(255, 255, 0, 0.8);
            transform: scale(1.1);
            transition: transform 0.1s;
            border-bottom: 3px solid #ffeb3b;
        }

        /* Стиль для уже нажатых клавиш */
        #key-display .completed-key {
            color: #c8e6c9 !important;
            opacity: 0.6;
        }

        /* --- Таймер Уровня --- */
        #level-timer-display {
            font-size: 1.8em;
            font-weight: bold;
            color: #ff4500;
            margin-top: 15px;
            min-height: 30px;
        }

        #message {
            font-size: 1.6em;
            font-weight: 600;
            color: #ffcc00;
            min-height: 30px;
            margin-top: 15px;
        }

        #score-time {
            font-size: 1.2em;
            margin-top: 20px;
            color: #c0c0c0;
        }

        button {
            padding: 12px 25px;
            font-size: 1.2em;
            cursor: pointer;
            background-color: #4CAF50; /* Нейтральный цвет для кнопок */
            color: white;
            border: none;
            border-radius: 8px;
            margin-top: 20px;
            transition: background-color 0.3s, transform 0.1s;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
        }

        button:hover {
            background-color: #45a049;
            transform: translateY(-2px);
        }

        /* Стиль для состояний Победа/Проигрыш */
        .game-over { background-color: #5d0f0f !important; box-shadow: 0 0 30px #e53e3e !important; }
        .win { background-color: #1f5022 !important; box-shadow: 0 0 30px #4CAF50 !important; }
    </style>
</head>
<body>
    <div class="container" id="main-container">

        <div id="main-menu">
            <h1>type=</h1>
            <p>Проверьте свою реакцию и скорость печати на 7 уровнях сложности!</p>
            <button id="menu-start-button">Начать игру</button>
        </div>

        <div id="game-container" style="display:none;">
            <h1>type=</h1>
            <div id="level-display"></div>
            <div id="level-timer-display"></div>
            <div id="key-display"></div>
            <div id="message"></div>
            <div id="score-time"></div>
            <button id="restart-button" style="display:none;">Начать снова</button>
            <button id="menu-button">В главное меню</button>
        </div>
    </div>

    <script>
        // --- Конфигурация игры ---
        const LEVELS = 7;
        
        const BASE_KEYS = ['1', '2', '3', '4', '5', '6', '7', '8', '9', '0', 'j', 'f', 'k', 'd', 'l', 's', 'a', 'g', 'h', 'z', 'x', 'c', 'v', 'b', 'n', 'm', 'q', 'w', 'e', 'r', 't', 'y', ';', 'Shift'];

        const DIFFICULTY_DATA = [
            { name: "Wuss", keysCount: 2, icon: "fas fa-feather-alt", timeLimitMs: 4000 },
            { name: "Easy", keysCount: 3, icon: "fas fa-hand-peace", timeLimitMs: 3500 },
            { name: "Medium", keysCount: 4, icon: "fas fa-running", timeLimitMs: 3000 },
            { name: "Hard", keysCount: 5, icon: "fas fa-skull", timeLimitMs: 2500 },
            { name: "Insane", keysCount: 6, icon: "fas fa-brain", timeLimitMs: 2000 },
            { name: "Challenge", keysCount: 7, icon: "fas fa-wrench", timeLimitMs: 1500 },
            { name: "X-trA", keysCount: 8, icon: "fas fa-fire-alt", timeLimitMs: 1000 }
        ];

        // --- Переменные состояния игры ---
        let currentLevel = 0;
        let requiredKeys = [];
        let keyIndex = 0; 
        let startTime = 0;
        let isGameRunning = false;
        let totalTimeTimerId = null;
        let levelTimerId = null;
        let levelTimeRemaining = 0;

        // --- Элементы DOM ---
        const mainMenu = document.getElementById('main-menu');
        const gameContainer = document.getElementById('game-container');
        const keyDisplay = document.getElementById('key-display');
        const levelDisplay = document.getElementById('level-display');
        const messageDisplay = document.getElementById('message');
        const scoreTimeDisplay = document.getElementById('score-time');
        const levelTimerDisplay = document.getElementById('level-timer-display');
        const menuStartButton = document.getElementById('menu-start-button');
        const restartButton = document.getElementById('restart-button');
        const menuButton = document.getElementById('menu-button');
        const mainContainer = document.getElementById('main-container');
        const body = document.body;

        // --- Функции Таймера ---

        function updateLevelTimer() {
            levelTimeRemaining -= 50;
            
            const seconds = (levelTimeRemaining / 1000).toFixed(2);
            levelTimerDisplay.textContent = `Таймер уровня: ${seconds} с`;

            if (levelTimeRemaining < 1000) {
                 levelTimerDisplay.style.color = 'red';
            } else {
                 levelTimerDisplay.style.color = '#ff4500';
            }

            if (levelTimeRemaining <= 0) {
                clearInterval(levelTimerId);
                endGame(false, null, null, "Время вышло! Вы не успели нажать все клавиши.");
                return;
            }
        }

        function startLevelTimer() {
            if (levelTimerId) clearInterval(levelTimerId);
            levelTimerId = setInterval(updateLevelTimer, 50);
            updateLevelTimer();
        }

        function updateTotalTime() {
            if (!isGameRunning) {
                clearInterval(totalTimeTimerId);
                return;
            }
            const timeElapsed = ((Date.now() - startTime) / 1000).toFixed(2);
            scoreTimeDisplay.textContent = `Общее время: ${timeElapsed} с`;
            totalTimeTimerId = setTimeout(updateTotalTime, 50); 
        }

        // --- Основная логика игры ---

        function startGame() {
            // Скрываем меню, показываем игру
            mainMenu.style.display = 'none';
            gameContainer.style.display = 'block';
            
            isGameRunning = true;
            currentLevel = 0;
            restartButton.style.display = 'none';
            menuButton.style.display = 'none'; // Скрываем кнопку "В меню" во время игры
            messageDisplay.textContent = '';
            body.classList.remove('game-over', 'win');
            
            // Сброс стиля контейнера на нейтральный перед первым уровнем
            mainContainer.className = 'container'; 

            startTime = Date.now();
            updateTotalTime();
            nextLevel();
            document.addEventListener('keydown', handleKeyPress);
        }

        function nextLevel() {
            if (currentLevel >= LEVELS) {
                endGame(true);
                return;
            }

            currentLevel++;
            const levelData = DIFFICULTY_DATA[currentLevel - 1];
            
            // 1. Стилизация контейнера
            mainContainer.className = 'container';
            mainContainer.classList.add(levelData.name.toLowerCase().replace('-', ''));
            
            // 2. Генерация последовательности
            requiredKeys = [];
            let availableKeys = [...BASE_KEYS];
            for (let i = 0; i < levelData.keysCount; i++) {
                const randomIndex = Math.floor(Math.random() * availableKeys.length);
                requiredKeys.push(availableKeys[randomIndex]);
                availableKeys.splice(randomIndex, 1);
            }

            keyIndex = 0;
            
            // 3. Запуск таймера уровня
            levelTimeRemaining = levelData.timeLimitMs;
            startLevelTimer(); 

            // 4. Обновляем интерфейс
            levelDisplay.innerHTML = `<i class="${levelData.icon}"></i> Уровень ${currentLevel} - **${levelData.name}**`;
            updateKeyDisplay();
        }

        function formatKeyForDisplay(key) {
            if (key === 'Shift') return 'SHIFT';
            if (key === ';') return '; / Ж';
            return key.toUpperCase();
        }

        function updateKeyDisplay() {
            const displayHTML = requiredKeys.map((key, index) => {
                let className = '';
                if (index === keyIndex) {
                    className = 'current-key';
                } else if (index < keyIndex) {
                    className = 'completed-key';
                }
                return `<span class="${className}">${formatKeyForDisplay(key)}</span>`;
            }).join(' ');

            keyDisplay.innerHTML = displayHTML;
        }

        function handleKeyPress(event) {
            if (!isGameRunning) return;

            const pressedKey = event.key === 'Shift' ? 'Shift' : event.key.toLowerCase();
            
            if (event.ctrlKey || event.altKey || event.metaKey) return;
            
            const expectedKey = requiredKeys[keyIndex];

            if (pressedKey === expectedKey) {
                keyIndex++;
                updateKeyDisplay();

                if (keyIndex === requiredKeys.length) {
                    clearInterval(levelTimerId);
                    messageDisplay.textContent = `Уровень ${currentLevel} пройден!`;
                    setTimeout(() => {
                        messageDisplay.textContent = '';
                        nextLevel();
                    }, 600); 
                }
            } else {
                endGame(false, formatKeyForDisplay(pressedKey), formatKeyForDisplay(expectedKey), `Неправильная клавиша!`);
            }
        }

        function endGame(isWin, wrongKey = null, expectedKey = null, reason = null) {
            isGameRunning = false;
            
            // Останавливаем все таймеры
            clearInterval(totalTimeTimerId);
            clearInterval(levelTimerId); 
            document.removeEventListener('keydown', handleKeyPress);
            
            // Показываем кнопки рестарта и меню
            restartButton.style.display = 'block';
            menuButton.style.display = 'block';
            
            levelTimerDisplay.textContent = ''; // Очищаем таймер уровня

            if (isWin) {
                const finalTime = ((Date.now() - startTime) / 1000).toFixed(2);
                keyDisplay.innerHTML = `ПОБЕДА!`;
                messageDisplay.textContent = `Вы завершили игру за ${finalTime} секунд!`;
                scoreTimeDisplay.textContent = 'Отличная реакция!';
                mainContainer.classList.add('win');
            } else {
                mainContainer.classList.add('game-over');
                keyDisplay.innerHTML = `ИГРА ОКОНЧЕНА`;
                
                if (reason) {
                    messageDisplay.textContent = reason;
                } else {
                    messageDisplay.textContent = `Ошибка: Вы нажали "${wrongKey}", ожидалась "${expectedKey}".`;
                }
                scoreTimeDisplay.textContent = `Пройдено уровней: ${currentLevel}`;
            }
            body.className = '';
        }

        function showMainMenu() {
            // Очищаем и показываем меню
            isGameRunning = false;
            if (totalTimeTimerId) clearInterval(totalTimeTimerId);
            if (levelTimerId) clearInterval(levelTimerId);
            document.removeEventListener('keydown', handleKeyPress);

            gameContainer.style.display = 'none';
            mainMenu.style.display = 'flex';
            
            // Сброс стиля контейнера на нейтральный
            mainContainer.className = 'container';
        }

        // --- Инициализация ---
        menuStartButton.addEventListener('click', startGame);
        restartButton.addEventListener('click', startGame);
        menuButton.addEventListener('click', showMainMenu);
        
        // Отображение вступительного текста при первом запуске
        levelDisplay.innerHTML = `Пройдите ${LEVELS} уровней!`;

    </script>
</body>
</html>
