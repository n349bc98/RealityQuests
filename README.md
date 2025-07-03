<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Reality Quest</title>
  <link rel="stylesheet" href="style.css">
</head>
<body class="low-level">
  <!-- Auth Section Start -->
  <div id="auth-section">
    <h2>Login / Register</h2>
    <input type="email" id="email" placeholder="Email" />
    <input type="password" id="password" placeholder="Password" />
    <button onclick="signUp()">Register</button>
    <button onclick="signIn()">Login</button>
    <p id="auth-status"></p>
  </div>
  <!-- Auth Section End -->

  <header>
    <h1>Reality Quest</h1>
    <div class="level-info">
      <span>Level: <strong>1</strong></span>
      <div class="exp-bar"><div class="exp-fill" style="width: 0%;"></div></div>
    </div>
  </header>
  <section class="stats-panel">
    <h2>Stats</h2>
    <div class="stats">
      <div class="stat">FOC: <span>5</span></div>
      <div class="stat">DISC: <span>3</span></div>
      <div class="stat">WILL: <span>4</span></div>
      <div class="stat">KNW: <span>6</span></div>
    </div>
  </section>
  <section class="quest-panel">
    <h2>Daily Quests</h2>
    <ul>
      <li><input type="checkbox" id="q1"> <label for="q1">Workout</label></li>
      <li><input type="checkbox" id="q2"> <label for="q2">Crypto Study</label></li>
      <li><input type="checkbox" id="q3"> <label for="q3">Reading</label></li>
      <li><input type="checkbox" id="q4"> <label for="q4">Journal</label></li>
      <li><input type="checkbox" id="q5"> <label for="q5">Game &lt; 1 hr</label></li>
    </ul>
    <button id="submitQuest">Submit Quests</button>
  </section>
  <section class="inventory">
    <h2>Inventory</h2>
    <ul>
      <li>Common x3</li>
      <li>Uncommon x1</li>
    </ul>
  </section>
  <section class="class-title">
    <h2>Class: Apprentice of Wealth</h2>
    <p>Active Title: Seeker</p>
  </section>
  <section class="mood">
    <h2>Today's Mood</h2>
    <select>
      <option>Happy</option>
      <option selected>Neutral</option>
      <option>Low</option>
      <option>Burnout</option>
    </select>
  </section>
  <footer class="nix-box">
    <p><strong>Nix:</strong> “Let’s get stronger, Eve. One quest at a time.”</p>
  </footer>
  <script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>
  <script src="script.js"></script>
</body>
</html>

const supabaseUrl = 'https://ancjeqqmuzmfdfqdgehl.supabase.co';
const supabaseKey = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6ImFuY2plcXFtdXptZmRmcWRnZWhsIiwicm9sZSI6ImFub24iLCJpYXQiOjE3NTE0NzAwOTAsImV4cCI6MjA2NzA0NjA5MH0.skh1kepa4hLXmjF_mG3HTUvJu1ggEqwy383U5wtJgwY';

const supabase = supabase.createClient(supabaseUrl, supabaseKey);

// Register (Sign Up)
async function signUp() {
  const email = document.getElementById("email").value;
  const password = document.getElementById("password").value;

  const { data, error } = await supabase.auth.signUp({
    email,
    password,
  });

  if (error) {
    document.getElementById("auth-status").textContent = "❌ " + error.message;
  } else {
    document.getElementById("auth-status").textContent = "✅ Register berhasil, cek email untuk verifikasi!";
  }
}

// Login (Sign In)
async function signIn() {
  const email = document.getElementById("email").value;
  const password = document.getElementById("password").value;

  const { data, error } = await supabase.auth.signInWithPassword({
    email,
    password,
  });

  if (error) {
    document.getElementById("auth-status").textContent = "❌ " + error.message;
  } else {
    document.getElementById("auth-status").textContent = "✅ Login berhasil!";
    // Di sini nanti kita bisa panggil function loadUserData()
  }
}

let level = 1;
let exp = 0;
let maxExp = 100;
let totalPoints = 0;

const expBar = document.querySelector('.exp-fill');
const levelDisplay = document.querySelector('.level-info strong');
const body = document.body;
const checkboxes = document.querySelectorAll('.quest-panel input[type="checkbox"]');
const submitButton = document.querySelector('#submitQuest');

submitButton.addEventListener('click', () => {
  let pointsGained = 0;
  checkboxes.forEach((box) => {
    if (box.checked && !box.disabled) {
      pointsGained += 1;
      box.disabled = true;
    }
  });

  if (pointsGained > 0) {
    totalPoints += pointsGained;
    const gainedExp = pointsGained * 5;
    addExp(gainedExp);
  }
});

function addExp(amount) {
  exp += amount;
  while (exp >= maxExp) {
    exp -= maxExp;
    level++;
    maxExp = Math.floor(maxExp * 1.15);
    updateVisualTheme();
  }
  updateUI();
}

function updateUI() {
  const expPercent = (exp / maxExp) * 100;
  expBar.style.width = `${expPercent}%`;
  levelDisplay.textContent = level;
}

function updateVisualTheme() {
  if (level >= 10) {
    body.classList.remove('low-level');
    body.classList.add('high-level');
  } else {
    body.classList.remove('high-level');
    body.classList.add('low-level');
  }
}

updateUI();
updateVisualTheme();

body {
  margin: 0;
  font-family: 'Segoe UI', sans-serif;
  background-color: #0A0A23;
  color: white;
  padding: 20px;
}

h1, h2 {
  text-shadow: 0 0 5px #1E90FF;
}

.low-level {
  --main-color: #1E90FF;
}

.high-level {
  --main-color: #A020F0;
}

#auth-section {
  margin-bottom: 30px;
  padding: 15px;
  border-radius: 10px;
  background: #222;
  box-shadow: 0 0 5px #1E90FF;
  max-width: 400px;
}

#auth-section input[type="email"],
#auth-section input[type="password"] {
  display: block;
  margin-bottom: 10px;
  padding: 8px;
  width: 90%;
  border-radius: 5px;
  border: 1px solid #444;
  background: #181826;
  color: #fff;
}

#auth-section button {
  margin: 3px 2px 10px 0;
  background-color: var(--main-color);
  border: none;
  color: white;
  padding: 8px 16px;
  border-radius: 5px;
  cursor: pointer;
  font-weight: bold;
}
#auth-section button:hover {
  opacity: 0.9;
}
#auth-status {
  min-height: 20px;
}

.level-info, .exp-bar {
  margin-top: 5px;
}

.exp-bar {
  width: 100%;
  height: 10px;
  background-color: #333;
  border-radius: 5px;
}

.exp-fill {
  height: 100%;
  background-color: var(--main-color);
  border-radius: 5px;
  transition: width 0.4s;
}

section {
  margin-top: 20px;
  padding: 15px;
  border: 1px solid #444;
  border-radius: 10px;
  background-color: #111;
  box-shadow: 0 0 10px var(--main-color);
}

.stats .stat {
  margin-bottom: 5px;
}

.quest-panel ul, .inventory ul {
  list-style: none;
  padding: 0;
}

.quest-panel li, .inventory li {
  margin-bottom: 5px;
}

button {
  background-color: var(--main-color);
  border: none;
  color: white;
  padding: 10px 20px;
  border-radius: 6px;
  cursor: pointer;
  font-weight: bold;
}

button:hover {
  opacity: 0.9;
}

select {
  padding: 5px;
  background: #222;
  color: white;
  border: 1px solid #555;
  border-radius: 5px;
}
