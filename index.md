---
layout: home
title: 학습 BLOG
list_title: 지금까지 쓴 글
---

📖🏕️개발을 배우면서 그날그날 배운 것 정리

- 배운 것: Git, GitHub, 마크다운
- 지금 하는 것: 🏕️ 부트캠프 <span id="bootcamp-day"></span>일차

<div style="background:#e5e5e5;border-radius:8px;overflow:hidden;height:20px;margin:8px 0;">
  <div id="bootcamp-progress-bar" style="height:100%;background:#4CAF50;width:0%;"></div>
</div>
<p id="bootcamp-progress-text"></p>

<script>
  const startDate = new Date(2026, 7, 26);   // 2026-08-26
  const endDate   = new Date(2027, 1, 16);   // 2027-02-16
  const totalWorkDays = 174;

  const holidays = [
    "2026-09-24", "2026-09-25",
    "2026-10-05", "2026-10-09",
    "2026-12-25",
    "2027-01-01",
    "2027-02-08", "2027-02-09"
  ];

  function formatDate(d) {
    const y = d.getFullYear();
    const m = String(d.getMonth() + 1).padStart(2, "0");
    const day = String(d.getDate()).padStart(2, "0");
    return `${y}-${m}-${day}`;
  }

  function isWorkDay(d) {
    const dow = d.getDay(); // 0=일, 6=토
    if (dow === 0 || dow === 6) return false;
    if (holidays.includes(formatDate(d))) return false;
    return true;
  }

  function countWorkDays(from, to) {
    let count = 0;
    let d = new Date(from);
    while (d <= to) {
      if (isWorkDay(d)) count++;
      d.setDate(d.getDate() + 1);
    }
    return count;
  }

  const today = new Date();
  today.setHours(0, 0, 0, 0);
  const cappedToday = today > endDate ? endDate : today;

  const passedDays = countWorkDays(startDate, cappedToday);
  const percent = Math.min(100, Math.round((passedDays / totalWorkDays) * 100));

  document.getElementById("bootcamp-day").textContent = passedDays;
  document.getElementById("bootcamp-progress-bar").style.width = percent + "%";
  document.getElementById("bootcamp-progress-text").textContent =
    passedDays + " / " + totalWorkDays + "일 진행 (" + percent + "%)";
</script>