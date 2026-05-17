                                                          <!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Cricket Career Pro</title>
    <style>
        body { font-family: 'Segoe UI', sans-serif; background: #f0f2f5; text-align: center; }
        .container { max-width: 600px; margin: 50px auto; background: white; padding: 20px; border-radius: 15px; box-shadow: 0 4px 15px rgba(0,0,0,0.1); }
        .hidden { display: none; }
        .grid { display: grid; grid-template-columns: repeat(2, 1fr); gap: 15px; margin-top: 20px; }
        .tile { padding: 40px; background: #2c3e50; color: white; cursor: pointer; border-radius: 10px; font-weight: bold; transition: 0.3s; }
        .tile:hover { background: #34495e; transform: scale(1.05); }
        .stats-bar { display: flex; justify-content: space-around; background: #eee; padding: 10px; border-radius: 5px; margin-bottom: 20px; }
        button { padding: 10px 20px; cursor: pointer; background: #27ae60; color: white; border: none; border-radius: 5px; }
    </style>
</head>
<body>

<div class="container">
    <h1>Cricket Career Sim</h1>

    <!-- Setup Screen -->
    <div id="setup-screen">
        <input type="text" id="player-name" placeholder="Enter Name"><br><br>
        <select id="player-role">
            <option value="Batsman">Batsman</option>
            <option value="Bowler">Bowler</option>
            <option value="All-Rounder">All-Rounder</option>
        </select><br><br>
        <button onclick="startGame()">Start Career</button>
    </div>

    <!-- Gameplay Screen -->
    <div id="game-screen" class="hidden">
        <div class="stats-bar">
            <div>Division: <span id="stat-div">3</span></div>
            <div>Matches: <span id="stat-matches">0</span>/10</div>
            <div>Runs: <span id="stat-runs">0</span></div>
            <div>Wickets: <span id="stat-wickets">0</span></div>
        </div>
        <h3 id="feedback">Select a tile to play your move!</h3>
        <div class="grid">
            <div class="tile" onclick="playTurn()">?</div>
            <div class="tile" onclick="playTurn()">?</div>
            <div class="tile" onclick="playTurn()">?</div>
            <div class="tile" onclick="playTurn()">?</div>
        </div>
    </div>
</div>

<script>
    let gameState = {
        name: "", role: "", div: 3, 
        runs: 0, wickets: 0, matches: 0, 
        currentMatchBalls: 0, out: false
    };

    const outcomes = ["0", "1", "2", "4", "6", "Wicket"];

    function startGame() {
        gameState.name = document.getElementById('player-name').value;
        gameState.role = document.getElementById('player-role').value;
        if(!gameState.name) return alert("Enter a name!");

        document.getElementById('setup-screen').classList.add('hidden');
        document.getElementById('game-screen').classList.remove('hidden');
        updateUI();
    }

    function playTurn() {
        if (gameState.matches >= 10) return checkPromotion();

        const result = outcomes[Math.floor(Math.random() * outcomes.length)];
        
        if (result === "Wicket") {
            gameState.wickets += (gameState.role === "Bowler" || gameState.role === "All-Rounder") ? 1 : 0;
            document.getElementById('feedback').innerText = "WICKET! Great delivery!";
            if(gameState.role === "Batsman") endInnings();
        } else {
            gameState.runs += parseInt(result);
            document.getElementById('feedback').innerText = `You scored ${result} run(s)!`;
        }

        // Logic: 6 balls per match for simplicity
        gameState.currentMatchBalls++;
        if(gameState.currentMatchBalls >= 6) {
            endInnings();
        }
        updateUI();
    }

    function endInnings() {
        gameState.matches++;
        gameState.currentMatchBalls = 0;
        alert(`Match ${gameState.matches} finished!`);
        if(gameState.matches >= 10) checkPromotion();
    }

    function updateUI() {
        document.getElementById('stat-div').innerText = gameState.div;
        document.getElementById('stat-matches').innerText = gameState.matches;
        document.getElementById('stat-runs').innerText = gameState.runs;
        document.getElementById('stat-wickets').innerText = gameState.wickets;
    }

    function checkPromotion() {
        let promoted = false;
        const { role, runs, wickets } = gameState;

        if (role === "Batsman" && runs >= 300) promoted = true;
        if (role === "Bowler" && wickets >= 15) promoted = true;
        if (role === "All-Rounder" && runs >= 200 && wickets >= 10) promoted = true;

        if (promoted && gameState.div > 1) {
            gameState.div--;
            alert(`CONGRATULATIONS! You've been promoted to Division ${gameState.div}`);
            resetSeason();
        } else if (gameState.matches >= 10) {
            alert("Season over. You didn't meet the targets. Try again!");
            resetSeason();
        }
    }

    function resetSeason() {
        gameState.matches = 0;
        gameState.runs = 0;
        gameState.wickets = 0;
        updateUI();
    }
</script>
</body>
</html>
  
