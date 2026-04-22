<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>XO Game with Win Animation</title>
  <style>
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
      font-family: Arial, sans-serif;
    }

    body {
      display: flex;
      justify-content: center;
      align-items: center;
      min-height: 100vh;
      background: linear-gradient(135deg, #ff6b6b, #feca57, #48dbfb, #1dd1a1, #5f27cd);
      background-size: 400% 400%;
      animation: gradientMove 10s ease infinite;
    }

    .container {
      text-align: center;
      color: white;
    }

    h1 {
      margin-bottom: 20px;
      font-size: 2.5rem;
    }

    .status {
      margin-bottom: 20px;
      font-size: 1.2rem;
      min-height: 30px;
    }

    .board {
      display: grid;
      grid-template-columns: repeat(3, 100px);
      grid-template-rows: repeat(3, 100px);
      gap: 10px;
      justify-content: center;
      margin-bottom: 20px;
      position: relative;
    }

    .cell {
      width: 100px;
      height: 100px;
      background: rgba(255,255,255,0.1);
      border: 2px solid rgba(255,255,255,0.2);
      border-radius: 15px;
      display: flex;
      justify-content: center;
      align-items: center;
      font-size: 2.5rem;
      font-weight: bold;
      cursor: pointer;
      transition: transform 0.2s ease, background 0.3s ease;
      backdrop-filter: blur(8px);
    }

    .cell:hover {
      transform: scale(1.05);
      background: rgba(255,255,255,0.2);
    }

    .cell.x {
      color: #ffcc00;
      animation: pop 0.3s ease;
    }

    .cell.o {
      color: #00e6ff;
      animation: pop 0.3s ease;
    }

    .winning-cell {
      background: #4caf50 !important;
      color: white !important;
      animation: glow 1s infinite alternate;
    }

    button {
      padding: 12px 24px;
      font-size: 1rem;
      border: none;
      border-radius: 10px;
      background: #ff9800;
      color: white;
      cursor: pointer;
      transition: background 0.3s ease, transform 0.2s ease;
    }

    button:hover {
      background: #e68900;
      transform: scale(1.05);
    }

    @keyframes gradientMove {
      0% {
        background-position: 0% 50%;
      }
      50% {
        background-position: 100% 50%;
      }
      100% {
        background-position: 0% 50%;
      }
    }

    @keyframes pop {
      0% {
        transform: scale(0);
        opacity: 0;
      }
      100% {
        transform: scale(1);
        opacity: 1;
      }
    }

    @keyframes glow {
      from {
        box-shadow: 0 0 10px #4caf50, 0 0 20px #4caf50;
      }
      to {
        box-shadow: 0 0 20px #81c784, 0 0 40px #81c784;
      }
    }

    .winner-banner {
      font-size: 1.5rem;
      margin-bottom: 15px;
      opacity: 0;
      transform: translateY(-20px);
      transition: all 0.5s ease;
    }

    .winner-banner.show {
      opacity: 1;
      transform: translateY(0);
    }

    .winner-screen {
      position: fixed;
      inset: 0;
      background: rgba(0, 0, 0, 0.85);
      display: flex;
      justify-content: center;
      align-items: center;
      flex-direction: column;
      color: white;
      z-index: 1000;
      opacity: 0;
      visibility: hidden;
      transition: all 0.5s ease;
    }

    .winner-screen.show {
      opacity: 1;
      visibility: visible;
    }

    .winner-box {
      background: rgba(255,255,255,0.1);
      padding: 40px;
      border-radius: 20px;
      text-align: center;
      backdrop-filter: blur(10px);
      animation: zoomIn 0.5s ease;
    }

    .winner-box h2 {
      font-size: 2.5rem;
      margin-bottom: 15px;
    }

    .winner-box p {
      font-size: 1.2rem;
      margin-bottom: 20px;
    }

    @keyframes zoomIn {
      from {
        transform: scale(0.5);
        opacity: 0;
      }
      to {
        transform: scale(1);
        opacity: 1;
      }
    }

    .winner-banner.show {
      opacity: 1;
      transform: translateY(0);
    }
      .confetti {
      position: fixed;
      top: -20px;
      width: 12px;
      height: 18px;
      opacity: 0.9;
      animation: confettiFall linear forwards;
      z-index: 1200;
      pointer-events: none;
    }

    @keyframes confettiFall {
      0% {
        transform: translateY(-20px) rotate(0deg);
        opacity: 1;
      }
      100% {
        transform: translateY(110vh) rotate(720deg);
        opacity: 0;
      }
    }

100% {
        transform: translateY(110vh) rotate(360deg);
        opacity: 0;
      }
    }

