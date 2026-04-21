---
title: "Career"
layout: archive
permalink: /categories/career/
author_profile: true
---

이력서, 포트폴리오, 면접 준비 등 개발자 커리어 전반을 다룹니다.

- **이력서**: PAAR 프레임워크, 수치화, 컨셉 설정
- **포트폴리오**: 프로젝트 선별, 스토리텔링
- **면접**: 기술 면접, 컬쳐핏, 질문 대응

{% assign posts = site.categories['Career'] %}
{% for post in posts %}
  {% include archive-single.html %}
{% endfor %}
