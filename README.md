index.html
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>編集できるチェックリスト</title>
  <style>
    body {
      font-family: sans-serif;
      padding: 20px;
      background: #f0f0f0;
    }
    h1 {
      text-align: center;
    }
    #checklist {
      list-style: none;
      padding: 0;
    }
    li {
      background: #fff;
      margin: 10px 0;
      padding: 10px;
      border-radius: 8px;
      display: flex;
      align-items: center;
      justify-content: space-between;
      box-shadow: 0 2px 5px rgba(0,0,0,0.1);
    }
    input[type="text"] {
      width: 70%;
      padding: 5px;
      font-size: 16px;
    }
    button {
      padding: 5px 10px;
      margin-left: 10px;
    }
    .controls {
      display: flex;
      justify-content: center;
      margin-top: 20px;
    }
  </style>
</head>
<body>
  <h1>📋 チェックリスト</h1>
  <ul id="checklist"></ul>

  <div class="controls">
    <input type="text" id="newItem" placeholder="新しい項目を追加">
    <button onclick="addItem()">追加</button>
  </div>

  <script>
    const checklist = document.getElementById("checklist");
    const newItemInput = document.getElementById("newItem");

    // チェックリストを保存・読み込み
    function loadList() {
      const saved = localStorage.getItem("checklist");
      if (saved) {
        checklist.innerHTML = saved;
        restoreEvents();
      }
    }

    function saveList() {
      localStorage.setItem("checklist", checklist.innerHTML);
    }

    function addItem() {
      const text = newItemInput.value.trim();
      if (!text) return;
      const li = document.createElement("li");
      const id = "task-" + Date.now();

      li.innerHTML = `
        <input type="checkbox" id="${id}">
        <label for="${id}">${text}</label>
        <button onclick="removeItem(this)">削除</button>
      `;

      checklist.appendChild(li);
      newItemInput.value = "";
      saveList();
    }

    function removeItem(btn) {
      const li = btn.parentElement;
      checklist.removeChild(li);
      saveList();
    }

    function restoreEvents() {
      const buttons = checklist.querySelectorAll("button");
      buttons.forEach(btn => {
        btn.onclick = () => {
          removeItem(btn);
        };
      });

      const checkboxes = checklist.querySelectorAll("input[type='checkbox']");
      checkboxes.forEach(cb => {
        cb.addEventListener("change", saveList);
      });
    }

    // 保存されたチェック状態も反映
    checklist.addEventListener("change", saveList);
    window.onload = loadList;
  </script>
</body>
</html>
