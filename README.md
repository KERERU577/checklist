<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>MHWildsやり込みリスト</title>
  <style>
    body {
      font-family: 'Helvetica Neue', sans-serif;
      background: #f2f2f2;
      margin: 0;
      padding: 2rem;
    }

    .container {
      max-width: 800px;
      margin: 0 auto;
      background: white;
      border-radius: 12px;
      padding: 1rem 2rem 2rem;
      box-shadow: 0 4px 10px rgba(0,0,0,0.1);
    }

    h1 {
      text-align: center;
      margin-bottom: 1rem;
    }

    .progress-container {
      margin-bottom: 1.5rem;
    }

    progress {
      width: 100%;
      height: 20px;
    }

    .row {
      display: flex;
      align-items: center;
      border-bottom: 1px solid #eee;
      padding: 0.5rem 0;
    }

    .row input[type="checkbox"] {
      width: 20px;
      height: 20px;
      accent-color: #4CAF50;
      margin: 0 10px;
    }

    .label {
      flex: 1;
      cursor: pointer;
    }

    .label[contenteditable]:focus {
      outline: 2px dashed #4CAF50;
    }

    button {
      padding: 4px 10px;
      border: none;
      border-radius: 4px;
      cursor: pointer;
    }

    .add-button {
      margin-top: 1rem;
      background: #4CAF50;
      color: white;
    }

    .check-label {
      font-size: 0.8em;
      color: #777;
      text-align: center;
    }
  </style>
</head>
<body>
  <div class="container">
    <h1>MHWildsやり込みリスト</h1>

    <div class="progress-container">
      <label>達成率：<span id="progress-text">0%</span></label>
      <progress id="progress" value="0" max="100"></progress>
    </div>

    <div id="checklist"></div>

    <button class="add-button" onclick="addRow()">＋ 行を追加</button>
  </div>

  <script>
    const checklist = document.getElementById('checklist');
    const progressBar = document.getElementById('progress');
    const progressText = document.getElementById('progress-text');

    const defaultNames = [
      "チャタカブラ","ケマトリス","ラバナ・バリナ","ババコンガ","バーラハーラ","ドシャグマ",
      "ウズ・トゥナ","ププロポル","レ・ダウ","ネルスキュラ","ヒラバミ","アジャラカン",
      "ヌ・エグドラ","護竜ドシャグマ","護竜リオレウス","ジン・ダハド","護竜オドガロン亜種","シーウー",
      "護竜アルシュベルド","ゾ・シア","イャンクック","ゲリョス","リオレイア","護竜アンジャナフ亜種",
      "リオレウス","グラビモス","ドドブランゴ","ゴア・マガラ","アルシュベルド","タマミツネ",
      "セルレギオス","ラギアクルス"
    ];

    let data = JSON.parse(localStorage.getItem('mhChecklist')) || [];

    function saveData() {
      localStorage.setItem('mhChecklist', JSON.stringify(data));
    }

    function updateProgress() {
      const totalChecks = data.length * 2;
      const checked = data.reduce((acc, row) => acc + (row.hunted ? 1 : 0) + (row.captured ? 1 : 0), 0);
      const percent = totalChecks ? Math.round((checked / totalChecks) * 100) : 0;
      progressBar.value = percent;
      progressText.textContent = percent + '%';
    }

    function render() {
      checklist.innerHTML = '';
      data.forEach((item, index) => {
        const row = document.createElement('div');
        row.className = 'row';

        const label = document.createElement('div');
        label.className = 'label';
        label.textContent = item.name || `モンスター ${index + 1}`;
        label.contentEditable = true;
        label.oninput = () => {
          data[index].name = label.textContent.trim();
          saveData();
        };

        const checks = document.createElement('div');
        checks.style.display = 'flex';
        checks.style.alignItems = 'center';

        const huntCheck = document.createElement('input');
        huntCheck.type = 'checkbox';
        huntCheck.checked = item.hunted;
        huntCheck.onchange = () => {
          data[index].hunted = huntCheck.checked;
          saveData();
          updateProgress();
        };

        const captureCheck = document.createElement('input');
        captureCheck.type = 'checkbox';
        captureCheck.checked = item.captured;
        captureCheck.onchange = () => {
          data[index].captured = captureCheck.checked;
          saveData();
          updateProgress();
        };

        const labels = document.createElement('div');
        labels.style.display = 'flex';
        labels.style.flexDirection = 'column';
        labels.style.marginRight = '8px';

        const huntLabel = document.createElement('div');
        huntLabel.className = 'check-label';
        huntLabel.textContent = '狩猟';

        const capLabel = document.createElement('div');
        capLabel.className = 'check-label';
        capLabel.textContent = '捕獲';

        labels.appendChild(huntLabel);
        labels.appendChild(capLabel);

        const checkWrapper = document.createElement('div');
        checkWrapper.style.display = 'flex';
        checkWrapper.style.flexDirection = 'column';
        checkWrapper.appendChild(huntCheck);
        checkWrapper.appendChild(captureCheck);

        checks.appendChild(labels);
        checks.appendChild(checkWrapper);

        row.appendChild(label);
        row.appendChild(checks);

        checklist.appendChild(row);
      });

      updateProgress();
    }

    function addRow() {
      data.push({ name: '', hunted: false, captured: false });
      saveData();
      render();
    }

    // 初回読み込み時に32体の名前を設定
    if (data.length === 0) {
      defaultNames.forEach(name => {
        data.push({ name: name, hunted: false, captured: false });
      });
      saveData();
    }

    render();
  </script>
</body>
</html>