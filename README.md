<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Café Chess</title>
<link href="https://fonts.googleapis.com/css2?family=Playfair+Display:ital,wght@0,500;0,700;1,400&family=Lato:wght@300;400;700&display=swap" rel="stylesheet">
<style>
  :root {
    --panel:    #4a2810; --panel2:   #3d2008;
    --border:   rgba(200,160,100,0.18);
    --latte:    #c8a882; --cream:    #f0e0c8; --foam:     #fdf6ee;
    --caramel:  #c07840; --caramel2: #a06030;
    --light-sq: #e8d5b0; --dark-sq:  #8b5530;
    --highlight:rgba(210,170,60,0.5); --move:rgba(60,160,80,0.5); --check:rgba(210,50,50,0.55);
  }
  *{margin:0;padding:0;box-sizing:border-box;}
  body{background:radial-gradient(ellipse at 40% 0%,#5a2e12 0%,#2a1206 55%,#1a0a04 100%);min-height:100vh;display:flex;flex-direction:column;align-items:center;font-family:'Lato',sans-serif;color:var(--cream);}

  header{text-align:center;padding:22px 0 16px;}
  h1{font-family:'Playfair Display',serif;font-size:48px;font-weight:700;color:var(--foam);text-shadow:0 2px 16px rgba(192,120,64,0.35),0 1px 0 rgba(0,0,0,0.6);letter-spacing:1px;line-height:1;}
  .subtitle{font-size:11px;letter-spacing:5px;text-transform:uppercase;color:var(--latte);margin-top:5px;font-weight:300;opacity:0.8;}

  .game-wrap{display:flex;gap:20px;align-items:flex-start;padding:0 20px 40px;width:100%;max-width:1020px;}
  .sidebar{width:210px;flex-shrink:0;display:flex;flex-direction:column;gap:10px;}
  .panel{background:linear-gradient(160deg,var(--panel),var(--panel2));border:1px solid var(--border);border-radius:16px;padding:14px 16px;}
  .panel-label{font-size:10px;font-weight:700;letter-spacing:2px;text-transform:uppercase;color:var(--caramel);margin-bottom:10px;display:flex;align-items:center;gap:5px;}

  .mode-row{display:flex;gap:6px;}
  .mode-btn{flex:1;padding:7px 0;background:rgba(0,0,0,0.25);border:1px solid rgba(255,255,255,0.07);border-radius:10px;color:var(--latte);font-family:'Lato',sans-serif;font-size:11px;font-weight:700;letter-spacing:0.5px;text-transform:uppercase;cursor:pointer;transition:all 0.18s;}
  .mode-btn.active{background:var(--caramel2);border-color:var(--caramel);color:var(--foam);}
  .mode-btn:not(.active):hover{background:rgba(192,120,64,0.15);}

  .diff-grid{display:grid;grid-template-columns:1fr 1fr;gap:5px;margin-bottom:6px;}
  .diff-btn{padding:6px 4px;text-align:center;background:rgba(0,0,0,0.2);border:1px solid rgba(255,255,255,0.07);border-radius:9px;color:var(--latte);font-family:'Lato',sans-serif;font-size:11.5px;font-weight:700;cursor:pointer;transition:all 0.18s;}
  .diff-btn.active{background:var(--caramel2);border-color:var(--caramel);color:var(--foam);}
  .diff-btn:not(.active):hover{background:rgba(192,120,64,0.15);}
  .lose-btn{width:100%;padding:7px;background:rgba(100,20,10,0.4);border:1px solid rgba(200,80,60,0.3);border-radius:10px;color:#e8a090;font-family:'Lato',sans-serif;font-size:11.5px;font-weight:700;cursor:pointer;transition:all 0.18s;letter-spacing:0.5px;}
  .lose-btn.active{background:linear-gradient(135deg,#7a2010,#a03020);border-color:#c05040;color:#ffc0b0;}
  .lose-btn:not(.active):hover{background:rgba(140,30,20,0.5);}
  #diff-desc{font-size:10.5px;color:rgba(200,168,130,0.65);line-height:1.5;margin-top:8px;font-style:italic;}

  #status{font-size:14px;color:var(--cream);line-height:1.5;min-height:36px;}
  #status .turn-dot{display:inline-block;width:9px;height:9px;border-radius:50%;border:2px solid var(--latte);margin-right:6px;vertical-align:middle;}
  #status .turn-dot.black{background:#111;} #status .turn-dot.white{background:var(--foam);}
  .ai-thinking{font-size:11px;color:var(--caramel);font-style:italic;margin-top:4px;min-height:15px;animation:pulse 1.4s ease-in-out infinite;}
  @keyframes pulse{0%,100%{opacity:1}50%{opacity:0.3}}

  .captured-label{font-size:10px;color:rgba(200,160,100,0.55);margin-bottom:3px;}
  .captured-display{display:flex;flex-wrap:wrap;gap:1px;min-height:20px;font-size:16px;line-height:1;margin-bottom:7px;}

  .move-list{font-size:12px;max-height:160px;overflow-y:auto;scrollbar-width:thin;scrollbar-color:var(--caramel2) transparent;font-family:'Courier New',monospace;}
  .move-list::-webkit-scrollbar{width:3px;} .move-list::-webkit-scrollbar-thumb{background:var(--caramel2);border-radius:2px;}
  .move-row{display:grid;grid-template-columns:22px 1fr 1fr;gap:4px;padding:2px 0;border-bottom:1px solid rgba(255,255,255,0.04);color:var(--latte);}
  .move-row .mn{color:var(--caramel);font-weight:700;}

  .action-btn{width:100%;padding:9px 0;background:linear-gradient(160deg,#5a3018,#3a1e0a);border:1px solid var(--border);border-radius:11px;color:var(--latte);font-family:'Lato',sans-serif;font-size:12px;font-weight:700;letter-spacing:1.5px;text-transform:uppercase;cursor:pointer;transition:all 0.18s;margin-bottom:6px;}
  .action-btn:hover{background:linear-gradient(160deg,var(--caramel2),#7a4020);color:var(--foam);border-color:var(--caramel);transform:translateY(-1px);}
  .action-btn:active{transform:none;}

  .board-wrap{flex:1;display:flex;flex-direction:column;align-items:center;}
  .board-frame{background:linear-gradient(150deg,#6a3c1a,#3e2008);padding:18px;border-radius:20px;border:1.5px solid rgba(200,150,80,0.3);box-shadow:0 0 0 1px rgba(255,255,255,0.03),0 12px 50px rgba(0,0,0,0.7),inset 0 1px 0 rgba(255,255,255,0.06);}

  .coords-wrap{display:grid;grid-template-columns:18px 1fr 18px;grid-template-rows:18px 1fr 18px;}
  .coord-side{display:flex;flex-direction:column;justify-content:space-around;}
  .coord-bottom{display:flex;flex-direction:row;justify-content:space-around;}
  .coord{font-size:10px;color:rgba(200,168,130,0.45);font-weight:700;width:60px;text-align:center;}
  .coord-side .coord{height:60px;width:18px;display:flex;align-items:center;justify-content:center;}

  .board-container{position:relative;width:480px;height:480px;border-radius:4px;overflow:hidden;}
  #board{display:grid;grid-template-columns:repeat(8,60px);grid-template-rows:repeat(8,60px);width:480px;height:480px;}
  #piece-layer{position:absolute;top:0;left:0;width:480px;height:480px;pointer-events:none;}

  .sq{width:60px;height:60px;cursor:pointer;position:relative;user-select:none;transition:filter 0.1s;}
  .sq.light{background:var(--light-sq);} .sq.dark{background:var(--dark-sq);}
  .sq.selected{background:var(--highlight)!important;}
  .sq.last-from{background:rgba(210,170,60,0.22)!important;} .sq.last-to{background:rgba(210,170,60,0.38)!important;}
  .sq.in-check{background:var(--check)!important;}
  .sq.can-move::after{content:'';position:absolute;top:50%;left:50%;transform:translate(-50%,-50%);width:18px;height:18px;border-radius:50%;background:var(--move);pointer-events:none;z-index:1;}
  .sq.can-capture::after{content:'';position:absolute;inset:4px;border-radius:50%;border:4px solid var(--move);pointer-events:none;z-index:1;}
  .sq:hover{filter:brightness(1.1);}
  /* Setup mode hover */
  .sq.setup-hover{filter:brightness(1.2);}
  .sq.setup-occupied{outline:2px solid rgba(210,170,60,0.4);outline-offset:-2px;}

  .piece-el{position:absolute;top:0;left:0;width:56px;height:56px;pointer-events:none;will-change:transform;z-index:10;}

  /* ── Custom setup UI ── */
  #setup-bar{display:none;flex-direction:column;gap:10px;margin-top:12px;width:100%;max-width:516px;}
  #setup-bar.visible{display:flex;}

  .setup-phase-label{
    text-align:center;font-family:'Playfair Display',serif;font-size:16px;
    color:var(--foam);font-weight:500;letter-spacing:0.5px;
  }
  .setup-phase-sub{text-align:center;font-size:11px;color:var(--latte);margin-top:2px;}

  .palette{
    display:flex;gap:6px;justify-content:center;align-items:center;
    background:linear-gradient(160deg,var(--panel),var(--panel2));
    border:1px solid var(--border);border-radius:14px;padding:10px 14px;
  }
  .palette-piece{
    width:54px;height:54px;border-radius:10px;cursor:pointer;
    border:2px solid transparent;transition:all 0.15s;
    display:flex;align-items:center;justify-content:center;
    background:rgba(0,0,0,0.15);
  }
  .palette-piece:hover{background:rgba(192,120,64,0.2);border-color:rgba(192,120,64,0.4);}
  .palette-piece.active{background:rgba(192,120,64,0.3);border-color:var(--caramel);box-shadow:0 0 10px rgba(192,120,64,0.3);}
  .palette-eraser{
    width:46px;height:54px;border-radius:10px;cursor:pointer;
    border:2px solid transparent;transition:all 0.15s;
    display:flex;align-items:center;justify-content:center;flex-direction:column;gap:2px;
    background:rgba(0,0,0,0.15);font-size:20px;color:var(--latte);
  }
  .palette-eraser:hover{background:rgba(200,80,60,0.2);border-color:rgba(200,80,60,0.4);}
  .palette-eraser.active{background:rgba(200,80,60,0.3);border-color:#c05040;}
  .palette-eraser span{font-size:9px;letter-spacing:0;color:var(--latte);}

  .setup-actions{display:flex;gap:8px;justify-content:center;}
  .setup-btn{
    padding:8px 18px;border-radius:10px;font-family:'Lato',sans-serif;font-size:12px;font-weight:700;
    letter-spacing:1px;text-transform:uppercase;cursor:pointer;border:1px solid;transition:all 0.18s;
  }
  .setup-btn.primary{background:linear-gradient(135deg,var(--caramel2),#7a4020);border-color:var(--caramel);color:var(--foam);}
  .setup-btn.primary:hover{background:linear-gradient(135deg,var(--caramel),#9a5030);transform:translateY(-1px);}
  .setup-btn.secondary{background:rgba(0,0,0,0.2);border-color:rgba(255,255,255,0.12);color:var(--latte);}
  .setup-btn.secondary:hover{background:rgba(192,120,64,0.15);border-color:rgba(192,120,64,0.3);}
  .setup-btn:disabled{opacity:0.4;cursor:not-allowed;transform:none!important;}

  #setup-error{text-align:center;font-size:11px;color:#e07060;min-height:16px;}

  /* Promo overlay */
  .promo-overlay{display:none;position:fixed;inset:0;background:rgba(15,6,0,0.88);z-index:100;align-items:center;justify-content:center;backdrop-filter:blur(4px);}
  .promo-overlay.active{display:flex;}
  .promo-box{background:linear-gradient(145deg,#5a3418,#2c1a0a);border:1px solid rgba(192,120,64,0.4);padding:28px;border-radius:20px;text-align:center;box-shadow:0 24px 60px rgba(0,0,0,0.85);}
  .promo-title{font-family:'Playfair Display',serif;font-size:20px;color:var(--caramel);margin-bottom:16px;}
  .promo-pieces{display:flex;gap:10px;justify-content:center;}
  .promo-piece{cursor:pointer;padding:8px;border:1px solid transparent;border-radius:12px;transition:all 0.18s;}
  .promo-piece:hover{border-color:var(--caramel);background:rgba(192,120,64,0.15);transform:scale(1.1);}
</style>
</head>
<body>

<header>
  <h1>☕ Café Chess</h1>
  <p class="subtitle">Est. Today &nbsp;·&nbsp; Good Moves &amp; Good Coffee</p>
</header>

<div class="game-wrap">
  <div class="sidebar">

    <div class="panel">
      <div class="panel-label">🎮 Mode</div>
      <div class="mode-row" style="margin-bottom:12px;">
        <button class="mode-btn" id="btn-2p"     onclick="setMode('2p')">2 Player</button>
        <button class="mode-btn active" id="btn-ai" onclick="setMode('ai')">vs AI</button>
        <button class="mode-btn" id="btn-custom" onclick="setMode('custom')">Custom</button>
      </div>
      <div id="diff-wrap">
        <div class="panel-label">☕ Difficulty</div>
        <div class="diff-grid">
          <button class="diff-btn" id="diff-decaf"   onclick="setDiff('decaf')">Decaf</button>
          <button class="diff-btn active" id="diff-medium" onclick="setDiff('medium')">Medium</button>
          <button class="diff-btn" id="diff-strong"  onclick="setDiff('strong')">Strong</button>
          <button class="diff-btn" id="diff-barista" onclick="setDiff('barista')">Barista</button>
        </div>
        <button class="lose-btn" id="diff-lose" onclick="setDiff('lose')">☠ Trying to Lose</button>
        <div id="diff-desc"></div>
      </div>
    </div>

    <div class="panel">
      <div class="panel-label">♟ Status</div>
      <div id="status"></div>
      <div class="ai-thinking" id="ai-thinking"></div>
    </div>

    <div class="panel">
      <div class="panel-label">🫙 Captured</div>
      <div class="captured-label">White took</div>
      <div class="captured-display" id="white-captured"></div>
      <div class="captured-label">Black took</div>
      <div class="captured-display" id="black-captured"></div>
    </div>

    <div class="panel">
      <div class="panel-label">📋 Moves</div>
      <div class="move-list" id="move-list"></div>
    </div>

    <div>
      <button class="action-btn" onclick="freshGame()">☕ Fresh Game</button>
      <button class="action-btn" onclick="flipBoard()">⇅ Flip Board</button>
      <button class="action-btn" id="fs-btn" onclick="toggleFullscreen()">⛶ Fullscreen</button>
    </div>

  </div>

  <div class="board-wrap">
    <div class="board-frame">
      <div class="coords-wrap">
        <div></div>
        <div class="coord-bottom" id="coord-top" style="height:18px;"></div>
        <div></div>
        <div class="coord-side" id="coord-left" style="width:18px;"></div>
        <div class="board-container">
          <div id="board"></div>
          <div id="piece-layer"></div>
        </div>
        <div class="coord-side" id="coord-right" style="width:18px;"></div>
        <div></div>
        <div class="coord-bottom" id="coord-bottom" style="height:18px;"></div>
        <div></div>
      </div>
    </div>

    <!-- Setup bar appears below board in custom mode -->
    <div id="setup-bar">
      <div>
        <div class="setup-phase-label" id="setup-phase-label"></div>
        <div class="setup-phase-sub" id="setup-phase-sub"></div>
      </div>
      <div class="palette" id="palette"></div>
      <div id="setup-error"></div>
      <div class="setup-actions">
        <button class="setup-btn secondary" onclick="clearSetupSide()">🗑 Clear</button>
        <button class="setup-btn secondary" id="btn-setup-prev" onclick="prevSetupPhase()" style="display:none">← Back</button>
        <button class="setup-btn primary"   id="btn-setup-next" onclick="nextSetupPhase()">Next: Place Black →</button>
      </div>
    </div>
  </div>
</div>

<div class="promo-overlay" id="promo-overlay">
  <div class="promo-box">
    <div class="promo-title">Promotion</div>
    <div class="promo-pieces" id="promo-pieces"></div>
  </div>
</div>

<script>
const SQ = 60;

function pieceSVG(type, color) {
  const f=color==='w'?'#f5edcc':'#1c1c1c', s=color==='w'?'#5c3a1e':'#d4c49a';
  const sh={
    P:`<circle cx="22.5" cy="12" r="5.5"/><path d="M11.5 37c0-5.5 3.5-10 11-10s11 4.5 11 10H11.5z"/><path d="M20 27v-5M25 27v-5"/><path d="M13 37h19" stroke-width="2"/>`,
    N:`<path d="M22 10c10.5 1 16.5 8 16 29H15c0-9 10-6.5 8-21"/><path d="M24 18c.38 5.12-5.5 7.5-8 3-1-2.5-5-4.5-5-4.5 6-1.5 9-3.5 11-8.5"/><circle cx="20" cy="13" r="1.5" fill="${s}"/><path d="M14 39h17" stroke-width="2"/>`,
    B:`<circle cx="22.5" cy="10" r="2.5"/><path d="M22.5 12.5C22.5 12.5 15 20 15 28c0 6 3 9 7.5 9s7.5-3 7.5-9c0-8-7.5-15.5-7.5-15.5z"/><path d="M13 39h19" stroke-width="2"/><path d="M22.5 12.5v24" stroke-width="0.8"/>`,
    R:`<path d="M9 39h27M12 36v-7h21v7M11 14h3V9h3v5h3V9h4v5h3V9h3v5h2v5a2 2 0 01-2 2H13a2 2 0 01-2-2v-5z"/><path d="M13 29h19"/>`,
    Q:`<circle cx="6.5" cy="12" r="2.5"/><circle cx="14" cy="8" r="2.5"/><circle cx="22.5" cy="6.5" r="2.5"/><circle cx="31" cy="8" r="2.5"/><circle cx="38.5" cy="12" r="2.5"/><path d="M9 26c8.5-8.5 15.5-8.5 27 0l2.5-12.5L31 25l-.3-14.1-8.2 14.5L14 10.9 13.5 25 6.5 13.5 9 26z"/><path d="M9 26c0 2 1.5 2 2.5 4 1 1.5 1 1 .5 3.5-1.5 1-4 .5-5 2-.5 1.5 0 2 0 2h26s.5-.5 0-2c-1-1.5-3.5-1-5-2-.5-2.5-.5-2 .5-3.5 1-2 2.5-2 2.5-4"/><path d="M11.5 30h22M12 33.5h21M11 38h23" stroke-width="1.5"/>`,
    K:`<path d="M22.5 11.63V6M20 8h5"/><path d="M22.5 25s4.5-7.5 3-10.5c0 0-1-2.5-3-2.5s-3 2.5-3 2.5c-1.5 3 3 10.5 3 10.5"/><path d="M12.5 37c5.5 3.5 14.5 3.5 20 0l1.5-5.5-11.5-8-11.5 8L12.5 37z"/><path d="M11.5 30c5.5-3 15.5-3 22 0"/><path d="M11.5 37h22" stroke-width="2"/>`
  };
  return `<svg viewBox="0 0 45 45" xmlns="http://www.w3.org/2000/svg" width="56" height="56"><g fill="${f}" stroke="${s}" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round" style="filter:drop-shadow(0 2px 3px rgba(0,0,0,0.6))">${sh[type]}</g></svg>`;
}

// Smaller SVG for palette
function pieceSVGSm(type, color) {
  const f=color==='w'?'#f5edcc':'#1c1c1c', s=color==='w'?'#5c3a1e':'#d4c49a';
  const sh={
    P:`<circle cx="22.5" cy="12" r="5.5"/><path d="M11.5 37c0-5.5 3.5-10 11-10s11 4.5 11 10H11.5z"/><path d="M20 27v-5M25 27v-5"/><path d="M13 37h19" stroke-width="2"/>`,
    N:`<path d="M22 10c10.5 1 16.5 8 16 29H15c0-9 10-6.5 8-21"/><path d="M24 18c.38 5.12-5.5 7.5-8 3-1-2.5-5-4.5-5-4.5 6-1.5 9-3.5 11-8.5"/><circle cx="20" cy="13" r="1.5" fill="${s}"/><path d="M14 39h17" stroke-width="2"/>`,
    B:`<circle cx="22.5" cy="10" r="2.5"/><path d="M22.5 12.5C22.5 12.5 15 20 15 28c0 6 3 9 7.5 9s7.5-3 7.5-9c0-8-7.5-15.5-7.5-15.5z"/><path d="M13 39h19" stroke-width="2"/><path d="M22.5 12.5v24" stroke-width="0.8"/>`,
    R:`<path d="M9 39h27M12 36v-7h21v7M11 14h3V9h3v5h3V9h4v5h3V9h3v5h2v5a2 2 0 01-2 2H13a2 2 0 01-2-2v-5z"/><path d="M13 29h19"/>`,
    Q:`<circle cx="6.5" cy="12" r="2.5"/><circle cx="14" cy="8" r="2.5"/><circle cx="22.5" cy="6.5" r="2.5"/><circle cx="31" cy="8" r="2.5"/><circle cx="38.5" cy="12" r="2.5"/><path d="M9 26c8.5-8.5 15.5-8.5 27 0l2.5-12.5L31 25l-.3-14.1-8.2 14.5L14 10.9 13.5 25 6.5 13.5 9 26z"/><path d="M9 26c0 2 1.5 2 2.5 4 1 1.5 1 1 .5 3.5-1.5 1-4 .5-5 2-.5 1.5 0 2 0 2h26s.5-.5 0-2c-1-1.5-3.5-1-5-2-.5-2.5-.5-2 .5-3.5 1-2 2.5-2 2.5-4"/><path d="M11.5 30h22M12 33.5h21M11 38h23" stroke-width="1.5"/>`,
    K:`<path d="M22.5 11.63V6M20 8h5"/><path d="M22.5 25s4.5-7.5 3-10.5c0 0-1-2.5-3-2.5s-3 2.5-3 2.5c-1.5 3 3 10.5 3 10.5"/><path d="M12.5 37c5.5 3.5 14.5 3.5 20 0l1.5-5.5-11.5-8-11.5 8L12.5 37z"/><path d="M11.5 30c5.5-3 15.5-3 22 0"/><path d="M11.5 37h22" stroke-width="2"/>`
  };
  return `<svg viewBox="0 0 45 45" xmlns="http://www.w3.org/2000/svg" width="44" height="44"><g fill="${f}" stroke="${s}" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round" style="filter:drop-shadow(0 2px 2px rgba(0,0,0,0.5))">${sh[type]}</g></svg>`;
}

const GLYPHS={wK:'♔',wQ:'♕',wR:'♖',wB:'♗',wN:'♘',wP:'♙',bK:'♚',bQ:'♛',bR:'♜',bB:'♝',bN:'♞',bP:'♟'};

const DIFFICULTIES={
  decaf:  {depth:1,noise:0.8, desc:'Just woke up. Moves randomly with a little thought.'},
  medium: {depth:2,noise:0.2, desc:"Had one shot. Knows what it's doing, mostly."},
  strong: {depth:3,noise:0.05,desc:'Double espresso. Will punish mistakes.'},
  barista:{depth:4,noise:0,   desc:'Pure coffee energy. Good luck.'},
  lose:   {depth:0,noise:0,   desc:'Actively trying to hand you the win. Accept it graciously.'},
};

let board,turn,selected,legalMoves,gameOver,flipped,lastMove;
let whiteCaptured,blackCaptured,moveHistory;
let enPassant,castling;
let gameMode='ai', difficulty='medium';

// ── Custom setup state ──
let setupPhase=0; // 0=white, 1=black
let setupBoard=null;
let selectedPalettePiece=null; // 'K','Q','R','B','N','P' or 'erase'
let inSetup=false;

// ── Mode / Diff ──
function setMode(m) {
  gameMode=m;
  ['2p','ai','custom'].forEach(id=>document.getElementById('btn-'+id).classList.toggle('active',id===m));
  document.getElementById('diff-wrap').style.display=(m==='ai')?'':'none';
  if (m==='custom') startCustomSetup();
  else { inSetup=false; document.getElementById('setup-bar').classList.remove('visible'); freshGame(); }
}

function setDiff(d) {
  difficulty=d;
  ['decaf','medium','strong','barista'].forEach(id=>document.getElementById('diff-'+id).classList.toggle('active',id===d));
  document.getElementById('diff-lose').classList.toggle('active',d==='lose');
  document.getElementById('diff-desc').textContent=DIFFICULTIES[d].desc;
}

// ── Custom setup ──
function startCustomSetup() {
  inSetup=true; setupPhase=0;
  setupBoard=Array.from({length:8},()=>Array(8).fill(null));
  selectedPalettePiece='Q';
  document.getElementById('setup-bar').classList.add('visible');
  document.getElementById('status').innerHTML='<em style="color:var(--latte)">Place your pieces</em>';
  document.getElementById('ai-thinking').textContent='';
  updateSetupUI();
  renderSetupBoard();
}

function updateSetupUI() {
  const color=setupPhase===0?'w':'b';
  const colorName=setupPhase===0?'White':'Black';
  document.getElementById('setup-phase-label').textContent=`Step ${setupPhase+1} of 2: Place ${colorName}'s Pieces`;
  document.getElementById('setup-phase-sub').textContent=
    `Click a piece below, then click squares. Must include exactly one ${colorName} King.`;
  document.getElementById('btn-setup-prev').style.display=setupPhase===1?'':'none';
  document.getElementById('btn-setup-next').textContent=
    setupPhase===0?'Next: Place Black →':'🎮 Start Game!';
  document.getElementById('setup-error').textContent='';

  // Build palette
  const pal=document.getElementById('palette');
  pal.innerHTML='';
  const pieces=['K','Q','R','B','N','P'];
  pieces.forEach(p=>{
    const btn=document.createElement('div');
    btn.className='palette-piece'+(selectedPalettePiece===p?' active':'');
    btn.innerHTML=pieceSVGSm(p,color);
    btn.onclick=()=>selectPalettePiece(p);
    pal.appendChild(btn);
  });
  // Eraser
  const eraser=document.createElement('div');
  eraser.className='palette-eraser'+(selectedPalettePiece==='erase'?' active':'');
  eraser.innerHTML='✕<span>Erase</span>';
  eraser.onclick=()=>selectPalettePiece('erase');
  pal.appendChild(eraser);
}

function selectPalettePiece(p) {
  selectedPalettePiece=p;
  updateSetupUI();
}

function renderSetupBoard() {
  const boardEl=document.getElementById('board');
  boardEl.innerHTML='';
  const files=['a','b','c','d','e','f','g','h'];
  const ranks=['8','7','6','5','4','3','2','1'];
  ['coord-bottom','coord-top','coord-left','coord-right'].forEach(id=>document.getElementById(id).innerHTML='');
  files.forEach(f=>{
    document.getElementById('coord-bottom').innerHTML+=`<div class="coord" style="width:60px">${f}</div>`;
    document.getElementById('coord-top').innerHTML+=`<div class="coord" style="width:60px"></div>`;
  });
  ranks.forEach(r=>{
    document.getElementById('coord-left').innerHTML+=`<div class="coord">${r}</div>`;
    document.getElementById('coord-right').innerHTML+=`<div class="coord"></div>`;
  });

  for (let dr=0;dr<8;dr++) {
    for (let dc=0;dc<8;dc++) {
      const r=dr, c=dc;
      const sq=document.createElement('div');
      sq.className='sq '+((r+c)%2===0?'light':'dark');
      if (setupBoard[r][c]) sq.classList.add('setup-occupied');
      sq.addEventListener('click',()=>handleSetupClick(r,c));
      sq.addEventListener('mouseenter',()=>sq.classList.add('setup-hover'));
      sq.addEventListener('mouseleave',()=>sq.classList.remove('setup-hover'));
      boardEl.appendChild(sq);
    }
  }
  renderSetupPieces();
}

function renderSetupPieces() {
  const layer=document.getElementById('piece-layer');
  layer.innerHTML='';
  for (let r=0;r<8;r++) for (let c=0;c<8;c++) {
    const p=setupBoard[r][c]; if (!p) continue;
    const x=c*SQ, y=r*SQ;
    const el=document.createElement('div');
    el.className='piece-el';
    el.style.transform=`translate(${x+2}px,${y+2}px)`;
    el.innerHTML=pieceSVG(p[1],p[0]);
    layer.appendChild(el);
  }
}

function handleSetupClick(r, c) {
  if (!inSetup) return;
  document.getElementById('setup-error').textContent='';
  const color=setupPhase===0?'w':'b';

  if (selectedPalettePiece==='erase') {
    setupBoard[r][c]=null;
  } else {
    // Pawns can't go on ranks 0 or 7
    if (selectedPalettePiece==='P'&&(r===0||r===7)) {
      document.getElementById('setup-error').textContent='Pawns can\'t go on the first or last rank.';
      return;
    }
    setupBoard[r][c]=color+selectedPalettePiece;
  }
  renderSetupPieces();
  // Update occupied class
  document.querySelectorAll('#board .sq').forEach(sq=>{
    const sr=parseInt(sq.dataset.r||sq.getAttribute('data-r')||[...document.getElementById('board').children].indexOf(sq)/8|0);
    // simpler: re-render
  });
  renderSetupBoard();
}

function clearSetupSide() {
  const color=setupPhase===0?'w':'b';
  for (let r=0;r<8;r++) for (let c=0;c<8;c++)
    if (setupBoard[r][c]&&setupBoard[r][c][0]===color) setupBoard[r][c]=null;
  renderSetupBoard();
}

function prevSetupPhase() {
  setupPhase=0;
  updateSetupUI();
  renderSetupBoard();
}

function nextSetupPhase() {
  // Validate current phase
  const color=setupPhase===0?'w':'b';
  const kings=setupBoard.flat().filter(p=>p===color+'K').length;
  if (kings===0) { document.getElementById('setup-error').textContent=`${color==='w'?'White':'Black'} needs at least one King!`; return; }
  if (kings>1)   { document.getElementById('setup-error').textContent=`${color==='w'?'White':'Black'} can only have one King!`; return; }

  if (setupPhase===0) {
    setupPhase=1;
    selectedPalettePiece='Q';
    updateSetupUI();
    renderSetupBoard();
  } else {
    // Launch game with custom board
    launchCustomGame();
  }
}

function launchCustomGame() {
  inSetup=false;
  document.getElementById('setup-bar').classList.remove('visible');
  board=setupBoard.map(row=>[...row]);
  turn='w'; selected=null; legalMoves=[]; gameOver=false; lastMove=null;
  whiteCaptured=[]; blackCaptured=[]; moveHistory=[];
  enPassant=null; castling={wK:false,wQ:false,bK:false,bQ:false}; // no castling in custom
  if (flipped===undefined) flipped=false;
  renderBoard(); updateStatus(); updateCaptured(); updateMoveList();
}

// ── Normal game ──
function freshGame() {
  if (gameMode==='custom') { startCustomSetup(); return; }
  board=[
    ['bR','bN','bB','bQ','bK','bB','bN','bR'],
    ['bP','bP','bP','bP','bP','bP','bP','bP'],
    [null,null,null,null,null,null,null,null],
    [null,null,null,null,null,null,null,null],
    [null,null,null,null,null,null,null,null],
    [null,null,null,null,null,null,null,null],
    ['wP','wP','wP','wP','wP','wP','wP','wP'],
    ['wR','wN','wB','wQ','wK','wB','wN','wR'],
  ];
  turn='w'; selected=null; legalMoves=[]; gameOver=false; lastMove=null;
  whiteCaptured=[]; blackCaptured=[]; moveHistory=[];
  enPassant=null; castling={wK:true,wQ:true,bK:true,bQ:true};
  if (flipped===undefined) flipped=false;
  renderBoard(); updateStatus(); updateCaptured(); updateMoveList();
}

function sqPos(r,c){return{x:(flipped?7-c:c)*SQ,y:(flipped?7-r:r)*SQ};}

function renderBoard() {
  if (inSetup){renderSetupBoard();return;}
  const boardEl=document.getElementById('board');
  boardEl.innerHTML='';
  const files=['a','b','c','d','e','f','g','h'];
  const ranks=['8','7','6','5','4','3','2','1'];
  const dFiles=flipped?[...files].reverse():files;
  const dRanks=flipped?[...ranks].reverse():ranks;
  ['coord-bottom','coord-top','coord-left','coord-right'].forEach(id=>document.getElementById(id).innerHTML='');
  dFiles.forEach(f=>{
    document.getElementById('coord-bottom').innerHTML+=`<div class="coord" style="width:60px">${f}</div>`;
    document.getElementById('coord-top').innerHTML+=`<div class="coord" style="width:60px"></div>`;
  });
  dRanks.forEach(r=>{
    document.getElementById('coord-left').innerHTML+=`<div class="coord">${r}</div>`;
    document.getElementById('coord-right').innerHTML+=`<div class="coord"></div>`;
  });
  const kingInCheck=isInCheck(turn,board)?findKing(turn,board):null;
  for (let dr=0;dr<8;dr++) {
    for (let dc=0;dc<8;dc++) {
      const r=flipped?7-dr:dr,c=flipped?7-dc:dc;
      const sq=document.createElement('div');
      sq.className='sq '+((r+c)%2===0?'light':'dark');
      sq.dataset.r=r;sq.dataset.c=c;
      if(selected&&selected[0]===r&&selected[1]===c)sq.classList.add('selected');
      if(lastMove){if(lastMove.fr===r&&lastMove.fc===c)sq.classList.add('last-from');if(lastMove.tr===r&&lastMove.tc===c)sq.classList.add('last-to');}
      if(kingInCheck&&kingInCheck[0]===r&&kingInCheck[1]===c)sq.classList.add('in-check');
      const isLegal=legalMoves.some(m=>m[0]===r&&m[1]===c);
      if(isLegal){const isCap=(board[r][c]&&board[r][c][0]!==turn)||(enPassant&&enPassant[0]===r&&enPassant[1]===c);sq.classList.add(isCap?'can-capture':'can-move');}
      sq.addEventListener('click',()=>handleClick(r,c));
      boardEl.appendChild(sq);
    }
  }
  renderPieceLayer();
}

function renderPieceLayer(skip) {
  const layer=document.getElementById('piece-layer');
  layer.innerHTML='';
  for(let r=0;r<8;r++)for(let c=0;c<8;c++){
    if(skip&&skip.r===r&&skip.c===c)continue;
    const p=board[r][c];if(!p)continue;
    const{x,y}=sqPos(r,c);
    const el=document.createElement('div');
    el.className='piece-el';
    el.style.transform=`translate(${x+2}px,${y+2}px)`;
    el.innerHTML=pieceSVG(p[1],p[0]);
    layer.appendChild(el);
  }
}

function animateMove(fr,fc,tr,tc,isCastle,callback){
  const piece=board[fr][fc];if(!piece){callback();return;}
  const from=sqPos(fr,fc),to=sqPos(tr,tc);
  const layer=document.getElementById('piece-layer');
  renderPieceLayer({r:fr,c:fc});
  const flyer=document.createElement('div');
  flyer.className='piece-el';
  flyer.style.transform=`translate(${from.x+2}px,${from.y+2}px)`;
  flyer.style.zIndex='30';
  flyer.innerHTML=pieceSVG(piece[1],piece[0]);
  layer.appendChild(flyer);
  let done=false;
  const finish=()=>{if(done)return;done=true;callback();};
  flyer.getBoundingClientRect();
  flyer.style.transition='transform 0.2s cubic-bezier(0.3,0,0.2,1)';
  flyer.style.transform=`translate(${to.x+2}px,${to.y+2}px)`;
  flyer.addEventListener('transitionend',finish,{once:true});
  setTimeout(finish,400);
  if(isCastle){
    const rFC=tc>fc?7:0,rTC=tc>fc?5:3;
    if(board[fr][rFC]){
      const rf=sqPos(fr,rFC),rt=sqPos(tr,rTC);
      const re=document.createElement('div');re.className='piece-el';
      re.style.transform=`translate(${rf.x+2}px,${rf.y+2}px)`;re.style.zIndex='28';
      re.innerHTML=pieceSVG('R',piece[0]);layer.appendChild(re);
      re.getBoundingClientRect();
      re.style.transition='transform 0.2s cubic-bezier(0.3,0,0.2,1)';
      re.style.transform=`translate(${rt.x+2}px,${rt.y+2}px)`;
    }
  }
}

function handleClick(r,c){
  if(inSetup)return;
  if(gameOver)return;
  if((gameMode==='ai'||gameMode==='custom'&&false)&&turn==='b')return;
  // In custom vs AI, block black too
  if(gameMode==='custom'&&turn==='b'&&gameMode!=='2p')return;
  const piece=board[r][c];
  if(selected){
    const isLegal=legalMoves.some(m=>m[0]===r&&m[1]===c);
    if(isLegal){makeMove(selected[0],selected[1],r,c);return;}
    if(piece&&piece[0]===turn){selected=[r,c];legalMoves=getLegalMoves(r,c,board,turn,enPassant,castling);renderBoard();return;}
    selected=null;legalMoves=[];renderBoard();return;
  }
  if(piece&&piece[0]===turn){selected=[r,c];legalMoves=getLegalMoves(r,c,board,turn,enPassant,castling);renderBoard();}
}

function makeMove(fr,fc,tr,tc,promoChoice){
  const piece=board[fr][fc];if(!piece)return;
  const captured=board[tr][tc];
  const nb=board.map(row=>[...row]);
  let newEP=null,newCast={...castling},notation='';
  const files='abcdefgh',ranks='87654321';
  if(piece[1]==='P'&&Math.abs(tr-fr)===2)newEP=[(fr+tr)/2,fc];
  let epCapture=null;
  if(piece[1]==='P'&&enPassant&&tr===enPassant[0]&&tc===enPassant[1]){epCapture=nb[fr][tc];nb[fr][tc]=null;}
  const isCastle=piece[1]==='K'&&Math.abs(tc-fc)===2;
  if(piece[1]==='K'){
    if(piece[0]==='w'){newCast.wK=false;newCast.wQ=false;}else{newCast.bK=false;newCast.bQ=false;}
    if(isCastle){if(tc>fc){nb[tr][5]=nb[tr][7];nb[tr][7]=null;}else{nb[tr][3]=nb[tr][0];nb[tr][0]=null;}}
  }
  if(piece[1]==='R'){if(fr===7&&fc===0)newCast.wQ=false;if(fr===7&&fc===7)newCast.wK=false;if(fr===0&&fc===0)newCast.bQ=false;if(fr===0&&fc===7)newCast.bK=false;}
  if(captured){if(tr===7&&tc===0)newCast.wQ=false;if(tr===7&&tc===7)newCast.wK=false;if(tr===0&&tc===0)newCast.bQ=false;if(tr===0&&tc===7)newCast.bK=false;}
  nb[tr][tc]=piece;nb[fr][fc]=null;
  const isPromo=piece[1]==='P'&&(tr===0||tr===7);
  if(isPromo){if(!promoChoice){showPromo(piece[0],ch=>makeMove(fr,fc,tr,tc,ch));return;}nb[tr][tc]=piece[0]+promoChoice;}
  if(isCastle)notation=tc>fc?'O-O':'O-O-O';
  else notation=(piece[1]!=='P'?piece[1]:'')+(captured||epCapture?(piece[1]==='P'?files[fc]:'')+'x':'')+files[tc]+ranks[tr]+(isPromo?'='+promoChoice:'');
  const capPiece=captured||epCapture;
  if(capPiece){if(turn==='w')whiteCaptured.push(capPiece);else blackCaptured.push(capPiece);}

  animateMove(fr,fc,tr,tc,isCastle,()=>{
    board=nb;enPassant=newEP;castling=newCast;lastMove={fr,fc,tr,tc};
    const next=turn==='w'?'b':'w';
    const inCheck=isInCheck(next,board);
    const hasMoves=hasLegalMoves(next,board,enPassant,castling);
    if(inCheck&&!hasMoves)notation+='#';else if(inCheck)notation+='+';
    if(turn==='b'){
      const last=moveHistory[moveHistory.length-1];
      if(last&&last.black===null)last.black=notation;
      else moveHistory.push({num:moveHistory.length+1,white:'',black:notation});
    }else{moveHistory.push({num:moveHistory.length+1,white:notation,black:null});}
    turn=next;selected=null;legalMoves=[];
    if(!hasMoves){
      gameOver=true;
      document.getElementById('status').innerHTML=inCheck
        ?`<strong style="color:var(--caramel)">Checkmate!</strong><br>${turn==='w'?'Black':'White'} wins ☕`
        :`<strong style="color:var(--caramel)">Stalemate!</strong><br>Draw`;
    }else{updateStatus(inCheck);}
    renderBoard();updateCaptured();updateMoveList();
    const isAiTurn=(gameMode==='ai'||gameMode==='custom')&&turn==='b';
    if(!gameOver&&isAiTurn){
      const msgs={decaf:'half-asleep...',medium:'thinking...',strong:'calculating...',barista:'deep in thought...',lose:'plotting defeat...'};
      document.getElementById('ai-thinking').textContent=msgs[difficulty]||'thinking...';
      setTimeout(()=>{doAiMove();document.getElementById('ai-thinking').textContent='';},350);
    }
  });
}

function showPromo(color,cb){
  const el=document.getElementById('promo-overlay');
  document.getElementById('promo-pieces').innerHTML='';
  ['Q','R','B','N'].forEach(p=>{
    const d=document.createElement('div');d.className='promo-piece';
    d.innerHTML=pieceSVG(p,color);
    d.onclick=()=>{el.classList.remove('active');cb(p);};
    document.getElementById('promo-pieces').appendChild(d);
  });
  el.classList.add('active');
}

function updateStatus(inCheck){
  if(gameOver)return;
  const col=turn==='w'?'White':'Black';
  const dot=`<span class="turn-dot ${turn==='w'?'white':'black'}"></span>`;
  document.getElementById('status').innerHTML=`${dot}${col}'s turn${inCheck?' <span style="color:#e07060">(Check!)</span>':''}`;
}
function updateCaptured(){
  document.getElementById('white-captured').innerHTML=whiteCaptured.map(p=>GLYPHS[p]).join('');
  document.getElementById('black-captured').innerHTML=blackCaptured.map(p=>GLYPHS[p]).join('');
}
function updateMoveList(){
  const el=document.getElementById('move-list');el.innerHTML='';
  moveHistory.forEach(m=>{
    const row=document.createElement('div');row.className='move-row';
    row.innerHTML=`<span class="mn">${m.num}.</span><span>${m.white||''}</span><span>${m.black||''}</span>`;
    el.appendChild(row);
  });
  el.scrollTop=el.scrollHeight;
}
function flipBoard(){flipped=!flipped;if(inSetup)renderSetupBoard();else renderBoard();}
function toggleFullscreen(){
  if(!document.fullscreenElement){document.documentElement.requestFullscreen();document.getElementById('fs-btn').textContent='✕ Exit Fullscreen';}
  else{document.exitFullscreen();document.getElementById('fs-btn').textContent='⛶ Fullscreen';}
}
document.addEventListener('fullscreenchange',()=>{if(!document.fullscreenElement)document.getElementById('fs-btn').textContent='⛶ Fullscreen';});

// ── AI ENGINE ──────────────────────────────────────────────────
const PIECE_VAL={P:100,N:320,B:330,R:500,Q:900,K:20000};
const PST={
  P:[[0,0,0,0,0,0,0,0],[50,50,50,50,50,50,50,50],[10,10,20,30,30,20,10,10],[5,5,10,25,25,10,5,5],[0,0,0,20,20,0,0,0],[5,-5,-10,0,0,-10,-5,5],[5,10,10,-20,-20,10,10,5],[0,0,0,0,0,0,0,0]],
  N:[[-50,-40,-30,-30,-30,-30,-40,-50],[-40,-20,0,0,0,0,-20,-40],[-30,0,10,15,15,10,0,-30],[-30,5,15,20,20,15,5,-30],[-30,0,15,20,20,15,0,-30],[-30,5,10,15,15,10,5,-30],[-40,-20,0,5,5,0,-20,-40],[-50,-40,-30,-30,-30,-30,-40,-50]],
  B:[[-20,-10,-10,-10,-10,-10,-10,-20],[-10,0,0,0,0,0,0,-10],[-10,0,5,10,10,5,0,-10],[-10,5,5,10,10,5,5,-10],[-10,0,10,10,10,10,0,-10],[-10,10,10,10,10,10,10,-10],[-10,5,0,0,0,0,5,-10],[-20,-10,-10,-10,-10,-10,-10,-20]],
  R:[[0,0,0,0,0,0,0,0],[5,10,10,10,10,10,10,5],[-5,0,0,0,0,0,0,-5],[-5,0,0,0,0,0,0,-5],[-5,0,0,0,0,0,0,-5],[-5,0,0,0,0,0,0,-5],[-5,0,0,0,0,0,0,-5],[0,0,0,5,5,0,0,0]],
  Q:[[-20,-10,-10,-5,-5,-10,-10,-20],[-10,0,0,0,0,0,0,-10],[-10,0,5,5,5,5,0,-10],[-5,0,5,5,5,5,0,-5],[0,0,5,5,5,5,0,-5],[-10,5,5,5,5,5,0,-10],[-10,0,5,0,0,0,0,-10],[-20,-10,-10,-5,-5,-10,-10,-20]],
  K:[[-30,-40,-40,-50,-50,-40,-40,-30],[-30,-40,-40,-50,-50,-40,-40,-30],[-30,-40,-40,-50,-50,-40,-40,-30],[-30,-40,-40,-50,-50,-40,-40,-30],[-20,-30,-30,-40,-40,-30,-30,-20],[-10,-20,-20,-20,-20,-20,-20,-10],[20,20,0,0,0,0,20,20],[20,30,10,0,0,10,30,20]],
};
function evaluate(bd){let s=0;for(let r=0;r<8;r++)for(let c=0;c<8;c++){const p=bd[r][c];if(!p)continue;const v=PIECE_VAL[p[1]];const pv=p[0]==='w'?PST[p[1]][r][c]:PST[p[1]][7-r][7-c];s+=p[0]==='w'?(v+pv):-(v+pv);}return s;}
function getAllMoves(bd,color,ep,cast){const m=[];for(let r=0;r<8;r++)for(let c=0;c<8;c++)if(bd[r][c]&&bd[r][c][0]===color)getLegalMoves(r,c,bd,color,ep,cast).forEach(([tr,tc])=>m.push([r,c,tr,tc]));return m;}
function applyMove(bd,fr,fc,tr,tc,cast){
  const nb=bd.map(r=>[...r]);const p=nb[fr][fc];let newEP=null,newCast={...cast};
  if(p[1]==='P'&&Math.abs(tr-fr)===2)newEP=[(fr+tr)/2,fc];
  if(p[1]==='P'&&nb[tr][tc]===null&&fc!==tc)nb[fr][tc]=null;
  if(p[1]==='K'){if(p[0]==='w'){newCast.wK=false;newCast.wQ=false;}else{newCast.bK=false;newCast.bQ=false;}if(Math.abs(tc-fc)===2){if(tc>fc){nb[tr][5]=nb[tr][7];nb[tr][7]=null;}else{nb[tr][3]=nb[tr][0];nb[tr][0]=null;}}}
  if(p[1]==='R'){if(fr===7&&fc===0)newCast.wQ=false;if(fr===7&&fc===7)newCast.wK=false;if(fr===0&&fc===0)newCast.bQ=false;if(fr===0&&fc===7)newCast.bK=false;}
  nb[tr][tc]=p;nb[fr][fc]=null;
  if(p[1]==='P'&&(tr===0||tr===7))nb[tr][tc]=p[0]+'Q';
  return{nb,newEP,newCast};
}
function minimax(bd,depth,alpha,beta,maximizing,ep,cast){
  if(depth===0)return evaluate(bd);
  const color=maximizing?'w':'b';
  const moves=getAllMoves(bd,color,ep,cast);
  if(!moves.length){if(isInCheck(color,bd))return maximizing?-99999:99999;return 0;}
  if(maximizing){let best=-Infinity;for(const[fr,fc,tr,tc]of moves){const{nb,newEP,newCast}=applyMove(bd,fr,fc,tr,tc,cast);best=Math.max(best,minimax(nb,depth-1,alpha,beta,false,newEP,newCast));alpha=Math.max(alpha,best);if(beta<=alpha)break;}return best;}
  else{let best=Infinity;for(const[fr,fc,tr,tc]of moves){const{nb,newEP,newCast}=applyMove(bd,fr,fc,tr,tc,cast);best=Math.min(best,minimax(nb,depth-1,alpha,beta,true,newEP,newCast));beta=Math.min(beta,best);if(beta<=alpha)break;}return best;}
}
function scoreLose(mv){
  const{fr,fc,tr,tc}=mv;const VAL={P:1,N:3,B:3,R:5,Q:9,K:0};
  const tb=board.map(r=>[...r]);const p=tb[fr][fc];
  if(p[1]==='P'&&enPassant&&tr===enPassant[0]&&tc===enPassant[1])tb[fr][tc]=null;
  tb[tr][tc]=p;tb[fr][fc]=null;let s=0;
  if(isSquareAttacked(tr,tc,'w',tb))s+=VAL[p[1]]*10;
  if(isInCheck('w',tb))s+=5;
  for(let r=0;r<8;r++)for(let c=0;c<8;c++)if(tb[r][c]&&tb[r][c][0]==='b'&&isSquareAttacked(r,c,'w',tb))s+=VAL[tb[r][c][1]]*2;
  const bk=findKing('b',tb);if(bk&&isSquareAttacked(bk[0],bk[1],'w',tb))s+=50;
  if(p[1]==='K')s+=tr*2;if(board[tr][tc]&&board[tr][tc][0]==='w')s-=VAL[board[tr][tc][1]]*8;
  if(!hasLegalMoves('b',tb,null,castling))s+=isInCheck('b',tb)?1000:-500;
  return s+Math.random()*0.5;
}
function doAiMove(){
  if(gameOver||turn!=='b')return;
  const allMoves=getAllMoves(board,'b',enPassant,castling);if(!allMoves.length)return;
  let best=null;
  if(difficulty==='lose'){
    const mvObjs=allMoves.map(([fr,fc,tr,tc])=>({fr,fc,tr,tc}));let bestS=-Infinity;
    mvObjs.forEach(mv=>{const s=scoreLose(mv);if(s>bestS){bestS=s;best=mv;}});
    const isPromo=board[best.fr][best.fc][1]==='P'&&best.tr===7;
    makeMove(best.fr,best.fc,best.tr,best.tc,isPromo?'B':undefined);return;
  }
  const{depth,noise}=DIFFICULTIES[difficulty];let bestScore=Infinity;
  const shuffled=[...allMoves].sort(()=>Math.random()-0.5);
  for(const[fr,fc,tr,tc]of shuffled){
    const{nb,newEP,newCast}=applyMove(board,fr,fc,tr,tc,castling);
    let score=minimax(nb,depth-1,-Infinity,Infinity,true,newEP,newCast);
    score+=noise*(Math.random()-0.5)*200;
    if(score<bestScore){bestScore=score;best={fr,fc,tr,tc};}
  }
  if(best){const isPromo=board[best.fr][best.fc][1]==='P'&&best.tr===7;makeMove(best.fr,best.fc,best.tr,best.tc,isPromo?'Q':undefined);}
}

// ── CHESS LOGIC ────────────────────────────────────────────────
function getLegalMoves(r,c,bd,color,ep,cast){
  return getPseudo(r,c,bd,color,ep,cast).filter(([tr,tc])=>{
    const tb=bd.map(row=>[...row]);
    if(bd[r][c][1]==='P'&&ep&&tr===ep[0]&&tc===ep[1])tb[r][tc]=null;
    if(bd[r][c][1]==='K'&&Math.abs(tc-c)===2){const step=tc>c?1:-1;if(isSquareAttacked(r,c,color==='w'?'b':'w',bd))return false;if(isSquareAttacked(r,c+step,color==='w'?'b':'w',bd))return false;}
    tb[tr][tc]=tb[r][c];tb[r][c]=null;return!isInCheck(color,tb);
  });
}
function getPseudo(r,c,bd,color,ep,cast){
  const p=bd[r][c];if(!p||p[0]!==color)return[];
  const t=p[1],opp=color==='w'?'b':'w',moves=[];
  const add=(tr,tc)=>{if(tr>=0&&tr<8&&tc>=0&&tc<8&&(!bd[tr][tc]||bd[tr][tc][0]===opp)){moves.push([tr,tc]);return!bd[tr][tc];}return false;};
  const slide=(dr,dc)=>{let tr=r+dr,tc=c+dc;while(tr>=0&&tr<8&&tc>=0&&tc<8){if(bd[tr][tc]){if(bd[tr][tc][0]===opp)moves.push([tr,tc]);break;}moves.push([tr,tc]);tr+=dr;tc+=dc;}};
  if(t==='P'){const dir=color==='w'?-1:1,start=color==='w'?6:1;if(r+dir>=0&&r+dir<8&&!bd[r+dir][c]){moves.push([r+dir,c]);if(r===start&&!bd[r+2*dir][c])moves.push([r+2*dir,c]);}[-1,1].forEach(dc=>{const tr=r+dir,tc=c+dc;if(tr>=0&&tr<8&&tc>=0&&tc<8){if(bd[tr][tc]&&bd[tr][tc][0]===opp)moves.push([tr,tc]);if(ep&&tr===ep[0]&&tc===ep[1])moves.push([tr,tc]);}});}
  if(t==='N')[[-2,-1],[-2,1],[-1,-2],[-1,2],[1,-2],[1,2],[2,-1],[2,1]].forEach(([dr,dc])=>add(r+dr,c+dc));
  if(t==='B'||t==='Q')[[-1,-1],[-1,1],[1,-1],[1,1]].forEach(([dr,dc])=>slide(dr,dc));
  if(t==='R'||t==='Q')[[-1,0],[1,0],[0,-1],[0,1]].forEach(([dr,dc])=>slide(dr,dc));
  if(t==='K'){[[-1,-1],[-1,0],[-1,1],[0,-1],[0,1],[1,-1],[1,0],[1,1]].forEach(([dr,dc])=>add(r+dr,c+dc));const row=color==='w'?7:0;if(r===row&&c===4){if(cast[color+'K']&&!bd[row][5]&&!bd[row][6]&&bd[row][7]&&bd[row][7][1]==='R')moves.push([row,6]);if(cast[color+'Q']&&!bd[row][3]&&!bd[row][2]&&!bd[row][1]&&bd[row][0]&&bd[row][0][1]==='R')moves.push([row,2]);}}
  return moves;
}
function isInCheck(color,bd){const k=findKing(color,bd);if(!k)return false;return isSquareAttacked(k[0],k[1],color==='w'?'b':'w',bd);}
function findKing(color,bd){for(let r=0;r<8;r++)for(let c=0;c<8;c++)if(bd[r][c]===color+'K')return[r,c];return null;}
function isSquareAttacked(r,c,byColor,bd){for(let fr=0;fr<8;fr++)for(let fc=0;fc<8;fc++){const p=bd[fr][fc];if(!p||p[0]!==byColor)continue;if(getAttacks(fr,fc,bd,byColor).some(([ar,ac])=>ar===r&&ac===c))return true;}return false;}
function getAttacks(r,c,bd,color){const p=bd[r][c],t=p[1],moves=[];const add=(tr,tc)=>{if(tr>=0&&tr<8&&tc>=0&&tc<8)moves.push([tr,tc]);};if(t==='P'){const dir=color==='w'?-1:1;add(r+dir,c-1);add(r+dir,c+1);}if(t==='N')[[-2,-1],[-2,1],[-1,-2],[-1,2],[1,-2],[1,2],[2,-1],[2,1]].forEach(([dr,dc])=>add(r+dr,c+dc));if(t==='B'||t==='Q')[[-1,-1],[-1,1],[1,-1],[1,1]].forEach(([dr,dc])=>{let tr=r+dr,tc=c+dc;while(tr>=0&&tr<8&&tc>=0&&tc<8){moves.push([tr,tc]);if(bd[tr][tc])break;tr+=dr;tc+=dc;}});if(t==='R'||t==='Q')[[-1,0],[1,0],[0,-1],[0,1]].forEach(([dr,dc])=>{let tr=r+dr,tc=c+dc;while(tr>=0&&tr<8&&tc>=0&&tc<8){moves.push([tr,tc]);if(bd[tr][tc])break;tr+=dr;tc+=dc;}});if(t==='K')[[-1,-1],[-1,0],[-1,1],[0,-1],[0,1],[1,-1],[1,0],[1,1]].forEach(([dr,dc])=>add(r+dr,c+dc));return moves;}
function hasLegalMoves(color,bd,ep,cast){for(let r=0;r<8;r++)for(let c=0;c<8;c++)if(bd[r][c]&&bd[r][c][0]===color&&getLegalMoves(r,c,bd,color,ep,cast).length>0)return true;return false;}

setDiff('medium');
freshGame();
</script>
</body>
</html>
