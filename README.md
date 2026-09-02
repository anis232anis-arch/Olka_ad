<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Olka Games</title>
  <script src="https://telegram.org/js/telegram-web-app.js"></script>
  <style>
    * { box-sizing: border-box; font-family: system-ui, -apple-system, sans-serif; }
    body {
      margin: 0;
      padding: 16px;
      text-align: center;
      background-color: var(--tg-theme-bg-color, #121212);
      color: var(--tg-theme-text-color, #ffffff);
    }
    .card {
      background: rgba(255, 255, 255, 0.05);
      border-radius: 16px;
      padding: 16px;
      margin-bottom: 20px;
      border: 1px solid rgba(255, 255, 255, 0.1);
    }
    .score-board {
      font-size: 24px;
      font-weight: bold;
      color: #f1c40f;
      margin-bottom: 15px;
    }
    /* عجلة الروليت */
    .wheel-container {
      position: relative;
      width: 220px;
      height: 220px;
      margin: 0 auto 15px auto;
    }
    .wheel {
      width: 100%;
      height: 100%;
      border-radius: 50%;
      border: 6px solid #f1c40f;
      background: conic-gradient(
        #e74c3c 0deg 60deg,
        #3498db 60deg 120deg,
        #2ecc71 120deg 180deg,
        #9b59b6 180deg 240deg,
        #e67e22 240deg 300deg,
        #1abc9c 300deg 360deg
      );
      transition: transform 3s cubic-bezier(0.17, 0.67, 0.12, 0.99);
    }
    .wheel-pointer {
      position: absolute;
      top: -10px;
      left: 50%;
      transform: translateX(-50%);
      width: 0;
      height: 0;
      border-left: 12px solid transparent;
      border-right: 12px solid transparent;
      border-top: 20px solid #ffffff;
      z-index: 10;
    }
    /* لعبة النقر */
    .coin-btn {
      width: 110px;
      height: 110px;
      border-radius: 50%;
      background: linear-gradient(145deg, #f39c12, #d35400);
      border: none;
      font-size: 40px;
      cursor: pointer;
      box-shadow: 0 8px 15px rgba(0,0,0,0.3);
      transition: transform 0.08s ease;
    }
    .coin-btn:active {
      transform: scale(0.92);
    }
    .btn {
      background-color: #0088cc;
      color: #fff;
      border: none;
      padding: 12px 24px;
      font-size: 16px;
      border-radius: 10px;
      cursor: pointer;
      font-weight: bold;
    }
    .btn:disabled {
      background-color: #555;
      cursor: not-allowed;
    }
  </style>
</head>
<body>

  <div class="score-board">
    💰 الرصيد: <span id="balance">0</span> نقطة
  </div>

  <!-- اللعبة الأولى: عجلة الحظ -->
  <div class="card">
    <h3>🎰 عجلة الحظ (روليت)</h3>
    <div class="wheel-container">
      <div class="wheel-pointer"></div>
      <div class="wheel" id="wheel"></div>
    </div>
    <button class="btn" id="spin-btn" onclick="spinWheel()">جرّب حظك (تدوير)</button>
    <p id="spin-result" style="height: 20px; font-weight: bold;"></p>
  </div>

  <!-- اللعبة الثانية: النقر السريع -->
  <div class="card">
    <h3>⚡ لعبة النقر لجمع النقاط</h3>
    <p>اضغط على العملة لزيادة رصيدك (+1 كل نقرة):</p>
    <button class="coin-btn" onclick="tapCoin()">🪙</button>
  </div>

  <button class="btn" style="background: transparent; border: 1px solid #777;" onclick="tg.close()">إغلاق</button>

  <script>
    const tg = window.Telegram.WebApp;
    tg.expand();

    // استعادة الرصيد المحفوظ أو البدء بـ 0
    let points = parseInt(localStorage.getItem('olka_points')) || 0;
    const balanceEl = document.getElementById('balance');
    balanceEl.innerText = points;

    function updatePoints(amount) {
      points += amount;
      balanceEl.innerText = points;
      localStorage.setItem('olka_points', points);
      if (tg.HapticFeedback) tg.HapticFeedback.impactOccurred('light');
    }

    // لعبة النقر
    function tapCoin() {
      updatePoints(1);
    }

    // لعبة الروليت
    let currentRotation = 0;
    let isSpinning = false;
    const rewards = [10, 50, 0, 100, 5, 20];

    function spinWheel() {
      if (isSpinning) return;
      isSpinning = true;

      const spinBtn = document.getElementById('spin-btn');
      const resultEl = document.getElementById('spin-result');
      spinBtn.disabled = true;
      resultEl.innerText = "العجلة تدور...";

      const extraDegrees = Math.floor(Math.random() * 360) + 1440; // 4 دورات كاملة على الأقل
      currentRotation += extraDegrees;
      document.getElementById('wheel').style.transform = `rotate(${currentRotation}deg)`;

      setTimeout(() => {
        // حساب الجائزة التقريبية
        const prize = rewards[Math.floor(Math.random() * rewards.length)];
        updatePoints(prize);
        resultEl.innerText = prize > 0 ? `مبروك! ربحت ${prize} نقطة 🎉` : "للأسف! حظ أوفر في المرة القادمة 😢";
        spinBtn.disabled = false;
        isSpinning = false;
      }, 3000);
    }
  </script>
</body>
</html>
