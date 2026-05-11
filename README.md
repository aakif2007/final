
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ILTES KIDZ Quiz</title>
    <style>
        :root {
            --button-color: #FF6B6B;
            --accent-color: #4ECDC4;
            --apple-red: #ff3b30;
            --font-main: 'Comic Sans MS', 'Chalkboard SE', sans-serif;
        }

        /* Animated Color Background */
        body {
            margin: 0; padding: 0;
            font-family: var(--font-main);
            background: linear-gradient(-45deg, #ee7752, #e73c7e, #23a6d5, #23d5ab, #ffefba);
            background-size: 400% 400%;
            animation: gradientBG 15s ease infinite;
            display: flex; justify-content: center; align-items: center;
            height: 100vh; overflow: hidden; text-align: center;
        }

        @keyframes gradientBG {
            0% { background-position: 0% 50%; }
            50% { background-position: 100% 50%; }
            100% { background-position: 0% 50%; }
        }

        .container {
            width: 90%; max-width: 550px; padding: 30px;
            background: rgba(255, 255, 255, 0.9); /* Slight transparency to see BG */
            border-radius: 40px;
            box-shadow: 0 15px 35px rgba(0,0,0,0.1);
            border: 8px solid var(--accent-color);
            position: relative;
            z-index: 1;
        }

        .page { display: none; }
        .active { display: block; animation: fadeIn 0.5s; }

        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }

        /* Welcome Page Inputs */
        .input-group { margin: 15px 0; text-align: left; }
        label { display: block; font-weight: bold; margin-bottom: 5px; color: #333; }
        input {
            width: 100%; padding: 12px; border-radius: 15px;
            border: 3px solid #eee; font-family: inherit; font-size: 1rem;
            box-sizing: border-box; outline: none;
        }

        /* Buttons */
        .btn {
            background-color: var(--button-color); color: white;
            border: none; padding: 15px 40px; font-size: 1.5rem;
            border-radius: 50px; cursor: pointer; transition: 0.2s;
            box-shadow: 0 5px 15px rgba(255, 107, 107, 0.4); margin-top: 15px;
        }
        .btn:hover { transform: scale(1.05); }

        /* Apple Loading Animation */
        #loader {
            display: none; position: fixed; top: 0; left: 0;
            width: 100%; height: 100%; background: rgba(255, 255, 255, 0.95);
            z-index: 100; flex-direction: column; justify-content: center; align-items: center;
        }

        .apple-container { animation: appleJump 1s infinite alternate; }
        .apple {
            width: 100px; height: 90px; background-color: var(--apple-red);
            border-radius: 50% 50% 50% 50% / 45% 45% 55% 55%;
            position: relative;
        }
        .apple::after {
            content: ''; position: absolute; top: -18px; left: 55%;
            width: 25px; height: 30px; background: #4caf50;
            border-radius: 100% 0% 100% 0%; transform: rotate(-10deg);
        }

        @keyframes appleJump {
            from { transform: translateY(0) scale(1); }
            to { transform: translateY(-30px) scale(1.1); }
        }

        /* Quiz Styles */
        .options-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; margin-top: 20px; }
        .option-btn {
            background: white; border: 3px solid #f0f0f0;
            padding: 15px; border-radius: 20px; cursor: pointer;
            font-size: 1.1rem; font-family: inherit; transition: 0.2s;
        }
        .option-btn:hover { background: #e0f7f6; border-color: var(--accent-color); }
    </style>
</head>
<body>

    <!-- Apple Loader -->
    <div id="loader">
        <div class="apple-container">
            <div class="apple"></div>
        </div>
        <h2 style="margin-top: 40px; color: var(--apple-red);">Picking apples...</h2>
    </div>

    <div class="container">
        <!-- Page 1: Welcome & Details -->
        <div id="page1" class="page active">
            <h1 style="color: #FF6B6B; margin: 0; font-size: 3rem;">ILTES KIDZ</h1>
            <h3 style="color: #6c5ce7; margin-top: 5px;">Mentor: MAZNA</h3>
            
            <form id="detailsForm" onsubmit="event.preventDefault(); startQuiz();">
                <div class="input-group">
                    <label>Enter Your Name:</label>
                    <input type="text" id="userName" placeholder="Type name here..." required>
                </div>
                <div class="input-group">
                    <label>Admission Number:</label>
                    <input type="text" id="admissionNo" placeholder="Type number here..." required>
                </div>
                <button type="submit" class="btn">Start Quiz! 🎨</button>
            </form>
        </div>

        <!-- Page 2: Quiz -->
        <div id="page2" class="page">
            <h4 id="question-count" style="color: #888;">Question 1 of 10</h4>
            <p id="question-text" style="font-size: 1.6rem; font-weight: bold; color: #333;"></p>
            <div id="options" class="options-grid"></div>
        </div>

        <!-- Page 3: Results -->
        <div id="page3" class="page">
            <div style="font-size: 4rem; animation: pulse 1s infinite;">🏆</div>
            <h1 style="color: #4ECDC4;">Great Job!</h1>
            <p id="congrats-text" style="font-size: 1.2rem;"></p>
            <h2 id="final-score" style="font-size: 3.5rem; color: #FF6B6B; margin: 10px 0;">0/10</h2>
            <button class="btn" onclick="location.reload()">Try Again</button>
        </div>
    </div>

    <script>
        const questions = [
            { q: "What color is an Apple?", a: ["Red", "Blue", "Purple", "Black"], c: "Red" },
            { q: "What color is the Sun?", a: ["Green", "Yellow", "Pink", "Blue"], c: "Yellow" },
            { q: "What color is Grass?", a: ["Red", "Orange", "Green", "White"], c: "Green" },
            { q: "What color is the Sky?", a: ["Blue", "Yellow", "Brown", "Purple"], c: "Blue" },
            { q: "What color is a Carrot?", a: ["Blue", "Green", "Orange", "Red"], c: "Orange" },
            { q: "What color is Milk?", a: ["White", "Black", "Red", "Green"], c: "White" },
            { q: "What color are Grapes?", a: ["Purple", "Gray", "Orange", "Yellow"], c: "Purple" },
            { q: "What color is Chocolate?", a: ["Blue", "Brown", "Pink", "White"], c: "Brown" },
            { q: "What color is a Flamingo?", a: ["Green", "Pink", "Black", "Yellow"], c: "Pink" },
            { q: "What color are Blueberries?", a: ["Red", "Yellow", "Blue", "Green"], c: "Blue" }
        ];

        let currentIndex = 0;
        let score = 0;

        function startQuiz() {
            document.getElementById('loader').style.display = 'flex';
            setTimeout(() => {
                document.getElementById('loader').style.display = 'none';
                document.getElementById('page1').classList.remove('active');
                document.getElementById('page2').classList.add('active');
                loadQuestion();
            }, 2500); 
        }

        function loadQuestion() {
            const q = questions[currentIndex];
            document.getElementById('question-count').innerText = `Question ${currentIndex + 1} of 10`;
            document.getElementById('question-text').innerText = q.q;
            
            const btnContainer = document.getElementById('options');
            btnContainer.innerHTML = '';

            q.a.forEach(opt => {
                const b = document.createElement('button');
                b.innerText = opt;
                b.className = 'option-btn';
                b.onclick = () => {
                    if(opt === q.c) score++;
                    currentIndex++;
                    if(currentIndex < questions.length) loadQuestion();
                    else showResults();
                };
                btnContainer.appendChild(b);
            });
        }

        function showResults() {
            const name = document.getElementById('userName').value;
            document.getElementById('page2').classList.remove('active');
            document.getElementById('page3').classList.add('active');
            document.getElementById('congrats-text').innerText = `${name}, you finished the quiz!`;
            document.getElementById('final-score').innerText = `${score} / 10`;
        }
    </script>
</body>
</html>
