---
title: Welcome to my blog
---
quiz-site/
├ index.html      ← メインページ（index.md を HTML に変換した想定）
├ style.css       ← デザイン用
└ script.js       ← クイズロジック
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width,initial-scale=1.0">
  <title>AI Literacy Quiz</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <div class="container">
    <h1>AI-Generated Content & Fake News Quiz</h1>
    <div id="quiz">
      <div id="timer">Time left: <span id="time">15</span>s</div>
      <div id="question"></div>
      <div id="choices"></div>
      <button id="next" disabled>Next</button>
    </div>
    <div id="result" class="hidden">
      <h2>Your AI Literacy Score</h2>
      <p id="scoreText"></p>
      <button id="restart">Retry Quiz</button>
    </div>
  </div>
  <script src="script.js"></script>
</body>
</html>
body {
  background: #eef2f5;
  font-family: Arial, sans-serif;
  margin: 0; padding: 0;
}
.container {
  max-width: 600px;
  margin: 50px auto;
  background: #fff;
  padding: 20px 30px;
  border-radius: 8px;
  box-shadow: 0 2px 10px rgba(0,0,0,0.1);
  text-align: center;
}
#timer {
  font-size: 1.1em;
  margin-bottom: 10px;
  color: #333;
}
#choices button {
  display: block;
  margin: 8px auto;
  padding: 10px;
  width: 80%;
  font-size: 1em;
}
button {
  cursor: pointer;
}
.hidden {
  display: none;
}
#next, #restart {
  margin-top: 20px;
  padding: 10px 20px;
  font-size: 1em;
}
// --- 質問データ（英語×4択×5問） ---
const quiz = [
  {
    question: "1. Which clue often reveals an AI-generated image?",
    choices: [
      "Odd reflections in eyes or glasses",
      "Perfectly even skin textures",
      "Visible camera model in EXIF data",
      "High contrast highlights"
    ],
    answer: 0  // reflections の不自然さがDeepfakeの典型手がかり【:contentReference[oaicite:5]{index=5}】
  },
  {
    question: "2. Which writing pattern hints at AI-generated text?",
    choices: [
      "Short, concise sentences",
      "Repetitive filler phrases",
      "Use of first-person anecdotes",
      "Frequent hyperlinks"
    ],
    answer: 1  // 過剰な冗長表現は生成AIの“特徴”【:contentReference[oaicite:6]{index=6}】
  },
  {
    question: "3. What metadata helps verify an article’s authenticity?",
    choices: [
      "Author name and publish date",
      "File’s byte size",
      "Font family",
      "Number of images"
    ],
    answer: 0  // 出典検証の基本は著者・時刻情報の確認【:contentReference[oaicite:7]{index=7}】
  },
  {
    question: "4. Which tool can assist in detecting AI-written text?",
    choices: [
      "OpenAI AI Text Classifier",
      "Google Docs Grammar Check",
      "Mendeley Reference Manager",
      "YouTube Auto-caption"
    ],
    answer: 0  // OpenAI公式の分類器を活用【:contentReference[oaicite:8]{index=8}】
  },
  {
    question: "5. A headline reads “Miracle cure—100% guaranteed!” What’s your first step?",
    choices: [
      "Share on social media",
      "Search fact-check sites like Snopes",
      "Trust because it sounds confident",
      "Ignore entire article"
    ],
    answer: 1  // センセーショナルな見出しには事実確認が必須【:contentReference[oaicite:9]{index=9}】
  }
];

let current = 0, score = 0, timeLeft = 15, timerId;

// --- 要素取得 ---
const qEl     = document.getElementById('question');
const cEl     = document.getElementById('choices');
const tEl     = document.getElementById('time');
const nextBtn = document.getElementById('next');
const result  = document.getElementById('result');
const quizDiv = document.getElementById('quiz');
const scoreT  = document.getElementById('scoreText');
const restart = document.getElementById('restart');

// --- タイマー処理 ---
function startTimer() {
  timeLeft = 15;
  tEl.textContent = timeLeft;
  timerId = setInterval(() => {
    timeLeft--;
    tEl.textContent = timeLeft;
    if (timeLeft === 0) {
      clearInterval(timerId);
      lockChoices();
    }
  }, 1000);
}

// --- 問題表示 ---
function showQuestion() {
  clearInterval(timerId);
  startTimer();
  nextBtn.disabled = true;
  const item = quiz[current];
  qEl.textContent = item.question;
  cEl.innerHTML = '';
  item.choices.forEach((ch, i) => {
    const btn = document.createElement('button');
    btn.textContent = ch;
    btn.onclick = () => select(i);
    cEl.appendChild(btn);
  });
}

// --- 回答選択処理 ---
function select(i) {
  clearInterval(timerId);
  lockChoices();
  if (i === quiz[current].answer) score++;
  nextBtn.disabled = false;
}

// --- 選択肢ロック＆色付け ---
function lockChoices() {
  Array.from(cEl.children).forEach((btn, i) => {
    btn.disabled = true;
    if (i === quiz[current].answer) btn.style.background = '#c8e6c9';
    else btn.style.background = '#ffcdd2';
  });
}

// --- 次の問題 or 結果表示 ---
nextBtn.onclick = () => {
  current++;
  if (current < quiz.length) showQuestion();
  else showResult();
};

// --- 結果画面 ---
function showResult() {
  quizDiv.classList.add('hidden');
  result.classList.remove('hidden');
  scoreT.textContent = `${score} / ${quiz.length}`;
}

// --- リスタート ---
restart.onclick = () => {
  current = 0; score = 0;
  result.classList.add('hidden');
  quizDiv.classList.remove('hidden');
  showQuestion();
};

// --- 初期化 ---
showQuestion();

