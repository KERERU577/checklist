# checklist<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>スマホ用チェックリスト</title>
  <style>
    body {
      font-family: sans-serif;
      padding: 20px;
      background: #f9f9f9;
    }
    h1 {
      text-align: center;
    }
    ul {
      list-style: none;
      padding: 0;
    }
    li {
      background: #fff;
      margin: 10px 0;
      padding: 10px;
      border-radius: 8px;
      box-shadow: 0 2px 5px rgba(0,0,0,0.1);
      display: flex;
      align-items: center;
    }
    input[type="checkbox"] {
      margin-right: 10px;
      transform: scale(1.5);
    }
  </style>
</head>
<body>
  <h1>📋 今日のやること</h1>
  <ul id="checklist">
    <li><input type="checkbox" id="task1"> <label for="task1">朝ごはんを食べる</label></li>
    <li><input type="checkbox" id="task2"> <label for="task2">メールを確認</label></li>
    <li><input type="checkbox" id="task3"> <label for="task3">30分運動する</label></li>
    <li><input type="checkbox" id="task4"> <label for="task4">買い物に行く</label></li>
  </ul>

  <script>
    const checkboxes = document.querySelectorAll('input[type="checkbox"]');
    checkboxes.forEach(checkbox => {
      const saved = localStorage.getItem(checkbox.id);
      if (saved === "true") checkbox.checked = true;

      checkbox.addEventListener('change', () => {
        localStorage.setItem(checkbox.id, checkbox.checked);
      });
    });
  </script>
</body>
</html>