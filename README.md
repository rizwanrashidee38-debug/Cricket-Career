# Cricket-Career<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CricCareer: Pro Cricket Simulator</title>
    <style>
        body {
            background-color: #1c2e24; /* Dark Cricket Green */
            color: #f5fdf9;
            font-family: 'Courier New', Courier, monospace;
            padding: 10px;
            margin: 0;
            display: flex;
            justify-content: center;
        }
        #game-container {
            max-width: 500px;
            width: 100%;
            border: 3px solid #cca43b; /* Gold Border */
            padding: 15px;
            background-color: #243d30;
            box-shadow: 0px 4px 12px rgba(0,0,0,0.6);
            border-radius: 8px;
            box-sizing: border-box;
        }
        h2, h3 {
            color: #cca43b;
            text-align: center;
            margin: 5px 0;
        }
        .setup-input, .setup-select {
            width: 100%;
            padding: 10px;
            background-color: #14241c;
            border: 1px solid #cca43b;
            color: #f5fdf9;
            font-family: inherit;
            margin-bottom: 15px;
            box-sizing: border-box;
        }
        .stats-box {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 8px;
            background-color: #14241c;
            padding: 10px;
            border-radius: 5px;
            margin-bottom: 12px;
        }
        .stat {
            font-weight: bold;
            font-size: 0.9rem;
            text-align: center;
        }
        #profile-box {
            background-color: #1a3025;
            border: 1px dashed #cca43b;
            padding: 10px;
            border-radius: 5px;
            margin-bottom: 15px;
            font-size: 0.85rem;
            line-height: 1.5;
        }
        #situation-box {
            background-color: #2d4f3e;
            padding: 12px;
            border-radius: 5px;
            border: 1px solid #cca43b;
            text-align: center;
            margin-bottom: 15px;
            min-height: 110px;
            display: flex;
            flex-direction: column;
            justify-content: center;
        }
        .btn-container {
            display: grid;
            grid-template-columns: 1fr;
            gap: 8px;
            margin-bottom: 10px;
        }
        button {
            width: 100%;
            padding: 12px;
            color: white;
            border: none;
            border-radius: 4px;
            font-weight: bold;
            font-size: 0.95rem;
            cursor: pointer;
            font-family: inherit;
        }
        button:active {
            transform: scale(0.98);
        }
        .hidden { display: none !important; }
    </style>
