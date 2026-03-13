<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Baju MCQ</title>
    <style>
        :root { --bg: #0f0f0f; --card: #1e1e1e; --red: #ff0000; --text: #ffffff; --green: #2ecc71; }
        body { font-family: 'Arial', sans-serif; background: var(--bg); color: var(--text); display: flex; justify-content: center; align-items: center; height: 100vh; margin: 0; }
        .quiz-box { width: 90%; max-width: 700px; background: var(--card); padding: 40px; border-radius: 20px; text-align: center; box-shadow: 0 20px 50px rgba(0,0,0,0.8); }
        .question-text { font-size: 2rem; margin-bottom: 30px; font-weight: bold; }
        .options-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 20px; }
        button { background: #333; color: white; border: none; padding: 25px; border-radius: 12px; font-size: 1.3rem; cursor: pointer; transition: 0.3s; }
        .correct { background: var(--green) !important; transform: scale(1.05); }
        .wrong { background: var(--red) !important; opacity: 0.7; }
        #next-btn { margin-top: 30px; background: #fff; color: #000; padding: 15px 40px; font-weight: bold; display: none; }
    </style>
</head>
<body>

<div class="quiz-box" id="quiz-box">
    <div id="loading">Connecting to Google Sheets...</div>
    <div id="content" style="display:none;">
        <div class="question-text" id="q-text"></div>
        <div class="options-grid" id="options-container"></div>
        <button id="next-btn" onclick="renderQuestion()">Next Question ➜</button>
    </div>
</div>

<script>
    // 🔗 PASTE YOUR PUBLISHED CSV URL HERE
    const SHEET_URL = "
https://docs.google.com/spreadsheets/d/e/2PACX-1vTR8J1rOMWJCh5SeyfDy1wG8RDuq8eZR1PUBjRDEA9Sp8CM8n0YC6a4LDKXwAhErlAhWaDjOJq4GVci/pub?output=csv
"; 

    let questions = [];
    let currentIdx = 0;

    async function fetchQuestions() {
        try {
            const response = await fetch(SHEET_URL);
            const data = await response.text();
            const rows = data.split('\n').slice(1); // Skip header row
            
            questions = rows.map(row => {
                const cols = row.split(',');
                return {
                    q: cols[0],
                    options: [cols[1], cols[2], cols[3], cols[4]],
                    correct: parseInt(cols[5])
                };
            });

            document.getElementById('loading').style.display = 'none';
            document.getElementById('content').style.display = 'block';
            renderQuestion();
        } catch (e) {
            document.getElementById('loading').innerText = "Error loading data. Check your URL!";
        }
    }

    function renderQuestion() {
        if (currentIdx >= questions.length) {
            document.getElementById('quiz-box').innerHTML = "<h1>Quiz Complete! 🏁</h1>";
            return;
        }

        const data = questions[currentIdx];
        document.getElementById('q-text').innerText = data.q;
        const container = document.getElementById('options-container');
        container.innerHTML = '';
        document.getElementById('next-btn').style.display = 'none';

        data.options.forEach((opt, i) => {
            const btn = document.createElement('button');
            btn.innerText = opt;
            btn.onclick = () => {
                const allBtns = container.querySelectorAll('button');
                allBtns.forEach(b => b.disabled = true);
                if(i === data.correct) {
                    btn.classList.add('correct');
                } else {
                    btn.classList.add('wrong');
                    allBtns[data.correct].classList.add('correct');
                }
                document.getElementById('next-btn').style.display = 'inline-block';
            };
            container.appendChild(btn);
        });
        currentIdx++;
    }

    fetchQuestions();
</script>
</body>
</html>
