<!DOCTYPE html>
<html lang="no">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Mattespill: Auto-Neste Modus</title>
    <style>
        body { font-family: 'Segoe UI', sans-serif; background-color: #1a1a2e; color: white; display: flex; justify-content: center; align-items: center; height: 100vh; margin: 0; }
        .game-container { background: #16213e; padding: 2rem; border-radius: 20px; box-shadow: 0 0 20px rgba(0,255,255,0.2); width: 420px; text-align: center; border: 2px solid #0f3460; }
        .stats { display: flex; justify-content: space-around; margin-bottom: 20px; background: #0f3460; padding: 15px; border-radius: 12px; }
        .stat-item { display: flex; flex-direction: column; font-size: 0.9rem; }
        .stat-value { font-size: 1.2rem; font-weight: bold; color: #4ecca3; }
        .math-box { font-size: 3.5rem; margin: 20px 0; text-shadow: 2px 2px #000; }
        input { font-size: 2rem; width: 120px; padding: 10px; text-align: center; border: 3px solid #4ecca3; border-radius: 10px; background: #1a1a2e; color: white; outline: none; }
        input:focus { box-shadow: 0 0 15px #4ecca3; }
        .btn-row { margin-top: 20px; display: flex; gap: 10px; justify-content: center; }
        button { cursor: pointer; padding: 12px; border: none; border-radius: 8px; font-weight: bold; transition: 0.2s; flex: 1; }
        #skipBtn { background: #e94560; color: white; }
        #skipBtn:disabled { opacity: 0.3; cursor: not-allowed; }
        .wheel-area, .roulette-area { margin-top: 20px; padding: 15px; border: 2px dashed #4ecca3; border-radius: 10px; animation: glow 1.5s infinite alternate; }
        @keyframes glow { from { box-shadow: 0 0 5px #4ecca3; } to { box-shadow: 0 0 20px #4ecca3; } }
        .msg { height: 25px; margin-top: 10px; font-weight: bold; }
    </style>
</head>
<body>

<div class="game-container">
    <div class="stats">
        <div class="stat-item"><span>POENG</span><div id="points" class="stat-value">0</div></div>
        <div class="stat-item"><span>GEMS</span><div id="gems" class="stat-value">0</div></div>
        <div class="stat-item"><span>HOPP</span><div id="skips" class="stat-value">0</div></div>
    </div>

    <div id="level-indicator" style="color: #95a5a6;">Nivå 1 (1-5 gangen)</div>
    <div class="math-box" id="question">Laster...</div>
    
    <input type="number" id="answer" autofocus autocomplete="off">
    
    <div id="message" class="msg"></div>

    <div class="btn-row">
        <button id="skipBtn" onclick="useSkip()">BRUK HOPP (⏭️)</button>
    </div>

    <div id="wheelArea" class="wheel-area" style="display:none;">
        <p>🎡 LYKKEHJUL KLART!</p>
        <button onclick="spinWheel()" style="background:#f1c40f; color:black;">SPINN NÅ</button>
    </div>

    <div id="rouletteArea" class="roulette-area" style="display:none;">
        <p>🎰 GEMS-RULETT</p>
        <button onclick="playRoulette()" style="background:#8e44ad; color:white;">SPILL (10 💎)</button>
    </div>
</div>

<script>
    let points = 0, gems = 0, skips = 0, streak = 0, level = 1, currentAns = 0;

    const inputField = document.getElementById('answer');

    function generateQuestion() {
        let min = (level === 1) ? 1 : 5;
        let max = (level === 1) ? 5 : 10;
        let n1 = Math.floor(Math.random() * (max - min + 1)) + min;
        let n2 = Math.floor(Math.random() * 10) + 1;
        
        currentAns = n1 * n2;
        document.getElementById('question').innerText = `${n1} × ${n2}`;
        inputField.value = '';
        updateUI();
    }

    // Denne funksjonen sjekker svaret automatisk mens du skriver!
    inputField.addEventListener('input', () => {
        let val = parseInt(inputField.value);
        if (val === currentAns) {
            points += 10;
            streak++;
            document.getElementById('message').innerText = "RIKTIG! ✨";
            document.getElementById('message').style.color = "#4ecca3";
            
            if (points >= 100) level = 2;
            
            checkBonuses();
            // Liten pause før neste oppgave så man rekker å se "Riktig!"
            setTimeout(generateQuestion, 400);
        }
    });

    function useSkip() {
        if (skips > 0) {
            skips--;
            document.getElementById('message').innerText = "Hoppet over!";
            generateQuestion();
        }
    }

    function checkBonuses() {
        if (streak >= 5) {
            document.getElementById('wheelArea').style.display = 'block';
        }
        if (gems >= 10) {
            document.getElementById('rouletteArea').style.display = 'block';
        } else {
            document.getElementById('rouletteArea').style.display = 'none';
        }
    }

    function spinWheel() {
        streak = 0;
        document.getElementById('wheelArea').style.display = 'none';
        let win = Math.random();
        if (win > 0.5) {
            let s = Math.floor(Math.random() * 3) + 1;
            skips += s;
            alert("Lykkehjul: Du fikk " + s + " hopp!");
        } else {
            let g = Math.floor(Math.random() * 10) + 1;
            gems += g;
            alert("Lykkehjul: Du fikk " + g + " gems!");
        }
        updateUI();
    }

    function playRoulette() {
        if (gems >= 10) {
            gems -= 10;
            let r = Math.random();
            if (r < 0.1) { // Grønn
                let s = Math.floor(Math.random() * 7) + 6;
                skips += s;
                alert("GRØNN! Ekstrem flaks! Du fikk " + s + " hopp!");
            } else if (r < 0.5) { // Svart
                let g = Math.floor(Math.random() * 15) + 10;
                gems += g;
                alert("SVART! Du vant " + g + " gems!");
            } else { // Rød
                let s = Math.floor(Math.random() * 3) + 3;
                skips += s;
                alert("RØD! Du fikk " + s + " hopp.");
            }
        }
        updateUI();
        checkBonuses();
    }

    function updateUI() {
        document.getElementById('points').innerText = points;
        document.getElementById('gems').innerText = gems;
        document.getElementById('skips').innerText = skips;
        document.getElementById('level-indicator').innerText = `Nivå ${level} (${level === 1 ? '1-5' : '5-10'} gangen)`;
        document.getElementById('skipBtn').disabled = (skips <= 0);
    }

    generateQuestion();
</script>

</body>
</html>
