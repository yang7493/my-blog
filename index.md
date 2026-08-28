---
layout: home
title: 학습 BLOG
list_title: 지금까지 쓴 글
---

📖🏕️개발을 배우면서 그날그날 배운 것 정리

- 배운 것: Git, GitHub, 마크다운
- 지금 하는 것: 🏕️ 부트캠프 3일차

{% assign posts_by_month = site.posts | group_by_exp: "post", "post.date | date: '%Y년 %m월'" %}
{% for month in posts_by_month %}
<h2>{{ month.name }}</h2>
<ul>
  {% for post in month.items %}
  <li>{{ post.date | date: "%d일" }} - <a href="{{ post.url | relative_url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
{% endfor %}