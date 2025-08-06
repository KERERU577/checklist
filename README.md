<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>表形式チェックリスト（20行）</title>
  <style>
    body {
      font-family: sans-serif;
      padding: 20px;
      background: #f5f5f5;
    }
    table {
      width: 100%;
      border-collapse: collapse;
      background: #fff;
      border-radius: 8px;
      overflow: hidden;
      box-shadow: 0 2px 5px rgba(0,0,0,0.1);
    }
    th, td {
      padding: 12px;
      border-bottom: 1px solid #eee;
      text-align: left;
    }
    th {
      background-color: #f0f0f0;
    }
    .controls {
      margin-top: 20px;
    }
    button {
      margin-left: 5px;
    }
  </style>
</head>
<body>
  <h1>📊 表形式チェックリスト（20行）</h1>

  <table id="taskTable">
    <thead>
      <tr>
        <th>✅</th>
        <th>タスク名</th>
        <th>メモ</th>
        <th>編集</th>
        <th>削除</th>
      </tr>
    </thead>
    <tbody id="taskBody"></tbody>
  </table>

  <div class="controls">
    <input type="text" id="taskInput" placeholder="タスク名">
    <input type="text" id="memoInput" placeholder="メモ">
    <button onclick="addTask()">追加</button>
  </div>

  <script>
    const taskBody = document.getElementById("taskBody");
    const taskInput = document.getElementById("taskInput");
    const memoInput = document.getElementById("memoInput");

    function saveTasks() {
      localStorage.setItem("tasks", taskBody.innerHTML);
    }

    function loadTasks() {
      const saved = localStorage.getItem("tasks");
      if (saved) {
        taskBody.innerHTML = saved;
        restoreEvents();
      } else {
        // 最初に20行生成
        for (let i = 0; i < 20; i++) {
          createRow("", "");
        }
      }
    }

    function createRow(task, memo) {
      const row = document.createElement("tr");
      row.innerHTML = `
        <td><input type="checkbox" onchange="saveTasks()"></td>
        <td><span>${task}</span></td>
        <td><span>${memo}</span></td>
        <td><button onclick="editTask(this)">編集</button></td>
        <td><button onclick="deleteTask(this)">削除</button></td>
      `;
      taskBody.appendChild(row);
    }

    function addTask() {
      const task = taskInput.value.trim();
      const memo = memoInput.value.trim();
      if (!task) return;
      createRow(task, memo);
      taskInput.value = "";
      memoInput.value = "";
      saveTasks();
    }

    function deleteTask(button) {
      const row = button.closest("tr");
      taskBody.removeChild(row);
      saveTasks();
    }

    function editTask(button) {
      const row = button.closest("tr");
      const taskCell = row.children[1];
      const memoCell = row.children[2];
      const taskText = taskCell.querySelector("span").textContent;
      const memoText = memoCell.querySelector("span").textContent;

      const newTask = prompt("タスク名を編集:", taskText);
      if (newTask !== null && newTask.trim() !== "") {
        taskCell.querySelector("span").textContent = newTask.trim();
      }

      const newMemo = prompt("メモを編集:", memoText);
      if (newMemo !== null) {
        memoCell.querySelector("span").textContent = newMemo.trim();
      }

      saveTasks();
    }

    window.onload = loadTasks;
  </script>
</body>
</html>
