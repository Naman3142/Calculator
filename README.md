<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>📟 Calculator – Made by Naman</title>
  <style>
    * { box-sizing: border-box; }
    body {
      font-family: 'Segoe UI', sans-serif;
      background: linear-gradient(145deg, #0f0f0f, #1c1c1c);
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      min-height: 100vh;
      margin: 0;
      color: white;
    }
    h1 {
      font-size: 2.5rem;
      background: linear-gradient(to right, #00f2ff, #ff00f2);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      margin-bottom: 20px;
    }
    .calculator {
      background: #1a1a1a;
      padding: 25px;
      border-radius: 20px;
      box-shadow: 0 8px 30px rgba(0, 255, 255, 0.15);
      max-width: 400px;
      width: 100%;
      position: relative;
    }
    .display {
      background: #000;
      color: #00ffe0;
      font-size: 2rem;
      padding: 20px;
      text-align: right;
      border-radius: 12px;
      margin-bottom: 20px;
      box-shadow: inset 0 0 10px #00ffe0;
    }
    .buttons {
      display: grid;
      grid-template-columns: repeat(5, 1fr);
      gap: 10px;
    }
    .buttons button {
      padding: 15px;
      font-size: 1.2em;
      border: none;
      border-radius: 10px;
      background: #333;
      color: #fff;
      cursor: pointer;
      transition: 0.2s;
    }
    .buttons button:hover {
      background: #00ffee;
      color: #000;
    }
    .buttons button[style*="grid-column"] {
      grid-column: span 2;
    }
    #muteBtn {
      position: absolute;
      top: -15px;
      right: -15px;
      background: #222;
      color: #0ff;
      border: 2px solid #0ff;
      border-radius: 30px;
      font-size: 0.9rem;
      padding: 6px 12px;
      cursor: pointer;
    }
  </style>
</head>
<body>
  <h1>📟 Calculator – Made by Naman</h1>
  <div class="calculator">
    <button id="muteBtn">🔊</button>
    <div class="display" id="display">0</div>
    <div class="buttons">
      <button onclick="press('C')">C</button>
      <button onclick="press('⌫')">⌫</button>
      <button onclick="press('(')">(</button>
      <button onclick="press(')')">)</button>
      <button onclick="press('%')">%</button>
      <button onclick="press('7')">7</button>
      <button onclick="press('8')">8</button>
      <button onclick="press('9')">9</button>
      <button onclick="press('/')">÷</button>
      <button onclick="press('√')">√</button>
      <button onclick="press('4')">4</button>
      <button onclick="press('5')">5</button>
      <button onclick="press('6')">6</button>
      <button onclick="press('*')">×</button>
      <button onclick="press('^')">^</button>
      <button onclick="press('1')">1</button>
      <button onclick="press('2')">2</button>
      <button onclick="press('3')">3</button>
      <button onclick="press('-')">-</button>
      <button onclick="press('.')">.</button>
      <button onclick="press('0')" style="grid-column: span 2;">0</button>
      <button onclick="press('=')">=</button>
      <button onclick="press('+')">+</button>
    </div>
  </div>

  <script>
    const display = document.getElementById('display');
    const muteBtn = document.getElementById('muteBtn');
    let isMuted = false;

    // Setup AudioContext
    const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
    let clickBuffer, errorBuffer;

    // Load sounds on interaction
    function loadSound(url, cb) {
      fetch(url)
        .then(res => res.arrayBuffer())
        .then(buf => audioCtx.decodeAudioData(buf, cb));
    }

    // Preload both
    loadSound("https://assets.mixkit.co/sfx/preview/mixkit-select-click-1109.mp3", buffer => clickBuffer = buffer);
    loadSound("https://assets.mixkit.co/sfx/preview/mixkit-wrong-answer-fail-notification-946.mp3", buffer => errorBuffer = buffer);

    function play(buffer) {
      if (isMuted || !buffer) return;
      if (audioCtx.state === 'suspended') audioCtx.resume();
      const src = audioCtx.createBufferSource();
      src.buffer = buffer;
      src.connect(audioCtx.destination);
      src.start(0);
    }

    function playClick() { play(clickBuffer); }
    function playError() { play(errorBuffer); }

    muteBtn.onclick = () => {
      isMuted = !isMuted;
      muteBtn.textContent = isMuted ? '🔇' : '🔊';
      playClick();
    };

    function press(key) {
      playClick();
      let text = display.innerText;

      if (key === 'C') return display.innerText = '0';
      if (key === '⌫') {
        display.innerText = text.length > 1 ? text.slice(0, -1) : '0';
        return;
      }
      if (key === '=') {
        try {
          let expr = text.replace(/÷/g, '/').replace(/×/g, '*').replace(/\^/g, '**');
          expr = expr.replace(/√(\d+)/g, 'Math.sqrt($1)');
          const result = eval(expr);
          display.innerText = result;
        } catch {
          playError();
          display.innerText = 'Error';
        }
        return;
      }
      if (text === '0' || text === 'Error') {
        display.innerText = key === '√' ? 'Math.sqrt(' : key;
      } else {
        display.innerText += key === '√' ? 'Math.sqrt(' : key;
      }
    }

    // Keyboard support
    document.addEventListener("keydown", e => {
      const key = e.key;
      if (!isNaN(key) || ['+', '-', '*', '/', '.', '%', '(', ')'].includes(key)) return press(key);
      if (key === 'Enter') return press('=');
      if (key === 'Backspace') return press('⌫');
      if (key.toLowerCase() === 'c') return press('C');
    });
  </script>
</body>
</html>
