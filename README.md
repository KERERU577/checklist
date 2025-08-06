<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>MHWildsの狩猟＆捕獲数100頭盛り</title>
  <style>
    body { font-family: 'Helvetica Neue', sans-serif; background: #f9f9f9; padding: 2rem; margin: 0; }
    .container { max-width: 900px; margin: 0 auto; background: #fff; padding: 2rem; border-radius: 12px; box-shadow: 0 4px 10px rgba(0,0,0,0.1); }
    h1 { text-align: center; margin-bottom: 1rem; }
    .options { margin-bottom: 1rem; }
    table { width: 100%; border-collapse: collapse; margin-bottom: 1.5rem; }
    th, td { border: 1px solid #ccc; padding: 0.5rem; text-align: center; }
    th { background-color: #eee; }
    td[contenteditable]:focus { outline: 2px dashed #4CAF50; }
    input[type="checkbox"] { width:20px;height:20px; accent-color:#4CAF50; }
    select { width: 80px; }
    .striped:nth-child(6n+1),
    .striped:nth-child(6n+2),
    .striped:nth-child(6n+3),
    .striped:nth-child(6n+4),
    .striped:nth-child(6n+5),
    .striped:nth-child(6n+6) {
      background-color: #fdfdfd;
    }
    .striped:nth-child(6n+7),
    .striped:nth-child(6n+8),
    .striped:nth-child(6n+9),
    .striped:nth-child(6n+10),
    .striped:nth-child(6n+11),
    .striped:nth-child(6n+12) {
      background-color: #f7f7f7;
    }
    .progress-container { margin-top: 1rem; }
    progress { width:100%; height:20px; }
    .add-button { display:inline-block; padding:0.5rem 1rem; background:#4CAF50; color:#fff; border:none; border-radius:6px; cursor:pointer; font-size:1rem; margin-bottom:1rem; }
  </style>
</head>
<body>
  <div class="container">
    <h1>MHWildsの狩猟＆捕獲数100頭盛り</h1>
    <div class="options">
      <label><input type="checkbox" id="enableSort"> 並べ替えを有効にする</label>
    </div>

    <table id="checklist">
      <thead>
        <tr>
          <th>モンスター名</th>
          <th>
            狩猟数<br />
            <select id="huntGoal" onchange="updateGoal('hunt')">
              <option value="0">0頭</option>
              <option value="100" selected>100頭</option>
              <option value="200">200頭</option>
              <option value="300">300頭</option>
            </select>
          </th>
          <th>☑</th>
          <th>
            捕獲数<br />
            <select id="capGoal" onchange="updateGoal('cap')">
              <option value="0">0頭</option>
              <option value="100" selected>100頭</option>
              <option value="200">200頭</option>
              <option value="300">300頭</option>
            </select>
          </th>
          <th>☑</th>
        </tr>
      </thead>
      <tbody></tbody>
    </table>

    <button class="add-button" onclick="addRow()">＋ 行を追加</button>

    <div class="progress-container">
      達成率：<span id="progress-text">0%</span><br />
      <progress id="progress" value="0" max="100"></progress>
    </div>
  </div>

  <script src="https://cdn.jsdelivr.net/npm/sortablejs@1.15.0/Sortable.min.js"></script>
  <script>
    const tbody = document.querySelector('#checklist tbody');
    const progressBar = document.getElementById('progress');
    const progressText = document.getElementById('progress-text');
    const enableSortCheckbox = document.getElementById('enableSort');

    const defaultNames = [
      "チャタカブラ","ケマトリス","ラバナ・バリナ","ババコンガ","バーラハーラ","ドシャグマ",
      "ウズ・トゥナ","ププロポル","レ・ダウ","ネルスキュラ","ヒラバミ","アジャラカン",
      "ヌ・エグドラ","護竜ドシャグマ","護竜リオレウス","ジン・ダハド","護竜オドガロン亜種","シーウー",
      "護竜アルシュベルド","ゾ・シア","イャンクック","ゲリョス","リオレイア","護竜アンジャナフ亜種",
      "リオレウス","グラビモス","ドドブランゴ","ゴア・マガラ","アルシュベルド","タマミツネ",
      "セルレギオス","ラギアクルス"
    ];

    // 頭数目標（狩猟、捕獲）を保存
    let goals = JSON.parse(localStorage.getItem('mhGoals')) || {hunt: 100, cap: 100};
    // チェックリストデータ
    let data = JSON.parse(localStorage.getItem('mhChecklist')) || [];

    // データ保存
    function save() {
      localStorage.setItem('mhChecklist', JSON.stringify(data));
      localStorage.setItem('mhGoals', JSON.stringify(goals));
    }

    // 進捗更新
    function updateProgress() {
      const total = data.length * 2;
      const checked = data.reduce((sum, item) => sum + (item.hunted ? 1 : 0) + (item.captured ? 1 : 0), 0);
      const pct = total ? Math.round((checked / total) * 100) : 0;
      progressBar.value = pct;
      progressText.textContent = pct + "%";
    }

    // 描画
    function render() {
      tbody.innerHTML = "";
      data.forEach((item, i) => {
        const tr = document.createElement('tr');
        tr.className = 'striped';
        tr.dataset.index = i;

        // モンスター名（編集可能）
        const tdName = document.createElement('td');
        tdName.textContent = item.name;
        tdName.contentEditable = true;
        tdName.oninput = () => {
          item.name = tdName.textContent.trim();
          save();
        };
        tr.appendChild(tdName);

        // 狩猟数目標ラベル
        const tdHuntLabel = document.createElement('td');
        tdHuntLabel.textContent = goals.hunt + '頭';
        tr.appendChild(tdHuntLabel);

        // 狩猟チェック
        const tdHuntCheck = document.createElement('td');
        const chHunt = document.createElement('input');
        chHunt.type = 'checkbox';
        chHunt.checked = item.hunted;
        chHunt.onchange = () => {
          item.hunted = chHunt.checked;
          save();
          updateProgress();
        };
        tdHuntCheck.appendChild(chHunt);
        tr.appendChild(tdHuntCheck);

        // 捕獲数目標ラベル
        const tdCapLabel = document.createElement('td');
        tdCapLabel.textContent = goals.cap + '頭';
        tr.appendChild(tdCapLabel);

        // 捕獲チェック
        const tdCapCheck = document.createElement('td');
        const chCap = document.createElement('input');
        chCap.type = 'checkbox';
        chCap.checked = item.captured;
        chCap.onchange = () => {
          item.captured = chCap.checked;
          save();
          updateProgress();
        };
        tdCapCheck.appendChild(chCap);
        tr.appendChild(tdCapCheck);

        tbody.appendChild(tr);
      });
      updateProgress();
    }

    // 行を追加
    function addRow() {
      data.push({name:'', hunted:false, captured:false});
      save();
      render();
      initSort();
    }

    // 目標の更新
    function updateGoal(type) {
      const val = parseInt(document.getElementById(type === 'hunt' ? 'huntGoal' : 'capGoal').value, 10);
      goals[type] = val;
      save();
      render();
    }

    // 並べ替え処理
    let sortable = null;
    function initSort() {
      if(sortable) sortable.destroy();
      if(enableSortCheckbox.checked){
        sortable = Sortable.create(tbody, {
          animation: 150,
          onEnd: e => {
            const [moved] = data.splice(e.oldIndex, 1);
            data.splice(e.newIndex, 0, moved);
            save();
            render();
          }
        });
      }
    }

    enableSortCheckbox.onchange = () => {
      initSort();
    };

    // 初期化
    if(!data.length){
      defaultNames.forEach(name => data.push({name, hunted:false, captured:false}));
      save();
    }
    window.onload = () => {
      // 目標値復元
      document.getElementById('huntGoal').value = goals.hunt;
      document.getElementById('capGoal').value = goals.cap;
      render();
      initSort();
    };
  </script>
</body>
</html>