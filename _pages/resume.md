---
permalink: /resume/
title: "Resume"
layout: single
author_profile: true
toc: true
toc_sticky: true
---

## 김현수 · Backend Engineer

AI 데이터·MLOps 플랫폼의 설계부터 국방 폐쇄망 납품까지 전 주기를 경험한 백엔드 개발자.
Python/Django/FastAPI 기반 REST API 설계, Ray 분산처리 Agent 서버 개발, SDK 기반 플러그인 에코시스템 구축.

📧 sbkhs274@gmail.com · [GitHub](https://github.com/charlie-hs) · [Blog](https://charlie-hs.github.io)

---

## Skills

**Backend**: Python, Django, DRF, FastAPI, Ray Serve, Celery, Redis

**Database**: PostgreSQL, MySQL, MongoDB

**DevOps**: Docker, GitHub Actions CI/CD, AWS (EC2, S3, Lambda, Kinesis)

**Frontend**: Vue 3, Nuxt (디자인 시스템 컴포넌트 개발 경험)

**AI/Data**: MCP, LLM API, 데이터 라벨링 파이프라인, AWS Kinesis 스트리밍

---

## Experience

### (주)데이터메이커 · 플랫폼개발팀 주임연구원
**2023.05 ~ 현재**

AI 데이터 라벨링·MLOps 플랫폼 Synapse 백엔드 개발 및 국방 폐쇄망 납품 전담.

**Synapse 2.0 MLOps 플랫폼 리뉴얼** (2024.01 ~ 현재)

- **P**: Synapse 1.0은 데이터 라벨링 플랫폼이었으나, 고객사가 요구하는 MLOps 워크플로우(학습→추론→배포)를 지원하지 못함. 국방 폐쇄망 환경에서 동작해야 하는 추가 제약.
- **A**: 플러그인 실행 구조에서 Ray Serve를 선택 — 분산 처리와 리소스 관리를 프레임워크 레벨에서 지원하여 자체 프로세스 관리 대비 운영 복잡도가 낮음. 폐쇄망 배포는 pip download로 wheel 사전 패키징 + Docker 기반 로컬 PyPI 서버로 해결.
- **A**: Python SDK 기반 플러그인 에코시스템 설계·개발, SSE 실시간 알림 시스템, Agent-Backend 통신 설계 (polling interval 최적화), Vue 3/Nuxt 프론트엔드 개발 (초기 1년), 폐쇄망 온프레미스 배포 및 현장 납품.
- **R**: 2025년 8월 국방 폐쇄망 첫 납품 완료. 코드 커버리지 80%+ 유지. GitHub Actions CI/CD 파이프라인 구축.

**기타 기여**

- LLM API 활용 사내 챗봇 MVP 개발 및 시연
- 제조업 고객사 업무 문서 자동 변환 PoC (Excel/PPT 파싱)
- PostgreSQL 쿼리 최적화 (filterset, indexing)
- 파일 업로드 체크썸 검증 기능 구현

### (주)코드스테이츠 · Data Engineering CA
**2022.02 ~ 2022.07**

- AI 부트캠프 Data Engineering 섹션 수강생 학습 지도
- 채점 자동화 ETL 파이프라인 개발 (MySQL/DynamoDB → Pandas → Google Sheets)

### 경보전기(주) · 연구개발부 개발 QA
**2019.09 ~ 2021.04**

- IEC 61850 기반 보호계전기 신제품(KPAM-5500) 시험 설계 및 수행
- KERI 인증 예비시험, EMI/EMC 시험 수행

---

## Projects

**멀티에이전트 개인 자동화 시스템 (OpenClaw)** · 2025.02 ~ 현재 · 1인

- **P**: 일일 저널링, 학습 정리, 블로그 포스팅 등 반복 작업에 매일 시간 소모. 맥락이 다른 작업이라 범용 AI 챗봇으로 처리 불가.
- **A**: 도메인별 에이전트 분리 + MCP 프로토콜로 오케스트레이션. 에이전트가 외부 도구를 표준화된 인터페이스로 호출 가능.
- **A**: 4개 도메인별 AI Agent 구현, Telegram Bot UI, Notion MCP 연동, Mac mini M4 홈서버 + Tailscale VPN 운영.
- **R**: 일일 자동화 실사용 운영 중. AI Agent 설계→구현→운영 전 주기 1인 완수.

**게임 로그 데이터 파이프라인 (기업협업)** · 2021.12 ~ 2022.01 · 3인

- **P**: 초당 2,000건+ 게임 로그 실시간 수집·적재 필요.
- **A**: 배치 vs 스트리밍 비교 후, Kinesis 기반 스트리밍 선택 — Lambda 통합 + Firehose→S3 자동화.
- **A**: Kinesis Stream→Firehose→S3 파이프라인 구현, 압축 알고리즘 적용, Athena 조회 검증.
- **R**: 초당 2,000건+ 로그 안정적 수집·적재 완료.

---

## Education

**학점은행제 · 컴퓨터공학 학사** · 2024 ~ 2026.04 취득

인공지능, 데이터베이스, 운영체제, 소프트웨어공학, 컴퓨터네트워크, 이산수학

**경운대학교 · 신소재에너지공학 학사** · 2013.03 ~ 2019.02 · GPA 4.32/4.50

자료구조 (A), 알고리즘설계 (A+), 영상처리공학 (A+) 병행 이수

**CodeStates AI Bootcamp 6기** · 2021.07 ~ 2022.01 (1,120시간)

---

## Certifications

- 정보처리기사 (2019.11)
- ADsP 데이터분석준전문가 (2021.09)
- 리눅스마스터 2급 (2024.03)