</style>
</head>
<body>
  <div class="container">
    <h1>Tic Tac Toe</h1>
    <div class="winner-banner" id="winnerBanner"></div>
    <div class="status" id="status">Player X's Turn</div>

    <div class="board" id="board">
      <div class="cell" data-index="0"></div>
      <div class="cell" data-index="1"></div>
      <div class="cell" data-index="2"></div>
      <div class="cell" data-index="3"></div>
      <div class="cell" data-index="4"></div>
      <div class="cell" data-index="5"></div>
      <div class="cell" data-index="6"></div>
      <div class="cell" data-index="7"></div>
      <div class="cell" data-index="8"></div>
    </div>

    <button onclick="restartGame()">Restart Game</button>
  </div>

  <div class="winner-screen" id="winnerScreen">
    <div class="winner-box">
      <h2 id="winnerTitle">Player X Wins!</h2>
      <p>Congratulations on winning the game 🎉</p>
      <button onclick="restartGame()">Play Again</button>
    </div>
  </div>

  <script>
    const cells = document.querySelectorAll('.cell');
    const statusText = document.getElementById('status');
    const winnerBanner = document.getElementById('winnerBanner');

    let currentPlayer = 'X';
    let gameActive = true;
    let board = ['', '', '', '', '', '', '', '', ''];

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

    cells.forEach(cell => {
      cell.addEventListener('click', handleCellClick);
    });

    function handleCellClick(e) {
      const cell = e.target;
      const index = cell.getAttribute('data-index');

      if (board[index] !== '' || !gameActive) return;

      board[index] = currentPlayer;
      cell.textContent = currentPlayer;
      cell.classList.add(currentPlayer.toLowerCase());

      checkWinner();
    }

    function checkWinner() {
      let roundWon = false;

      for (let combo of winningCombinations) {
        const [a, b, c] = combo;

        if (board[a] && board[a] === board[b] && board[a] === board[c]) {
          roundWon = true;

          cells[a].classList.add('winning-cell');
          cells[b].classList.add('winning-cell');
          cells[c].classList.add('winning-cell');

          winnerBanner.textContent = `🎉 Player ${currentPlayer} Wins!`;
          document.getElementById('winnerTitle').textContent = `Player ${currentPlayer} Wins!`;
          document.getElementById('winnerScreen').classList.add('show');
          winnerBanner.classList.add('show');

          createConfettiAnimation();

          statusText.textContent = `Player ${currentPlayer} is the Winner!`;
          gameActive = false;
          break;
        }
      }

      if (!roundWon && !board.includes('')) {
        statusText.textContent = "It's a Draw!";
        winnerBanner.textContent = '🤝 Draw Game!';
        document.getElementById('winnerTitle').textContent = 'Draw Game!';
        document.getElementById('winnerScreen').classList.add('show');
        winnerBanner.classList.add('show');
        gameActive = false;
        return;
      }

      if (!roundWon) {
        currentPlayer = currentPlayer === 'X' ? 'O' : 'X';
        statusText.textContent = `Player ${currentPlayer}'s Turn`;
      }
    }

    function createConfettiAnimation() {
      const colors = ['#ff4d4d', '#ffd633', '#4dff88', '#4da6ff', '#ff66cc', '#ffffff'];

      for (let i = 0; i < 80; i++) {
        const confetti = document.createElement('div');
        confetti.classList.add('confetti');
        confetti.style.left = Math.random() * 100 + 'vw';
        confetti.style.background = colors[Math.floor(Math.random() * colors.length)];
        confetti.style.animationDuration = (Math.random() * 3 + 2) + 's';
        confetti.style.width = (Math.random() * 8 + 6) + 'px';
        confetti.style.height = (Math.random() * 12 + 8) + 'px';
        confetti.style.transform = `rotate(${Math.random() * 360}deg)`;

        document.body.appendChild(confetti);

        setTimeout(() => {
          confetti.remove();
        }, 5000);
      }
    }

    function restartGame() {
      board = ['', '', '', '', '', '', '', '', ''];
      currentPlayer = 'X';
      gameActive = true;
      statusText.textContent = "Player X's Turn";
      winnerBanner.textContent = '';
      winnerBanner.classList.remove('show');
      document.getElementById('winnerScreen').classList.remove('show');

      cells.forEach(cell => {
        cell.textContent = '';
        cell.className = 'cell';
      });
    }
  </script>
</body>
</html>
