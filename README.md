# Framing_Game
Game to understand camera framing

<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Shot Size Trainer - Educational Game</title>
    <style>
        :root {
            --bg-color: #1a1a1a;
            --card-bg: #2d2d2d;
            --accent-color: #e50914;
            --text-color: #ffffff;
            --correct-color: #2e7d32;
            --incorrect-color: #c62828;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: var(--bg-color);
            color: var(--text-color);
            display: flex;
            flex-direction: column;
            align-items: center;
            min-height: 100vh;
            margin: 0;
            padding: 20px;
        }

        h1 {
            margin-bottom: 5px;
            color: var(--accent-color);
            text-transform: uppercase;
            letter-spacing: 2px;
        }

        .subtitle {
            color: #aaa;
            margin-bottom: 25px;
        }

        .game-container {
            background-color: var(--card-bg);
            border-radius: 12px;
            padding: 25px;
            box-shadow: 0 8px 24px rgba(0,0,0,0.5);
            max-width: 600px;
            width: 100%;
            text-align: center;
            position: relative;
        }

        .score-board {
            font-size: 1.1rem;
            margin-bottom: 15px;
            display: flex;
            justify-content: space-between;
            padding: 0 10px;
        }

        .viewfinder {
            position: relative;
            border: 4px solid #444;
            border-radius: 8px;
            overflow: hidden;
            background-color: #000;
            margin: 0 auto 20px auto;
            width: 100%;
            max-width: 480px;
            aspect-ratio: 16 / 9;
        }

        canvas {
            width: 100%;
            height: 100%;
            display: block;
        }

        /* Viewfinder Overlay Elements */
        .hud-top {
            position: absolute;
            top: 10px;
            left: 10px;
            right: 10px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            font-family: monospace;
            font-size: 1rem;
            color: #fff;
            text-shadow: 1px 1px 2px #000;
            pointer-events: none;
        }

        .rec-container {
            display: flex;
            align-items: center;
            gap: 6px;
            color: var(--accent-color);
            font-weight: bold;
        }

        .dot {
            width: 12px;
            height: 12px;
            background-color: var(--accent-color);
            border-radius: 50%;
        }

        .blinking {
            animation: blink 1s infinite;
        }

        @keyframes blink {
            0%, 100% { opacity: 1; }
            50% { opacity: 0; }
        }

        .tc-timer {
            background-color: rgba(0, 0, 0, 0.6);
            padding: 2px 8px;
            border-radius: 4px;
            letter-spacing: 1px;
            color: #00ff00;
        }

        .crosshair {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            width: 20px;
            height: 20px;
            border: 1px solid rgba(255,255,255,0.3);
            pointer-events: none;
        }

        .options-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
            gap: 10px;
            margin-bottom: 20px;
        }

        button.btn-option {
            background-color: #3d3d3d;
            color: white;
            border: 2px solid #555;
            padding: 12px;
            border-radius: 6px;
            cursor: pointer;
            font-size: 0.95rem;
            font-weight: 600;
            transition: all 0.2s ease;
        }

        button.btn-option:hover {
            background-color: #4d4d4d;
            border-color: #888;
        }

        button.btn-option.correct {
            background-color: var(--correct-color) !important;
            border-color: #4caf50 !important;
        }

        button.btn-option.incorrect {
            background-color: var(--incorrect-color) !important;
            border-color: #f44336 !important;
        }

        .feedback {
            min-height: 50px;
            font-size: 1rem;
            line-height: 1.4;
            margin-bottom: 15px;
        }

        /* Modal Styles */
        .modal-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.85);
            display: flex;
            justify-content: center;
            align-items: center;
            opacity: 0;
            pointer-events: none;
            transition: opacity 0.3s ease;
            z-index: 1000;
        }

        .modal-overlay.active {
            opacity: 1;
            pointer-events: auto;
        }

        .modal-card {
            background: var(--card-bg);
            border: 2px solid var(--accent-color);
            border-radius: 12px;
            padding: 30px;
            width: 90%;
            max-width: 400px;
            text-align: center;
            box-shadow: 0 10px 30px rgba(0,0,0,0.8);
        }

        .modal-card h2 {
            margin-top: 0;
            color: var(--accent-color);
        }

        .score-summary {
            margin: 20px 0;
            font-size: 1.1rem;
            line-height: 1.8;
            text-align: left;
            background: #111;
            padding: 15px;
            border-radius: 8px;
        }

        .final-score {
            font-size: 1.5rem;
            color: #4caf50;
            font-weight: bold;
            border-top: 1px solid #444;
            padding-top: 10px;
            margin-top: 10px;
        }
    </style>
