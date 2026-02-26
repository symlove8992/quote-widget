[progress.html](https://github.com/user-attachments/files/25561138/progress.html)
<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>신청 현황</title>
<link href="https://fonts.googleapis.com/css2?family=Noto+Sans+KR:wght@300;400;700&display=swap" rel="stylesheet">
<style>
  * { margin: 0; padding: 0; box-sizing: border-box; }

  body {
    background: #ffffff;
    font-family: 'Noto Sans KR', sans-serif;
    display: flex;
    align-items: center;
    justify-content: center;
    min-height: 100vh;
    padding: 24px;
  }

  .card {
    width: 100%;
    max-width: 600px;
    background: #fff;
    border-radius: 16px;
    padding: 32px 36px;
    box-shadow: 0 2px 20px rgba(0,0,0,0.06);
    border: 1px solid #f0f0f0;
  }

  .header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 20px;
  }

  .title {
    font-size: 15px;
    font-weight: 700;
    color: #1a1a1a;
    letter-spacing: -0.3px;
  }

  .badge {
    font-size: 13px;
    font-weight: 700;
    color: #fff;
    background: linear-gradient(135deg, #667eea, #764ba2);
    padding: 4px 12px;
    border-radius: 20px;
  }

  .count-row {
    display: flex;
    align-items: baseline;
    gap: 4px;
    margin-bottom: 14px;
  }

  .count-current {
    font-size: 42px;
    font-weight: 700;
    color: #1a1a1a;
    line-height: 1;
  }

  .count-divider {
    font-size: 24px;
    color: #ccc;
    font-weight: 300;
  }

  .count-total {
    font-size: 24px;
    color: #aaa;
    font-weight: 300;
  }

  .count-unit {
    font-size: 13px;
    color: #aaa;
    margin-left: 4px;
  }

  .bar-bg {
    width: 100%;
    height: 14px;
    background: #f0f0f0;
    border-radius: 99px;
    overflow: hidden;
    margin-bottom: 12px;
  }

  .bar-fill {
    height: 100%;
    border-radius: 99px;
    background: linear-gradient(90deg, #667eea, #764ba2);
    transition: width 1s cubic-bezier(0.4, 0, 0.2, 1);
    width: 0%;
  }

  .bar-fill.complete {
    background: linear-gradient(90deg, #11998e, #38ef7d);
  }

  .footer {
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  .status {
    font-size: 12px;
    color: #aaa;
  }

  .status.ready {
    color: #11998e;
    font-weight: 700;
  }

  .remaining {
    font-size: 12px;
    color: #aaa;
  }
</style>
</head>
<body>

<div class="card">
  <div class="header">
    <div class="title">📸 증명사진 신청 현황</div>
    <div class="badge" id="percent-badge">로딩중...</div>
  </div>

  <div class="count-row">
    <div class="count-current" id="current">-</div>
    <div class="count-divider">/</div>
    <div class="count-total">10</div>
    <div class="count-unit">팀</div>
  </div>

  <div class="bar-bg">
    <div class="bar-fill" id="bar"></div>
  </div>

  <div class="footer">
    <div class="status" id="status">불러오는 중...</div>
    <div class="remaining" id="remaining"></div>
  </div>
</div>

<script>
const GOAL = 10;

async function fetchCount() {
  try {
    const res = await fetch('/api/notion');
    const data = await res.json();
    if (data.error) throw new Error(data.error);
    updateUI(data.count);
  } catch (e) {
    document.getElementById('status').textContent = '데이터를 불러올 수 없어요';
    document.getElementById('status').style.color = '#e74c3c';
    document.getElementById('percent-badge').textContent = '오류';
    document.getElementById('percent-badge').style.background = '#e74c3c';
  }
}

function updateUI(count) {
  const percent = Math.min(Math.round((count / GOAL) * 100), 100);
  const remaining = Math.max(GOAL - count, 0);
  const isComplete = count >= GOAL;

  document.getElementById('current').textContent = count;
  document.getElementById('percent-badge').textContent = percent + '%';

  const bar = document.getElementById('bar');
  setTimeout(() => { bar.style.width = percent + '%'; }, 100);
  if (isComplete) bar.classList.add('complete');

  const status = document.getElementById('status');
  if (isComplete) {
    status.textContent = '✅ 목표 달성! 진행 가능해요';
    status.classList.add('ready');
  } else {
    status.textContent = '신청 진행중';
  }

  document.getElementById('remaining').textContent = isComplete
    ? '🎉 10팀 완성!'
    : `목표까지 ${remaining}팀 남았어요`;
}

fetchCount();
setInterval(fetchCount, 30000);
</script>
</body>
</html>
