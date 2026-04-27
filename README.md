# Baseeeee<!DOCTYPE html>
<html>
<head>
  <title>JERE Exchange</title>
  <meta name="viewport" content="width=device-width, initial-scale=1">

  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

  <style>
    body {
      margin: 0;
      font-family: 'Segoe UI', sans-serif;
      background: #020617;
      color: #e2e8f0;
    }

    .hidden { display:none; }

    #login {
      display:flex;
      flex-direction:column;
      align-items:center;
      justify-content:center;
      height:100vh;
    }

    .login-box {
      background:#0f172a;
      padding:30px;
      border-radius:12px;
      width:300px;
      text-align:center;
    }

    input {
      width:100%;
      padding:10px;
      margin:10px 0;
      border:none;
      border-radius:6px;
      background:#1e293b;
      color:white;
    }

    button {
      width:100%;
      padding:10px;
      margin-top:10px;
      border:none;
      border-radius:6px;
      cursor:pointer;
      font-weight:bold;
    }

    .btn-primary { background:#22c55e; color:black; }
    .btn-secondary { background:#334155; color:white; }

    .topbar {
      background:#0f172a;
      padding:15px;
      display:flex;
      justify-content:space-between;
    }

    .container { display:flex; }

    .sidebar {
      width:220px;
      background:#020617;
      border-right:1px solid #1e293b;
      padding:20px;
    }

    .menu-item {
      padding:10px;
      margin:10px 0;
      cursor:pointer;
      border-radius:6px;
    }

    .menu-item:hover { background:#1e293b; }

    .main { flex:1; padding:20px; }

    .card {
      background:#0f172a;
      padding:20px;
      border-radius:12px;
      margin-bottom:15px;
    }

    .grid {
      display:grid;
      grid-template-columns:repeat(auto-fit,minmax(200px,1fr));
      gap:15px;
    }

    .green { color:#22c55e; }
  </style>
</head>

<body>

<!-- LOGIN -->
<div id="login">
  <div class="login-box">
    <h2>🚀 JERE Exchange</h2>

    <input id="username" placeholder="Username">
    <input id="password" type="password" placeholder="Password">

    <button class="btn-primary" onclick="loginUser()">Login</button>
    <button class="btn-secondary" onclick="register()">Register</button>
  </div>
</div>

<!-- APP -->
<div id="app" class="hidden">

  <div class="topbar">
    <span>👋 <span id="user"></span></span>
    <button onclick="logout()">Logout</button>
  </div>

  <div class="container">

    <div class="sidebar">
      <h3>💰 JERE</h3>
      <div class="menu-item" onclick="show('dashboard')">Dashboard</div>
      <div class="menu-item" onclick="show('trade')">Trade</div>
      <div class="menu-item" onclick="show('chart')">Chart</div>
      <div class="menu-item" onclick="show('wallet')">Wallet</div>
    </div>

    <div class="main">

      <!-- DASHBOARD -->
      <div id="dashboard">
        <div class="grid">
          <div class="card">
            <h3>Balance</h3>
            <h2>$<span id="balance"></span></h2>
          </div>

          <div class="card">
            <h3>BTC Price</h3>
            <h2>$<span id="btcPrice"></span></h2>
          </div>

          <div class="card">
            <h3>Status</h3>
            <h2 class="green">Active</h2>
          </div>
        </div>
      </div>

      <!-- TRADE -->
      <div id="trade" class="hidden">
        <div class="card">
          <h2>Trade BTC</h2>
          <button class="btn-primary" onclick="buy()">Buy $100</button>
          <button class="btn-secondary" onclick="sell()">Sell $100</button>
          <p id="tradeMsg"></p>
        </div>
      </div>

      <!-- CHART -->
      <div id="chart" class="hidden">
        <div class="card">
          <h2>Market Chart</h2>
          <canvas id="chartCanvas"></canvas>
        </div>
      </div>

      <!-- WALLET -->
      <div id="wallet" class="hidden">
        <div class="card">
          <h2>Wallet</h2>
          <p>Balance: $<span id="walletBalance"></span></p>
          <button onclick="deposit()">Deposit</button>
          <button onclick="withdraw()">Withdraw</button>
        </div>
      </div>

    </div>
  </div>
</div>

<script>
let balance = Number(localStorage.getItem("balance")) || 1000;

// REGISTER
function register() {
  let u = document.getElementById("username").value;
  let p = document.getElementById("password").value;

  if (!u || !p) return alert("Fill all fields");

  localStorage.setItem("user", u);
  localStorage.setItem("pass", btoa(p));

  alert("Account created!");
}

// LOGIN
function loginUser() {
  let u = document.getElementById("username").value;
  let p = document.getElementById("password").value;

  if (
    u === localStorage.getItem("user") &&
    btoa(p) === localStorage.getItem("pass")
  ) {
    startApp();
  } else {
    alert("Wrong details");
  }
}

// START
function startApp() {
  document.getElementById("login").style.display = "none";
  document.getElementById("app").classList.remove("hidden");

  document.getElementById("user").innerText = localStorage.getItem("user");

  updateBalance();
  loadPrice();
  loadChart();
}

// NAV
function show(page) {
  ["dashboard","trade","chart","wallet"].forEach(id => {
    document.getElementById(id).classList.add("hidden");
  });
  document.getElementById(page).classList.remove("hidden");
}

// BALANCE
function updateBalance() {
  document.getElementById("balance").innerText = balance;
  document.getElementById("walletBalance").innerText = balance;
  localStorage.setItem("balance", balance);
}

// ACTIONS
function deposit() {
  balance += 100;
  updateBalance();
}

function withdraw() {
  if (balance >= 50) balance -= 50;
  updateBalance();
}

function buy() {
  if (balance >= 100) {
    balance -= 100;
    document.getElementById("tradeMsg").innerText = "Bought BTC";
  } else {
    document.getElementById("tradeMsg").innerText = "Not enough balance";
  }
  updateBalance();
}

function sell() {
  balance += 100;
  document.getElementById("tradeMsg").innerText = "Sold BTC";
  updateBalance();
}

// PRICE
function loadPrice() {
  fetch("https://api.coingecko.com/api/v3/simple/price?ids=bitcoin&vs_currencies=usd")
    .then(r => r.json())
    .then(d => {
      document.getElementById("btcPrice").innerText = d.bitcoin.usd;
    });
}

// CHART
function loadChart() {
  fetch("https://api.coingecko.com/api/v3/coins/bitcoin/market_chart?vs_currency=usd&days=7")
    .then(r => r.json())
    .then(d => {
      let prices = d.prices.map(p => p[1]);

      new Chart(document.getElementById("chartCanvas"), {
        type: "line",
        data: {
          labels: prices.map((_, i) => i),
          datasets: [{ data: prices, label: "BTC" }]
        }
      });
    });
}

// LOGOUT
function logout() {
  localStorage.clear();
  location.reload();
}

// AUTO LOGIN
window.onload = function () {
  if (localStorage.getItem("user")) startApp();
};
</script>

</body>
</html>
