<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Math Dungeons!</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Nunito:wght@700;900&display=swap');

        body {
            margin: 0; padding: 0;
            background-color: #2c3e50;
            font-family: 'Nunito', sans-serif;
            display: flex; flex-direction: column;
            align-items: center; justify-content: center;
            height: 100vh; color: white; overflow: hidden;
        }

        #game-container {
            width: 90%; max-width: 600px;
            background: #34495e;
            border-radius: 20px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.5);
            position: relative;
            overflow: hidden;
            border: 4px solid #f39c12;
        }

        /* Top HUD */
        #hud {
            display: flex; justify-content: space-between;
            background: #2c3e50; padding: 15px 20px;
            border-bottom: 4px solid #1abc9c;
            font-size: 1.2rem;
        }
        .stat-box { display: flex; align-items: center; gap: 10px; }
        
        #hunger-container { width: 150px; height: 20px; background: #c0392b; border-radius: 10px; overflow: hidden; border: 2px solid #fff;}
        #hunger-bar { height: 100%; width: 100%; background: #e74c3c; transition: width 0.3s; }

        /* Screens */
        .screen { padding: 30px; text-align: center; display: none; min-height: 400px; }
        .screen.active { display: block; }

        h1 { color: #f1c40f; text-shadow: 2px 2px #d35400; font-size: 2.5rem; margin-top: 0;}
        h2 { color: #3498db; }

        /* Dungeon Grid */
        .dungeon-grid {
            display: grid; grid-template-columns: 1fr 1fr; gap: 15px; margin-top: 20px;
        }
        .dungeon-btn {
            background: #9b59b6; color: white; border: none; padding: 15px;
            font-size: 1.1rem; border-radius: 10px; cursor: pointer;
            border-bottom: 4px solid #8e44ad; transition: 0.1s; font-family: 'Nunito', sans-serif;
        }
        .dungeon-btn:active { transform: translateY(4px); border-bottom: 0px; }
        .dungeon-btn:hover { background: #af7ac5; }
        .dungeon-btn.boss { grid-column: span 2; background: #e67e22; border-color: #d35400; }

        /* Gameplay Area */
        #question-box { font-size: 4rem; margin: 20px 0; color: #ecf0f1; text-shadow: 2px 2px #2c3e50; }
        #answer-input {
            font-size: 2rem; width: 150px; text-align: center;
            border-radius: 10px; border: 3px solid #3498db; padding: 10px;
            font-family: 'Nunito', sans-serif; background: #ecf0f1;
        }
        
        #timer-container {
            width: 100%; height: 25px; background: #7f8c8d; border-radius: 15px;
            margin: 20px 0; overflow: hidden; border: 3px solid #2c3e50;
        }
        #timer-bar { height: 100%; width: 100%; background: #2ecc71; }

        /* Buttons */
        .action-btn {
            background: #1abc9c; color: white; border: none; padding: 15px 30px;
            font-size: 1.5rem; border-radius: 15px; cursor: pointer;
            border-bottom: 5px solid #16a085; font-weight: bold; margin-top: 20px; font-family: inherit;
        }
        .action-btn:active { transform: translateY(5px); border-bottom: 0px; }
        .action-btn:disabled { background: #95a5a6; border-color: #7f8c8d; cursor: not-allowed; }

        #shop-toggle { background: #f1c40f; border-color: #f39c12; color: #d35400; margin-left: 10px; }

        /* Shop Modal */
        #shop-screen {
            position: absolute; top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(44, 62, 80, 0.95); display: none; flex-direction: column;
            padding: 20px; box-sizing: border-box; z-index: 10;
        }
        .shop-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; overflow-y: auto; max-height: 350px;}
        .shop-item { background: #34495e; padding: 15px; border-radius: 10px; border: 2px solid #bdc3c7; }
        .shop-item button { background: #2ecc71; border: none; padding: 8px; border-radius: 5px; color: white; cursor: pointer; font-weight: bold; margin-top: 10px; width: 100%;}
        .shop-item button:hover { background: #27ae60; }
        #close-shop { background: #e74c3c; margin-top: 20px; }

        #feedback { font-size: 1.5rem; height: 30px; margin-top: 10px; font-weight: bold;}
    </style>
</head>
<body>

<div id="game-container">
    <div id="hud">
        <div class="stat-box">🪙 <span id="coin-ui">0</span></div>
        <div class="stat-box">
            🍗 
            <div id="hunger-container">
                <div id="hunger-bar"></div>
            </div>
        </div>
        <button id="shop-toggle" onclick="toggleShop()">🛒 Shop</button>
    </div>

    <!-- MAIN MENU -->
    <div id="menu" class="screen active">
        <h1>Math Dungeons</h1>
        <p>Answer fast, earn coins, don't starve!</p>
        <div class="dungeon-grid">
            <button class="dungeon-btn" onclick="startDungeon(1, 'Addition')">1. Addition Area</button>
            <button class="dungeon-btn" onclick="startDungeon(2, 'Subtraction')">2. Subtraction Swamp</button>
            <button class="dungeon-btn" onclick="startDungeon(3, 'Add & Sub')">3. Mixed Plains (+ -)</button>
            <button class="dungeon-btn" onclick="startDungeon(4, 'Multiplication')">4. Multiply Mountain</button>
            <button class="dungeon-btn" onclick="startDungeon(5, 'Division')">5. Division Desert</button>
            <button class="dungeon-btn" onclick="startDungeon(6, 'Mult & Div')">6. Mixed Cave (x ÷)</button>
            <button class="dungeon-btn boss" onclick="startDungeon(7, 'All Operations')">7. The Ultimate Boss Castle</button>
        </div>
    </div>

    <!-- GAMEPLAY SCREEN -->
    <div id="game" class="screen">
        <h2 id="dungeon-title">Dungeon 1</h2>
        <div id="timer-container"><div id="timer-bar"></div></div>
        <div id="question-box">5 + 5 = ?</div>
        <input type="number" id="answer-input" autocomplete="off" placeholder="?">
        <div id="feedback"></div>
        <button id="next-btn" class="action-btn" onclick="nextQuestion()" style="display:none;">Next Question</button>
        <button class="action-btn" onclick="quitDungeon()" style="background:#e74c3c; border-color:#c0392b; display: block; margin: 20px auto 0; font-size: 1rem; padding: 10px 20px;">Run Away (Menu)</button>
    </div>

    <!-- SHOP SCREEN -->
    <div id="shop-screen">
        <h1>🛒 The Shop</h1>
        <div class="shop-grid">
            <div class="shop-item">
                <h3>🍏 Small Apple</h3>
                <p>+10 Hunger</p>
                <button onclick="buyItem('food', 10, 3)">Buy (3 🪙)</button>
            </div>
            <div class="shop-item">
                <h3>🍔 Burger</h3>
                <p>+20 Hunger</p>
                <button onclick="buyItem('food', 20, 5)">Buy (5 🪙)</button>
            </div>
            <div class="shop-item">
                <h3>🍖 Mega Meat</h3>
                <p>+30 Hunger</p>
                <button onclick="buyItem('food', 30, 7)">Buy (7 🪙)</button>
            </div>
            <div class="shop-item">
                <h3>🧪 Blue Potion</h3>
                <p>+1s Time Limit</p>
                <button onclick="buyItem('time', 1, 10)">Buy (10 🪙)</button>
            </div>
            <div class="shop-item">
                <h3>🧪 Purple Potion</h3>
                <p>+2s Time Limit</p>
                <button onclick="buyItem('time', 2, 18)">Buy (18 🪙)</button>
            </div>
            <div class="shop-item">
                <h3>🧪 Golden Potion</h3>
                <p>+3s Time Limit</p>
                <button onclick="buyItem('time', 3, 25)">Buy (25 🪙)</button>
            </div>
        </div>
        <button id="close-shop" class="action-btn" onclick="toggleShop()">Close Shop</button>
    </div>
</div>

<script>
    // --- AUDIO SYSTEM ---
    const AudioContext = window.AudioContext || window.webkitAudioContext;
    let audioCtx;
    
    function initAudio() {
        if (!audioCtx) audioCtx = new AudioContext();
        if (audioCtx.state === 'suspended') audioCtx.resume();
    }

    function playSound(type) {
        if (!audioCtx) return;
        const osc = audioCtx.createOscillator();
        const gain = audioCtx.createGain();
        osc.connect(gain);
        gain.connect(audioCtx.destination);
        const now = audioCtx.currentTime;

        if (type === 'correct') {
            osc.type = 'sine';
            osc.frequency.setValueAtTime(400, now);
            osc.frequency.exponentialRampToValueAtTime(800, now + 0.1);
            gain.gain.setValueAtTime(0.2, now);
            gain.gain.exponentialRampToValueAtTime(0.01, now + 0.2);
            osc.start(now); osc.stop(now + 0.2);
        } else if (type === 'wrong' || type === 'timeout') {
            osc.type = 'sawtooth';
            osc.frequency.setValueAtTime(150, now);
            osc.frequency.exponentialRampToValueAtTime(50, now + 0.3);
            gain.gain.setValueAtTime(0.2, now);
            gain.gain.exponentialRampToValueAtTime(0.01, now + 0.3);
            osc.start(now); osc.stop(now + 0.3);
        } else if (type === 'coin') {
            osc.type = 'square';
            osc.frequency.setValueAtTime(1200, now);
            osc.frequency.setValueAtTime(1600, now + 0.05);
            gain.gain.setValueAtTime(0.1, now);
            gain.gain.exponentialRampToValueAtTime(0.01, now + 0.15);
            osc.start(now); osc.stop(now + 0.15);
        } else if (type === 'buy') {
            osc.type = 'triangle';
            osc.frequency.setValueAtTime(600, now);
            osc.frequency.linearRampToValueAtTime(1200, now + 0.2);
            gain.gain.setValueAtTime(0.15, now);
            gain.gain.linearRampToValueAtTime(0.01, now + 0.2);
            osc.start(now); osc.stop(now + 0.2);
        } else if (type === 'gameover') {
            osc.type = 'sawtooth';
            osc.frequency.setValueAtTime(200, now);
            osc.frequency.linearRampToValueAtTime(50, now + 1);
            gain.gain.setValueAtTime(0.3, now);
            gain.gain.linearRampToValueAtTime(0.01, now + 1);
            osc.start(now); osc.stop(now + 1);
        }
    }

    // --- GAME STATE ---
    let coins = 0;
    let hunger = 50;
    const maxHunger = 50;
    let baseTime = 5000; // 5 seconds in ms
    let extraTime = 0; // Potion upgrades in ms
    
    let currentDungeon = 0;
    let currentAnswer = 0;
    let timeRemaining = 0;
    let timerInterval;
    let hungerInterval;
    let isWaitingForNext = false;
    let isGameOver = false;

    // DOM Elements
    const dom = {
        coinUI: document.getElementById('coin-ui'),
        hungerBar: document.getElementById('hunger-bar'),
        menu: document.getElementById('menu'),
        game: document.getElementById('game'),
        shop: document.getElementById('shop-screen'),
        dungeonTitle: document.getElementById('dungeon-title'),
        questionBox: document.getElementById('question-box'),
        answerInput: document.getElementById('answer-input'),
        timerBar: document.getElementById('timer-bar'),
        feedback: document.getElementById('feedback'),
        nextBtn: document.getElementById('next-btn')
    };

    // --- GAME LOGIC ---
    function updateHUD() {
        dom.coinUI.innerText = coins;
        const hungerPercent = (hunger / maxHunger) * 100;
        dom.hungerBar.style.width = hungerPercent + '%';
        if (hungerPercent > 50) dom.hungerBar.style.background = '#2ecc71';
        else if (hungerPercent > 20) dom.hungerBar.style.background = '#f1c40f';
        else dom.hungerBar.style.background = '#e74c3c';
    }

    // Hunger System (1 point every 2.5 seconds)
    function startHunger() {
        if (hungerInterval) clearInterval(hungerInterval);
        hungerInterval = setInterval(() => {
            if (isGameOver) return;
            hunger--;
            updateHUD();
            if (hunger <= 0) gameOver("You starved!");
        }, 2500);
    }

    function toggleShop() {
        initAudio();
        if (dom.shop.style.display === 'flex') {
            dom.shop.style.display = 'none';
        } else {
            dom.shop.style.display = 'flex';
        }
    }

    function buyItem(type, amount, cost) {
        initAudio();
        if (coins >= cost) {
            coins -= cost;
            playSound('buy');
            if (type === 'food') {
                hunger = Math.min(maxHunger, hunger + amount);
            } else if (type === 'time') {
                extraTime += (amount * 1000);
            }
            updateHUD();
        } else {
            alert("Not enough coins!");
        }
    }

    function startDungeon(level, name) {
        initAudio();
        if (isGameOver) {
            hunger = 50; coins = 0; extraTime = 0; isGameOver = false;
        }
        currentDungeon = level;
        dom.menu.classList.remove('active');
        dom.game.classList.add('active');
        dom.dungeonTitle.innerText = name;
        if (!hungerInterval) startHunger();
        nextQuestion();
    }

    function quitDungeon() {
        clearInterval(timerInterval);
        dom.game.classList.remove('active');
        dom.menu.classList.add('active');
    }

    function gameOver(reason) {
        isGameOver = true;
        clearInterval(timerInterval);
        clearInterval(hungerInterval);
        playSound('gameover');
        alert("GAME OVER: " + reason + "\nYou collected " + coins + " coins.");
        quitDungeon();
    }

    function nextQuestion() {
        isWaitingForNext = false;
        dom.nextBtn.style.display = 'none';
        dom.answerInput.style.display = 'block';
        dom.answerInput.disabled = false;
        dom.answerInput.value = '';
        dom.answerInput.focus();
        dom.feedback.innerText = '';
        
        generateMath(currentDungeon);
        startTimer();
    }

    function generateMath(level) {
        let op = level;
        if (level === 3) op = Math.random() > 0.5 ? 1 : 2; // mixed + -
        if (level === 6) op = Math.random() > 0.5 ? 4 : 5; // mixed * /
        if (level === 7) op = Math.floor(Math.random() * 4) + 1; // 1 to 4 mapped below
        if (level === 7 && op === 3) op = 4; // remap 3 to *
        if (level === 7 && op === 4) op = 5; // remap 4 to /

        let a, b;
        switch(op) {
            case 1: // Addition
                a = Math.floor(Math.random() * 20) + 1;
                b = Math.floor(Math.random() * 20) + 1;
                currentAnswer = a + b;
                dom.questionBox.innerText = `${a} + ${b}`;
                break;
            case 2: // Subtraction (Positive answers only)
                a = Math.floor(Math.random() * 20) + 10;
                b = Math.floor(Math.random() * a) + 1; 
                currentAnswer = a - b;
                dom.questionBox.innerText = `${a} - ${b}`;
                break;
            case 4: // Multiplication
                a = Math.floor(Math.random() * 10) + 2;
                b = Math.floor(Math.random() * 10) + 2;
                currentAnswer = a * b;
                dom.questionBox.innerText = `${a} × ${b}`;
                break;
            case 5: // Division (Whole numbers only)
                b = Math.floor(Math.random() * 10) + 2;
                currentAnswer = Math.floor(Math.random() * 10) + 2;
                a = currentAnswer * b;
                dom.questionBox.innerText = `${a} ÷ ${b}`;
                break;
        }
    }

    function startTimer() {
        clearInterval(timerInterval);
        const totalTime = baseTime + extraTime;
        timeRemaining = totalTime;
        
        dom.timerBar.style.background = '#2ecc71';
        dom.timerBar.style.width = '100%';

        const updateRate = 50; // Update every 50ms for smooth bar
        timerInterval = setInterval(() => {
            if (isWaitingForNext || isGameOver) return;
            
            timeRemaining -= updateRate;
            const percentage = (timeRemaining / totalTime) * 100;
            dom.timerBar.style.width = percentage + '%';

            if (percentage < 30) dom.timerBar.style.background = '#e74c3c';
            else if (percentage < 60) dom.timerBar.style.background = '#f1c40f';

            if (timeRemaining <= 0) {
                clearInterval(timerInterval);
                dom.timerBar.style.width = '0%';
                handleAnswer(false, "Timeout!");
            }
        }, updateRate);
    }

    function handleAnswer(isCorrect, msg = "") {
        isWaitingForNext = true;
        dom.answerInput.disabled = true;
        dom.nextBtn.style.display = 'block';
        dom.nextBtn.focus();

        if (isCorrect) {
            coins++;
            updateHUD();
            playSound('correct');
            setTimeout(() => playSound('coin'), 200);
            dom.feedback.innerText = "⭐ Correct! +1 🪙";
            dom.feedback.style.color = '#2ecc71';
        } else {
            playSound(msg === "Timeout!" ? 'timeout' : 'wrong');
            dom.feedback.innerText = `❌ ${msg} The answer was ${currentAnswer}`;
            dom.feedback.style.color = '#e74c3c';
        }
    }

    // Input Handling
    dom.answerInput.addEventListener('keypress', (e) => {
        if (e.key === 'Enter' && !isWaitingForNext) {
            const val = parseInt(dom.answerInput.value);
            if (isNaN(val)) return;
            if (val === currentAnswer) {
                handleAnswer(true);
            } else {
                handleAnswer(false, "Wrong!");
            }
        }
    });

    // Handle Enter key on the Next button too
    dom.nextBtn.addEventListener('keypress', (e) => {
        if (e.key === 'Enter') {
            nextQuestion();
        }
    });

    updateHUD();
</script>
</body>
</html>
