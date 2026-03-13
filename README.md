<!DOCTYPE html>
<html lang="no">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Gange-Galskap: Ultimativ Utgave</title>
    <style>
        :root {
            --bg: #0f172a;
            --card: #1e293b;
            --accent: #38bdf8;
            --success: #4ad66d;
            --gold: #fbbf24;
            --purple: #a855f7;
            --danger: #ef4444;
        }

        body {
            font-family: 'Segoe UI', Roboto, sans-serif;
            background-color: var(--bg);
            color: white;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
            overflow: hidden;
        }

        .game-container {
            background: var(--card);
            padding: 2rem;
            border-radius: 2rem;
            box-shadow: 0 20px 50px rgba(0,0,0,0.5), inset 0 0 20px rgba(56, 189, 248, 0.1);
            width: 100%;
            max-width: 400px;
            text-align: center;
            border: 2px solid #334155;
            position: relative;
        }

        .stats-grid {
            display: grid;
            grid-template-columns: 1fr 1fr 1fr;
            gap: 10px;
            margin-bottom: 20px;
        }

        .stat-box {
            background: #0f172a;
            padding: 10px;
            border-radius: 1rem;
            border: 1px solid #334155;
        }

        .stat-label { font-size: 0.7rem; color: #94a3b8; text-transform: uppercase; display: block; }
        .stat-value { font-size: 1.2rem; font-weight: bold; color: var(--accent); }
        #pts { color: var(--gold); }
        #gms { color: var(--purple); }

        .level-badge {
            display: inline-block;
            padding: 4px 12px;
            border-radius: 20px;
            background: #334155;
            font-size: 0.8rem;
            font-weight: bold;
            margin-bottom: 10px;
            color: var(--accent);
        }

        .math-area {
            font-size: 4rem;
            margin: 1rem 0;
            font-weight: 800;
            text-shadow: 0 4px 10px rgba(0,0,0,0.3);
        }

        input {
            background: #0f172a;
            border: 3px solid #334155;
            color: white;
            font-size: 2.5rem;
            width: 150px;
            text-align: center;
            padding: 10px;
            border-radius: 1rem;
            outline: none;
            transition: border-color 0.3s;
        }

        input:focus { border-color: var(--accent); }

        .btn-group {
            display: flex;
            flex-direction: column;
            gap: 10px;
            margin-top: 25px;
        }

        button {
            border: none;
            padding: 14px;
            border-radius: 12px;
            font-weight: bold;
            font-size: 1rem;
            cursor: pointer;
            transition: all 0.2s;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        button:active { transform: scale(0.98); }
        button:disabled { opacity: 0.3; cursor: not-allowed; filter: grayscale(1); }

        .btn-wheel { background: var(--gold); color: #451a03; }
        .btn-roulette { background: var(--purple); color: white; }
        .btn-skip { background: var(--danger); color: white; }

        .feedback {
            height: 30px;
            margin-top: 15px;
            font-weight: bold;
            font-size: 1.2rem;
            color: var(--success);
        }

        /* Enkle animasjoner */
        @keyframes pop {
            0% { transform: scale(1); }
            50% { transform: scale(1.1); }
            100% { transform: scale(1); }
        }
        .correct-anim { animation: pop 0.3s ease; }
    </style>
</head>
<body>

<div class="game-container" id="gameCard">
    <div class="level-badge" id="lvlDisp">NIVÅ 1 (1-5)</div>
    
    <div class="stats-grid">
        <div class="stat-box">
            <span class="stat-label">Poeng</span>
            <span class="stat-value" id="pts">0</span>
        </div>
        <div class="stat-box">
            <span class="stat-label">Gems</span>
            <span class="stat-value" id="gms">0</span>
        </div>
        <div class="stat-box">
            <span class="stat-label">Hopp</span>
            <span class="stat-value" id="skp">0</span>
        </div>
    </div>

    <div class="math-area" id="que">? × ?</div>
    <input type="number" id="ans" autofocus placeholder="Svar..." autocomplete="off">
    <div id="feedback" class="feedback"></div>

    <div class="btn-group">
        <button id="wheelBtn" class="btn-wheel" onclick="spinWheel()">
            <span>🎡 Lykkehjul</span> <span>50p</span>
        </button>
        <button id="roulBtn" class="btn-roulette" onclick="playRoulette()">
            <span>🎰 Gems-Rulett</span> <span>10💎</span>
        </button>
        <button id="skipBtn" class="btn-skip" onclick="useSkip()">
            <span>⏭️ Bruk Hopp-over</span> <span id="skpCount">0</span>
        </button>
    </div>
</div>

<script>
    let points = 0, gems = 0, skips = 0, level = 1, currentAns = 0;

    const queEl = document.getElementById('que');
    const ansInp = document.getElementById('ans');
    const feedEl = document.getElementById('feedback');
    const gameCard = document.getElementById('gameCard');

    function generateQuestion() {
        let min = (level === 1) ? 1 : 5;
        let max = (level === 1) ? 5 : 10;
        let n1 = Math.floor(Math.random() * (max - min + 1)) + min;
        let n2 = Math.floor(Math.random() * 10) + 1;
        
        currentAns = n1 * n2;
        queEl.innerText = `${n1} × ${n2}`;
        ansInp.value = '';
        updateUI();
    }

    ansInp.addEventListener('input', () => {
        if (parseInt(ansInp.value) === currentAns) {
            points += 10;
            feedEl.innerText = "RIKTIG! ✨";
            gameCard.classList.add('correct-anim');
            
            if (points >= 150 && level === 1) {
                level = 2;
                alert("NIVÅ 2! Nå blir det vanskeligere (5-10 gangen)!");
            }

            setTimeout(() => {
                feedEl.innerText = "";
                gameCard.classList.remove('correct-anim');
                generateQuestion();
            }, 500);
        }
    });

    function useSkip() {
        if (skips > 0) {
            skips--;
            feedEl.innerText = "Hoppet over! 💨";
            setTimeout(() => { feedEl.innerText = ""; generateQuestion(); }, 400);
        }
    }

    function spinWheel() {
        if (points >= 50) {
            points -= 50;
            let roll = Math.random();
            if (roll > 0.5) {
                let win = Math.floor(Math.random() * 3) + 1;
                skips += win;
                alert(`🎡 Lykkehjulet stoppet på ${win} hopp-over!`);
            } else {
                let win = Math.floor(Math.random() * 10) + 1;
                gems += win;
                alert(`🎡 Lykkehjulet stoppet på ${win} gems!`);
            }
            updateUI();
        }
    }

    function playRoulette() {
        if (gems >= 10) {
            gems -= 10;
            let roll = Math.random();
            if (roll < 0.1) { // Grønn
                let win = Math.floor(Math.random() * 7) + 6;
                skips += win;
                alert(`🟩 GRØNN! JACKPOT! Du vant ${win} hopp!`);
            } else if (roll < 0.5) { // Svart
                let win = Math.floor(Math.random() * 15) + 10;
                gems += win;
                alert(`⬛ SVART! Du vant ${win} nye gems!`);
            } else { // Rød
                let win = Math.floor(Math.random() * 3) + 3;
                skips += win;
                alert(`🟥 RØD! Du vant ${win} hopp.`);
            }
            updateUI();
        }
    }

    function updateUI() {
        document.getElementById('pts').innerText = points;
        document.getElementById('gms').innerText = gems;
        document.getElementById('skp').innerText = skips;
        document.getElementById('skpCount').innerText = skips;
        document.getElementById('lvlDisp').innerText = `NIVÅ ${level} (${level === 1 ? '1-5' : '5-10'} GANGEN)`;
        
        document.getElementById('wheelBtn').disabled = points < 50;
        document.getElementById('roulBtn').disabled = gems < 10;
        document.getElementById('skipBtn').disabled = skips <= 0;
    }

    // Start
    generateQuestion();
</script>

</body>
</html>
