# Waon-
和音信号クイズ
# スマホ対応版の和音モールス信号クイズHTMLをファイルに保存します

html_code_mobile = """<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>和音モールス信号クイズ</title>
  <style>
    body {
      font-family: sans-serif;
      text-align: center;
      margin: 10px;
      padding: 0 10px;
      background: #222;
      color: #eee;
    }
    #signal {
      width: 150px;
      height: 150px;
      margin: 20px auto;
      border-radius: 50%;
      background-color: black;
      box-shadow: 0 0 20px 5px black;
      transition: background-color 0.2s ease;
    }
    label {
      display: inline-block;
      margin: 10px 15px;
      font-size: 1.1em;
    }
    input[type="number"],
    input[type="text"] {
      font-size: 1.2em;
      padding: 5px 8px;
      width: 60px;
      border-radius: 5px;
      border: none;
      text-align: center;
    }
    button {
      font-size: 1.3em;
      padding: 10px 20px;
      margin: 15px 5px;
      border-radius: 8px;
      border: none;
      background-color: #0066cc;
      color: white;
      cursor: pointer;
      box-shadow: 0 4px 8px rgba(0,0,0,0.3);
      transition: background-color 0.3s ease;
    }
    button:hover {
      background-color: #004a99;
    }
    #quizArea > div {
      margin: 12px 0;
      font-size: 1.3em;
    }
    #quizArea input[type="text"] {
      width: 40px;
      font-size: 1.5em;
    }
    #submitBtn.hidden {
      display: none;
    }
    table {
      margin: 15px auto;
      border-collapse: collapse;
      width: 90%;
      max-width: 400px;
      color: white;
    }
    th, td {
      border: 1px solid #ddd;
      padding: 8px 12px;
    }
    th {
      background-color: #004a99;
    }
    tr:nth-child(even) {
      background-color: #333;
    }
    tr:nth-child(odd) {
      background-color: #222;
    }
    @media (max-width: 480px) {
      #signal {
        width: 120px;
        height: 120px;
      }
      button {
        width: 100%;
        font-size: 1.4em;
      }
      input[type="number"], input[type="text"] {
        width: 50px;
        font-size: 1.4em;
      }
    }
  </style>
</head>
<body>
  <h1>和音モールス信号クイズ</h1>
  <label>問題数: <input type="number" id="questionCount" value="5" min="1" max="20" /></label>
  <label>間隔（秒）: <input type="number" id="intervalSec" value="3" min="1" /></label>
  <br />
  <button id="startBtn" onclick="startQuiz()">スタート</button>

  <div id="signal" aria-label="モールス信号の光表示"></div>
  <div id="quizArea"></div>
  <button id="submitBtn" class="hidden" onclick="checkAnswers()">答え合わせ</button>
  <div id="resultArea"></div>

  <script>
    const morseMap = {
      "あ": "－－・－－", "い": "・－", "う": "・・－", "え": "・－・・・", "お": "・－・・－",
      "か": "・－・・", "き": "－・－・・", "く": "・・・－", "け": "－・－－・", "こ": "－－－－",
      "さ": "－・－・－", "し": "－－・－・", "す": "－－－・－", "せ": "・－－－・", "そ": "－－－・",
      "た": "・－・", "ち": "・・－・", "つ": "・－－・", "て": "・－・－－", "と": "・－－・・",
      "な": "・－・", "に": "－・－・", "ぬ": "・－－・－", "ね": "－－・－", "の": "・－－・",
      "は": "－・・・", "ひ": "－－・・－", "ふ": "－－・・", "へ": "・", "ほ": "－・・",
      "ま": "－・・－", "み": "・・－・－", "む": "－・", "め": "・－－・・", "も": "－・・－・",
      "や": "・－－", "ゆ": "－・・－－", "よ": "－－",
      "ら": "・・・", "り": "－－・", "る": "－・－－", "れ": "－－－", "ろ": "・－・－",
      "わ": "－・－", "を": "・－－－", "ん": "・－・－・"
    };

    const dotColor = "white";
    const dashColor = "red";
    const offColor = "black";
    const dotTime = 250;
    const dashTime = 750;
    const spaceTime = 250;

    let quiz = [];
    let userAnswers = [];

    function sleep(ms) {
      return new Promise(resolve => setTimeout(resolve, ms));
    }

    async function flashMorse(code) {
      const signal = document.getElementById("signal");
      for (let mark of code) {
        if (mark === "・") {
          signal.style.backgroundColor = dotColor;
          await sleep(dotTime);
        } else if (mark === "－") {
          signal.style.backgroundColor = dashColor;
          await sleep(dashTime);
        }
        signal.style.backgroundColor = offColor;
        await sleep(spaceTime);
      }
    }

    async function startQuiz() {
      const startBtn = document.getElementById("startBtn");
      startBtn.disabled = true;

      const count = parseInt(document.getElementById("questionCount").value);
      const interval = parseInt(document.getElementById("intervalSec").value) * 1000;
      const keys = Object.keys(morseMap);
      quiz = [];
      userAnswers = [];

      document.getElementById("quizArea").innerHTML = "";
      document.getElementById("resultArea").innerHTML = "";
      document.getElementById("submitBtn").classList.add("hidden");

      for (let i = 0; i < count; i++) {
        const randomChar = keys[Math.floor(Math.random() * keys.length)];
        quiz.push(randomChar);

        await flashMorse(morseMap[randomChar]);

        const qDiv = document.createElement("div");
        qDiv.innerHTML = `問題${i + 1}: <input type="text" maxlength="1" data-index="${i}" autocomplete="off" autocorrect="off" autocapitalize="off" spellcheck="false" />`;
        document.getElementById("quizArea").appendChild(qDiv);

        await sleep(interval);
      }

      document.getElementById("submitBtn").classList.remove("hidden");
      startBtn.disabled = false;
    }

    function checkAnswers() {
      const inputs = document.querySelectorAll("#quizArea input");
      let correct = 0;
      let resultHTML = "<h2>結果</h2><table>";

      resultHTML += "<tr><th>問題</th><th>正解</th><th>あなたの答え</th><th>結果</th></tr>";
      inputs.forEach((input, idx) => {
        const userInput = input.value.trim();
        const correctChar = quiz[idx];
        const isCorrect = userInput === correctChar;
        if (isCorrect) correct++;

        resultHTML += `<tr>
          <td>${idx + 1}</td>
          <td>${correctChar}</td>
          <td>${userInput || "（空白）"}</td>
          <td style="color:${isCorrect ? "lightgreen" : "red"}">${isCorrect ? "○" : "×"}</td>
        </tr>`;
      });

      resultHTML += `</table><p>正解数: ${correct} / ${quiz.length}</p>`;
      document.getElementById("resultArea").innerHTML = resultHTML;
    }
  </script>
</body>
</html>
"""

file_path_mobile = "/mnt/data/waon_morse_quiz_mobile.html"
with open(file_path_mobile, "w", encoding="utf-8") as f:
    f.write(html_code_mobile)

file_path_mobile