</head>
<body>

    <div id="game-container">
        <div id="setup-screen">
            <h2>PRO CRICKET REGISTRATION</h2>
            <hr style="border: 1px solid #cca43b; margin-bottom: 20px;">
            <label>ENTER YOUR PLAYER NAME:</label>
            <input type="text" id="player-name-input" class="setup-input" placeholder="Your Name" value="Rizwan">
            
            <label>CHOOSE YOUR PLAYER ROLE:</label>
            <select id="player-role-select" class="setup-select">
                <option value="Aggressive Batsman">💥 Aggressive Batsman (High Risk/Reward)</option>
                <option value="Classic Batsman">🏏 Technical Batsman (Consistent/Steady)</option>
                <option value="All-Rounder">🔄 Batting All-Rounder (Balanced Core)</option>
            </select>
            
            <button onclick="startGame()" style="background-color: #cca43b; color: #14241c; font-size: 1.1rem;">SIGN CONTRACT ✍🏽</button>
        </div>

        <div id="main-board" class="hidden">
            <h2>CRICKET CAREER</h2>
            
            <div class="stats-box">
                <div class="stat">Match: <span id="match-num" style="color: #ff9800;">1</span></div>
                <div class="stat">Level: <span id="career-level" style="color: #00e676;">Domestic T20</span></div>
                <div class="stat" style="grid-column: span 2; border-bottom: 1px dashed #2d4f3e; padding-bottom: 5px; margin-bottom: 5px;">
                    Career Runs: <span id="total-runs" style="color: #ffeb3b;">0</span>
                </div>
                <div class="stat" style="grid-column: span 2; background-color: #14241c; padding: 4px; border-radius: 3px;">
                    Net Worth: ₹<span id="player-wealth" style="color: #ffeb3b;">10</span> Lakhs
                </div>
            </div>

            <h3>PLAYER VITAL STATS</h3>
            <div id="profile-box">
                <strong>Player:</strong> <span id="lbl-name" style="color:#cca43b;">---</span><br>
                <strong>Role Profile:</strong> <span id="lbl-role">---</span><br>
                <hr style="border: 0; border-top: 1px solid #2d4f3e; margin: 8px 0;">
                <strong>Form/Confidence:</strong> <span id="lbl-form" style="color: #00e676;">60</span>/100 (Higher form increases shot success)<br>
                <strong>Fitness Levels:</strong> <span id="lbl-fitness" style="color: #3388ff;">90</span>% (Low fitness risks injury game-over)<br>
                <strong>Fan Base:</strong> <span id="lbl-fans" style="color: #ffeb3b;">1,000</span> (Unlocks IPL & National selectors)
            </div>

            <div id="situation-box">
                <h3 id="situation-title" style="color: #cca43b; font-size: 1rem;">LIVE MATCH SITUATION</h3>
                <p id="situation-text">Walking out out to the crease...</p>
            </div>

            <div class="btn-container" id="match-actions">
                <button onclick="playShot('aggressive')" style="background-color: #d32f2f;">PLAY LOFTED ATTACKING SHOT (Risk of Wicket)</button>
                <button onclick="playShot('classic')" style="background-color: #2e7d32;">DRIVE SAFELY FOR CRAWLED RUNS (Low Risk)</button>
                <button onclick="playShot('leave')" style="background-color: #f57c00;">DEFEND/LEAVE BALL (Restores Fitness)</button>
            </div>

            <div class="btn-container hidden" id="turn-actions">
                <button onclick="advanceMatch()" style="background-color: #cca43b; color: #14241c; font-size: 1rem;">COLLECT MATCH FEES & GO TO NEXT FIXTURE ⏳</button>
            </div>
        </div>
    </div>

    <script>
        // Core game variables
        let matchNumber = 1;
        let totalRuns = 0;
        let playerWealth = 10; 
        let careerTier = "Domestic T20";

        // Player traits
        let playerName = "";
        let playerRole = "";
        let playerForm = 60;
        let playerFitness = 90;
        let fanBase = 1000;

        // Situations database
        const matchSituations = [
            {
                text: "Final over. Team needs 12 runs to win off 3 deliveries. A fast bowler charges down the line.",
                aggressive: { runs: 6, form: 10, fitness: -5, fans: 1500, wealth: 2, log: "💥 MASSIVE SIX OVER LONG-ON! The crowd erupts into complete madness!" },
                classic: { runs: 1, form: -5, fitness: -2, fans: -200, wealth: 1, log: "🏏 Saved with a simple single, but the run rate is slipping away. Fans are anxious." },
                leave: { runs: 0, form: -15, fitness: 5, fans: -500, wealth: 1, log: "🛡️ You defended. Dot ball. The stadium is booing your lack of intent." }
            },
            {
                text: "The spin bowlers are putting on immense pressure. The fielders are closed in, choking down single variations.",
                aggressive: { runs: 0, form: -10, fitness: -3, fans: -300, wealth: 1, log: "❌ OUT! You went for a wild sweep shot but got caught right at deep mid-wicket. Short innings." },
                classic: { runs: 4, form: 15, fitness: -5, fans: 800, wealth: 2, log: "🏏 BEAUTIFUL TIMING! A textbook cover-drive splits the inner ring gap cleanly for four!" },
                leave: { runs: 0, form: 5, fitness: 8, fans: 100, wealth: 1, log: "🛡️ Safely padded away. You read the spin variation and conserved body stamina." }
            },
            {
                text: "A devastating batting collapse occurred. Team is down 45/5. You are anchoring the crease under massive pressure.",
                aggressive: { runs: 4, form: 5, fitness: -6, fans: 400, wealth: 1, log: "🚀 Slogged across the line for a bouncy boundary, but almost edged it to slips." },
                classic: { runs: 2, form: 10, fitness: -8, fans: 600, wealth: 1, log: "🏃 Clean placement. You flicked it past square leg and sprinted hard for an intensive double." },
                leave: { runs: 0, form: 10, fitness: 10, fans: 300, wealth: 1, log: "🛡️ Left alone. Your patience is slowly wearing down the bowling attack's stamina profile." }
            }
        ];

        let currentSituation = null;

        function startGame() {
            playerName = document.getElementById("player-name-input").value.trim() || "Rizwan";
            playerRole = document.getElementById("player-role-select").value;

            document.getElementById("lbl-name").innerText = playerName;
            document.getElementById("lbl-role").innerText = playerRole;

            document.getElementById("setup-screen").classList.add("hidden");
            document.getElementById("main-board").classList.remove("hidden");

            updateUI();
            nextSituation();
        }

        function nextSituation() {
            document.getElementById("match-actions").classList.remove("hidden");
            document.getElementById("turn-actions").classList.add("hidden");

            let randomIndex = Math.floor(Math.random() * matchSituations.length);
            currentSituation = matchSituations[randomIndex];
            document.getElementById("situation-text").innerText = currentSituation.text;
        }

        function playShot(type) {
            let result = currentSituation[type];

            // Form math modifier trick (High form tilts luck percentages in favor of aggression)
            let successChance = Math.random() * 100 + (playerForm - 60);
            
            if (type === 'aggressive' && successChance < 40) {
                // Modifies output dynamically into a crash dismissal if luck metrics drop low
                document.getElementById("situation-text").innerText = "❌ HOLED OUT! You went for a massive boundary but mistimed the shot completely. Caught at long-off!";
                playerForm = Math.max(10, playerForm - 15);
                fanBase = Math.max(200, fanBase - 300);
            } else {
                // Apply standard database updates
                totalRuns += result.runs;
                playerForm = Math.max(0, Math.min(100, playerForm + result.form));
                playerFitness = Math.max(0, Math.min(100, playerFitness + result.fitness));
                fanBase = Math.max(0, fanBase + result.fans);
                playerWealth += result.wealth;
                document.getElementById("situation-text").innerText = result.log;
            }

            // Hide action inputs
            document.getElementById("match-actions").classList.add("hidden");
            document.getElementById("turn-actions").classList.remove("hidden");

            updateUI();
            checkCareerSurvival();
        }

        function advanceMatch() {
            matchNumber++;
            
            // Check progression levels based on active fan metrics count thresholds
            if (fanBase >= 15000 && careerTier === "IPL League T20") {
                careerTier = "International National Cap";
                alert("🇮🇳 NATIONAL TEAM CALL-UP! Your incredible performances earned you the official India International cap!");
            } else if (fanBase >= 5000 && careerTier === "Domestic T20") {
                careerTier = "IPL League T20";
                alert("🏏 IPL AUCTION SELECTION! A premier T20 franchise signed you up on a major contract!");
                playerWealth += 50; // Huge contract bonus payout
            }

            updateUI();
            checkCareerSurvival();
            if (playerFitness > 0 && playerForm > 0) {
                nextSituation();
            }
        }

        function checkCareerSurvival() {
            if (playerFitness <= 15) {
                endGame("CAREER ENDING INJURY", "Your body broke down completely under chronic physical match fatigue. You were forced to retire from pro cricket.");
            } else if (playerForm <= 10) {
                endGame("DROPPED FROM SQUAD", "Your batting form hit rock-bottom. Team selectors dropped you indefinitely from all professional cricket leagues.");
            }
        }

        function updateUI() {
            document.getElementById("match-num").innerText = matchNumber;
            document.getElementById("career-level").innerText = careerTier;
            document.getElementById("total-runs").innerText = totalRuns;
            document.getElementById("player-wealth").innerText = playerWealth;

            document.getElementById("lbl-form").innerText = playerForm;
            document.getElementById("lbl-fitness").innerText = playerFitness;
            document.getElementById("lbl-fans").innerText = fanBase.toLocaleString();
        }

        function endGame(title, text) {
            document.getElementById("game-container").innerHTML = `
                <h2 style="color: #d32f2f;">${title}</h2>
                <div style='background-color:#14241c; padding:15px; border-radius:5px; margin-bottom:15px; text-align:center;'>
                    <p>${text}</p>
                    <p style='color:#ffeb3b;'>Total Career Runs: ${totalRuns}</p>
                    <p style='color:#cca43b;'>Final Net Worth: ₹${playerWealth} Lakhs</p>
                </div>
                <button onclick="window.location.reload()" style="background-color: #cca43b; color:#14241c; width:100%;">START NEXT CAREER</button>
            `;
        }
    </script>
</body>
</html>
