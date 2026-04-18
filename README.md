<html lang="fr">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Offre Limitée</title>
  <link href="https://fonts.googleapis.com/css2?family=Bebas+Neue&family=DM+Sans:wght@400;600&display=swap" rel="stylesheet"/>
  <style>
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

    :root {
      --gold: #e02020;
      --dark: #0a0a0a;
      --mid: #140808;
      --text: #f5f5f5;
      --muted: #888;
    }

    body {
      background: var(--dark);
      color: var(--text);
      font-family: 'DM Sans', sans-serif;
      min-height: 100vh;
      display: flex;
      align-items: center;
      justify-content: center;
      overflow: hidden;
    }

    /* Grain overlay */
    body::before {
      content: '';
      position: fixed;
      inset: 0;
      background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 200 200' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.85' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)' opacity='0.04'/%3E%3C/svg%3E");
      pointer-events: none;
      z-index: 10;
      opacity: 0.4;
    }

    .glow {
      position: fixed;
      width: 600px;
      height: 600px;
      border-radius: 50%;
      background: radial-gradient(circle, rgba(220,32,32,0.10) 0%, transparent 70%);
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      pointer-events: none;
    }

    .wrapper {
      text-align: center;
      padding: 40px 24px;
      position: relative;
      z-index: 1;
      animation: fadeIn 1s ease both;
    }

    @keyframes fadeIn {
      from { opacity: 0; transform: translateY(20px); }
      to   { opacity: 1; transform: translateY(0); }
    }

    .badge {
      display: inline-block;
      background: var(--gold);
      color: #fff;
      font-family: 'DM Sans', sans-serif;
      font-weight: 600;
      font-size: 11px;
      letter-spacing: 2px;
      text-transform: uppercase;
      padding: 6px 16px;
      border-radius: 100px;
      margin-bottom: 28px;
    }

    h1 {
      font-family: 'Bebas Neue', sans-serif;
      font-size: clamp(32px, 8vw, 56px);
      letter-spacing: 2px;
      line-height: 1.1;
      margin-bottom: 10px;
      color: var(--text);
    }

    .sub {
      color: var(--muted);
      font-size: 14px;
      margin-bottom: 48px;
      letter-spacing: 0.5px;
    }

    .timer {
      display: flex;
      gap: 16px;
      justify-content: center;
      align-items: flex-start;
      margin-bottom: 48px;
    }

    .unit {
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 8px;
    }

    .box {
      background: var(--mid);
      border: 1px solid #222;
      border-radius: 12px;
      padding: 18px 24px;
      min-width: 90px;
      position: relative;
      overflow: hidden;
    }

    .box::after {
      content: '';
      position: absolute;
      left: 0; right: 0;
      top: 50%;
      height: 1px;
      background: #1e1e1e;
    }

    .box .num {
      font-family: 'Bebas Neue', sans-serif;
      font-size: clamp(48px, 10vw, 72px);
      line-height: 1;
      color: var(--gold);
      display: block;
      transition: transform 0.15s ease;
    }

    .box .num.flip {
      animation: flip 0.2s ease;
    }

    @keyframes flip {
      0%   { transform: scaleY(1); }
      50%  { transform: scaleY(0); }
      100% { transform: scaleY(1); }
    }

    .label {
      font-size: 10px;
      letter-spacing: 2px;
      text-transform: uppercase;
      color: var(--muted);
    }

    .sep {
      font-family: 'Bebas Neue', sans-serif;
      font-size: 56px;
      color: #333;
      line-height: 1;
      margin-top: 18px;
    }

    .status {
      font-size: 13px;
      color: var(--muted);
      letter-spacing: 0.5px;
    }

    .status span {
      color: #5fdf7a;
      font-weight: 600;
    }

    .expired-msg {
      font-family: 'Bebas Neue', sans-serif;
      font-size: 36px;
      color: var(--muted);
      letter-spacing: 2px;
    }

    @media (max-width: 480px) {
      .box { min-width: 70px; padding: 14px 16px; }
      .sep { font-size: 40px; }
      .timer { gap: 8px; }
    }
  </style>
</head>
<body>
  <div class="glow"></div>
  <div class="wrapper">
    <div class="badge">⚡ Offre du jour</div>
    <h1>Prix réduit<br>disponible</h1>
    <p class="sub">Cette offre est disponible ce weekend seulement.</p>

    <div class="timer" id="timer">
      <div class="unit">
        <div class="box"><span class="num" id="hours">00</span></div>
        <span class="label">Heures</span>
      </div>
      <div class="sep">:</div>
      <div class="unit">
        <div class="box"><span class="num" id="minutes">00</span></div>
        <span class="label">Minutes</span>
      </div>
      <div class="sep">:</div>
      <div class="unit">
        <div class="box"><span class="num" id="seconds">00</span></div>
        <span class="label">Secondes</span>
      </div>
    </div>

    <p class="status" id="status"><span>●</span> Offre active</p>
  </div>

  <script>
    function pad(n) { return String(n).padStart(2, '0'); }

    function animateFlip(el) {
      el.classList.remove('flip');
      void el.offsetWidth;
      el.classList.add('flip');
    }

    const hoursEl   = document.getElementById('hours');
    const minutesEl = document.getElementById('minutes');
    const secondsEl = document.getElementById('seconds');
    const statusEl  = document.getElementById('status');

    let prevH = '', prevM = '', prevS = '';

    function update() {
      const now = new Date();
      const day = now.getDay(); // 0=Sun, 6=Sat

      // Active window: Saturday 00:00 to Sunday 23:59:59
      const isActive = (day === 6) || (day === 0);

      if (!isActive) {
        hoursEl.textContent   = '00';
        minutesEl.textContent = '00';
        secondsEl.textContent = '00';
        statusEl.innerHTML = '⏸ Offre expirée';
        statusEl.style.color = '#888';
        return;
      }

      // Calculate remaining time until Sunday midnight (Monday 00:00:00)
      const endOfSunday = new Date(now);
      // Find next Monday 00:00:00
      const daysUntilMonday = (8 - now.getDay()) % 7 || 7;
      endOfSunday.setDate(now.getDate() + (day === 0 ? 1 : 2));
      endOfSunday.setHours(0, 0, 0, 0);

      const remaining = Math.floor((endOfSunday - now) / 1000);
      const rh = Math.floor(remaining / 3600);
      const rm = Math.floor((remaining % 3600) / 60);
      const rs = remaining % 60;

      const sh = pad(rh), sm = pad(rm), ss = pad(rs);

      if (sh !== prevH) { animateFlip(hoursEl);   hoursEl.textContent   = sh; prevH = sh; }
      if (sm !== prevM) { animateFlip(minutesEl); minutesEl.textContent = sm; prevM = sm; }
      if (ss !== prevS) { animateFlip(secondsEl); secondsEl.textContent = ss; prevS = ss; }

      statusEl.innerHTML = '<span>●</span> Offre active';
      statusEl.style.color = '';
    }

    update();
    setInterval(update, 1000);
  </script>
</body>
</html>
