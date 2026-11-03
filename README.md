# QR-CODE-READER-2
Reading QR codes

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>QR Code Reader · Live Camera & Image</title>
<style>
  * { box-sizing: border-box; margin: 0; font-family: system-ui, 'Segoe UI', Roboto, sans-serif; }
  body {
    background: linear-gradient(145deg, #eef2f7 0%, #dfe9f5 100%);
    min-height: 100vh;
    padding: 1.5rem;
    display: flex;
    justify-content: center;
    align-items: flex-start;
  }
  .app {
    max-width: 900px;
    width: 100%;
    background: rgba(255,255,255,.85);
    backdrop-filter: blur(10px);
    border-radius: 2rem;
    padding: 2rem;
    box-shadow: 0 25px 50px -12px rgba(0,20,40,.25);
    border: 1px solid rgba(255,255,255,.8);
  }
  h1 {
    font-size: 1.9rem;
    color: #0b2b4a;
    display: flex;
    align-items: center;
    gap: .6rem;
    margin-bottom: .3rem;
  }
  h1 span {
    background: #1e3f6e;
    color: white;
    font-size: .85rem;
    padding: .2rem .8rem;
    border-radius: 100px;
    font-weight: 500;
  }
  .subhead {
    color: #2e4b6b;
    font-size: .95rem;
    margin-bottom: 1.5rem;
    border-left: 4px solid #3f7ec6;
    padding: .6rem 1rem;
    background: rgba(63,126,198,.06);
    border-radius: 0 12px 12px 0;
  }
  .tabs {
    display: flex;
    gap: .5rem;
    margin-bottom: 1.2rem;
    flex-wrap: wrap;
  }
  .tab {
    padding: .6rem 1.2rem;
    border-radius: 100px;
    border: 1.5px solid #c8dcf0;
    background: white;
    color: #1b3b5a;
    cursor: pointer;
    font-weight: 600;
    font-size: .9rem;
    transition: .15s;
  }
  .tab.active {
    background: #1e3f6e;
    color: white;
    border-color: #1e3f6e;
  }
  .panel { display: none; }
  .panel.active { display: block; }

  .video-wrap {
    position: relative;
    width: 100%;
    max-width: 500px;
    aspect-ratio: 1/1;
    background: #0b1a2a;
    border-radius: 1.2rem;
    overflow: hidden;
    margin: 0 auto 1rem;
    box-shadow: 0 10px 25px -8px rgba(0,0,0,.4);
  }
  #video { width: 100%; height: 100%; object-fit: cover; }
  .scan-line {
    position: absolute;
    top: 0; left: 0; right: 0;
    height: 3px;
    background: linear-gradient(90deg, transparent, #4ade80, transparent);
    box-shadow: 0 0 12px #4ade80;
    animation: scan 2.2s linear infinite;
    display: none;
  }
  .scan-line.active { display: block; }
  @keyframes scan {
    0%   { top: 5%; }
    50%  { top: 95%; }
    100% { top: 5%; }
  }
  .corner {
    position: absolute;
    width: 30px; height: 30px;
    border: 3px solid #4ade80;
    opacity: .9;
  }
  .corner.tl { top: 12px; left: 12px; border-right: none; border-bottom: none; border-radius: 8px 0 0 0; }
  .corner.tr { top: 12px; right: 12px; border-left: none; border-bottom: none; border-radius: 0 8px 0 0; }
  .corner.bl { bottom: 12px; left: 12px; border-right: none; border-top: none; border-radius: 0 0 0 8px; }
  .corner.br { bottom: 12px; right: 12px; border-left: none; border-top: none; border-radius: 0 0 8px 0; }

  .btn-row { display: flex; gap: .7rem; flex-wrap: wrap; justify-content: center; margin-bottom: 1rem; }
  .btn {
    padding: .8rem 1.5rem;
    border-radius: 100px;
    border: 1.5px solid #b8d0e8;
    background: white;
    color: #1b3b5a;
    font-weight: 600;
    font-size: .95rem;
    cursor: pointer;
    transition: .15s;
    display: inline-flex;
    align-items: center;
    gap: .5rem;
  }
  .btn:hover { background: #e6eff9; transform: translateY(-1px); }
  .btn.primary { background: #1e3f6e; color: white; border-color: #1e3f6e; }
  .btn.primary:hover { background: #0f2d4e; }
  .btn.danger { border-color: #e0a0a0; color: #a02020; }
  .btn.danger:hover { background: #fdeaea; }
  .btn:disabled { opacity: .45; cursor: not-allowed; transform: none; }

  .drop-zone {
    border: 2.5px dashed #a8c5e0;
    border-radius: 1.2rem;
    padding: 2.5rem 1rem;
    text-align: center;
    color: #4d6d8b;
    cursor: pointer;
    transition: .15s;
    background: #f8fbff;
  }
  .drop-zone:hover, .drop-zone.drag {
    border-color: #3f7ec6;
    background: #eef5fc;
  }
  .drop-zone .big-icon { font-size: 2.5rem; display: block; margin-bottom: .5rem; }

  .result-box {
    background: #f2f8ff;
    border: 1px solid #c8dcf0;
    border-radius: 1rem;
    padding: 1rem 1.2rem;
    margin-top: 1rem;
    min-height: 90px;
    font-size: .95rem;
    word-break: break-word;
    white-space: pre-wrap;
  }
  .result-label {
    font-size: .78rem;
    text-transform: uppercase;
    letter-spacing: .08em;
    color: #4d6d8b;
    font-weight: 700;
    margin-bottom: .4rem;
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
  .result-content {
    font-family: ui-monospace, 'SF Mono', Menlo, monospace;
    font-size: 1rem;
    color: #0b2b4a;
    line-height: 1.5;
  }
  .result-content:empty::before {
    content: 'No QR code detected yet…';
    color: #90a8c0;
    font-style: italic;
    font-family: system-ui, sans-serif;
  }
  .result-actions { display: flex; gap: .5rem; margin-top: .8rem; flex-wrap: wrap; }
  .mini-btn {
    font-size: .78rem;
    padding: .35rem .8rem;
    border-radius: 60px;
    border: 1px solid #c8dcf0;
    background: white;
    color: #1b3b5a;
    cursor: pointer;
    font-weight: 600;
    text-decoration: none;
    display: inline-flex;
    align-items: center;
    gap: .3rem;
  }
  .mini-btn:hover { background: #e6eff9; }

  .status {
    font-size: .82rem;
    color: #4d6d8b;
    text-align: center;
    margin-top: .6rem;
    min-height: 1.2em;
  }
  .status.error { color: #b02020; }
  .status.ok { color: #1e8a4a; }

  .history {
    margin-top: 1.2rem;
    max-height: 180px;
    overflow-y: auto;
  }
  .history-item {
    display: flex;
    justify-content: space-between;
    align-items: center;
    gap: .5rem;
    padding: .5rem .7rem;
    border-bottom: 1px solid #e4edf6;
    font-size: .85rem;
    font-family: ui-monospace, monospace;
    color: #1b3b5a;
  }
  .history-item:last-child { border-bottom: none; }
  .history-item .time { color: #7d97b0; font-size: .75rem; white-space: nowrap; }
  .history-empty { color: #90a8c0; font-style: italic; font-size: .85rem; padding: .5rem; }

  canvas.hidden { display: none; }
</style>
</head>
<body>
<div class="app">
  <h1>📷 QR Code Reader <span>live</span></h1>
  <div class="subhead">
    Scan with your camera or upload an image · decodes URLs, text, WiFi, vCards, and more
  </div>

  <div class="tabs">
    <button class="tab active" data-panel="camera">🎥 Live Camera</button>
    <button class="tab" data-panel="image">🖼️ Upload Image</button>
    <button class="tab" data-panel="history">🕘 History</button>
  </div>

  <!-- CAMERA PANEL -->
  <div class="panel active" id="panel-camera">
    <div class="video-wrap">
      <video id="video" playsinline muted></video>
      <div class="scan-line" id="scanLine"></div>
      <div class="corner tl"></div>
      <div class="corner tr"></div>
      <div class="corner bl"></div>
      <div class="corner br"></div>
    </div>
    <div class="btn-row">
      <button class="btn primary" id="startBtn">▶ Start Camera</button>
      <button class="btn danger" id="stopBtn" disabled>⏹ Stop</button>
      <button class="btn" id="switchBtn" disabled>🔄 Switch Camera</button>
    </div>
    <div class="status" id="camStatus">Camera is off.</div>
  </div>

  <!-- IMAGE PANEL -->
  <div class="panel" id="panel-image">
    <div class="drop-zone" id="dropZone">
      <span class="big-icon">🖼️</span>
      <strong>Click to choose an image</strong><br>
      or drag &amp; drop a QR code image here
    </div>
    <input type="file" id="fileInput" accept="image/*" style="display:none">
    <canvas id="canvas" class="hidden"></canvas>
    <div class="status" id="imgStatus">Select an image containing a QR code.</div>
  </div>

  <!-- SHARED RESULT -->
  <div class="result-box">
    <div class="result-label">
      <span>Decoded Result</span>
      <span id="resultType" style="font-weight:500; text-transform:none; letter-spacing:0;"></span>
    </div>
    <div class="result-content" id="resultContent"></div>
    <div class="result-actions" id="resultActions" style="display:none;">
      <button class="mini-btn" id="copyBtn">📋 Copy</button>
      <a class="mini-btn" id="openLink" target="_blank" rel="noopener" style="display:none;">🔗 Open Link</a>
      <button class="mini-btn" id="clearResultBtn">✕ Clear</button>
    </div>
  </div>

  <!-- HISTORY PANEL -->
  <div class="panel" id="panel-history">
    <div class="history" id="historyList">
      <div class="history-empty">Scanned codes will appear here.</div>
    </div>
    <div class="btn-row" style="margin-top: 1rem;">
      <button class="btn danger" id="clearHistoryBtn">🗑 Clear History</button>
    </div>
  </div>
</div>

<!-- REAL QR DECODING LIBRARY (jsQR) -->
<script src="https://cdn.jsdelivr.net/npm/jsqr@1.4.0/dist/jsQR.min.js"></script>
<script>
(function(){
  "use strict";

  /* ==================== DOM ==================== */
  const video       = document.getElementById('video');
  const canvas      = document.getElementById('canvas');
  const ctx         = canvas.getContext('2d', { willReadFrequently: true });
  const scanLine    = document.getElementById('scanLine');
  const startBtn    = document.getElementById('startBtn');
  const stopBtn     = document.getElementById('stopBtn');
  const switchBtn   = document.getElementById('switchBtn');
  const camStatus   = document.getElementById('camStatus');
  const dropZone    = document.getElementById('dropZone');
  const fileInput   = document.getElementById('fileInput');
  const imgStatus   = document.getElementById('imgStatus');
  const resultContent = document.getElementById('resultContent');
  const resultType  = document.getElementById('resultType');
  const resultActions = document.getElementById('resultActions');
  const copyBtn     = document.getElementById('copyBtn');
  const openLink    = document.getElementById('openLink');
  const clearResultBtn = document.getElementById('clearResultBtn');
  const historyList = document.getElementById('historyList');
  const clearHistoryBtn = document.getElementById('clearHistoryBtn');

  /* ==================== STATE ==================== */
  let stream = null;
  let scanning = false;
  let rafId = null;
  let facingMode = 'environment';
  let lastDecoded = '';
  let lastDecodedAt = 0;
  let history = [];

  try {
    history = JSON.parse(localStorage.getItem('qr_history') || '[]');
  } catch(e) { history = []; }

  /* ==================== TABS ==================== */
  document.querySelectorAll('.tab').forEach(tab => {
    tab.addEventListener('click', () => {
      document.querySelectorAll('.tab').forEach(t => t.classList.remove('active'));
      document.querySelectorAll('.panel').forEach(p => p.classList.remove('active'));
      tab.classList.add('active');
      document.getElementById('panel-' + tab.dataset.panel).classList.add('active');
      if (tab.dataset.panel !== 'camera' && scanning) stopCamera();
    });
  });

  /* ==================== RESULT DISPLAY ==================== */
  function classifyResult(text) {
    if (/^https?:\/\//i.test(text)) return 'URL';
    if (/^WIFI:/i.test(text)) return 'WiFi';
    if (/^mailto:/i.test(text)) return 'Email';
    if (/^tel:/i.test(text)) return 'Phone';
    if (/^BEGIN:VCARD/i.test(text)) return 'Contact';
    if (/^SMSTO:/i.test(text)) return 'SMS';
    if (/^geo:/i.test(text)) return 'Location';
    return 'Text';
  }

  function showResult(text) {
    lastDecoded = text;
    resultContent.textContent = text;
    const type = classifyResult(text);
    resultType.textContent = type;
    resultActions.style.display = 'flex';

    if (/^https?:\/\//i.test(text)) {
      openLink.href = text;
      openLink.style.display = 'inline-flex';
    } else if (/^mailto:/i.test(text)) {
      openLink.href = text;
      openLink.style.display = 'inline-flex';
    } else {
      openLink.style.display = 'none';
    }
  }

  function clearResult() {
    resultContent.textContent = '';
    resultType.textContent = '';
    resultActions.style.display = 'none';
    openLink.style.display = 'none';
    lastDecoded = '';
  }

  copyBtn.addEventListener('click', async () => {
    if (!lastDecoded) return;
    try {
      await navigator.clipboard.writeText(lastDecoded);
      copyBtn.textContent = '✓ Copied!';
      setTimeout(() => copyBtn.textContent = '📋 Copy', 1400);
    } catch (e) {
      copyBtn.textContent = '⚠ Failed';
      setTimeout(() => copyBtn.textContent = '📋 Copy', 1400);
    }
  });
  clearResultBtn.addEventListener('click', clearResult);

  /* ==================== HISTORY ==================== */
  function addToHistory(text) {
    if (!text) return;
    const now = Date.now();
    if (history.length && history[0].text === text && now - history[0].ts < 3000) return;
    history.unshift({ text, ts: now });
    if (history.length > 50) history.pop();
    saveHistory();
    renderHistory();
  }

  function saveHistory() {
    try { localStorage.setItem('qr_history', JSON.stringify(history)); } catch(e) {}
  }

  function renderHistory() {
    if (!history.length) {
      historyList.innerHTML = '<div class="history-empty">Scanned codes will appear here.</div>';
      return;
    }
    historyList.innerHTML = history.map((h, i) => {
      const time = new Date(h.ts).toLocaleTimeString();
      const short = h.text.length > 70 ? h.text.slice(0, 67) + '…' : h.text;
      const safe = short.replace(/</g,'&lt;').replace(/>/g,'&gt;');
      return `<div class="history-item" data-index="${i}" title="${h.text.replace(/"/g,'&quot;')}">
        <span style="cursor:pointer; overflow:hidden; text-overflow:ellipsis; white-space:nowrap;">${safe}</span>
        <span class="time">${time}</span>
      </div>`;
    }).join('');

    historyList.querySelectorAll('.history-item').forEach(el => {
      el.addEventListener('click', () => {
        const item = history[+el.dataset.index];
        if (item) {
          showResult(item.text);
          document.querySelector('.tab[data-panel="camera"]').click();
        }
      });
    });
  }

  clearHistoryBtn.addEventListener('click', () => {
    history = [];
    saveHistory();
    renderHistory();
  });

  /* ==================== CAMERA ==================== */
  async function startCamera() {
    if (scanning) return;
    camStatus.textContent = 'Requesting camera permission…';
    camStatus.className = 'status';

    if (!navigator.mediaDevices || !navigator.mediaDevices.getUserMedia) {
      camStatus.textContent = '❌ Camera API not available. Use HTTPS or localhost.';
      camStatus.className = 'status error';
      return;
    }

    try {
      stream = await navigator.mediaDevices.getUserMedia({
        video: { facingMode: { ideal: facingMode }, width: { ideal: 1280 }, height: { ideal: 1280 } },
        audio: false
      });
      video.srcObject = stream;
      await video.play();

      scanning = true;
      startBtn.disabled = true;
      stopBtn.disabled = false;
      switchBtn.disabled = false;
      scanLine.classList.add('active');
      camStatus.textContent = '🔍 Scanning… point at a QR code.';
      camStatus.className = 'status ok';

      video.addEventListener('loadedmetadata', () => {
        canvas.width  = video.videoWidth;
        canvas.height = video.videoHeight;
      }, { once: true });

      if (video.readyState >= 1) {
        canvas.width  = video.videoWidth;
        canvas.height = video.videoHeight;
      }

      requestAnimationFrame(tick);
    } catch (err) {
      scanning = false;
      camStatus.textContent = '❌ Camera error: ' + err.message;
      camStatus.className = 'status error';
      startBtn.disabled = false;
    }
  }

  function stopCamera() {
    scanning = false;
    if (rafId) { cancelAnimationFrame(rafId); rafId = null; }
    if (stream) {
      stream.getTracks().forEach(t => t.stop());
      stream = null;
    }
    video.srcObject = null;
    scanLine.classList.remove('active');
    startBtn.disabled = false;
    stopBtn.disabled = true;
    switchBtn.disabled = true;
    camStatus.textContent = 'Camera is off.';
    camStatus.className = 'status';
  }

  function tick() {
    if (!scanning) return;

    if (video.readyState === video.HAVE_ENOUGH_DATA) {
      if (canvas.width !== video.videoWidth) {
        canvas.width  = video.videoWidth;
        canvas.height = video.videoHeight;
      }
      ctx.drawImage(video, 0, 0, canvas.width, canvas.height);
      const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);

      const code = jsQR(imageData.data, imageData.width, imageData.height, {
        inversionAttempts: 'dontInvert'
      });

      if (code && code.data) {
        const now = Date.now();
        if (code.data !== lastDecoded || now - lastDecodedAt > 2000) {
          lastDecodedAt = now;
          showResult(code.data);
          addToHistory(code.data);
          if (navigator.vibrate) navigator.vibrate(80);
          camStatus.textContent = '✅ QR code detected!';
          camStatus.className = 'status ok';
        }
      }
    }
    rafId = requestAnimationFrame(tick);
  }

  startBtn.addEventListener('click', startCamera);
  stopBtn.addEventListener('click', stopCamera);

  switchBtn.addEventListener('click', async () => {
    facingMode = facingMode === 'environment' ? 'user' : 'environment';
    stopCamera();
    await new Promise(r => setTimeout(r, 300));
    startCamera();
  });

  /* ==================== IMAGE UPLOAD ==================== */
  dropZone.addEventListener('click', () => fileInput.click());

  dropZone.addEventListener('dragover', e => {
    e.preventDefault();
    dropZone.classList.add('drag');
  });
  dropZone.addEventListener('dragleave', () => dropZone.classList.remove('drag'));
  dropZone.addEventListener('drop', e => {
    e.preventDefault();
    dropZone.classList.remove('drag');
    const file = e.dataTransfer.files[0];
    if (file && file.type.startsWith('image/')) handleImageFile(file);
  });

  fileInput.addEventListener('change', e => {
    const file = e.target.files[0];
    if (file) handleImageFile(file);
  });

  function handleImageFile(file) {
    imgStatus.textContent = '⏳ Reading image…';
    imgStatus.className = 'status';

    const img = new Image();
    img.onload = () => {
      canvas.width  = img.naturalWidth;
      canvas.height = img.naturalHeight;
      ctx.drawImage(img, 0, 0);

      const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);
      let code = jsQR(imageData.data, imageData.width, imageData.height, {
        inversionAttempts: 'attemptBoth'
      });

      // Try upscaled version if first attempt fails (helps small QR codes)
      if (!code) {
        const scale = Math.min(3, 2000 / Math.max(img.naturalWidth, img.naturalHeight));
        if (scale > 1) {
          canvas.width  = Math.floor(img.naturalWidth * scale);
          canvas.height = Math.floor(img.naturalHeight * scale);
          ctx.drawImage(img, 0, 0, canvas.width, canvas.height);
          const big = ctx.getImageData(0, 0, canvas.width, canvas.height);
          code = jsQR(big.data, big.width, big.height, { inversionAttempts: 'attemptBoth' });
        }
      }

      if (code && code.data) {
        showResult(code.data);
        addToHistory(code.data);
        imgStatus.textContent = '✅ QR code decoded successfully.';
        imgStatus.className = 'status ok';
        if (navigator.vibrate) navigator.vibrate(80);
      } else {
        imgStatus.textContent = '❌ No QR code found in this image. Try a clearer or higher-resolution image.';
        imgStatus.className = 'status error';
      }
    };
    img.onerror = () => {
      imgStatus.textContent = '❌ Could not load that image file.';
      imgStatus.className = 'status error';
    };
    img.src = URL.createObjectURL(file);
    fileInput.value = '';
  }

  /* ==================== INIT ==================== */
  renderHistory();
  if (!window.jsQR) {
    camStatus.textContent = '⚠ QR library failed to load. Check your internet connection.';
    camStatus.className = 'status error';
  }
})();
</script>
</body>
</html>
