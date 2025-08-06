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
    .striped:nth-child(6n+1),
    .striped:nth-child(6n+2),
    .striped:nth-child(6n+3),
    .striped:nth-child(6n+4),
    .striped:nth-child(6n+5),
    .striped:nth-child(6n+6) { /* apply at row group-level */
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
    <h1>MHWildsやり込みリスト</h1>
    <div class="options">
      <label><input type="checkbox" id="enableSort"> 並べ替えを有効にする</label>
    </div>

    <table id="checklist">
      <thead>
        <tr><th>モンスター名</th><th>狩猟</th><th>☑</th><th>捕獲</th><th>☑</th></tr>
      </thead>
      <tbody></tbody>
    </table>

    <button class="add-button" onclick="addRow()">＋ 行を追加</button>

    <div class="progress-container">
      達成率：<span id="progress-text">0%</span><br>
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

    let data = JSON.parse(localStorage.getItem('mhChecklist')) || [];

    function save() { localStorage.setItem('mhChecklist', JSON.stringify(data)); }

    function updateProgress() {
      const total = data.length * 2;
      const checked = data.reduce((s,i)=>s+(i.hunted?1:0)+(i.captured?1:0), 0);
      const pct = total ? Math.round(checked/total*100) : 0;
      progressBar.value = pct; progressText.textContent = pct + "%";
    }

    function render() {
      tbody.innerHTML = '';
      data.forEach((item,i)=>{
        const tr = document.createElement('tr');
        tr.className = 'striped';
        tr.dataset.index = i;
        const tdName = document.createElement('td');
        tdName.textContent = item.name;
        tdName.contentEditable = true;
        tdName.oninput = ()=>{ item.name = tdName.textContent.trim(); save(); };
        const tdHuntLabel = document.createElement('td'); tdHuntLabel.textContent='狩猟';
        const tdHunt = document.createElement('td');
        const chH = document.createElement('input'); chH.type='checkbox'; chH.checked=item.hunted;
        chH.onchange = ()=>{ item.hunted = chH.checked; save(); updateProgress(); };
        tdHunt.appendChild(chH);
        const tdCapLabel = document.createElement('td'); tdCapLabel.textContent='捕獲';
        const tdCap = document.createElement('td');
        const chC = document.createElement('input'); chC.type='checkbox'; chC.checked=item.captured;
        chC.onchange = ()=>{ item.captured = chC.checked; save(); updateProgress(); };
        tdCap.appendChild(chC);
        tr.append(tdName, tdHuntLabel, tdHunt, tdCapLabel, tdCap);
        tbody.appendChild(tr);
      });
      updateProgress();
    }

    function addRow(){
      data.push({name:'',hunted:false,captured:false});
      save(); render();
      initSort();
    }

    if(!data.length){
      defaultNames.forEach(n=>data.push({name:n,hunted:false,captured:false}));
      save();
    }
    render();

    let sortable = null;
    function initSort(){
      if(sortable) sortable.destroy();
      if(enableSortCheckbox.checked){
        sortable = Sortable.create(tbody, {
          animation: 150,
          onEnd: e=>{
            const [moved] = data.splice(e.oldIndex, 1);
            data.splice(e.newIndex,0,moved);
            save(); render();
          }
        });
      }
    }
    enableSortCheckbox.onchange = ()=>{ initSort(); };

    // 初回初期化
    initSort();
  </script>
</body>
</html>