</head>
<body>

    <h1>Director's Lens</h1>
    <div class="subtitle">Match the camera angle to the correct shot size terminology</div>

    <div class="game-container">
        <div class="score-board">
            <span>Round: <strong id="round-num">1</strong>/<span id="total-rounds">5</span></span>
            <span>Accuracy Score: <strong id="score">0</strong></span>
        </div>

        <div class="viewfinder">
            <div class="hud-top">
                <div class="rec-container">
                    <div id="rec-dot" class="dot blinking"></div>
                    <span id="rec-text">REC</span>
                </div>
                <div class="tc-timer" id="timecode">00:00:00:00</div>
            </div>
            <div class="crosshair"></div>
            <canvas id="stage" width="640" height="360"></canvas>
        </div>

        <div id="options" class="options-grid"></div>

        <div id="feedback" class="feedback">Select the matching shot size for the frame above.</div>
    </div>

    <!-- Final Score Popup Modal -->
    <div class="modal-overlay" id="score-modal">
        <div class="modal-card">
            <h2>PRODUCTION COMPLETE!</h2>
            <p>Here is your final wrap summary:</p>
            <div class="score-summary">
                <div>Correct Answers: <strong id="modal-correct">0</strong></div>
                <div>Timecode Elapsed: <strong id="modal-time">00:00:00:00</strong></div>
                <div>Base Score: <strong id="modal-base-score">0</strong></div>
                <div>Time Bonus: <strong id="modal-bonus-score">0</strong></div>
                <div class="final-score">TOTAL SCORE: <span id="modal-total-score">0</span></div>
            </div>
            <button class="btn-option" style="width: 100%; background: var(--accent-color);" onclick="closeModalAndRestart()">Play Again</button>
        </div>
    </div>

    <script>
        // --- Web Audio API Chiptune Sound Engine ---
        let audioCtx = null;
        let isMusicPlaying = false;
        let musicTimer = null;

        function initAudio() {
            if (!audioCtx) {
                audioCtx = new (window.AudioContext || window.webkitAudioContext)();
            }
            if (audioCtx.state === 'suspended') {
                audioCtx.resume();
            }
        }

        // Play Success Sound (Upward Chime)
        function playSuccessSound() {
            if (!audioCtx) return;
            const now = audioCtx.currentTime;
            const osc = audioCtx.createOscillator();
            const gain = audioCtx.createGain();

            osc.type = 'triangle';
            osc.frequency.setValueAtTime(523.25, now); // C5
            osc.frequency.setValueAtTime(659.25, now + 0.1); // E5
            osc.frequency.setValueAtTime(783.99, now + 0.2); // G5
            osc.frequency.setValueAtTime(1046.50, now + 0.3); // C6

            gain.gain.setValueAtTime(0.15, now);
            gain.gain.exponentialRampToValueAtTime(0.001, now + 0.5);

            osc.connect(gain);
            gain.connect(audioCtx.destination);
            osc.start(now);
            osc.stop(now + 0.5);
        }

        // Play Fail Sound (Downward Buzz)
        function playFailSound() {
            if (!audioCtx) return;
            const now = audioCtx.currentTime;
            const osc = audioCtx.createOscillator();
            const gain = audioCtx.createGain();

            osc.type = 'sawtooth';
            osc.frequency.setValueAtTime(180, now);
            osc.frequency.linearRampToValueAtTime(80, now + 0.3);

            gain.gain.setValueAtTime(0.2, now);
            gain.gain.exponentialRampToValueAtTime(0.001, now + 0.35);

            osc.connect(gain);
            gain.connect(audioCtx.destination);
            osc.start(now);
            osc.stop(now + 0.35);
        }

        // Synthesized Background Chiptune Loop
        function startBackgroundMusic() {
            if (isMusicPlaying) return;
            isMusicPlaying = true;

            const notes = [261.63, 329.63, 392.00, 329.63, 293.66, 349.23, 440.00, 349.23]; // Simple loop melody
            let step = 0;

            musicTimer = setInterval(() => {
                if (!audioCtx || !isMusicPlaying) return;
                const now = audioCtx.currentTime;
                const osc = audioCtx.createOscillator();
                const gain = audioCtx.createGain();

                osc.type = 'square';
                osc.frequency.setValueAtTime(notes[step % notes.length], now);

                gain.gain.setValueAtTime(0.02, now); // Soft background volume
                gain.gain.exponentialRampToValueAtTime(0.001, now + 0.18);

                osc.connect(gain);
                gain.connect(audioCtx.destination);
                osc.start(now);
                osc.stop(now + 0.2);

                step++;
            }, 250); // 120 BPM tempo
        }

        function stopBackgroundMusic() {
            isMusicPlaying = false;
            if (musicTimer) clearInterval(musicTimer);
        }

        // --- Game Logic ---
        const SHOT_TYPES = [
            {
                id: 'ECU',
                name: 'Extreme Close-Up',
                desc: 'Focuses intensely on a single detail or feature (e.g., eyes) to convey strong emotion or key details.',
                imgUrl: 'images/resize-uploaded-image (1).jpeg',
                draw: drawECU
            },
            {
                id: 'CU',
                name: 'Close-Up',
                desc: 'Frames the head and shoulders, emphasizing facial expression and emotional state.',
                imgUrl: 'images/Wonder_Woman_close_up_16_9.width-1431.jpg',
                draw: drawCU
            },
            {
                id: 'MS',
                name: 'Medium Shot',
                desc: 'Frames the subject from roughly the waist up, balancing character emotion with surroundings.',
                imgUrl: 'images/Camera-Shot-Guide-Cowboy-Shot-Wonder-Woman-StudioBinder.jpeg',
                draw: drawMS
            },
            {
                id: 'LS',
                name: 'Long Shot',
                desc: 'Displays the full human body top-to-bottom within its immediate environment.',
                imgUrl: 'images/Camera-Shot-Guide-Full-Shot-2-Django-Unchained-StudioBinder.jpg',
                draw: drawLS
            },
            {
                id: 'ELS',
                name: 'Extreme Long Shot',
                desc: 'Emphasizes scale and landscape; human figures appear tiny or distant in the environment.',
                imgUrl: 'images/Into_the_Wild_long_shot_2.width-1431.png',
                draw: drawELS
            }
        ];

        let currentQuestionIndex = 0;
        let score = 0;
        let correctAnswersCount = 0;
        let activeQuestions = [];
        let canAnswer = true;

        // Timecode Variables
        let startTime = 0;
        let elapsedMilliseconds = 0;
        let timerInterval = null;

        const canvas = document.getElementById('stage');
        const ctx = canvas.getContext('2d');

        function initGame() {
            activeQuestions = [...SHOT_TYPES].sort(() => Math.random() - 0.5);
            currentQuestionIndex = 0;
            score = 0;
            correctAnswersCount = 0;
            document.getElementById('score').innerText = score;
            document.getElementById('total-rounds').innerText = activeQuestions.length;

            startTimer();
            setRecState(true);

            loadQuestion();
        }

        function startTimer() {
            clearInterval(timerInterval);
            startTime = Date.now();
            elapsedMilliseconds = 0;

            timerInterval = setInterval(() => {
                elapsedMilliseconds = Date.now() - startTime;
                document.getElementById('timecode').innerText = formatTimecode(elapsedMilliseconds);
            }, 40);
        }

        function stopTimer() {
            clearInterval(timerInterval);
        }

        function formatTimecode(ms) {
            let totalSeconds = Math.floor(ms / 1000);
            let hours = Math.floor(totalSeconds / 3600);
            let minutes = Math.floor((totalSeconds % 3600) / 60);
            let seconds = totalSeconds % 60;
            let frames = Math.floor((ms % 1000) / (1000 / 24));

            return `${pad(hours)}:${pad(minutes)}:${pad(seconds)}:${pad(frames)}`;
        }

        function pad(num) {
            return num.toString().padStart(2, '0');
        }

        function setRecState(isRecording) {
            const dot = document.getElementById('rec-dot');
            const text = document.getElementById('rec-text');
            if (isRecording) {
                dot.classList.add('blinking');
                dot.style.backgroundColor = 'var(--accent-color)';
                text.innerText = 'REC';
                text.style.color = 'var(--accent-color)';
            } else {
                dot.classList.remove('blinking');
                dot.style.backgroundColor = '#555';
                text.innerText = 'STOP';
                text.style.color = '#aaa';
            }
        }

        function loadQuestion() {
            canAnswer = true;
            document.getElementById('feedback').innerText = 'Select the matching shot size for the frame above.';
            document.getElementById('round-num').innerText = currentQuestionIndex + 1;

            const currentData = activeQuestions[currentQuestionIndex];
            
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            if (currentData.imgUrl) {
                const img = new Image();
                img.src = currentData.imgUrl;
                img.onload = () => {
                    ctx.drawImage(img, 0, 0, canvas.width, canvas.height);
                };
                img.onerror = () => {
                    currentData.draw(ctx, canvas.width, canvas.height);
                };
            } else {
                currentData.draw(ctx, canvas.width, canvas.height);
            }

            const optionsContainer = document.getElementById('options');
            optionsContainer.innerHTML = '';

            const choices = [...SHOT_TYPES].sort(() => Math.random() - 0.5);

            choices.forEach(type => {
                const btn = document.createElement('button');
                btn.className = 'btn-option';
                btn.innerText = type.name;
                btn.onclick = () => {
                    initAudio(); // Start AudioContext on user interaction
                    startBackgroundMusic();
                    checkAnswer(type.id, btn);
                };
                optionsContainer.appendChild(btn);
            });
        }

        function checkAnswer(selectedId, btn) {
            if (!canAnswer) return;
            canAnswer = false;

            const currentData = activeQuestions[currentQuestionIndex];
            const buttons = document.querySelectorAll('.btn-option');

            if (selectedId === currentData.id) {
                btn.classList.add('correct');
                score += 100;
                correctAnswersCount++;
                document.getElementById('score').innerText = score;
                document.getElementById('feedback').innerHTML = `<strong style="color: #4caf50;">Correct!</strong> ${currentData.desc}`;
                playSuccessSound();
            } else {
                btn.classList.add('incorrect');
                buttons.forEach(b => {
                    if (b.innerText === currentData.name) b.classList.add('correct');
                });
                document.getElementById('feedback').innerHTML = `<strong style="color: #f44336;">Incorrect.</strong> This is a <strong>${currentData.name}</strong>. ${currentData.desc}`;
                playFailSound();
            }

            // Pause briefly (1.8 seconds) so player sees result, then proceed automatically
            setTimeout(() => {
                if (currentQuestionIndex < activeQuestions.length - 1) {
                    currentQuestionIndex++;
                    loadQuestion();
                } else {
                    finishGame();
                }
            }, 1800);
        }

        function finishGame() {
            stopTimer();
            stopBackgroundMusic();
            setRecState(false);

            const elapsedSeconds = Math.floor(elapsedMilliseconds / 1000);
            const timeBonus = Math.max(0, 500 - (elapsedSeconds * 10));
            const totalScore = score + timeBonus;

            document.getElementById('modal-correct').innerText = `${correctAnswersCount} / ${activeQuestions.length}`;
            document.getElementById('modal-time').innerText = formatTimecode(elapsedMilliseconds);
            document.getElementById('modal-base-score').innerText = score;
            document.getElementById('modal-bonus-score').innerText = timeBonus;
            document.getElementById('modal-total-score').innerText = totalScore;

            document.getElementById('score-modal').classList.add('active');
        }

        function closeModalAndRestart() {
            document.getElementById('score-modal').classList.remove('active');
            initGame();
        }

        // --- Procedural Scene Drawings ---

        function drawBackground(ctx, width, height) {
            const grad = ctx.createLinearGradient(0, 0, 0, height);
            grad.addColorStop(0, '#2c3e50');
            grad.addColorStop(0.6, '#e74c3c');
            grad.addColorStop(1, '#f39c12');
            ctx.fillStyle = grad;
            ctx.fillRect(0, 0, width, height);

            ctx.fillStyle = '#ffeaad';
            ctx.beginPath();
            ctx.arc(width * 0.7, height * 0.5, 40, 0, Math.PI * 2);
            ctx.fill();

            ctx.fillStyle = '#1a252f';
            ctx.beginPath();
            ctx.moveTo(0, height);
            ctx.lineTo(0, height * 0.65);
            ctx.lineTo(width * 0.3, height * 0.55);
            ctx.lineTo(width * 0.6, height * 0.7);
            ctx.lineTo(width, height * 0.5);
            ctx.lineTo(width, height);
            ctx.fill();
        }

        function drawECU(ctx, w, h) {
            ctx.fillStyle = '#e0ac69';
            ctx.fillRect(0, 0, w, h);
            ctx.strokeStyle = '#2c1605';
            ctx.lineWidth = 6;
            
            ctx.fillStyle = '#ffffff';
            ctx.beginPath();
            ctx.ellipse(w * 0.3, h * 0.5, 90, 45, 0, 0, Math.PI * 2);
            ctx.fill();
            ctx.stroke();

            ctx.fillStyle = '#2e6f40';
            ctx.beginPath();
            ctx.arc(w * 0.3, h * 0.5, 30, 0, Math.PI * 2);
            ctx.fill();

            ctx.fillStyle = '#ffffff';
            ctx.beginPath();
            ctx.ellipse(w * 0.7, h * 0.5, 90, 45, 0, 0, Math.PI * 2);
            ctx.fill();
            ctx.stroke();

            ctx.fillStyle = '#2e6f40';
            ctx.beginPath();
            ctx.arc(w * 0.7, h * 0.5, 30, 0, Math.PI * 2);
            ctx.fill();
        }

        function drawCU(ctx, w, h) {
            drawBackground(ctx, w, h);

            ctx.fillStyle = '#34495e';
            ctx.beginPath();
            ctx.ellipse(w * 0.5, h * 1.1, 180, 100, 0, Math.PI, 0);
            ctx.fill();

            ctx.fillStyle = '#e0ac69';
            ctx.beginPath();
            ctx.arc(w * 0.5, h * 0.45, 110, 0, Math.PI * 2);
            ctx.fill();

            ctx.fillStyle = '#2c1605';
            ctx.beginPath();
            ctx.arc(w * 0.43, h * 0.42, 10, 0, Math.PI * 2);
            ctx.arc(w * 0.57, h * 0.42, 10, 0, Math.PI * 2);
            ctx.fill();
        }

        function drawMS(ctx, w, h) {
            drawBackground(ctx, w, h);

            ctx.fillStyle = '#34495e';
            ctx.fillRect(w * 0.4, h * 0.55, w * 0.2, h * 0.5);

            ctx.fillStyle = '#e0ac69';
            ctx.beginPath();
            ctx.arc(w * 0.5, h * 0.35, 50, 0, Math.PI * 2);
            ctx.fill();
        }

        function drawLS(ctx, w, h) {
            drawBackground(ctx, w, h);

            const cx = w * 0.5;
            const ground = h * 0.85;

            ctx.strokeStyle = '#2c3e50';
            ctx.lineWidth = 10;
            ctx.beginPath();
            ctx.moveTo(cx - 10, ground);
            ctx.lineTo(cx - 10, ground - 70);
            ctx.moveTo(cx + 10, ground);
            ctx.lineTo(cx + 10, ground - 70);
            ctx.stroke();

            ctx.fillStyle = '#e74c3c';
            ctx.fillRect(cx - 20, ground - 130, 40, 60);

            ctx.fillStyle = '#e0ac69';
            ctx.beginPath();
            ctx.arc(cx, ground - 150, 18, 0, Math.PI * 2);
            ctx.fill();
        }

        function drawELS(ctx, w, h) {
            drawBackground(ctx, w, h);

            const cx = w * 0.5;
            const ground = h * 0.65;

            ctx.fillStyle = '#111';
            ctx.beginPath();
            ctx.arc(cx, ground - 12, 3, 0, Math.PI * 2);
            ctx.fill();
            ctx.fillRect(cx - 2, ground - 9, 4, 9);
        }

        window.onload = initGame;
    </script>
</body>
</html>
