<div class="grid">
  <div class="card">
    <div class="icon">☑️</div>
    <div class="title">朝ごはん</div>
  </div>
  <div class="card notdone">
    <div class="icon">⬜</div>
    <div class="title">メール確認</div>
  </div>
  <!-- 追加カード -->
</div>.grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(140px, 1fr));
  gap: 1rem;
  padding: 1rem;
}
.card {
  background: #fff;
  padding: 10px;
  border-radius: 10px;
  text-align: center;
  box-shadow: 0 2px 6px rgba(0,0,0,0.1);
}
.card.notdone {
  opacity: 0.5;
}
.icon {
  font-size: 24px;
}
.title {
  margin-top: 8px;
}