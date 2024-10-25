<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>لعبة إكس و أو</title>
    <style>
        /* التنسيق العام والخلفية */
        body {
            font-family: 'Arial', sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100vh;
            margin: 0;
            background: linear-gradient(to right, #4e54c8, #8f94fb);
            color: #ffffff;
        }

        /* تنسيق النصوص والعناوين */
        h1, h2 {
            color: #ffffff;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.2);
            margin: 20px 0;
        }

        /* تنسيق شاشات اللعبة */
        #start-screen, #game-screen {
            display: none;
            flex-direction: column;
            align-items: center;
            padding: 20px;
            border-radius: 12px;
            box-shadow: 0 6px 12px rgba(0, 0, 0, 0.3);
            background-color: rgba(255, 255, 255, 0.1);
            width: 90%;
            max-width: 400px;
        }
        #start-screen {
            display: flex;
        }

        /* تنسيق إدخالات النص */
        input[type="text"] {
            padding: 10px;
            margin: 10px 0;
            font-size: 1em;
            width: 80%;
            border-radius: 8px;
            border: 1px solid #ddd;
            outline: none;
            background-color: #ffffff;
            color: #333;
        }

        /* تنسيق الأزرار */
        button {
            padding: 10px 20px;
            font-size: 1em;
            cursor: pointer;
            border: none;
            border-radius: 8px;
            background-color: #6a6ded;
            color: white;
            transition: background-color 0.3s ease, transform 0.2s ease;
            margin: 5px;
        }

        button:hover {
            background-color: #5859d6;
            transform: scale(1.05);
        }

        /* تنسيق اللوحة */
        .board {
            display: grid;
            grid-template-columns: repeat(3, 100px);
            gap: 10px;
            margin-top: 20px;
        }
        .cell {
            width: 100px;
            height: 100px;
            font-size: 2.5em;
            text-align: center;
            line-height: 100px;
            cursor: pointer;
            background-color: rgba(255, 255, 255, 0.8);
            border: 2px solid #333;
            transition: background-color 0.2s ease, transform 0.2s ease;
            border-radius: 8px;
        }
        .cell.X { color: #4e54c8; }
        .cell.O { color: #ff4f4f; }

        .cell:hover {
            background-color: #c0c4ff;
            transform: scale(1.05);
        }

        /* زر العودة */
        #restart-button {
            margin-top: 20px;
            font-size: 1em;
            background-color: #ff4f4f;
        }

        #restart-button:hover {
            background-color: #ff2f2f;
        }

        /* الرسائل */
        #status-message {
            margin-top: 20px;
            font-weight: bold;
            color: #ff4747;
            font-size: 1.2em;
            text-shadow: 1px 1px 4px rgba(0, 0, 0, 0.3);
        }

        /* تأثير الفوز */
        @keyframes winner-animation {
            0%, 100% { transform: scale(1); }
            50% { transform: scale(1.3); }
        }
        .winner {
            animation: winner-animation 0.5s ease-in-out infinite;
            color: #ffd700; /* اللون الذهبي للفوز */
        }
    </style>
</head>
<body>

    <!-- شاشة البداية -->
    <div id="start-screen">
        <h1>أهلاً بك في لعبة إكس و أو</h1>
        <button onclick="showLocalMultiplayer()">اللعب مع صديق</button>
        <button onclick="alert('ميزة اللعب أونلاين قيد التطوير')">اللعب أونلاين</button>
        <div id="name-inputs" class="hidden">
            <input type="text" id="player1-name" placeholder="اسم اللاعب الأول">
            <input type="text" id="player2-name" placeholder="اسم اللاعب الثاني">
            <button onclick="startGame()">ابدأ اللعب</button>
        </div>
    </div>

    <!-- شاشة اللعبة -->
    <div id="game-screen" class="hidden">
        <h2 id="welcome-message"></h2>
        <div class="board" id="board">
            <div class="cell" onclick="makeMove(0)"></div>
            <div class="cell" onclick="makeMove(1)"></div>
            <div class="cell" onclick="makeMove(2)"></div>
            <div class="cell" onclick="makeMove(3)"></div>
            <div class="cell" onclick="makeMove(4)"></div>
            <div class="cell" onclick="makeMove(5)"></div>
            <div class="cell" onclick="makeMove(6)"></div>
            <div class="cell" onclick="makeMove(7)"></div>
            <div class="cell" onclick="makeMove(8)"></div>
        </div>
        <p id="status-message"></p>
        <button id="restart-button" class="hidden" onclick="restartGame()">العودة إلى القائمة الرئيسية</button>
    </div>

    <script>
        let board = ["", "", "", "", "", "", "", "", ""];
        let currentPlayer = "X";
        let gameActive = true;
        let player1 = "";
        let player2 = "";

        function showLocalMultiplayer() {
            document.getElementById("name-inputs").classList.remove("hidden");
        }

        function startGame() {
            player1 = document.getElementById("player1-name").value.trim();
            player2 = document.getElementById("player2-name").value.trim();
            if (player1 === "" || player2 === "") {
                alert("يرجى إدخال أسماء كلا اللاعبين.");
                return;
            }
            document.getElementById("welcome-message").textContent = `مرحباً، ${player1} و ${player2}!`;
            document.getElementById("start-screen").style.display = "none";
            document.getElementById("game-screen").style.display = "flex";
        }

        function makeMove(index) {
            if (board[index] !== "" || !gameActive) {
                return;
            }
            board[index] = currentPlayer;
            const cell = document.getElementsByClassName("cell")[index];
            cell.textContent = currentPlayer;
            cell.classList.add(currentPlayer);
            checkWinner();
            currentPlayer = currentPlayer === "X" ? "O" : "X";
        }

        function checkWinner() {
            const winningCombinations = [
                [0, 1, 2],
                [3, 4, 5],
                [6, 7, 8],
                [0, 3, 6],
                [1, 4, 7],
                [2, 5, 8],
                [0, 4, 8],
                [2, 4, 6]
            ];
            for (let combination of winningCombinations) {
                const [a, b, c] = combination;
                if (board[a] && board[a] === board[b] && board[a] === board[c]) {
                    const winner = currentPlayer === "X" ? player1 : player2;
                    document.getElementById("status-message").textContent = `اللاعب ${winner} فاز!`;
                    gameActive = false;
                    document.getElementById("restart-button").classList.remove("hidden");
                    document.getElementsByClassName("cell")[a].classList.add("winner");
                    document.getElementsByClassName("cell")[b].classList.add("winner");
                    document.getElementsByClassName("cell")[c].classList.add("winner");
                    return;
                }
            }
            if (!board.includes("")) {
                document.getElementById("status-message").textContent = "تعادل!";
                gameActive = false;
                document.getElementById("restart-button").classList.remove("hidden");
            }
        }

        function restartGame() {
            board = ["", "", "", "", "", "", "", "", ""];
            currentPlayer = "X";
            gameActive = true;
            document.getElementById("status-message").textContent = "";
            document.getElementById("restart-button").classList.add("hidden");
            Array.from(document.getElementsByClassName("cell")).forEach(cell => {
                cell.textContent = "";
                cell.className = "cell";
            });
            document.getElementById("game-screen").style.display = "none";
            document.getElementById("start-screen").style.display = "flex";
            document.getElementById("name-inputs").classList.add("hidden");
        }
    </script>

</body>
</html>
