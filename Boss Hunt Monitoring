<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Boss Hunt Scheduler</title>
<style>
  body { font-family: Arial, sans-serif; background: #1e1e1e; color: #fff; padding: 20px; }
  h1 { color: #ff6f61; }
  h2, h3 { margin-top: 20px; }
  table { width: 100%; border-collapse: collapse; margin-top: 10px; }
  th, td { border: 1px solid #555; padding: 8px; text-align: center; }
  input, button, select { padding: 5px; margin: 5px; }
  #current-boss { font-size: 2em; font-weight: bold; color: yellow; text-align: center; margin-bottom: 20px; }
</style>
</head>
<body>

<h1>Boss Hunt Scheduler</h1>
<h2 id="current-boss">No boss respawning now</h2>

<!-- Admin Login -->
<div id="admin-login">
  <h3>Admin Login</h3>
  <label>Username: <input type="text" id="admin-username" /></label>
  <label>Password: <input type="password" id="admin-password" /></label>
  <button onclick="checkAdmin()">Login</button>
</div>

<!-- Admin Panels (Hidden until login) -->
<div id="admin-panels" style="display:none;">
  <div class="admin">
    <h3>Admin 1: Add Boss with Type and Fixed Respawn</h3>
    <label>Boss Type:
      <select id="boss-type">
        <option value="">Select Type</option>
        <option value="Fixed">Fixed</option>
        <option value="Dynamic">Dynamic</option>
      </select>
    </label>
    <label>Boss Name: <input type="text" id="boss-name" placeholder="Boss1" /></label>
    <label>Respawn Duration (hours): <input type="number" id="respawn-hours" placeholder="35" /></label>
    <button onclick="addBoss()">Add Boss</button>
  </div>

  <div class="admin">
    <h3>Admin 2: Log Boss Kill Date & Time</h3>
    <label>Select Boss:
      <select id="boss-select">
        <option value="">Select Boss</option>
      </select>
    </label>
    <label>Killed Date: <input type="date" id="killed-date" /></label>
    <label>Killed Time (HH:MM, PH Time): <input type="time" id="killed-time" /></label>
    <button onclick="logBossKill()">Log Kill</button>
  </div>
</div>

<h2>🕒 Soon to Respawn (<12h)</h2>
<table id="soon-table">
  <thead>
    <tr>
      <th>Logo</th>
      <th>Boss Name</th>
      <th>Type</th>
      <th>Next Respawn (PH Time)</th>
      <th>Time Remaining</th>
    </tr>
  </thead>
  <tbody></tbody>
</table>

<h2>⏳ Upcoming (12–24h)</h2>
<table id="upcoming-table">
  <thead>
    <tr>
      <th>Logo</th>
      <th>Boss Name</th>
      <th>Type</th>
      <th>Next Respawn (PH Time)</th>
      <th>Time Remaining</th>
    </tr>
  </thead>
  <tbody></tbody>
</table>

<h2>📊 Dynamic Bosses</h2>
<table id="dynamic-table">
  <thead>
    <tr>
      <th>Logo</th>
      <th>Boss Name</th>
      <th>Next Respawn (PH Time)</th>
      <th>Time Remaining</th>
    </tr>
  </thead>
  <tbody></tbody>
</table>

<h2>📌 Fixed Bosses</h2>
<table id="fixed-table">
  <thead>
    <tr>
      <th>Logo</th>
      <th>Boss Name</th>
      <th>Next Respawn (PH Time)</th>
      <th>Time Remaining</th>
    </tr>
  </thead>
  <tbody></tbody>
</table>

<h2>🟡 Still Alive</h2>
<table id="still-alive-table">
  <thead>
    <tr>
      <th>Logo</th>
      <th>Boss Name</th>
      <th>Type</th>
    </tr>
  </thead>
  <tbody></tbody>
</table>

<script>
const ADMIN_CREDENTIALS = { username: "RonJayvee", password: "mypassword123" };
let bosses = {}; // {name: {type, respawnHours, lastKilled}}

// Admin login
function checkAdmin(){
  const username = document.getElementById('admin-username').value;
  const password = document.getElementById('admin-password').value;

  if(username === ADMIN_CREDENTIALS.username && password === ADMIN_CREDENTIALS.password){
    document.getElementById('admin-login').style.display = 'none';
    document.getElementById('admin-panels').style.display = 'block';
    setDefaultKilledDate(); // set default date when admin logs in
  } else {
    alert("Incorrect admin credentials!");
  }
}

// Admin 1: Add Boss
function addBoss() {
  const type = document.getElementById('boss-type').value;
  const name = document.getElementById('boss-name').value.trim();
  const hours = parseFloat(document.getElementById('respawn-hours').value);
  if(!type || !name || isNaN(hours) || hours <= 0) { 
    alert('Enter valid type, name, and hours!'); 
    return; 
  }

  bosses[name] = { type: type, respawnHours: hours, lastKilled: null };
  updateBossDropdown();

  // Immediately add to Fixed/Dynamic table if no kill logged
  const tableBody = type === 'Fixed' ? document.getElementById('fixed-table').querySelector('tbody') : 
                                       document.getElementById('dynamic-table').querySelector('tbody');
  const row = document.createElement('tr');
  const icon = type === 'Fixed' ? '📌' : '📊';
  row.id = `row-${name}`;
  row.innerHTML = `<td>${icon}</td><td>${name}</td><td>-</td><td>-</td>`;
  tableBody.appendChild(row);

  updateTables(); 
}

// Admin 2: Log Boss Kill
function updateBossDropdown() {
  const select = document.getElementById('boss-select');
  select.innerHTML = '<option value="">Select Boss</option>';
  Object.keys(bosses).forEach(name=>{
    const opt = document.createElement('option'); opt.value=name; opt.textContent=name;
    select.appendChild(opt);
  });
}

function logBossKill() {
  const name = document.getElementById('boss-select').value;
  const dateInput = document.getElementById('killed-date').value;
  const timeInput = document.getElementById('killed-time').value;
  if(!name || !dateInput || !timeInput){ alert('Select boss, date, and time!'); return; }

  const [year, month, day] = dateInput.split('-').map(Number);
  const [hh, mm] = timeInput.split(':').map(Number);
  const killedAt = new Date(year, month-1, day, hh, mm, 0, 0);
  bosses[name].lastKilled = killedAt;
  updateTables();
}

// Set default killed date to today
function setDefaultKilledDate(){
  const now = new Date();
  const year = now.getFullYear();
  const month = (now.getMonth()+1).toString().padStart(2,'0');
  const day = now.getDate().toString().padStart(2,'0');
  const today = `${year}-${month}-${day}`;
  document.getElementById('killed-date').value = today;
}

// Boss logic
function getNextSpawn(boss){
  if(!boss.lastKilled) return null;
  return new Date(boss.lastKilled.getTime() + boss.respawnHours*3600000);
}

function formatTime(ms){
  if(ms<0) ms=0;
  const totalSec = Math.floor(ms/1000);
  const h = Math.floor(totalSec/3600);
  const m = Math.floor((totalSec%3600)/60);
  const s = totalSec%60;
  return `${h.toString().padStart(2,'0')}:${m.toString().padStart(2,'0')}:${s.toString().padStart(2,'0')}`;
}

function updateTables(){
  const soonBody = document.getElementById('soon-table').querySelector('tbody');
  const upcomingBody = document.getElementById('upcoming-table').querySelector('tbody');
  const fixedBody = document.getElementById('fixed-table').querySelector('tbody');
  const dynamicBody = document.getElementById('dynamic-table').querySelector('tbody');
  const stillBody = document.getElementById('still-alive-table').querySelector('tbody');

  soonBody.innerHTML = ''; upcomingBody.innerHTML = ''; fixedBody.innerHTML = ''; dynamicBody.innerHTML = ''; stillBody.innerHTML = '';

  const nowPH = new Date();

  Object.keys(bosses).forEach(name=>{
    const boss = bosses[name];
    const nextSpawn = getNextSpawn(boss);
    const remainingMs = nextSpawn ? nextSpawn - nowPH : null;
    const remainingHrs = remainingMs ? remainingMs/3600000 : null;

    let color = '';
    if(remainingHrs !== null){
      if(remainingHrs <= 12 && remainingHrs > 0) {
        const percent = remainingHrs/12;
        const r = 255;
        const g = Math.floor(255 * percent);
        const b = 0;
        color = `rgb(${r},${g},${b})`;
      } else if(remainingHrs > 12 && remainingHrs <= 24) {
        color = 'lightblue';
      }
    }

    if(remainingHrs !== null && remainingHrs <= 12 && remainingHrs > 0){
      const row = document.createElement('tr'); row.style.background = color;
      row.innerHTML = `<td>🕒</td><td>${name}</td><td>${boss.type}</td><td>${nextSpawn.toLocaleString('en-US',{timeZone:'Asia/Manila'})}</td><td>${formatTime(remainingMs)}</td>`;
      soonBody.appendChild(row);
    } else if(remainingHrs !== null && remainingHrs > 12 && remainingHrs <= 24){
      const row = document.createElement('tr'); row.style.background = color;
      row.innerHTML = `<td>⏳</td><td>${name}</td><td>${boss.type}</td><td>${nextSpawn.toLocaleString('en-US',{timeZone:'Asia/Manila'})}</td><td>${formatTime(remainingMs)}</td>`;
      upcomingBody.appendChild(row);
    } else if(boss.lastKilled){ 
      const typeRow = document.createElement('tr');
      const icon = boss.type==='Fixed' ? '📌' : '📊';
      typeRow.innerHTML = `<td>${icon}</td><td>${name}</td><td>${nextSpawn.toLocaleString('en-US',{timeZone:'Asia/Manila'})}</td><td>${formatTime(remainingMs)}</td>`;
      if(boss.type==='Fixed') fixedBody.appendChild(typeRow);
      else dynamicBody.appendChild(typeRow);
    } else { 
      const aliveRow = document.createElement('tr');
      aliveRow.innerHTML = `<td>🟡</td><td>${name}</td><td>${boss.type}</td>`;
      stillBody.appendChild(aliveRow);
    }
  });

  updateCurrentBoss();
}

function updateCurrentBoss() {
  const nowPH = new Date();
  const respawning = Object.keys(bosses).find(name=>{
    const boss = bosses[name];
    const nextSpawn = getNextSpawn(boss);
    return nextSpawn && nextSpawn <= nowPH && nextSpawn > new Date(nowPH-1000*60*5);
  });
  const header = document.getElementById('current-boss');
  if(respawning){
    header.textContent = `Boss Respawning Now: ${respawning}`;
    header.style.color = 'yellow';
    header.style.fontSize = '2.5em';
  } else {
    header.textContent = 'No boss respawning now';
    header.style.color = 'white';
    header.style.fontSize = '2em';
  }
}

setInterval(updateTables, 1000);
</script>
</body>
</html>
