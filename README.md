<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>MHWildsやり込みリスト</title>
  <style>
    /* 省略：従来のCSSをそのまま使用 */
  </style>
</head>
<body>
  <div class="container">
    <h1>MHWildsやり込みリスト</h1>

    <table id="checklist">
      <thead>
        <tr>
          <th>モンスター名</th><th>狩猟数</th><th>狩猟 ☑</th><th>捕獲数</th><th>捕獲 ☑</th>
        </tr>
      </thead>
      <tbody></tbody>
    </table>

    <button class="add-button" onclick="addRow()">＋ 行を追加</button>

    <div class="progress-container">
      <label>達成率：<span id="progress-text">0%</span></label>
      <progress id="progress" value="0" max="100"></progress>
    </div>
  </div>

  <!-- Drag&Dropライブラリ -->
  <script src="https://cdn.jsdelivr.net/npm/sortablejs@1.15.0/Sortable.min.js"></script>

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
    const checked = data.reduce((sum, item) =>
      sum + (item.hunted ? 1 : 0) + (item.captured ? 1 : 0), 0);
    const percent = total ? Math.round((checked / total) * 100) : 0;
    progressBar.value = percent; progressText.textContent = percent + "%";
  }

  function render() {
    checklistBody.innerHTML = '';
    data.forEach((item, index) => {
      const tr = document.createElement('tr');
      tr.dataset.index = index;

      const nameTd = document.createElement('td');
      nameTd.textContent = item.name; nameTd.contentEditable = true;
      nameTd.oninput = () => { item.name = nameTd.textContent.trim(); saveData(); };

      const huntLabelTd = document.createElement('td'); huntLabelTd.textContent = '狩猟';
      const huntTd = document.createElement('td');
      const huntCheckbox = document.createElement('input');
      huntCheckbox.type = 'checkbox'; huntCheckbox.checked = item.hunted;
      huntCheckbox.onchange = () => { item.hunted = huntCheckbox.checked; saveData(); updateProgress(); };
      huntTd.appendChild(huntCheckbox);

      const capLabelTd = document.createElement('td'); capLabelTd.textContent = '捕獲';
      const capTd = document.createElement('td');
      const capCheckbox = document.createElement('input');
      capCheckbox.type = 'checkbox'; capCheckbox.checked = item.captured;
      capCheckbox.onchange = () => { item.captured = capCheckbox.checked; saveData(); updateProgress(); };
      capTd.appendChild(capCheckbox);

      tr.append(nameTd, huntLabelTd, huntTd, capLabelTd, capTd);
      checklistBody.appendChild(tr);
    });
    updateProgress();
  }

  function addRow() {
    data.push({ name: '', hunted: false, captured: false });
    saveData(); render(); initDrag();
  }

  if (!data.length) {
    defaultNames.forEach(name => data.push({ name, hunted: false, captured: false }));
    saveData();
  }

  render();

  // Drag&Drop 初期化
  function initDrag() {
    Sortable.create(checklistBody, {
      animation: 150,
      onEnd: evt => {
        const [moved] = data.splice(evt.oldIndex, 1);
        data.splice(evt.newIndex, 0, moved);
        saveData(); render();
      }
    });
  }

  initDrag();
  </script>
</body>
</html>