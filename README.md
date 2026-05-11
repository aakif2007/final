<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ILTES KIDZ Quiz</title>
    <style>
        /* General Styles */
        :root {
            --primary-bg: #FFEFBA;
            --button-color: #FF6B6B;
            --accent-color: #4ECDC4;
            --font-main: 'Comic Sans MS', 'Chalkboard SE', sans-serif;
        }

        body {
            margin: 0;
            padding: 0;
            font-family: var(--font-main);
            background: linear-gradient(135deg, #FFEFBA 0%, #FFFFFF 100%);
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            overflow: hidden;
            text-align: center;
        }

        .container {
            width: 90%;
            max-width: 600px;
            padding: 20px;
            background: white;
            border-radius: 30px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.1);
            border: 5px solid var(--accent-color);
        }

        /* Page visibility */
        .page { display: none; }
        .active { display: block; animation: fadeIn 0.5s; }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }

        /* Typography */
        h1 { color: #FF6B6B; font-size: 3rem; margin-bottom: 10px; }
        h2 { color: #45B7D1; margin-top: 0; }
        p { font-size: 1.2rem; }

        /* Buttons */
        .btn {
            background-color: var(--button-color);
            color: white;
            border: none;
            padding: 15px 40px;
            font-size: 1.5rem;
            border-radius: 50px;
            cursor: pointer;
            transition: transform 0.2s, background 0.2s;
            box-shadow: 0 5px 15px rgba(255, 107, 107, 0.4);
            margin-top: 20px;
        }

        .btn:hover { transform: scale(1.1); background-color: #ff5252; }

        /* Loading Screen */
        #loader {
            display: none;
            position: fixed;
            top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(255, 255, 255, 0.9);
            z-index: 100;
            flex-direction: column;
            justify-content: center;
            align-items: center;
        }

        .spinner {
            width: 60px;
            height: 60px;
            border: 8px solid #f3f3f3;
            border-top: 8px solid var(--accent-color);
            border-radius: 50%;
            animation: spin 1s linear infinite;
        }

        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }

        /* Quiz Styles */
        .options-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 15px;
            margin-top: 20px;
        }

        .option-btn {
            background: #f8f9fa;
            border: 3px solid #eee;
            padding: 15px;
            border-radius: 15px;
            cursor: pointer;
            font-size: 1.1rem;
            transition: all 0.2s;
        }

        .option-btn:hover { border-color: var(--accent-color); background: #e0f7f6; }

        /* Congratulations Animation */
        .confetti {
            font-size: 4rem;
            animation: bounce 1s infinite;
        }

        @keyframes bounce {
            0%, 100% { transform: translateY(0); }
            50% { transform: translateY(-20px); }
        }
    </style>
</head>
<body>

    <!-- Loading Overlay -->
    <div id="loader">
        <div class="spinner"></div>
        <p>Getting the colors ready...</p>
    </div>

    <div class="container">
        <!-- Page 1: Welcome -->
        <div id="page1" class="page active">
            <h1>WELCOME TO<br>'ILTES KIDZ'</h1>
            <h2 style="color: #6c5ce7;">Mentor: MAZNA</h2>
            <p>Are you ready to play with colors?</p>
            <button class="btn" onclick="startQuizTransition()">Start Quiz! 🎨</button>
        </div>

        <!-- Page 2: Quiz -->
        <div id="page2" class="page">
            <h2 id="question-number">Question 1 of 10</h2>
            <p id="question-text" style="font-weight: bold; font-size: 1.5rem;"></p>
            <div id="options" class="options-grid"></div>
        </div>

        <!-- Page 3: Thank You -->
        <div id="page3" class="page">
            <div class="confetti">🎉🥳🎈</div>
            <h1>Congratulations!</h1>
            <p>You finished the ILTES KIDZ Color Challenge.</p>
            <h2 id="final-score">Score: 0/10</h2>
            <button class="btn" onclick="location.reload()">Play Again</button>
        </div>
    </div>

    <script>
        const questions = [
            { q: "What color is the sky on a sunny day?", a: ["Blue", "Green", "Red", "Yellow"], correct: "Blue" },
            { q: "What color is a ripe banana?", a: ["Purple", "Yellow", "Blue", "Pink"], correct: "Yellow" },
            { q: "What color do you get if you mix Red and White?", a: ["Black", "Green", "Pink", "Orange"], correct: "Pink" },
            { q: "Grass is usually what color?", a: ["Blue", "Green", "Brown", "Purple"], correct: "Green" },
            { q: "What color is a strawberry?", a: ["Red", "Blue", "Yellow", "Orange"], correct: "Red" },
            { q: "An orange fruit is what color?", a: ["Green", "Purple", "Orange", "Red"], correct: "Orange" },
            { q: "What color are grapes usually?", a: ["Yellow", "Purple", "Blue", "Red"], correct: "Purple" },
            { q: "What color is a chocolate bar?", a: ["White", "Brown", "Black", "Pink"], correct: "Brown" },
            { q: "What color is a cloud before it rains?", a: ["Yellow", "Gray", "Green", "Orange"], correct: "Gray" },
            { q: "What color is the Sun?", a: ["Yellow", "Blue", "Green", "Purple"], correct: "Yellow" }
        ];

        let currentQuestionIndex = 0;
        let score = 0;

        function showPage(pageId) {
            document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
            document.getElementById(pageId).classList.add('active');
        }

        function startQuizTransition() {
            document.getElementById('loader').style.display = 'flex';
            setTimeout(() => {
                document.getElementById('loader').style.display = 'none';
                showPage('page2');
                loadQuestion();
            }, 1500); // 1.5 second loading animation
        }

        function loadQuestion() {
            const currentQ = questions[currentQuestionIndex];
            document.getElementById('question-number').innerText = `Question ${currentQuestionIndex + 1} of 10`;
            document.getElementById('question-text').innerText = currentQ.q;
            
            const optionsContainer = document.getElementById('options');
            optionsContainer.innerHTML = '';

            currentQ.a.forEach(answer => {
                const button = document.createElement('button');
                button.innerText = answer;
                button.className = 'option-btn';
                button.onclick = () => checkAnswer(answer);
                optionsContainer.appendChild(button);
            });
        }

        function checkAnswer(selected) {
            if (selected === questions[currentQuestionIndex].correct) {
                score++;
            }

            currentQuestionIndex++;

            if (currentQuestionIndex < questions.length) {
                loadQuestion();
            } else {
                showFinalResults();
            }
        }

        function showFinalResults() {
            showPage('page3');
            document.getElementById('final-score').innerText = `Your Score: ${score} / 10`;
        }
    </script>
</body>
</html>
