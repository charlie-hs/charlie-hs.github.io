---
title: "경제뉴스"
layout: archive
permalink: /categories/economy/
author_profile: true
---

매일 경제뉴스를 한 건씩 골라 정리합니다. 기사 요약에서 그치지 않고, 용어를 직접 풀어 쓰고 "왜 중요한가 / 내 해석"까지 적어 누적합니다.

- **거시**: 기준금리, 환율, 물가, 경제전망
- **중소기업·정책금융**: 자금조달, 보증, 한계기업, 구조조정
- **금융시사**: ESG, 공급망, 자본시장 이슈

{% assign posts = site.categories['Economy'] %}
{% for post in posts %}
  {% include archive-single.html %}
{% endfor %}
