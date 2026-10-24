<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no" />
  <title>Valorant Mobile Style FPS</title>
  <style>
    :root{
      --bg:#070d14;
      --panel: rgba(10,16,22,0.58);
      --line: rgba(255,255,255,0.12);
      --text:#eef7ff;
      --muted:#9aaec0;
      --cyan:#7de7ff;
      --green:#8cf0c5;
      --red:#ff5d7a;
      --gold:#ffd36a;
    }

    *{box-sizing:border-box;-webkit-tap-highlight-color:transparent;}
    html,body{
      margin:0; width:100%; height:100%; overflow:hidden;
      background:#000; color:var(--text);
      font-family:Arial, Helvetica, sans-serif;
      touch-action:none;
    }

    body{
      position:relative;
      background:radial-gradient(circle at center,#0a111a,#03070d 55%);
    }

    canvas{
      display:block; width:100vw; height:100vh;
      background:#000;
      touch-action:none;
    }

    #hud{
      position:fixed; inset:0; pointer-events:none; z-index:20;
    }

    .panel{
      background:var(--panel);
      border:1px solid var(--line);
      box-shadow:inset 0 0 16px rgba(125,231,255,0.08), 0 0 16px rgba(125,231,255,0.05);
      backdrop-filter:blur(2px);
    }

    #top{
      position:absolute; left:18px; top:18px;
      width:230px; padding:12px 14px; border-radius:14px;
    }

    .row{
      display:flex; justify-content:space-between; align-items:center;
      margin:6px 0; font-weight:700; letter-spacing:.09em;
      text-transform:uppercase; font-size:12px;
    }

    .value{
      color:var(--cyan); font-weight:900; letter-spacing:.08em;
      font-size:15px;
    }

    #crosshair{
      position:absolute; left:50%; top:50%;
      width:28px; height:28px; transform:translate(-50%,-50%);
    }
    .line{
      position:absolute; background:rgba(255,255,255,0.8);
      box-shadow:0 0 10px rgba(255,255,255,0.2);
    }
    .line.h{ left:50%; top:50%; width:2px; height:28px; transform:translate(-50%,-50%); }
    .line.v{ left:50%; top:50%; width:28px; height:2px; transform:translate(-50%,-50%); }

    #ammoPanel{
      position:absolute; left:50%; bottom:18px; transform:translateX(-50%);
      width:min(420px, calc(100vw - 28px));
      padding:10px 16px; border-radius:16px;
      display:flex; align-items:center; justify-content:space-between; gap:12px;
    }
    #ammo{ font-size:22px; font-weight:900; letter-spacing:.08em; }
    #reloadState{
      font-size:12px; font-weight:900; letter-spacing:.18em;
      text-transform:uppercase; color:var(--gold); opacity:0; transition:opacity .12s ease;
    }
    #reloadState.show{ opacity:1; }

    #message{
      position:absolute; left:50%; top:50%; transform:translate(-50%,-50%);
      font-size:30px; font-weight:900; letter-spacing:.14em;
      text-transform:uppercase; color:var(--text);
      white-space:nowrap; opacity:0; transition:opacity .12s ease;
      text-shadow:0 0 18px rgba(125,231,255,0.45);
    }
    #message.show{ opacity:1; }

    #controls{
      position:absolute; left:0; right:0; bottom:0; height:220px; pointer-events:none;
    }

    .touch-zone{
      position:absolute; border-radius:50%; pointer-events:auto;
      background:rgba(255,255,255,0.04);
      border:1px solid rgba(255,255,255,0.12);
      box-shadow:inset 0 0 16px rgba(255,255,255,0.04);
    }

    #joyBase{
      left:24px; bottom:24px; width:138px; height:138px;
    }
    #joyThumb{
      position:absolute; left:50%; top:50%;
      width:52px; height:52px; border-radius:50%;
      transform:translate(-50%,-50%);
      background:linear-gradient(135deg, rgba(125,231,255,0.8), rgba(140,240,197,0.7));
      border:1px solid rgba(255,255,255,0.18);
      box-shadow:0 10px 18px rgba(125,231,255,0.2);
    }

    #aimBase{
      right:26px; bottom:24px; width:150px; height:150px;
    }
    #aimDot{
      position:absolute; left:50%; top:50%;
      width:54px; height:54px; border-radius:50%;
      transform:translate(-50%,-50%);
      background:linear-gradient(135deg, rgba(255,93,122,0.7), rgba(255,211,106,0.7));
      border:1px solid rgba(255,255,255,0.18);
      box-shadow:0 10px 20px rgba(255,93,122,0.2);
    }

    .action-btn{
      position:absolute; right:54px; bottom:168px;
      width:92px; height:92px; border-radius:50%;
      border:1px solid rgba(255,255,255,0.16);
      background:radial-gradient(circle at 35% 30%, rgba(255,130,130,0.8), rgba(255,93,122,0.34) 35%, rgba(0,0,0,0.22) 100%);
      box-shadow:0 12px 24px rgba(255,93,122,0.18), inset 0 0 16px rgba(255,255,255,0.08);
      color:white; font-weight:900; letter-spacing:.12em; font-size:12px;
      pointer-events:auto; user-select:none;
    }

    .skill-btn{
      position:absolute; right:20px; bottom:240px;
      width:60px; height:60px; border-radius:14px;
      border:1px solid rgba(255,255,255,0.12);
      color:var(--text); font-weight:900; letter-spacing:.12em;
      font-size:11px; background:rgba(255,255,255,0.04);
      pointer-events:auto; display:flex; align-items:center; justify-content:center;
      box-shadow:0 0 18px rgba(125,231,255,0.08);
    }
    .skill-btn.g{ right:92px; bottom:240px; }
    .skill-btn.e{ right:20px; bottom:180px; }
    .skill-btn.c{ right:92px; bottom:180px; }

    #reloadBtn{
      position:absolute; right:24px; bottom:108px;
      width:90px; height:42px; border-radius:10px;
      pointer-events:auto; border:1px solid rgba(255,255,255,0.12);
      background:rgba(255,255,255,0.04); color:var(--text);
      font-size:11px; font-weight:900; letter-spacing:.16em; text-transform:uppercase;
    }

    @media (pointer: fine) {
      #controls { display: none; }
      #hud { pointer-events: none; }
    }
  </style>
