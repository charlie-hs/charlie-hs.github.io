---
title: "AI Engineering"
layout: archive
permalink: /categories/ai-engineering/
author_profile: true
---

LLM 애플리케이션 개발부터 프로덕션 운영까지, AI 엔지니어링 전반을 다룹니다.

- **Foundation**: LLM 아키텍처, Prompt Engineering
- **Application**: LangChain, LangGraph, RAG, VectorDB, MCP
- **Customization**: Fine-tuning, LoRA/QLoRA
- **Production**: Harness Engineering, Guardrailing, 평가 & 모니터링

{% assign posts = site.categories['AI-Engineering'] %}
{% for post in posts %}
  {% include archive-single.html %}
{% endfor %}
