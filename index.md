---
layout: home
title: 🫟학습 BLOG
list_title: 지금까지 쓴 글
---

📖🏕️개발을 배우면서 그날그날 배운 것 정리

- 📧yang7493@g.eulji.ac.kr
- DATE : 🏕️ 부트캠프 <span id="bootcamp-day"></span>일차

<div style="background:#e5e5e5;border-radius:8px;overflow:hidden;height:20px;margin:8px 0;">
  <div id="bootcamp-progress-bar" style="height:100%;background:#4CAF50;width:0%;"></div>
</div>
<p id="bootcamp-progress-text"></p>

<script>
  const startDate = new Date(2026, 7, 26);   // 2026-08-26
  const endDate   = new Date(2027, 1, 16);   // 2027-02-16
  const totalWorkDays = 175;

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

<input type="text" id="search-input" placeholder="🔍검색어를 입력하세요" style="width:100%;padding:8px;margin:12px 0;box-sizing:border-box;">
<ul id="results-container"></ul>

<script src="https://cdn.jsdelivr.net/npm/simple-jekyll-search@1.10.0/dest/simple-jekyll-search.min.js"></script>
<script>
  SimpleJekyllSearch({
    searchInput: document.getElementById('search-input'),
    resultsContainer: document.getElementById('results-container'),
    json: '{{ "/search.json" | relative_url }}',
    searchResultTemplate: '<li><a href="{url}">{title}</a> <small>({date})</small></li>',
    noResultsText: '검색 결과가 없습니다.',
    limit: 10,
    fuzzy: false
  });
</script>



<style>
  @media (max-width: 1200px) {
    #blog-calendar { position: static !important; width: 100% !important; margin-top: 20px; }
  }
  .cal-day { padding:6px 0; border-radius:4px; }
  .cal-day.has-post { background:#e8f5e9; cursor:pointer; font-weight:bold; }
  .cal-day.has-post:hover { background:#c8e6c9; }
  .cal-day.selected { outline:2px solid #4CAF50; }
</style>


<div id="blog-sidebar" style="position:fixed; right:20px; top:120px; width:260px;">

  <div id="blog-calendar" style="border:1px solid #ddd; border-radius:8px; padding:12px; background:#fff; font-size:14px;">
    <div style="display:flex; justify-content:space-between; align-items:center; margin-bottom:8px;">
      <button id="cal-prev" style="border:none;background:none;cursor:pointer;font-size:16px;">‹</button>
      <strong id="cal-title"></strong>
      <button id="cal-next" style="border:none;background:none;cursor:pointer;font-size:16px;">›</button>
    </div>
    <div id="cal-grid" style="display:grid; grid-template-columns:repeat(7,1fr); gap:4px; text-align:center;"></div>
    <div id="cal-posts" style="margin-top:12px; font-size:13px;"></div>
  </div>

  <div style="margin-top:16px; border:1px solid #ddd; border-radius:8px; padding:6px; background:#fff; overflow:hidden;">
    <div style="transform:scale(0.85); transform-origin: top left; width:117%;">
      <script src="https://giscus.app/client.js"
              data-repo="yang7493/my-blog"
              data-repo-id="R_kgDOUGl-4w"
              data-category="Announcements"
              data-category-id="DIC_kwDOUGl-484DEs1V"
              data-mapping="pathname"
              data-strict="0"
              data-reactions-enabled="1"
              data-emit-metadata="0"
              data-input-position="bottom"
              data-theme="light"
              data-lang="ko"
              crossorigin="anonymous"
              async>
      </script>
    </div>
  </div>

</div>

<style>
  @media (max-width: 1200px) {
    #blog-sidebar { position: static !important; width: 100% !important; margin-top: 20px; }
    #blog-sidebar > div:last-child > div { transform: none !important; width:100% !important; }
  }
</style>

<script>
(function () {
  fetch('{{ "/search.json" | relative_url }}?v={{ site.time | date: "%s" }}')
    .then(res => res.json())
    .then(posts => {
      const postsByDate = {};
      posts.forEach(p => {
        if (!postsByDate[p.date]) postsByDate[p.date] = [];
        postsByDate[p.date].push(p);
      });

      let current = new Date();

      function render() {
        const year = current.getFullYear();
        const month = current.getMonth();
        document.getElementById('cal-title').textContent = year + '년 ' + (month + 1) + '월';

        const grid = document.getElementById('cal-grid');
        grid.innerHTML = '';

        ['일','월','화','수','목','금','토'].forEach(d => {
          const el = document.createElement('div');
          el.textContent = d;
          el.style.fontWeight = 'bold';
          grid.appendChild(el);
        });

        const firstDay = new Date(year, month, 1).getDay();
        const daysInMonth = new Date(year, month + 1, 0).getDate();

        for (let i = 0; i < firstDay; i++) {
          grid.appendChild(document.createElement('div'));
        }

        for (let d = 1; d <= daysInMonth; d++) {
          const dateStr = year + '-' + String(month + 1).padStart(2, '0') + '-' + String(d).padStart(2, '0');
          const cell = document.createElement('div');
          cell.textContent = d;
          cell.className = 'cal-day' + (postsByDate[dateStr] ? ' has-post' : '');
          if (postsByDate[dateStr]) {
            cell.addEventListener('click', () => showPosts(dateStr, cell));
          }
          grid.appendChild(cell);
        }
      }

      function showPosts(dateStr, cellEl) {
        document.querySelectorAll('.cal-day.selected').forEach(el => el.classList.remove('selected'));
        cellEl.classList.add('selected');

        const list = postsByDate[dateStr] || [];
        const container = document.getElementById('cal-posts');
        container.innerHTML = '<strong>' + dateStr + '</strong><ul style="padding-left:18px;margin:6px 0 0;">' +
          list.map(p => '<li><a href="' + p.url + '">' + p.title + '</a></li>').join('') +
          '</ul>';
      }

      document.getElementById('cal-prev').addEventListener('click', () => { current.setMonth(current.getMonth() - 1); render(); });
      document.getElementById('cal-next').addEventListener('click', () => { current.setMonth(current.getMonth() + 1); render(); });

      render();
    });
})();
</script>



