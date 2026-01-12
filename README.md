
<html lang="de">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Visuelle Suche</title>

<style>
body {
  font-family: Arial, sans-serif;
  background: #f2f2f7;
  text-align: center;
  padding: 20px;
}

h1 { color:#333; }

h2 { margin-top: 15px; }
h2 span { color: red; font-weight: bold; }

#arena {
  position: relative;
  width: 300px;
  height: 300px;
  margin: 25px auto;
}

.letter {
  position: absolute;
  font-size: 18px;
  font-weight: bold;
  user-select: none;
}

.red { color: red; }
.blue { color: blue; }

button {
  padding: 10px 24px;
  margin: 12px;
  font-size: 18px;
  cursor: pointer;
  border-radius: 6px;
}

#result {
  display: none;
  font-size: 18px;
  margin-top: 30px;
}
</style>
</head>

<body>

<h1>Visuelle Suche</h1>
<h2 id="question">Ist da ein <span>rotes T</span>?</h2>

<div id="arena"></div>

<div id="buttons">
  <button onclick="answer(true)">Ja</button>
  <button onclick="answer(false)">Nein</button>
</div>

<div id="result"></div>

<script>
const TOTAL_TASKS = 30;

let currentTask = 0;
let score = 0;
let correctPresent = false;
let reactionTimes = [];
let startTime = 0;

const arena = document.getElementById("arena");
const resultDiv = document.getElementById("result");
const question = document.getElementById("question");
const buttons = document.getElementById("buttons");

function freePosition(x, y, used) {
  for (let p of used) {
    if (Math.hypot(p.x - x, p.y - y) < 22) return false;
  }
  return true;
}

function randomPosition(used) {
  for (let i = 0; i < 200; i++) {
    let x = Math.random() * 280;
    let y = Math.random() * 280;
    if (freePosition(x, y, used)) return {x,y};
  }
  return {x:0, y:0};
}

function newTask() {
  arena.innerHTML = "";
  let used = [];
  startTime = performance.now();

  correctPresent = Math.random() < 0.5;

  let distractors = 18 + Math.floor(Math.random() * 8);

  // EIN echtes rotes normales T
  if (correctPresent) {
    let pos = randomPosition(used);
    used.push(pos);

    let t = document.createElement("div");
    t.className = "letter red";
    t.style.left = pos.x + "px";
    t.style.top = pos.y + "px";
    t.style.transform = "rotate(0deg)";
    t.textContent = "T";
    arena.appendChild(t);
  }

  // falsche T
  for (let i = 0; i < distractors; i++) {
    let pos = randomPosition(used);
    used.push(pos);

    let rotate = [90,180,270][Math.floor(Math.random()*3)];
    let color = Math.random() < 0.5 ? "red" : "blue";

    let t = document.createElement("div");
    t.className = "letter " + color;
    t.style.left = pos.x + "px";
    t.style.top = pos.y + "px";
    t.style.transform = `rotate(${rotate}deg)`;
    t.textContent = "T";
    arena.appendChild(t);
  }
}

function answer(userYes) {
  let rt = performance.now() - startTime;
  reactionTimes.push(rt);

  if (userYes === correctPresent) score++;
  currentTask++;

  if (currentTask >= TOTAL_TASKS) finish();
  else newTask();
}

function finish() {
  arena.innerHTML = "";
  question.style.display = "none";
  buttons.style.display = "none";

  let sum = 0;
  for (let rt of reactionTimes) sum += rt;
  let avgRT = Math.round(sum / reactionTimes.length);

  resultDiv.style.display = "block";
  resultDiv.innerHTML = `
    <div style="background:white; padding:20px; border-radius:12px;">
      <h2>Dein Ergebnis</h2>
      <p><strong>${score} von ${TOTAL_TASKS}</strong> Aufgaben richtig</p>
      <p><strong>Ø Reaktionszeit:</strong> ${avgRT} ms</p>
    </div>
  `;
}

newTask();
</script>

</body>
</html>
