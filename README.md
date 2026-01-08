// # corruptedhacking6446.github.io
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Clicker Game - Coin Click</title>
  <style>
    :root{--bg:#071026;--card:#081425;--accent:#FFD54A;--muted:#9FB1C8}
    *{box-sizing:border-box}
    html,body{height:100%;margin:0;font-family:Inter,system-ui,Arial,Helvetica}
    body{background:linear-gradient(180deg,#041025 0%,#071026 100%);color:#eaf3ff;display:flex;align-items:center;justify-content:center;padding:24px}
    .app{width:100%;max-width:920px}
    .card{background:linear-gradient(180deg,var(--card),#051222);padding:18px;border-radius:14px;box-shadow:0 14px 40px rgba(2,6,23,0.6)}
    header{display:flex;justify-content:space-between;align-items:center;margin-bottom:14px}
    h1{margin:0;font-size:18px}

    .layout{display:grid;grid-template-columns:1fr 320px;gap:16px}
    @media (max-width:900px){.layout{grid-template-columns:1fr}}

    /* Coin button */
    .coin-wrap{display:flex;flex-direction:column;gap:12px;align-items:center}
    .coin{width:220px;height:220px;border-radius:50%;display:flex;align-items:center;justify-content:center;cursor:pointer;user-select:none;transform-origin:center;transition:transform 140ms cubic-bezier(.2,.9,.2,1)}
    .coin svg{width:80%;height:80%;display:block}
    .coin:active{transform:scale(0.96)}

    /* glow & shine */
    .coin{background:radial-gradient(circle at 30% 25%, #fff3c6 0%, #ffd54a 25%, #e6a800 60%, #b36b00 100%);box-shadow:0 18px 40px rgba(255,181,59,0.14), inset 0 -12px 36px rgba(0,0,0,0.18)}
    .score{font-size:28px;font-weight:700}
    .meta{color:var(--muted);font-size:13px}

    /* particle container */
    .particles{position:relative;width:220px;height:220px;pointer-events:none}
    .particle{position:absolute;width:10px;height:10px;border-radius:50%;opacity:0;transform:translate(-50%,-50%)}

    /* shop / info */
    .panel{display:flex;flex-direction:column;gap:10px}
    .btn{background:linear-gradient(180deg,#12324a,#0b2536);border:none;padding:8px 10px;border-radius:8px;color:#dff1ff;cursor:pointer}
  </style>
</head>
<body>
  <div class="app">
    <div class="card">
      <header>
        <h1>Coin Clicker</h1>
        <div class="meta">Click the coin or press <kbd>Space</kbd> to earn points</div>
      </header>

      <div class="layout">
        <main class="coin-wrap">
          <div id="coinArea" class="coin" title="Click for points">
            <!-- simple SVG coin -->
            <svg viewBox="0 0 100 100" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
              <defs>
                <radialGradient id="g" cx="30%" cy="25%">
                  <stop offset="0%" stop-color="#fff9d6"/>
                  <stop offset="40%" stop-color="#ffd54a"/>
                  <stop offset="100%" stop-color="#c67f00"/>
                </radialGradient>
              </defs>
              <circle cx="50" cy="50" r="44" fill="url(#g)" stroke="#b87b00" stroke-width="3" />
              <circle cx="50" cy="50" r="30" fill="rgba(255,255,255,0.06)" />
              <text x="50" y="56" font-size="28" font-weight="700" text-anchor="middle" fill="#5a2f00">¢</text>
            </svg>
            <div class="particles" id="particles"></div>
          </div>

          <div class="score" id="scoreDisplay">0</div>
          <div class="meta">Total clicks: <span id="clickCount">0</span></div>
        </main>

        <aside class="panel">
  <div class="card" style="padding:12px">
    <div style="font-weight:700;font-size:18px;margin-bottom:6px">Shop</div>
    <div id="shop" class="shop-container"></div>

<script>
// --- GAME STATE ---
let points = 0;
let cps = 0;
let clicks = 0;

// --- UI ELEMENTS ---
const scoreDisplay = document.getElementById("scoreDisplay");
const clickCount = document.getElementById("clickCount");
const coinArea = document.getElementById("coinArea");

function updateUI() {
  scoreDisplay.textContent = Math.floor(points);
  clickCount.textContent = clicks;
}

// --- SAVE / LOAD ---
function saveGame() {
  localStorage.setItem("coinSave", JSON.stringify({points,cps,clicks,shopItems}));
}
function loadGame() {
  const data = JSON.parse(localStorage.getItem("coinSave"));
  if (!data) return;
  points = data.points;
  cps = data.cps;
  clicks = data.clicks;
  shopItems = data.shopItems;
  updateUI();
}

// --- CLICK COIN ---
coinArea.addEventListener("click", ()=>{
  points++;
  clicks++;
  updateUI();
  saveGame();
});

// --- SHOP ---
let shopItems = [
  { id:"cursor", name:"Cursor", baseCost:10, level:0, cps:0.1 },
  { id:"farm", name:"Farm", baseCost:100, level:0, cps:1 },
  { id:"factory", name:"Factory", baseCost:1000, level:0, cps:10 },
  { id:"bank", name:"Bank", baseCost:8000, level:0, cps:40 },
  { id:"lab", name:"Lab", baseCost:20000, level:0, cps:150 },
  { id:"mine", name:"Gold Mine", baseCost:75000, level:0, cps:500 },
  { id:"city", name:"Coin City", baseCost:250000, level:0, cps:2000 },
  { id:"portal", name:"Time Portal", baseCost:1500000, level:0, cps:12000 },
  { id:"planet", name:"Coin Planet", baseCost:10000000, level:0, cps:90000 },
  { id:"universe", name:"Coin Universe", baseCost:75000000, level:0, cps:750000 }
];

function generateShop(){
  const shop = document.getElementById("shop");
  shop.innerHTML = "";

  shopItems.forEach(item=>{
    const cost = Math.floor(item.baseCost * Math.pow(1.15,item.level));
    const div = document.createElement("div");
    div.className = "shop-item";
    div.style.padding = "6px";
    div.style.border = "1px solid #12324a";
    div.style.marginBottom = "6px";
    div.style.borderRadius = "6px";
    div.innerHTML = `<strong>${item.name}</strong> (Lv. ${item.level})<br>Cost: ${cost}<br>+${item.cps} CPS`;
    div.onclick = ()=>buyItem(item.id);
    shop.appendChild(div);
  });
}

function buyItem(id){
  const item = shopItems.find(i=>i.id===id);
  const cost = Math.floor(item.baseCost * Math.pow(1.15,item.level));
  if(points>=cost){
    points-=cost;
    cps+=item.cps;
    item.level++;
    updateUI();
    generateShop();
    saveGame();
  }
}

// --- PASSIVE INCOME ---
setInterval(()=>{
  points += cps;
  updateUI();
  saveGame();
},1000);

// --- STARTUP ---
loadGame();
generateShop();
</script>
</html>
