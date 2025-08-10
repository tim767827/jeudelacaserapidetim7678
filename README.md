# jeudelacaserapidetim7678
jeu de la case rapide
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Jeu du Carré Rapide - Version Animée</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f4f4f4;
            display: flex;
            flex-direction: column;
            align-items: center;
            padding: 20px;
        }
        h1 {
            color: #333;
        }
        #topBar {
            display: flex;
            justify-content: space-between;
            width: 400px;
            margin-bottom: 10px;
            font-size: 18px;
        }
        #gameArea {
            width: 400px;
            height: 400px;
            border: 2px solid #333;
            background-color: white;
            position: relative;
            overflow: hidden;
        }
        .carre {
            position: absolute;
            width: 50px;
            height: 50px;
            background-color: red;
            cursor: pointer;
            border-radius: 8px;
            transition: all 0.2s ease;
        }
        table {
            border-collapse: collapse;
            width: 350px;
            margin-top: 20px;
            background: white;
            box-shadow: 0 2px 8px rgba(0,0,0,0.1);
        }
        th, td {
            padding: 10px;
            text-align: center;
            border-bottom: 1px solid #ccc;
        }
        th {
            background-color: #007BFF;
            color: white;
        }
        #rejouerBtn {
            margin-top: 15px;
            padding: 10px 15px;
            border: none;
            background-color: #28a745;
            color: white;
            cursor: pointer;
            border-radius: 5px;
            display: none;
        }
        #rejouerBtn:hover {
            background-color: #218838;
        }
        .fadeIn {
            animation: fadeIn 0.5s ease forwards;
        }
        @keyframes fadeIn {
            from {opacity: 0;}
            to {opacity: 1;}
        }
        button.deleteBtn {
            background: red;
            color: white;
            border: none;
            padding: 5px;
            cursor: pointer;
            border-radius: 4px;
        }
        button.deleteBtn:hover {
            background: darkred;
        }
    </style>
</head>
<body>

    <h1>Jeu du Carré Rapide</h1>

    <div id="topBar">
        <span>Temps : <span id="time">10</span>s</span>
        <span>Score : <span id="score">0</span></span>
    </div>

    <div id="gameArea"></div>

    <button id="rejouerBtn" onclick="startGame()">Rejouer</button>

    <h2>Tableau des Scores</h2>
    <table id="scoreTable">
        <thead>
            <tr>
                <th>Joueur</th>
                <th>Score</th>
                <th>Action</th>
            </tr>
        </thead>
        <tbody>
            <!-- Scores affichés ici -->
        </tbody>
    </table>

    <script>
        const gameArea = document.getElementById("gameArea");
        const scoreDisplay = document.getElementById("score");
        const timeDisplay = document.getElementById("time");
        const rejouerBtn = document.getElementById("rejouerBtn");
        const scores = [];

        let score = 0;
        let timeLeft = 10;
        let timer;
        let carre;

        function startGame() {
            // Reset
            score = 0;
            timeLeft = 10;
            scoreDisplay.textContent = score;
            timeDisplay.textContent = timeLeft;
            rejouerBtn.style.display = "none";
            if (carre) carre.remove();
            clearInterval(timer);

            // Lancer timer
            timer = setInterval(() => {
                timeLeft--;
                timeDisplay.textContent = timeLeft;
                if (timeLeft <= 0) {
                    clearInterval(timer);
                    timer = null;
                    endGame();
                }
            }, 1000);

            spawnCarre();
        }

        function spawnCarre() {
            if (carre) carre.remove();

            carre = document.createElement("div");
            carre.classList.add("carre", "fadeIn");

            const maxX = gameArea.clientWidth - 50;
            const maxY = gameArea.clientHeight - 50;
            carre.style.left = Math.random() * maxX + "px";
            carre.style.top = Math.random() * maxY + "px";

            carre.onclick = () => {
                score++;
                scoreDisplay.textContent = score;
                spawnCarre();
            };

            gameArea.appendChild(carre);
        }

        function endGame() {
            if (carre) carre.remove();
            const nom = prompt("Partie terminée ! Entrez votre nom :");
            if (nom) {
                ajouterScore(nom, score);
            }
            rejouerBtn.style.display = "inline-block";
        }

        function ajouterScore(joueur, score) {
            scores.push({ joueur, score });
            scores.sort((a, b) => b.score - a.score);

            // Sauvegarde dans localStorage
            localStorage.setItem("scores", JSON.stringify(scores));

            afficherScores();
        }

        function afficherScores() {
            const tbody = document.querySelector("#scoreTable tbody");
            tbody.innerHTML = "";
            scores.forEach((s, index) => {
                const row = document.createElement("tr");
                row.classList.add("fadeIn");
                row.innerHTML = `
                    <td>${s.joueur}</td>
                    <td>${s.score}</td>
                    <td><button class="deleteBtn" onclick="supprimerScore(${index})">❌</button></td>
                `;
                tbody.appendChild(row);
            });
        }

        function supprimerScore(index) {
            scores.splice(index, 1);
            localStorage.setItem("scores", JSON.stringify(scores)); // Mise à jour du stockage
            afficherScores();
        }

        // Charger les scores enregistrés au lancement
        window.onload = () => {
            const sauvegarde = localStorage.getItem("scores");
            if (sauvegarde) {
                scores.push(...JSON.parse(sauvegarde));
                afficherScores();
            }
        };

        // Lance la partie au premier clic dans la zone
        gameArea.addEventListener("click", () => {
            if (!timer) startGame();
        });
    </script>

</body>
</html>