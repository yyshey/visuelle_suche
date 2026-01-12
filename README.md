# visuelle_suche

<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Visuelle Suche - Rotes T</title>
<style>
body { font-family: Arial, sans-serif; background:#f0f0f5; text-align:center; padding:20px; }
h1 { color:#333; }
h2 { margin-top:20px; color:red; } /* Frage in Rot */
button { padding:10px 25px; margin:15px; font-size:18px; cursor:pointer; border-radius:5px; }
#result { font-size:20px; font-weight:bold; margin-top:30px; color:#222; }
#letters { position:relative; width:300px; height:300px; margin:20px auto; border:1px solid transparent; }
.letter { position:absolute; font-size:18px; font-weight:bold; user-select:none; }
.red { color:red; }
.blue { color:blue; }
</style>
</head>
<body>
<h1>Interaktiver Visuelle-Suche-Test</h1>
<h2 id="question">Ist da ein rotes T?</h2>
<div id="letters"></div>
<div id="buttons">
  <button onclick="answer(true)">Ja</button>
  <button onclick="answer(false)">Nein</button>
</div>
<div id="result"></div>

<script>
let total = 27;
let current = 0;
let score = 0;
let lettersDiv = document.getElementById('letters');
let resultDiv = document.getElementById('result');
let questionH2 = document.getElementById('question');
let buttonsDiv = document.getElementById('buttons');

function randomRotation(){ return [0,90,180,270][Math.floor(Math.random()*4)]; }
function randomColor(){ return Math.random() < 0.5 ? 'red' : 'blue'; }

// Prüfen, ob Position frei ist (kein Überlappen)
function isValidPosition(x, y, positions, size=20){
  for(let p of positions){
    let dx = x - p.x;
    let dy = y - p.y;
    if(Math.sqrt(dx*dx + dy*dy) < size) return false;
  }
  return true;
}

function randomPosition(existing){
  let maxAttempts = 100;
  for(let i=0;i<maxAttempts;i++){
    let x = Math.random() * 270;
    let y = Math.random() * 270;
    if(isValidPosition(x, y, existing, 20)) return {x:x, y:y};
  }
  return {x: Math.random()*270, y: Math.random()*270};
}

// Neue Aufgabe generieren
function generateTask(){
  let task = [];
  let positions = [];
  let hasRedT = Math.random() < 0.5; // 50% Chance
  let numLetters = 15 + Math.floor(Math.random()*10); // 15-24 T's

  let redInserted = false;
  let redIndex = hasRedT ? Math.floor(Math.random()*numLetters) : -1; // Index für das richtige rote T

  for(let i=0;i<numLetters;i++){
    let rotate = randomRotation();
    let pos = randomPosition(positions);
    positions.push(pos);

    // Nur ein rotes T
    if(i === redIndex){
      task.push({red:true, rotate:0, color:'red', x:pos.x, y:pos.y});
      redInserted = true;
    } else {
      // Falsches T: kann rot oder blau sein, aber kein richtiges rotes T
      let color = Math.random() < 0.5 ? 'blue' : 'red';
      task.push({red:false, rotate:rotate, color:color, x:pos.x, y:pos.y});
    }
  }

  return {task:task, hasRedT:redInserted};
}

// Aufgabe anzeigen
function showTask(){
  if(current >= total){
    lettersDiv.innerHTML = "";
    questionH2.style.display = 'none';
    buttonsDiv.style.display = 'none';
    resultDiv.innerHTML = `Du hast ${score} von ${total} richtig!`;
    return;
  }

  let tdata = generateTask();
  window.currentTask = tdata;
  lettersDiv.innerHTML = "";

  for(let c of tdata.task){
    let span = document.createElement('span');
    span.classList.add('letter');
    span.classList.add(c.red ? 'red' : c.color);
    span.style.transform = `rotate(${c.rotate}deg)`;
    span.style.left = c.x + 'px';
    span.style.top = c.y + 'px';
    span.textContent = 'T';
    lettersDiv.appendChild(span);
  }
}

function answer(userAnswer){
  if(userAnswer === window.currentTask.hasRedT){
    score++;
  }
  current++;
  showTask();
}

// Start
showTask();
</script>
</body>
</html>

