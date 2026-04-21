---
permalink: /portfolio/
title: "Portfolio"
layout: single
author_profile: true
toc: true
toc_sticky: true
---

AI 데이터 라벨링·MLOps 플랫폼의 설계부터 국방 폐쇄망 납품까지 전 주기를 경험한 백엔드 개발자입니다. Python/FastAPI/Django 기반 REST API 설계, Ray 분산처리 Agent 서버 개발, SDK 기반 플러그인 에코시스템 구축 등 AI 서비스 인프라 전반에 걸친 실무 경험을 보유하고 있습니다.

---

## Skills

### Backend
- **Languages**: Python
- **Frameworks**: Django, Django REST Framework, FastAPI, Ray Serve
- **Databases**: PostgreSQL, MySQL, MongoDB, Redis
- **Async/Realtime**: Celery, SSE(Server-Sent Events)

### DevOps & Cloud
- **Cloud**: AWS (EC2, S3, Lambda, Kinesis, Athena, RDS)
- **Container**: Docker, Docker Compose
- **CI/CD**: GitHub Actions
- **Network**: Tailscale VPN, SSH 기반 원격 배포

### Frontend
- **Frameworks**: Vue 3, Nuxt
- **UI Libraries**: Shadcn-vue, Nuxt UI, Formkit

### AI / Data
- **MLOps**: Ray (Serve/Job), 데이터 라벨링 파이프라인
- **AI Agent**: MCP(Model Context Protocol), LLM API, 컨텍스트 엔지니어링
- **Data Pipeline**: AWS Kinesis, S3, Athena, ETL

---

## Projects

### 1. Synapse 2.0 MLOps 플랫폼 리뉴얼

**기간**: 2024.01 ~ 현재 \| **소속**: (주)데이터메이커 플랫폼개발팀

**기술 스택**: Django, FastAPI, Ray Serve, PostgreSQL, Redis, Vue 3/Nuxt, Docker

#### Problem

Synapse 1.0은 데이터 라벨링 플랫폼이었지만, 고객사가 요구하는 MLOps 워크플로우(학습 → 추론 → 배포)를 지원하지 못했습니다. 특히 국방 분야 폐쇄망 고객사에 납품해야 하는 상황에서, 외부 네트워크가 완전히 차단된 환경에서도 동작하는 플랫폼이 필요했습니다.

#### Analyze

플랫폼 리뉴얼의 핵심 과제는 두 가지였습니다.

**플러그인 시스템 아키텍처**: 외부 개발자가 자체 ML 파이프라인을 플러그인으로 개발·등록할 수 있어야 했습니다. Agent 서버가 플러그인을 실행하는 구조에서, Ray Serve를 선택한 이유는 분산 처리와 리소스 관리를 프레임워크 레벨에서 지원하기 때문입니다. 자체 프로세스 관리 대비 운영 복잡도가 낮았습니다.

**폐쇄망 배포 전략**: pip install이 불가능한 환경에서 10개 이상의 Python 패키지 의존성을 관리해야 했습니다. pip download로 wheel 파일을 사전 패키징하고, Docker 기반 로컬 PyPI 서버를 구축하는 방식으로 해결했습니다.

#### Action

- Python SDK 기반 플러그인 에코시스템 설계·개발 (외부 개발자가 SDK로 플러그인 개발 → 플랫폼에 업로드 → Ray Serve에서 실행)
- 비정형 데이터 수집 플러그인 개발: 이미지 추출, PDF→이미지 변환, 비디오 포맷 변환, COCO 데이터셋 처리
- SSE 기반 실시간 알림 시스템 구현 (작업 진행률, 완료 알림)
- Agent-Backend 통신 설계: 데이터 교환 포맷, polling interval 최적화 (부하와 실시간성 균형)
- Vue 3/Nuxt 기반 프론트엔드 디자인 시스템 컴포넌트 개발 (초기 1년)
- Docker 기반 폐쇄망 온프레미스 배포 및 현장 납품

#### Result

- 2025년 8월 국방 분야 폐쇄망 환경 첫 납품 완료
- 폐쇄망 네트워크 주소 매핑 이슈를 현장에서 동적 주소 매핑 로직 추가로 해결
- API TestCase 기반 코드 커버리지 80%+ 유지
- GitHub Actions CI/CD 자동화 파이프라인 구축
- 현재 유지보수 및 고도화 진행 중

---

### 2. 멀티에이전트 개인 자동화 시스템 (OpenClaw)

**기간**: 2025.02 ~ 현재 \| **역할**: 1인 설계·개발 (개인 프로젝트)

**기술 스택**: Python, MCP(Model Context Protocol), Telegram Bot API, Notion API, Tailscale VPN

#### Problem

일일 저널링, 학습 정리, 블로그 포스팅 같은 반복 작업에 매일 상당한 시간을 쓰고 있었습니다. 각 작업은 단순하지만 맥락이 다르기 때문에, 범용 AI 챗봇으로는 처리하기 어려웠습니다.

#### Analyze

단일 에이전트로 모든 작업을 처리하면 프롬프트가 비대해지고 품질이 떨어집니다. 도메인별로 에이전트를 분리하되, 서로 데이터를 공유할 수 있는 오케스트레이션이 필요했습니다. MCP 프로토콜을 선택한 이유는, 에이전트가 외부 도구(Notion, 캘린더 등)를 표준화된 인터페이스로 호출할 수 있기 때문입니다.

