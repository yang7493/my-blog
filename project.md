---
layout: page
title: PROJECT
permalink: /project/
---

{% assign project_count = 0 %}
<ul>
{% for post in site.posts %}
{% assign body = post.content %}
{% assign is_result = false %}
{% if body contains "프로젝트 결과물" %}{% assign is_result = true %}{% endif %}
{% if post.deploy_url %}{% assign is_result = true %}{% endif %}
{% if is_result %}
{% assign project_count = project_count | plus: 1 %}
<li style="margin-bottom:10px;">
<a href="{{ post.url | relative_url }}">{{ post.title }}</a>
<small style="color:#888;">({{ post.date | date: "%Y-%m-%d" }})</small>
{% if post.deploy_url %}
<div style="margin-top:2px;"><a href="{{ post.deploy_url }}" style="font-size:13px;">🔗 {{ post.deploy_url }}</a></div>
{% endif %}
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
<p>아직 프로젝트 결과물이 없습니다.</p>
{% else %}
<p style="color:#888;font-size:14px;">총 {{ project_count }}개</p>
{% endif %}
