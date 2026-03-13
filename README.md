<!DOCTYPE html>
<html lang="no">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Mattespill: Gangemesteren</title>
    <style>
        body { font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; background-color: #f0f2f5; display: flex; justify-content: center; align-items: center; height: 100vh; margin: 0; }
        .game-container { background: white; padding: 2rem; border-radius: 15px; box-shadow: 0 10px 25px rgba(0,0,0,0.1); width: 400px; text-align: center; }
        .stats { display: flex; justify-content: space-around; margin-bottom: 20px; background: #eef2f3; padding: 10px; border-radius: 10px; font-weight: bold; }
        .math-box { font-size: 2.5rem; margin: 20px 0; color: #333; }
        input { font-size: 1.5rem; width: 80px; padding: 5px; text-align: center; border: 2px solid #ddd; border-radius: 5px; }
        button { cursor: pointer; padding: 10px 20px; border: none; border-radius: 5px; background: #007bff; color: white; font-weight: bold; transition: 0.3s; }
        button:hover { background: #0056b3; }
        button:disabled { background: #ccc; }
        .wheel-area, .roulette-area { margin-top: 20px; border-top: 2px solid #eee; padding-top: 15px; display: none; }
        .msg { margin-top: 15px; font-weight: bold; min-height: 20px; }
        .gem-text { color: #9b59b6; }
        .skip-text { color: #27ae60; }
    </style>
</head>
<body>

<div class="game-container">
    <h2>✖️ Gangemesteren</h2>
    
    <div class="stats">
        <div>⭐ Poeng: <span id="points">0</span></div>
        <div>💎 Gems: <span id="gems">0</span></div>
        <div>⏭️ Hopp: <span id="skips">0</span></div>
    </div>

    <div id="level-indicator">Nivå 1 (1-5)</div>
    <div class="math-box" id="question">Laster...</div>
    
    <input type="number" id="answer" placeholder="?">
    <button onclick="checkAnswer()">Svar</button>
    <button id="skipBtn" onclick="useSkip()" style="background:#27ae60;">Bruk hopp</button>

    <div id="message" class="msg"></div>

    <div id="wheelArea" class="wheel-area">
        <p>🎉 5 på rad! Du har vunnet et spinn!</p>
        <button onclick="spinWheel()" style="background:#f1c40f; color:black;">Spinn Lykkehjulet!</button>
    </div>

    <div id="rouletteArea" class="roulette-area">
        <p>💎 Du har nok gems til Rulett!</p>
        <button onclick="playRoulette()" style="background:#8e44ad;">Spill Rulett (10 Gems)</button>
    </div>
</div>

<script>
    let points = 0;
    let gems = 0;
    let skips = 0;
    let streak = 0;
    let currentAns = 0;
    let level = 1;

    function generateQuestion() {
        let min = (level === 1) ? 1 : 5;
        let max = (level === 1) ? 5 : 10;
        let n1 = Math.floor(Math.random() * (max - min + 1)) + min;
        let n2 = Math.floor(Math.random() * 10) + 1;
        
        currentAns = n1 * n2;
        document.getElementById('question').innerText = `${n1} × ${n2}`;
        document.getElementById('answer').value = '';
        document.getElementById('answer').focus();
        updateUI();
    }

    function checkAnswer() {
        let userAns = parseInt(document.getElementById('answer').value);
        let msg = document.getElementById('message');

        if (userAns === currentAns) {
            points += 10;
            streak++;
            msg.innerText = "Riktig! +10 poeng";
            msg.style.color = "green";
            if (points >= 100) level = 2;
        } else {
            streak = 0;
            msg.innerText = "Feil, prøv igjen!";
            msg.style.color = "red";
        }
        
        checkBonuses();
        generateQuestion();
    }

    function useSkip() {
        if (skips > 0) {
            skips--;
            document.getElementById('message').innerText = "Hoppet over oppgaven!";
            generateQuestion();
        }
    }

    function checkBonuses() {
        // Lykkehjul trigges ved 5 på rad
        if (streak >= 5) {
            document.getElementById('wheelArea').style.display = 'block';
        }
        // Rulett trigges hvis man har 10 gems
        if (gems >= 10) {
            document.getElementById('rouletteArea').style.display = 'block';
        } else {
            document.getElementById('rouletteArea').style.display = 'none';
        }
    }

    function spinWheel() {
        streak = 0;
        document.getElementById('wheelArea').style.display = 'none';
        
        let winType = Math.random();
        if (winType > 0.5) {
            let wonSkips = Math.floor(Math.random() * 3) + 1;
            skips += wonSkips;
            alert(`Lykkehjulet stoppet på: ${wonSkips} Hopp-over!`);
        } else {
            let wonGems = Math.floor(Math.random() * 10) + 1;
            gems += wonGems;
            alert(`Lykkehjulet stoppet på: ${wonGems} Gems!`);
        }
        updateUI();
    }

    function playRoulette() {
        if (gems >= 10) {
            gems -= 10;
            let roll = Math.random();
            if (roll < 0.1) { // Grønn (10% sjanse)
                let s = Math.floor(Math.random() * 7) + 6;
                skips += s;
                alert(`GRØNN! Du vant ${s} hopp-over!`);
            } else if (roll < 0.5) { // Svart (40% sjanse)
                let g = Math.floor(Math.random() * 20) + 10;
                gems += g;
                alert(`SVART! Du vant ${g} nye gems!`);
            } else { // Rød (50% sjanse)
                let s = Math.floor(Math.random() * 3) + 3;
                skips += s;
                alert(`RØD! Du vant ${s} hopp-over.`);
            }
        }
        updateUI();
        checkBonuses();
    }

    function updateUI() {
        document.getElementById('points').innerText = points;
        document.getElementById('gems').innerText = gems;
        document.getElementById('skips').innerText = skips;
        document.getElementById('level-indicator').innerText = `Nivå ${level} (${level === 1 ? '1-5' : '5-10'})`;
        document.getElementById('skipBtn').disabled = skips <= 0;
    }

    // Start spillet
    generateQuestion();
</script>

</body>
</html>
