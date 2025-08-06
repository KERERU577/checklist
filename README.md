<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>個人的なやり込みリスト</title>
  <style>
    body {
      font-family: 'Helvetica Neue', sans-serif;
      background: #f2f2f2;
      margin: 0;
      padding: 2rem;
      color: #333;
    }
    h1 {
      text-align: center;
      margin-bottom: 2rem;
    }
    .container {
      max-width: 700px;
      margin: 0 auto;
      background: white;
      border-radius: 12px;
      padding: 1rem 2rem;
      box-shadow: 0 4px 10px rgba(0,0,0,0.1);
    }
    .row {
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 0.5rem 0;
      border-bottom: 1px solid #eee;
    }
    .label {
      flex: 1;
    }
    .checks {
      display: flex;
      gap: 1rem;
    }
    input[type="checkbox"] {
      width: 20px;
      height: 20px;
      accent-color: #4CAF50;
    }
    .row:last-child {
      border-bottom: none;
    }
  </style>
</head>
<body>
  <h1>狩猟数＆捕獲数100頭盛り チェックリスト</h1>
  <div class="container">
    <!-- 32行分 -->
    <!-- 1行 -->
    <div class="row">
      <div class="label">モンスター 1</div>
      <div class="checks">
        <label><input type="checkbox"></label>
        <label><input type="checkbox"></label>
      </div>
    </div>

    <!-- 以下をコピーして32行まで増やす -->
    <!-- 2〜32行目 -->
    <!-- JSで自動生成してもOK -->

    <!-- JSで自動生成 -->
    <script>
      const container = document.querySelector('.container');
      for (let i = 2; i <= 32; i++) {
        const row = document.createElement('div');
        row.className = 'row';
        row.innerHTML = `
          <div class="label">モンスター ${i}</div>
          <div class="checks">
            <label><input type="checkbox"></label>
            <label><input type="checkbox"></label>
          </div>
        `;
        container.appendChild(row);
      }
    </script>
  </div>
</body>
</html>