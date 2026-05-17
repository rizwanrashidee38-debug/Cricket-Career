                                                       <!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Pro Cricket Career Sim</title>
    <style>
        :root { --primary: #1a2a6c; --secondary: #b21f1f; --accent: #fdbb2d; }
        body { font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; background: #222; color: white; display: flex; justify-content: center; padding: 20px; }
        .game-card { width: 100%; max-width: 700px; background: #333; padding: 25px; border-radius: 15px; border: 2px solid var(--accent); box-shadow: 0 10px 30px rgba(0,0,0,0.5); }
        .hidden { display: none; }
        .stats-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 10px; margin: 20px 0; background: #444; padding: 15px; border-radius: 10px; }
        .tile-grid { display: grid; grid-template-columns: repeat(2, 1fr); gap: 15px; margin-top: 20px; }
        .tile { height: 100px; background: linear-gradient(145deg, var(--primary), #0d1535); border-radius: 10px; cursor: pointer; display: flex; align-items: center; justify-content: center; font-size: 2rem; transition: 0.2s; border: 1px solid #555; }
        .tile:hover { transform: scale(1.02); border-color: var(--accent); }
        .log { background: #111; height: 100px; overflow-y: auto; padding: 10px; margin-top: 20px; border-radius: 5px; font-size: 0.9rem; text-align: left; }
        input, select, button { padding: 12px; margin: 5px; border-radius: 5px; border: none; }
        button { background: var(--accent); color: #000; font-weight: bold; cursor: pointer; }
        .over-display { font-size: 1.2rem; color: var(--accent); font-weight: bold; }
    </style>
</head>
<body>

<div class="game-card">
    <h1>🏏 Cricket Career Pro</h1>
    
    <!-- Setup -->
    <div id="setup">
        <input type="text" id="pName" placeholder="Player Name">
        <select id="pNation">
            <option value="India">India</option>
            <option value="Australia">Australia</option>
            <option value="England">England</option>
            <option value="Pakistan">Pakistan</option>
        </select>
        <select id="pRole">
            <option value="Batsman">Specialist Batsman</option>
            <option value="Bowler">Specialist Bowler</option>
            <option value="All-Rounder">All-Rounder</option>
        </select>
        <br><br>
        <button onclick="initGame()">Start Professional Career</button>
    </div>

    <!-- Match Engine -->
    <div id="gameplay" class="hidden">
        <div class="stats-grid">
            <div>Div: <b id="ui-div">3</b></div>
            <div>Matches: <b id="ui-matches">0</b>/10</div>
            <div>Runs: <b id="ui-runs">0</b></div>
            <div>Wickets: <b id="ui-wickets">0</b></div>
        </div>
        
        <div class="over-display">
            Match <span id="ui-match-num">1</span> | 
            Overs: <span id="ui-overs">0.0</span>/20
        </div>

        <div class="tile-grid">
            <div class="tile" onclick="handleInput()">?</div>
            <div class="tile" onclick="handleInput()">?</div>
            <div class="tile" onclick="handleInput()">?</div>
            <div class="tile" onclick="handleInput()">?</div>
        </div>

        <div class="log" id="game-log">Welcome to the crease! Choose a tile to play.</div>
    </div>
</div>

<script>
    let player = {
        name: "", nation: "", role: "",
        div: 3, seasonRuns: 0, seasonWickets: 0, matchesPlayed: 0,
        currentBalls: 0, isOut: false
    };

    const outcomes = [0, 1, 2, 4, 6, "OUT"];

    function initGame() {
        player.name = document.getElementById('pName').value || "Player";
        player.nation = document.getElementById('pNation').value;
        player.role = document.getElementById('pRole').value;
        
        document.getElementById('setup').classList.add('hidden');
        document.getElementById('gameplay').classList.remove('hidden');
        addLog(`Career started in Division 3 for ${player.nation}. Target: 10 matches.`);
    }

    function handleInput() {
        if(player.isOut || player.currentBalls >= 120) return;

        let res = outcomes[Math.floor(Math.random() * outcomes.length)];
        processBall(res, true);
    }

    function processBall(res, isPlayer) {
        player.currentBalls++;
        
        if(res === "OUT") {
            if(isPlayer) {
                addLog("<span style='color:red'>OUT! Your innings has ended.</span>");
                player.isOut = true;
                simulateRemainingMatch();
            } else {
                player.seasonWickets++;
            }
        } else {
            if(isPlayer) player.seasonRuns += res;
            addLog(isPlayer ? `You scored ${res} runs.` : `Bowled a ball, conceded ${res} runs.`);
        }

        updateUI();

        if(!player.isOut && player.currentBalls >= 120) {
            addLog("End of 20 overs!");
            finishMatch();
        }
    }

    function simulateRemainingMatch() {
        addLog("Simulating the rest of the match...");
        // Simulation logic: If you're a bowler/all-rounder, you still get to bowl your 4 overs
        if(player.role !== "Batsman") {
            let ballsLeftToBowl = 24; // Standard 4-over spell
            for(let i=0; i<ballsLeftToBowl; i++) {
                let res = outcomes[Math.floor(Math.random() * outcomes.length)];
                if(res === "OUT") player.seasonWickets++;
            }
        }
        setTimeout(finishMatch, 1500);
    }

    function finishMatch() {
        player.matchesPlayed++;
        player.currentBalls = 0;
        player.isOut = false;
        
        alert(`Match ${player.matchesPlayed} Complete!`);
        
        if(player.matchesPlayed >= 10) {
            checkPromotion();
        }
        updateUI();
    }

    function checkPromotion() {
        let success = false;
        if(player.role === "Batsman" && player.seasonRuns >= 300) success = true;
        if(player.role === "Bowler" && player.seasonWickets >= 15) success = true;
        if(player.role === "All-Rounder" && player.seasonRuns >= 200 && player.seasonWickets >= 10) success = true;

        if(success) {
            player.div = Math.max(1, player.div - 1);
            alert(`PROMOTED! You are now in Division ${player.div}`);
        } else {
            alert("Season Failed. You didn't meet the targets. Replaying Division.");
        }
        
        player.matchesPlayed = 0;
        player.seasonRuns = 0;
        player.seasonWickets = 0;
    }

    function addLog(msg) {
        const log = document.getElementById('game-log');
        log.innerHTML = `> ${msg}<br>` + log.innerHTML;
    }

    function updateUI() {
        document.getElementById('ui-div').innerText = player.div;
        document.getElementById('ui-matches').innerText = player.matchesPlayed;
        document.getElementById('ui-runs').innerText = player.seasonRuns;
        document.getElementById('ui-wickets').innerText = player.seasonWickets;
        document.getElementById('ui-match-num').innerText = player.matchesPlayed + 1;
        
        let overs = Math.floor(player.currentBalls / 6);
        let balls = player.currentBalls % 6;
        document.getElementById('ui-overs').innerText = `${overs}.${balls}`;
    }
</script>
</body>
</html>
           