</head>
<body>
  <canvas id="game"></canvas>

  <div id="hud">
    <div id="top" class="panel">
      <div class="row"><span>HP</span><span id="hpValue" class="value">100</span></div>
      <div class="row"><span>Armor</span><span id="armorValue" class="value">100</span></div>
      <div class="row"><span>Round</span><span id="roundValue" class="value">1</span></div>
    </div>

    <div id="crosshair">
      <div class="line h"></div>
      <div class="line v"></div>
    </div>

    <div id="ammoPanel" class="panel">
      <div id="ammo">12 / 48</div>
      <div id="reloadState">Reloading</div>
    </div>

    <div id="message">Area Clear</div>

    <div id="controls">
      <div id="joyBase" class="touch-zone">
        <div id="joyThumb"></div>
      </div>

      <div id="aimBase" class="touch-zone">
        <div id="aimDot"></div>
      </div>

      <button class="action-btn" id="fireBtn">FIRE</button>

      <button class="skill-btn g" id="qBtn">Q</button>
      <button class="skill-btn e" id="eBtn">E</button>
      <button class="skill-btn c" id="cBtn">C</button>

      <button id="reloadBtn">Reload</button>
    </div>
  </div>

  <script>
    const canvas = document.getElementById("game");
    const ctx = canvas.getContext("2d");

    const hpValue = document.getElementById("hpValue");
    const armorValue = document.getElementById("armorValue");
    const roundValue = document.getElementById("roundValue");
    const ammoValue = document.getElementById("ammo");
    const reloadState = document.getElementById("reloadState");
    const messageEl = document.getElementById("message");

    const map = [
      "11111111111111111111",
      "10000000000000000001",
      "10111101101101111001",
      "10000100001000001001",
      "10110110111011011001",
      "10010000000001000001",
      "10110111101101111001",
      "10000001001000000001",
      "11101001011101101001",
      "10001001000001001001",
      "10111011101101111001",
      "10000000010000000001",
      "11111111111111111111"
    ];

    const FOV = Math.PI / 3.2;
    const MAX_DEPTH = 22;

    const player = {
      x: 2.5,
      y: 2.5,
      angle: 0,
      hp: 100,
      armor: 100,
      ammo: 12,
      reserve: 48,
      fireCooldown: 0,
      reloadTimer: 0,
      flash: 0,
      qCooldown: 0,
      eCooldown: 0,
      cCooldown: 0,
      smokeTimer: 0
    };

    const enemies = [];
    const smokeClouds = [];
    const keys = {};

    let round = 1;
    let lastTs = 0;
    let fireHeld = false;
    let gameRunning = true;

    const joy = {
      active: false,
      pointerId: null,
      x: 0,
      y: 0,
      base: document.getElementById("joyBase"),
      thumb: document.getElementById("joyThumb"),
      maxRadius: 42
    };

    const aim = {
      active: false,
      pointerId: null,
      x: 0,
      y: 0,
      base: document.getElementById("aimBase"),
      dot: document.getElementById