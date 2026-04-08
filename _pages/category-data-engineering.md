---
title: "데이터 엔지니어링"
layout: archive
permalink: /categories/data-engineering/
author_profile: true
---

데이터 파이프라인, ETL, 데이터 웨어하우스, 빅데이터 처리 등 데이터 엔지니어링 관련 학습 내용입니다.

{% assign posts = site.categories['Data-Engineering'] %}
{% for post in posts %}
  {% include archive-single.html %}
{% endfor %}
