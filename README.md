<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8">
  <title>Clean Gauge Cluster JGVRP - Fully Synced</title>
  <style>
    * { 
      box-sizing: border-box; 
      margin: 0; 
      padding: 0; 
      font-family: 'Segoe UI', Arial, sans-serif; 
    }
    
    html, body { 
      background: transparent !important; 
      overflow: hidden; 
      width: 100vw;
      height: 100vh; 
      position: relative; 
      color: #fff; 
    }

    /* Posisi Cluster di Pojok Kanan Bawah */
    .cluster-container {
      position: absolute;
      bottom: 45px;
      right: 45px;
      display: flex;
      align-items: center;
      transform: scale(1.25);
      transform-origin: bottom right;
      filter: drop-shadow(0 0 18px rgba(0, 0, 0, 0.95));
      z-index: 10;
    }

    /* Lingkaran Speedometer Utama (Kiri) */
    .main-dial {
      position: relative;
      width: 280px;
      height: 280px;
      border-radius: 50%;
      background: radial-gradient(circle, rgba(25, 0, 0, 0.95) 0%, rgba(8, 0, 0, 0.98) 70%, rgba(0, 0, 0, 1) 100%);
      border: 3px solid #331111;
      box-shadow: 0 0 25px rgba(255, 0, 0, 0.5), inset 0 0 20px rgba(255, 0, 0, 0.25);
      display: flex;
      justify-content: center;
      align-items: center;
    }

    .dial-svg {
      position: absolute;
      width: 100%;
      height: 100%;
    }

    /* Speedometer Digital di Dalam Kotak Utama */
    .boxed-digital-speed {
      position: absolute;
      left: 100px;
      top: 75px;
      width: 80px;
      height: 42px;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      z-index: 6;
      background: rgba(15, 0, 0, 0.75);
      border: 1.5px solid rgba(255, 42, 42, 0.5);
      border-radius: 6px;
      box-shadow: inset 0 0 8px rgba(255, 0, 0, 0.3), 0 0 8px rgba(255, 0, 0, 0.2);
    }

    .digital-val {
      font-size: 24px;
      font-weight: 900;
      color: #ffffff;
      font-family: 'Courier New', Courier, monospace;
      font-variant-numeric: tabular-nums;
      text-shadow: 0 0 10px rgba(255, 255, 255, 0.9), 0 0 15px rgba(255, 0, 0, 0.8);
      line-height: 1;
      text-align: center;
      width: 100%;
    }

    .digital-unit-sub {
      font-size: 8px;
      font-weight: 800;
      color: #ff6666;
      letter-spacing: 1px;
      margin-top: 1px;
      text-shadow: 0 0 4px #ff0000;
    }

    /* Jarum Speedometer Utama */
    .needle-wrapper {
      position: absolute;
      width: 100%;
      height: 100%;
      border-radius: 50%;
      transform: rotate(141.5deg);
      transition: transform 0.12s cubic-bezier(0.1, 0.5, 0.1, 1);
      z-index: 10;
      pointer-events: none;
    }

    .needle-wrapper.sweeping {
      transition: transform 1.2s cubic-bezier(0.4, 0, 0.2, 1) !important;
    }

    .needle {
      position: absolute;
      top: 50%;
      left: 50%;
      width: 92px;
      height: 3.5px;
      background: linear-gradient(90deg, #ff1a1a 0%, #ffffff 100%);
      transform-origin: 0% 50%;
      border-radius: 2px;
      box-shadow: 0 0 10px #ff0000, 0 0 18px #ff0000;
      margin-top: -1.75px;
    }

    .needle-cap {
      position: absolute;
      width: 22px;
      height: 22px;
      background: #110505;
      border: 2.5px solid #ff2a2a;
      border-radius: 50%;
      z-index: 12;
      box-shadow: 0 0 8px #ff0000;
    }

    /* Notifikasi Icon di Dalam Dial Utama */
    .main-status-icons {
      position: absolute;
      bottom: 72px;
      display: flex;
      gap: 8px;
      z-index: 5;
    }

    .icon-box {
      width: 18px;
      height: 18px;
      display: flex;
      align-items: center;
      justify-content: center;
    }

    .icon-svg {
      width: 100%;
      height: 100%;
      fill: none;
      stroke: rgba(255, 255, 255, 0.2);
      stroke-width: 28;
      stroke-linecap: round;
      stroke-linejoin: round;
      transition: all 0.3s ease;
    }

    .icon-box.active-red .icon-svg {
      stroke: #ff2a2a;
      filter: drop-shadow(0 0 6px #ff0000);
    }

    @keyframes blink-yellow {
      0% { stroke: #ffcc00; filter: drop-shadow(0 0 6px #ffaa00); opacity: 1; }
      50% { stroke: #554400; filter: none; opacity: 0.3; }
      100% { stroke: #ffcc00; filter: drop-shadow(0 0 6px #ffaa00); opacity: 1; }
    }

    .icon-box.blink-yellow .icon-svg {
      animation: blink-yellow 0.8s infinite;
    }

    .bottom-info-box {
      position: absolute;
      bottom: 22px;
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 2px;
      z-index: 5;
    }

    .gear-display-box {
      background: rgba(15, 0, 0, 0.85);
      border: 1.5px solid #ff2a2a;
      padding: 1px 10px;
      border-radius: 4px;
      color: #ffcc00;
      font-size: 18px;
      font-weight: 900;
      font-family: 'Courier New', monospace;
      text-shadow: 0 0 8px #ffcc00;
      box-shadow: 0 0 8px rgba(255, 0, 0, 0.4);
    }

    .gear-label-text {
      font-size: 11px;
      font-weight: 900;
      color: #ff2a2a;
      letter-spacing: 0.5px;
      text-shadow: 0 0 5px #ff0000;
    }

    /* Kolom Lingkaran Kecil Kanan */
    .side-dials-column {
      display: flex;
      flex-direction: column;
      gap: 12px;
      margin-left: -22px;
      z-index: 2;
    }

    .small-analog-dial {
      position: relative;
      width: 120px;
      height: 120px;
      border-radius: 50%;
      background: radial-gradient(circle, rgba(20, 0, 0, 0.95) 0%, rgba(5, 0, 0, 0.98) 100%);
      border: 3px solid #331111;
      box-shadow: 0 0 15px rgba(255, 0, 0, 0.4), inset 0 0 12px rgba(255, 0, 0, 0.25);
      display: flex;
      justify-content: center;
      align-items: center;
    }

    .small-dial-svg {
      position: absolute;
      width: 100%;
      height: 100%;
    }

    .small-needle-wrapper {
      position: absolute;
      width: 100%;
      height: 100%;
      border-radius: 50%;
      transform: rotate(135deg);
      transition: transform 0.3s cubic-bezier(0.1, 0.5, 0.1, 1);
      z-index: 10;
      pointer-events: none;
    }

    .small-needle {
      position: absolute;
      top: 50%;
      left: 50%;
      width: 38px;
      height: 2px;
      background: linear-gradient(90deg, #ff1a1a 0%, #ffffff 100%);
      transform-origin: 0% 50%;
      box-shadow: 0 0 8px #ff0000;
      margin-top: -1px;
    }

    .small-needle-cap {
      position: absolute;
      width: 14px;
      height: 14px;
      background: #110505;
      border: 2px solid #ff2a2a;
      border-radius: 50%;
      z-index: 12;
      box-shadow: 0 0 6px #ff0000;
    }

    .mini-icon-center {
      position: absolute;
      top: 25px;
      width: 14px;
      height: 14px;
      fill: rgba(255, 255, 255, 0.4);
      filter: drop-shadow(0 0 3px #ff0000);
      z-index: 4;
    }

    .small-boxed-digital {
      position: absolute;
      bottom: 16px;
      width: 48px;
      height: 21px;
      background: rgba(12, 0, 0, 0.92);
      border: 1.2px solid rgba(255, 42, 42, 0.65);
      border-radius: 4px;
      display: flex;
      align-items: center;
      justify-content: center;
      z-index: 15;
      box-shadow: inset 0 0 5px rgba(255,0,0,0.35), 0 0 5px rgba(0,0,0,0.8);
    }

    .small-digital-val {
      font-size: 11px;
      font-weight: 900;
      font-family: 'Courier New', Courier, monospace;
      font-variant-numeric: tabular-nums;
      line-height: 1;
      text-align: center;
    }

    .status-good { color: #00ff66; text-shadow: 0 0 6px #00ff66; }
    .status-warning { color: #ffcc00; text-shadow: 0 0 6px #ffcc00; }
    .status-danger { color: #ff2a2a; text-shadow: 0 0 6px #ff2a2a; }

    /* Panel Uji Coba Browser (Akan Otomatis Disembunyikan di Game FiveM) */
    .test-panel {
      position: absolute;
      bottom: 25px;
      left: 25px;
      background: rgba(15, 5, 8, 0.95);
      padding: 14px 18px;
      border-radius: 8px;
      border: 1px solid rgba(255, 42, 42, 0.3);
      width: 270px;
      box-shadow: 0 10px 25px rgba(0,0,0,0.8);
      z-index: 100;
    }
    .test-panel h3 { font-size: 12px; margin-bottom: 8px; color: #ff2a2a; text-align: center; text-shadow: 0 0 6px #ff0000; }
    .test-group { margin-bottom: 6px; }
    .test-group label { display: block; font-size: 10px; margin-bottom: 2px; color: #ccc; }
    .test-group input, .test-group select { width: 100%; background: #150505; border: 1px solid #441111; color: #fff; padding: 4px; border-radius: 4px; font-size: 11px; }
    .test-checkboxes { display: grid; grid-template-columns: 1fr 1fr; gap: 4px; margin: 8px 0; font-size: 10px; }
    .btn-demo { width: 100%; background: #990000; color: #fff; border: 1px solid #ff3333; padding: 6px; font-weight: bold; border-radius: 4px; cursor: pointer; margin-top: 4px; font-size: 11px; transition: 0.2s; text-shadow: 0 0 5px #000; }
    .btn-demo:hover { background: #cc0000; box-shadow: 0 0 10px rgba(255,0,0,0.6); }
  </style>
</head>
<body>

  <div class="cluster-container" id="cluster-hud">

    <!-- Speedometer Utama (Kiri) -->
    <div class="main-dial">
      <svg class="dial-svg" viewBox="0 0 300 300" id="speedo-svg"></svg>

      <div class="boxed-digital-speed">
        <div class="digital-val" id="digital-speed-val">0</div>
        <div class="digital-unit-sub">MPH</div>
      </div>

      <div class="main-status-icons">
        <div class="icon-box" id="icon-engine" title="Engine">
          <svg class="icon-svg" viewBox="0 0 512 512">
            <path d="M256 64v192" />
            <path d="M128 160a160 160 0 1 0 256 0" />
          </svg>
        </div>
        <div class="icon-box" id="icon-check-engine" title="Engine Health">
          <svg class="icon-svg" viewBox="0 0 512 512" style="fill: none;">
            <path d="M400 192v128h-32v48h-64v-48H208v48h-64v-48h-32V192h32v-32h64v32h128v-32h64v32z" />
            <path d="M80 256H40v-48h40M432 256h40v-48h-40" />
          </svg>
        </div>
        <div class="icon-box" id="icon-seatbelt" title="Seatbelt">
          <svg class="icon-svg" viewBox="0 0 512 512">
            <path d="M128 96v192c0 53 43 96 96 96h64c53 0 96-43 96-96V96" />
            <path d="M256 160v160" />
            <path d="M192 416h128" />
          </svg>
        </div>
        <div class="icon-box" id="icon-lock" title="Lock">
          <svg class="icon-svg" viewBox="0 0 512 512">
            <rect x="144" y="224" width="224" height="200" rx="48" />
            <path d="M176 224V160a80 80 0 01160 0v64" fill="none" />
            <circle cx="256" cy="304" r="32" />
          </svg>
        </div>
        <div class="icon-box" id="icon-lights" title="Lights">
          <svg class="icon-svg" viewBox="0 0 512 512">
            <path d="M128 192h192a96 96 0 0196 96 96 96 0 01-96 96H128z" />
            <path d="M128 288H64M160 210l-40-40M160 366l-40 40" />
          </svg>
        </div>
      </div>

      <div class="bottom-info-box">
        <div class="gear-display-box" id="gear-val">1</div>
        <div class="gear-label-text">GEAR</div>
      </div>

      <div class="needle-wrapper" id="main-needle-wrapper">
        <div class="needle"></div>
      </div>
      <div class="needle-cap"></div>
    </div>

    <!-- Kolom Dial Kecil Kanan -->
    <div class="side-dials-column">
      
      <!-- Lingkaran Bensin -->
      <div class="small-analog-dial">
        <svg class="small-dial-svg" viewBox="0 0 200 200" id="fuel-svg"></svg>
        <svg class="mini-icon-center" viewBox="0 0 24 24">
          <path d="M19.77 7.23l.01-.01-3.72-3.72L15 4.56l2.11 2.11c-.94.36-1.61 1.26-1.61 2.33 0 1.38 1.12 2.5 2.5 2.5.36 0 .69-.08 1-.21v7.21c0 .55-.45 1-1 1s-1-.45-1-1V14c0-1.1-.9-2-2-2H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h6c1.1 0 2-.9 2-2v-4h1v3.5c0 1.38 1.12 2.5 2.5 2.5s2.5-1.12 2.5-2.5V9c0-.69-.28-1.32-.73-1.77zM11 10H5V5h6v5z"/>
        </svg>
        <div class="small-needle-wrapper" id="fuel-needle-wrapper">
          <div class="small-needle"></div>
        </div>
        <div class="small-needle-cap"></div>
        <div class="small-boxed-digital">
          <span class="small-digital-val status-good" id="digital-fuel-val">100%</span>
        </div>
      </div>

      <!-- Lingkaran Kondisi Mesin -->
      <div class="small-analog-dial">
        <svg class="small-dial-svg" viewBox="0 0 200 200" id="engine-svg"></svg>
        <svg class="mini-icon-center" viewBox="0 0 24 24">
          <path d="M7 4h10v2H7zm0 14h10v2H7zm11-7h1v3h-1zm-14 0h1v3H4zm2-3h12v8H6z"/>
        </svg>
        <div class="small-needle-wrapper" id="engine-needle-wrapper">
          <div class="small-needle"></div>
        </div>
        <div class="small-needle-cap"></div>
        <div class="small-boxed-digital">
          <span class="small-digital-val status-good" id="digital-engine-val">100%</span>
        </div>
      </div>

    </div>

  </div>

  <!-- Panel Uji Coba Browser -->
  <div class="test-panel" id="test-panel">
    <h3>PANEL UJI COBA</h3>
    <div class="test-group">
      <label>Speed: <span id="speed-label">0</span></label>
      <input type="range" id="range-speed" min="0" max="180" value="0">
    </div>
    <div class="test-group">
      <label>Fuel (%): <span id="fuel-label">100</span></label>
      <input type="range" id="range-fuel" min="0" max="100" value="100">
    </div>
    <div class="test-group">
      <label>Engine Health (%): <span id="engine-label">100</span></label>
      <input type="range" id="range-engine-health" min="0" max="100" value="100">
    </div>
    <div class="test-group">
      <label>Gear:</label>
      <select id="select-gear">
        <option value="R">R</option>
        <option value="N">N</option>
        <option value="1" selected>1</option>
        <option value="2">2</option>
        <option value="3">3</option>
        <option value="4">4</option>
        <option value="5">5</option>
        <option value="6">6</option>
      </select>
    </div>
    <div class="test-checkboxes">
      <label><input type="checkbox" id="chk-engine"> Engine ON</label>
      <label><input type="checkbox" id="chk-seatbelt"> Seatbelt</label>
      <label><input type="checkbox" id="chk-lock"> Lock</label>
      <label><input type="checkbox" id="chk-lights"> Lights</label>
    </div>
    <button class="btn-demo" id="btn-start-demo">▶ Test Animasi Jarum</button>
  </div>

  <script>
    const START_DEG = 141.5; 
    const TOTAL_SWEEP_DEG = 257;

    function renderDialSVG() {
      const svg = document.getElementById('speedo-svg');
      if (!svg) return;
      const cx = 150, cy = 150;
      let html = '';

      const totalTicks = 90;
      for (let i = 0; i <= totalTicks; i++) {
        let angle = START_DEG + (i / totalTicks) * TOTAL_SWEEP_DEG;
        let rad = angle * (Math.PI / 180);
        let isMajor = i % 5 === 0;
        let rOuter = 135, rInner = isMajor ? 118 : 126;

        let x1 = cx + rOuter * Math.cos(rad);
        let y1 = cy + rOuter * Math.sin(rad);
        let x2 = cx + rInner * Math.cos(rad);
        let y2 = cy + rInner * Math.sin(rad);

        html += `<line x1="${x1}" y1="${y1}" x2="${x2}" y2="${y2}" stroke="#ff2a2a" stroke-width="${isMajor ? 2.8 : 1.4}" stroke-linecap="round" filter="drop-shadow(0 0 3px #ff0000)"/>`;
      }

      for (let mph = 0; mph <= 180; mph += 20) {
        let pct = mph / 180;
        let angle = START_DEG + (pct * TOTAL_SWEEP_DEG);
        let rad = angle * (Math.PI / 180);
        let rText = 100;

        let x = cx + rText * Math.cos(rad);
        let y = cy + rText * Math.sin(rad);

        html += `<text x="${x}" y="${y}" fill="#ffffff" font-size="13" font-weight="900" text-anchor="middle" dominant-baseline="central" font-family="Arial, sans-serif" filter="drop-shadow(0 0 4px #ff0000)">${mph}</text>`;
      }

      svg.innerHTML = html;
    }

    function renderSmallDialSVG(svgId, labelStart, labelEnd) {
      const svg = document.getElementById(svgId);
      if (!svg) return;
      const cx = 100, cy = 100;
      let html = '';
      
      const smallStartDeg = 135;
      const smallSweepDeg = 270;

      const totalTicks = 20;
      for (let i = 0; i <= totalTicks; i++) {
        let angle = smallStartDeg + (i / totalTicks) * smallSweepDeg;
        let rad = angle * (Math.PI / 180);
        let isMajor = i % 5 === 0;
        let rOuter = 85, rInner = isMajor ? 73 : 79;

        let x1 = cx + rOuter * Math.cos(rad);
        let y1 = cy + rOuter * Math.sin(rad);
        let x2 = cx + rInner * Math.cos(rad);
        let y2 = cy + rInner * Math.sin(rad);

        html += `<line x1="${x1}" y1="${y1}" x2="${x2}" y2="${y2}" stroke="#ff2a2a" stroke-width="${isMajor ? 2 : 1}" stroke-linecap="round" filter="drop-shadow(0 0 2px #ff0000)"/>`;
      }

      if (labelStart && labelEnd) {
        let radStart = smallStartDeg * (Math.PI / 180);
        let radEnd = (smallStartDeg + smallSweepDeg) * (Math.PI / 180);
        let rText = 60;

        let xStart = cx + rText * Math.cos(radStart);
        let yStart = cy + rText * Math.sin(radStart);
        let xEnd = cx + rText * Math.cos(radEnd);
        let yEnd = cy + rText * Math.sin(radEnd);

        html += `<text x="${xStart}" y="${yStart}" fill="#ff3333" font-size="9" font-weight="900" text-anchor="middle" dominant-baseline="central" filter="drop-shadow(0 0 3px #ff0000)">${labelStart}</text>`;
        html += `<text x="${xEnd}" y="${yEnd}" fill="#00ff66" font-size="9" font-weight="900" text-anchor="middle" dominant-baseline="central" filter="drop-shadow(0 0 3px #00ff66)">${labelEnd}</text>`;
      }

      svg.innerHTML = html;
    }

    renderDialSVG();
    renderSmallDialSVG('fuel-svg', 'E', 'F');
    renderSmallDialSVG('engine-svg', '', '');

    const digitalSpeedVal = document.getElementById('digital-speed-val');
    const digitalFuelVal = document.getElementById('digital-fuel-val');
    const digitalEngineVal = document.getElementById('digital-engine-val');
    const gearText = document.getElementById('gear-val');
    const mainNeedleWrapper = document.getElementById('main-needle-wrapper');
    const fuelNeedleWrapper = document.getElementById('fuel-needle-wrapper');
    const engineNeedleWrapper = document.getElementById('engine-needle-wrapper');

    const iconEngine = document.getElementById('icon-engine');
    const iconCheckEngine = document.getElementById('icon-check-engine');
    const iconSeatbelt = document.getElementById('icon-seatbelt');
    const iconLock = document.getElementById('icon-lock');
    const iconLights = document.getElementById('icon-lights');

    let vehicleData = {
      speed: 0,
      fuel: 100,
      engineHealth: 100,
      gear: 1,
      engine: false,
      seatbelt: false,
      locked: false,
      lights: false
    };

    let isSweeping = false;

    // Fungsi Animasi Jarum Saat Mesin Nyalakan
    function triggerNeedleSweep() {
      if (isSweeping) return;
      isSweeping = true;

      mainNeedleWrapper.classList.add('sweeping');
      let maxAngle = START_DEG + TOTAL_SWEEP_DEG;
      mainNeedleWrapper.style.transform = `rotate(${maxAngle}deg)`;

      setTimeout(() => {
        mainNeedleWrapper.style.transform = `rotate(${START_DEG}deg)`;
        setTimeout(() => {
          mainNeedleWrapper.classList.remove('sweeping');
          isSweeping = false;
          setSpeed(vehicleData.speed);
        }, 1200);
      }, 1200);
    }

    function setSpeed(speed) {
      if (isSweeping) return;
      let rounded = Math.max(0, Math.min(180, Math.round(speed)));
      if (digitalSpeedVal) digitalSpeedVal.innerText = rounded;
      let pct = rounded / 180;
      let targetAngle = START_DEG + (pct * TOTAL_SWEEP_DEG);
      if (mainNeedleWrapper) mainNeedleWrapper.style.transform = `rotate(${targetAngle}deg)`;
    }

    function setFuel(fuelPct) {
      let rounded = Math.max(0, Math.min(100, Math.round(fuelPct)));
      if (digitalFuelVal) {
        digitalFuelVal.innerText = rounded + '%';
        digitalFuelVal.className = 'small-digital-val';
        if (rounded > 50) digitalFuelVal.classList.add('status-good');
        else if (rounded >= 20) digitalFuelVal.classList.add('status-warning');
        else digitalFuelVal.classList.add('status-danger');
      }

      let pct = rounded / 100;
      let angle = 135 + (pct * 270);
      if (fuelNeedleWrapper) fuelNeedleWrapper.style.transform = `rotate(${angle}deg)`;
    }

    function setEngineHealth(healthPct) {
      let rounded = Math.max(0, Math.min(100, Math.round(healthPct)));
      if (digitalEngineVal) {
        digitalEngineVal.innerText = rounded + '%';
        digitalEngineVal.className = 'small-digital-val';
        if (rounded > 50) digitalEngineVal.classList.add('status-good');
        else if (rounded >= 20) digitalEngineVal.classList.add('status-warning');
        else digitalEngineVal.classList.add('status-danger');
      }

      let pct = rounded / 100;
      let angle = 135 + (pct * 270);
      if (engineNeedleWrapper) engineNeedleWrapper.style.transform = `rotate(${angle}deg)`;
    }

    function updateUI() {
      // Seatbelt
      if (iconSeatbelt) {
        iconSeatbelt.classList.toggle('active-red', Boolean(vehicleData.seatbelt));
      }
      // Engine Status Icon
      if (iconEngine) {
        iconEngine.classList.toggle('active-red', Boolean(vehicleData.engine));
      }

      // Engine Health Warning
      if (iconCheckEngine) {
        iconCheckEngine.classList.remove('active-red', 'blink-yellow');
        if (vehicleData.engineHealth < 30) {
          iconCheckEngine.classList.add('blink-yellow');
        } else if (vehicleData.engineHealth < 60) {
          iconCheckEngine.classList.add('active-red');
        }
      }

      // Lock & Lights
      if (iconLock) iconLock.classList.toggle('active-red', Boolean(vehicleData.locked));
      if (iconLights) iconLights.classList.toggle('active-red', Boolean(vehicleData.lights));

      setFuel(vehicleData.fuel);
      setEngineHealth(vehicleData.engineHealth);
      if (gearText) gearText.innerText = vehicleData.gear;
      if (!isSweeping) setSpeed(vehicleData.speed);
    }

    /* SISTEM KOMPATIBILITAS DATA FIVEM YANG LUAS */
    window.addEventListener('message', (event) => {
      const data = event.data;
      if (!data) return;

      // Sembunyikan panel uji coba browser jika dijalankan dalam game FiveM
      const testPanel = document.getElementById('test-panel');
      if (testPanel) testPanel.style.display = 'none';

      // Visibilitas HUD dari game
      const cluster = document.getElementById('cluster-hud');
      if (cluster && data.showHUD !== undefined) {
        cluster.style.display = data.showHUD ? 'flex' : 'none';
      }

      // 1. Kecepatan (Mendukung data speed / kecepatan / velocity)
      if (data.speed !== undefined) vehicleData.speed = data.speed;

      // 2. Bensin (Mendukung berbagai penamaan variabel bensin framework)
      let fVal = data.fuel !== undefined ? data.fuel : (data.fuelLevel !== undefined ? data.fuelLevel : data.bensin);
      if (fVal !== undefined) vehicleData.fuel = fVal;

      // 3. Kondisi Mesin (Mendukung 0-1000 atau 0-100)
      let eVal = data.engineHealth !== undefined ? data.engineHealth : (data.engine !== undefined && typeof data.engine === 'number' ? data.engine : data.health);
      if (eVal !== undefined) {
        if (eVal > 100) eVal = eVal / 10;
        vehicleData.engineHealth = eVal;
      }

      // 4. Gigi / Gear
      if (data.gear !== undefined) {
        vehicleData.gear = (data.gear === 0 || data.gear === "0") ? "R" : data.gear;
      }

      // 5. Status Mesin & Animasi Nyala (Mendeteksi perubahan engine mati ke nyala)
      let isEngineRunning = data.engineOn !== undefined ? data.engineOn : (data.engine !== undefined ? data.engine : null);
      if (isEngineRunning !== null && typeof isEngineRunning === 'boolean') {
        let wasOff = !vehicleData.engine;
        vehicleData.engine = isEngineRunning;
        if (wasOff && vehicleData.engine) {
          triggerNeedleSweep(); // Animasi jarum jalan saat mesin dinyalakan!
        }
      }

      // 6. Seatbelt (Mendukung belt / seatbelt / isSeatbelted)
      let sBelt = data.seatbelt !== undefined ? data.seatbelt : (data.belt !== undefined ? data.belt : data.isSeatbelted);
      if (sBelt !== undefined) vehicleData.seatbelt = Boolean(sBelt);

      // 7. Lock / Kunci Pintu (Mendukung locked / doorLock / isLocked)
      let vLock = data.locked !== undefined ? data.locked : (data.doorLock !== undefined ? data.doorLock : data.isLocked);
      if (vLock !== undefined) {
        // Terkadang status doorLock berupa angka (0 = terbuka, 1/2 = terkunci)
        vehicleData.locked = typeof vLock === 'number' ? (vLock > 0) : Boolean(vLock);
      }

      // 8. Lampu (Mendukung lights / headlights)
      let vLights = data.lights !== undefined ? data.lights : data.headlights;
      if (vLights !== undefined) vehicleData.lights = Boolean(vLights);

      updateUI();
    });

    /* KONTROLER PANEL UJI COBA (Hanya tampil di Browser) */
    const rangeSpeed = document.getElementById('range-speed');
    const speedLabel = document.getElementById('speed-label');
    const rangeFuel = document.getElementById('range-fuel');
    const fuelLabel = document.getElementById('fuel-label');
    const rangeEngineHealth = document.getElementById('range-engine-health');
    const engineLabel = document.getElementById('engine-label');
    const selectGear = document.getElementById('select-gear');

    const chkEngine = document.getElementById('chk-engine');
    const chkSeatbelt = document.getElementById('chk-seatbelt');
    const chkLock = document.getElementById('chk-lock');
    const chkLights = document.getElementById('chk-lights');

    rangeSpeed.addEventListener('input', (e) => {
      let val = Number(e.target.value);
      speedLabel.innerText = val;
      vehicleData.speed = val;
      setSpeed(val);
    });

    rangeFuel.addEventListener('input', (e) => {
      vehicleData.fuel = Number(e.target.value);
      fuelLabel.innerText = vehicleData.fuel;
      updateUI();
    });

    rangeEngineHealth.addEventListener('input', (e) => {
      vehicleData.engineHealth = Number(e.target.value);
      engineLabel.innerText = vehicleData.engineHealth;
      updateUI();
    });

    selectGear.addEventListener('change', (e) => {
      vehicleData.gear = e.target.value;
      updateUI();
    });

    chkEngine.addEventListener('change', (e) => {
      let wasOff = !vehicleData.engine;
      vehicleData.engine = e.target.checked;
      if (wasOff && vehicleData.engine) triggerNeedleSweep();
      updateUI();
    });

    chkSeatbelt.addEventListener('change', (e) => {
      vehicleData.seatbelt = e.target.checked;
      updateUI();
    });

    chkLock.addEventListener('change', (e) => {
      vehicleData.locked = e.target.checked;
      updateUI();
    });

    chkLights.addEventListener('change', (e) => {
      vehicleData.lights = e.target.checked;
      updateUI();
    });

    document.getElementById('btn-start-demo').addEventListener('click', () => {
      triggerNeedleSweep();
    });

    setSpeed(0);
    updateUI();
  </script>
</body>
</html>
