<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Wingo Game - Original Demo</title>
<style>
  body { font-family: Arial, sans-serif; background:#f0f0f0; text-align:center; padding:20px; }
  h1 { color:#333; }
  #board { display:flex; justify-content:center; flex-wrap:wrap; max-width:360px; margin:20px auto; }
  .number { width:60px; height:60px; margin:5px; display:flex; justify-content:center; align-items:center; font-weight:bold; font-size:20px; border-radius:8px; color:white; }
  .small { background:#3498db; }
  .big { background:#e74c3c; }
  .violet { background:#8e44ad !important; }
  #history { margin-top:30px; }
  .result { display:inline-block; width:80px; height:40px; margin:3px; line-height:40px; color:white; border-radius:5px; font-weight:bold; }
  #timer { font-size:18px; margin-top:20px; color:#555; }
</style>
</head>
<body>

<h1>Wingo Game Demo</h1>

<div id="board"></div>

<div id="timer">Next round in: <span id="countdown">60</span>s</div>

<h2>Last 10 Results</h2>
<div id="history"></div>

<script>
// --- Game Variables ---
const numbers = [0,1,2,3,4,5,6,7,8,9];
let history = [];
let countdown = 60; // seconds
let sequencePerDay = 0; // last 4 digits
let lastMinute = null;

// --- Render Board ---
const boardDiv = document.getElementById('board');
function renderBoard() {
  boardDiv.innerHTML = '';
  numbers.forEach(n => {
    let div = document.createElement('div');
    div.className = 'number';
    if (n===0 || n===5) div.classList.add('violet');
    else if (n>=1 && n<=4) div.classList.add('small');
    else div.classList.add('big');
    div.innerText = n;
    boardDiv.appendChild(div);
  });
}
renderBoard();

// --- Helper: Pad number with leading zeros ---
function pad(num, size){ return num.toString().padStart(size,'0'); }

// --- Get Period Number ---
function getPeriodNumber(){
  // IST time
  let now = new Date();
  let istOffset = 5.5 * 60; // IST = UTC +5:30
  let utc = now.getTime() + now.getTimezoneOffset()*60000;
  let istTime = new Date(utc + istOffset*60000);

  let year = istTime.getFullYear();
  let month = pad(istTime.getMonth()+1,2);
  let day = pad(istTime.getDate(),2);
  let hour = pad(istTime.getHours(),2);
  let minute = pad(istTime.getMinutes(),2);

  let currentMinute = hour + minute;

  // Check if minute changed -> reset sequence
  if(lastMinute !== currentMinute){
    sequencePerDay = 0;
    lastMinute = currentMinute;
  }

  sequencePerDay++;
  if(sequencePerDay>1440) sequencePerDay=1; // limit 1440 per day

  return `${year}${month}${day}${hour}${minute}${pad(sequencePerDay,4)}`;
}

// --- Update History UI ---
function updateHistoryUI(){
  const historyDiv = document.getElementById('history');
  historyDiv.innerHTML = '';
  history.slice(-10).forEach(item=>{
    let div = document.createElement('div');
    div.className = 'result';
    div.innerText = `${item.period} : ${item.result}`;
    let n = item.result;
    if(n===0 || n===5) div.style.background='#8e44ad';
    else if(n>=1 && n<=4) div.style.background='#3498db';
    else div.style.background='#e74c3c';
    historyDiv.appendChild(div);
  });
}

// --- Draw Result ---
function drawResult(){
  let result = Math.floor(Math.random()*10); // 0-9
  let period = getPeriodNumber();
  history.push({period,result});
  updateHistoryUI();
  console.log(`Round: ${period}, Result: ${result}`);
}

// --- Countdown Timer ---
const countdownSpan = document.getElementById('countdown');
function startCountdown(){
  drawResult(); // initial draw
  setInterval(()=>{
    countdown--;
    if(countdown<=0){
      drawResult();
      countdown=60; // reset
    }
    countdownSpan.innerText = countdown;
  },1000);
}

startCountdown();

</script>

</body>
</html>
