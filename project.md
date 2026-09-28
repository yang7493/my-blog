---
layout: page
title: PROJECT
permalink: /project/
---
 `project`

{% assign project_count = 0 %}
<ul>
{% for post in site.posts %}
{% assign terms = post.categories | concat: post.tags | join: "," | downcase %}
{% if terms contains "project" or terms contains "프로젝트" %}
{% assign project_count = project_count | plus: 1 %}
<li>
<a href="{{ post.url | relative_url }}">{{ post.title }}</a>
<small style="color:#888;">({{ post.date | date: "%Y-%m-%d" }})</small>
{% if post.tags.size > 0 %}
<div style="margin-top:2px;">
{% for tag in post.tags %}<span style="display:inline-block;background:#f0e5f5;color:#7b1fa2;border-radius:4px;padding:1px 6px;margin-right:4px;font-size:12px;">{{ tag }}</span>{% endfor %}
</div>
{% endif %}
</li>
{% endif %}
{% endfor %}
</ul>

{% if project_count == 0 %}
<p>아직 프로젝트 글이 없습니다.</p>
{% else %}
<p style="color:#888;font-size:14px;">총 {{ project_count }}개의 글</p>
{% endif %}