#### Action

- 4개 도메인별 AI Agent 설계·구현 (코칭, 커리어, 스터디, 블로깅)
- 컨텍스트 엔지니어링으로 에이전트별 역할·지시·맥락 분리
- Telegram Bot API 기반 사용자 인터페이스 구축
- Notion MCP 서버 연동으로 에이전트 외부 도구 활용
- Mac mini M4 홈서버 상시 운영, Tailscale VPN으로 원격 관리

#### Result

- 일일 저널링, 학습 정리, 블로그 포스팅 자동화 실사용 운영 중
- AI Agent 설계 → 구현 → 운영 전 주기를 1인으로 완수
- MCP 기반 멀티에이전트 오케스트레이션 설계·운영 경험 확보

---

### 3. 게임 로그 데이터 파이프라인 (기업협업)

**기간**: 2021.12 ~ 2022.01 (4주) \| **역할**: Data Engineer (3인 팀)

**기술 스택**: AWS Kinesis, Lambda, CloudWatch, S3, Athena, Python

#### Problem

게임 서버에서 초당 2,000건 이상의 로그가 발생하는데, 이를 실시간으로 수집·저장하고 분석 가능한 형태로 적재해야 했습니다.

#### Analyze

배치 처리 vs 스트리밍 처리를 비교했을 때, 게임 로그의 특성상 실시간 수집이 필수였습니다. AWS Kinesis 기반 스트리밍 파이프라인을 선택한 이유는, Lambda와의 통합이 자연스럽고 S3 적재까지 Firehose로 자동화할 수 있었기 때문입니다.

#### Action

- Kinesis Stream → Firehose → S3 파이프라인 아키텍처 설계·구현
- Lambda + CloudWatch 기반 스케줄링 구현
- API 로그데이터 로컬 인코딩 (압축 알고리즘 적용)
- S3 압축 데이터 복원 및 Athena 조회 검증

#### Result

- 초당 2,000건+ 게임 로그 안정적 수집·적재 완료
- 수집 → 처리 → 적재 → 조회 전 과정 팀 협업 경험

---

## Experience

### (주)데이터메이커 — 플랫폼개발팀 주임연구원
**2023.05 ~ 현재**

AI 데이터 라벨링·MLOps 플랫폼 Synapse의 백엔드 설계·개발·운영 및 국방 폐쇄망 납품 전담.

- Django/FastAPI 기반 REST API 설계 및 개발
- Python SDK 및 플러그인 시스템 아키텍처 구축
- Ray Serve 기반 Agent 서버 설계·개발
- SSE 기반 실시간 알림 시스템 구현
- 방산 고객사 폐쇄망 환경 납품 (wheel 패키징, Docker PyPI 서버, 현장 트러블슈팅)
- LLM API 활용 사내 챗봇 MVP 개발
- 제조업 고객사 업무 문서 자동 변환 PoC (Excel/PPT 파싱)
- API TestCase 기반 코드 품질 관리 (Coverage 80%+)
- GitHub Actions CI/CD 자동화 파이프라인 구축
- Vue 3/Nuxt 기반 프론트엔드 개발 지원 (2024)

### (주)코드스테이츠 — Data Engineering Coaching Assistant
**2022.02 ~ 2022.07**

AI 부트캠프 Data Engineering 섹션의 수강생 학습 지도 및 채점 자동화.

- Python, Database, Docker 기술 Q&A 및 학습 멘토링
- 채점 자동화 ETL 파이프라인 개발 (MySQL/DynamoDB → Pandas → Google Sheets)

### 경보전기(주) — 연구개발부 개발 QA
**2019.09 ~ 2021.04**

IEC 61850 기반 보호계전기 제품 개발 및 시험평가.

- 신제품(KPAM-5500) 보호계전 시험 설계 및 수행
- KERI 인증 예비시험, EMI/EMC 시험 수행
- 품질관리부서 협업 및 제품 사용설명서 작성

---

## Education

### 학점은행제 — 컴퓨터공학 학사
**2024 ~ 2026.04 (취득)**

실무와 병행하며 부족한 CS 기초를 보완했습니다.

- 인공지능, 데이터베이스, 운영체제, 소프트웨어공학
- 컴퓨터네트워크, 시스템프로그래밍, 이산수학

### 경운대학교 — 신소재에너지공학 학사
**2013.03 ~ 2019.02** \| GPA 4.32 / 4.50

매 학기 CS 과목을 병행 이수했습니다.

- 자료구조 (A), 알고리즘설계 (A+), 영상처리공학 (A+)

### CodeStates AI Bootcamp 6기
**2021.07 ~ 2022.01** (1,120시간)

- EDA, 머신러닝, 데이터 엔지니어링, 딥러닝 (NLP, 컴퓨터 비전)

---

## Certifications

- 정보처리기사 (2019.11)
- ADsP 데이터분석준전문가 (2021.09)
- 리눅스마스터 2급 (2024.03)

---

## Contact

- **Email**: sbkhs274@gmail.com
- **GitHub**: [charlie-hs](https://github.com/charlie-hs)
- **Blog**: [charlie-hs.github.io](https://charlie-hs.github.io)
