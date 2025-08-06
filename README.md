<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>MHWildsやり込みリスト</title>
  <style>
    body {
      font-family: 'Helvetica Neue', sans-serif;
      background: #f9f9f9;
      padding: 2rem;
      margin: 0;
    }

    .container {
      max-width: 900px;
      margin: 0 auto;
      background: #fff;
      padding: 2rem;
      border-radius: 12px;
      box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
    }

    h1 {
      text-align: center;
      margin-bottom: 2rem;
    }

    table {
      width: 100%;
      border-collapse: collapse;
      margin-bottom: 1.5rem;
    }

    th, td {
      border: 1px solid #ccc;
      padding: 0.5rem;
      text-align: center;
    }

    th {
      background-color: #eee;
    }

    td[contenteditable]:focus {
      outline: 2px dashed #4CAF50;
    }

    input[type="checkbox"] {
      width: 20px;
      height: 20px;
      accent-color: #4CAF50;
    }

    .progress-container {
      margin-top: 1rem;
    }

    progress {
      width: 100%;
      height: 20px;
    }

    .add-button {
      display: inline-block;
      padding: 0.5rem 1rem;
      background-color: #4CAF50;
      color: white;
      border: none;
      border-radius: 6px;
      cursor: pointer;
      font-size: 1rem;
      margin-top: 1rem;
    }
  </style>
</head>
<body>
  <div class="container">
    <h1>MHWildsやり込みリスト</h1>

    <table id="checklist">
      <thead>
        <tr>
          <th>モンスター名</th>
          <th>狩猟数</th>
          <th>狩猟 ☑</th>
          <th>捕獲数</th>
          <th>捕獲 ☑</th>
        </tr>
      </thead>
      <tbody>
        <!-- 行はJSで生成 -->
      </tbody>
    </table>

    <button class="add-button" onclick="addRow()">＋ 行を追加</button>

    <div class="progress-container">
      <label>達成率：<span id="progress-text">0%</span></label>
      <progress id="progress" value="0" max="100"></progress>
    </div>
  </div>
  <script>
    const checklistBody = document.querySelector('#checklist tbody');
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
      const total = data.length * 2;
      const checked = data.reduce((sum, item) => sum + (item.hunted ? 1 : 0) + (item.captured ? 1 : 0), 0);
      const percent = total ? Math.round((checked / total) * 100) : 0;
      progressBar.value = percent;
      progressText.textContent = percent + "%";
    }

    function render() {
      checklistBody.innerHTML = '';
      data.forEach((item, index) => {
        const tr = document.createElement('tr');

        // モンスター名
        const nameTd = document.createElement('td');
        nameTd.textContent = item.name || `モンスター ${index + 1}`;
        nameTd.contentEditable = true;
        nameTd.oninput = () => {
          data[index].name = nameTd.textContent.trim();
          saveData();
        };

        // 狩猟ラベル
        const huntLabelTd = document.createElement('td');
        huntLabelTd.textContent = '狩猟';

        // 狩猟チェック
        const huntTd = document.createElement('td');
        const huntCheckbox = document.createElement('input');
        huntCheckbox.type = 'checkbox';
        huntCheckbox.checked = item.hunted;
        huntCheckbox.onchange = () => {
          data[index].hunted = huntCheckbox.checked;
          saveData();
          updateProgress();
        };
        huntTd.appendChild(huntCheckbox);

        // 捕獲ラベル
        const capLabelTd = document.createElement('td');
        capLabelTd.textContent = '捕獲';

        // 捕獲チェック
        const capTd = document.createElement('td');
        const capCheckbox = document.createElement('input');
        capCheckbox.type = 'checkbox';
        capCheckbox.checked = item.captured;
        capCheckbox.onchange = () => {
          data[index].captured = capCheckbox.checked;
          saveData();
          updateProgress();
        };
        capTd.appendChild(capCheckbox);

        // Append all
        tr.appendChild(nameTd);
        tr.appendChild(huntLabelTd);
        tr.appendChild(huntTd);
        tr.appendChild(capLabelTd);
        tr.appendChild(capTd);
        checklistBody.appendChild(tr);
      });

      updateProgress();
    }

    function addRow() {
      data.push({ name: '', hunted: false, captured: false });
      saveData();
      render();
    }

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