---
layout: single
title: "5년차 백엔드 개발자 로드맵: 도장깨기"
date: 2025-04-07
categories: Backend
tags: [Roadmap, Backend, Career, Senior]
toc: true
toc_sticky: true
published: true
---

5년차 시니어 백엔드 개발자로 성장하기 위한 심화 역량들입니다.
3년차 로드맵을 기반으로 더 깊이 있는 지식을 쌓아가세요.

---

## 1. 분산 시스템

### 분산 시스템 이론
- [ ] CAP 정리 이해 및 트레이드오프
- [ ] PACELC 정리
- [ ] 일관성 모델 (Strong, Eventual, Causal)
- [ ] 분산 트랜잭션 (2PC, Saga Pattern)
- [ ] 분산 락 (Distributed Lock)
- [ ] 합의 알고리즘 (Raft, Paxos) 개념

### 분산 시스템 패턴
- [ ] Circuit Breaker 패턴
- [ ] Bulkhead 패턴
- [ ] Retry with Backoff
- [ ] Event Sourcing
- [ ] CQRS (Command Query Responsibility Segregation)

---

## 2. 마이크로서비스 아키텍처

### 설계
- [ ] 서비스 경계 설계 (Bounded Context)
- [ ] API Gateway 패턴
- [ ] Service Mesh 이해 (Istio, Linkerd)
- [ ] 서비스 디스커버리
- [ ] Configuration Management

### 통신
- [ ] 동기 통신 (REST, gRPC)
- [ ] 비동기 통신 (Message Queue)
- [ ] gRPC 프로토콜 버퍼 설계
- [ ] GraphQL 설계 및 구현

### 데이터 관리
- [ ] Database per Service 패턴
- [ ] Shared Database 안티패턴 이해
- [ ] 데이터 동기화 전략
- [ ] 이벤트 기반 데이터 일관성

---

## 3. 메시지 큐 & 이벤트 스트리밍

### 메시지 브로커
- [ ] Apache Kafka 심화
  - [ ] 파티셔닝 전략
  - [ ] Consumer Group 관리
  - [ ] Exactly-once 시맨틱
- [ ] RabbitMQ 심화
- [ ] AWS SQS/SNS 활용

### 이벤트 기반 아키텍처
- [ ] Event-Driven Architecture 설계
- [ ] 이벤트 스키마 버저닝
- [ ] Dead Letter Queue 처리
- [ ] 이벤트 순서 보장

---

## 4. 대용량 트래픽 처리

### 확장성
- [ ] 수평적 확장 (Horizontal Scaling)
- [ ] 수직적 확장 한계 이해
- [ ] Stateless 서비스 설계
- [ ] 오토스케일링 전략

### 캐싱 전략
- [ ] 다계층 캐싱 (L1, L2 Cache)
- [ ] Cache Invalidation 전략
- [ ] Write-through vs Write-back
- [ ] Cache Stampede 방지

### 성능
- [ ] 데이터베이스 샤딩
- [ ] Read Replica 활용
- [ ] CDN 활용
- [ ] 비동기 처리 최적화

---

## 5. Kubernetes & 클라우드 네이티브

### Kubernetes 심화
- [ ] Pod 설계 (sidecar, init container)
- [ ] Deployment 전략 (Rolling, Blue-Green, Canary)
- [ ] StatefulSet 활용
- [ ] Helm Chart 작성
- [ ] Custom Resource Definition (CRD)
- [ ] Operator 패턴 이해

### 클라우드 아키텍처
- [ ] Multi-AZ / Multi-Region 설계
- [ ] Serverless 아키텍처 (Lambda, Fargate)
- [ ] Infrastructure as Code (Terraform)
- [ ] GitOps (ArgoCD, Flux)

---

## 6. 데이터베이스 고급

### 분산 데이터베이스
- [ ] 샤딩 전략 설계 및 구현
- [ ] Global Transaction 관리
- [ ] CockroachDB / TiDB / Vitess 이해

### 시계열 & 특수 목적 DB
- [ ] 시계열 DB (InfluxDB, TimescaleDB)
- [ ] 그래프 DB (Neo4j) 기초
- [ ] Vector DB (Pinecone, Milvus) 기초

### 데이터 마이그레이션
- [ ] 무중단 스키마 변경
- [ ] 대용량 데이터 마이그레이션 전략
- [ ] Blue-Green Database 배포

---

## 7. 관측성 (Observability) 심화

### 분산 추적
- [ ] Distributed Tracing (Jaeger, Zipkin)
- [ ] OpenTelemetry 적용
- [ ] Trace Context 전파

### 메트릭 & 로깅
- [ ] SLI/SLO/SLA 정의 및 관리
- [ ] Error Budget 개념
- [ ] 커스텀 메트릭 설계
- [ ] Log Aggregation 파이프라인

### 장애 대응
- [ ] Chaos Engineering 기초 (Chaos Monkey)
- [ ] 게임데이 (Gameday) 실행
- [ ] Runbook 작성
- [ ] 인시던트 관리 프로세스

---

## 8. 보안 심화

### 애플리케이션 보안
- [ ] Zero Trust Architecture
- [ ] mTLS 구현
- [ ] API Security (Rate Limiting, Throttling)
- [ ] 보안 감사 로그

### 인프라 보안
- [ ] 네트워크 세그멘테이션
- [ ] Secrets Management 심화
- [ ] SIEM 연동
- [ ] 취약점 스캐닝 자동화

---

## 9. 리더십 & 기술 리딩

### 기술 리더십
- [ ] 기술 의사결정 프레임워크
- [ ] RFC/Design Doc 작성
- [ ] 기술 부채 관리
- [ ] 팀 기술 스택 선정

### 코드 리뷰 & 멘토링
- [ ] 효과적인 코드 리뷰 리딩
- [ ] 주니어 개발자 멘토링
- [ ] 페어 프로그래밍 실천
- [ ] 기술 온보딩 문서 작성

### 프로젝트 관리
- [ ] 기술적 일정 산정
- [ ] 리스크 식별 및 관리
- [ ] 이해관계자 커뮤니케이션
- [ ] 포스트모템 리딩

---

## 10. 시스템 설계 인터뷰 대비

### 대표 설계 문제
- [ ] URL Shortener 설계
- [ ] Rate Limiter 설계
- [ ] 채팅 시스템 설계
- [ ] 뉴스피드 시스템 설계
- [ ] 알림 시스템 설계
- [ ] 검색 자동완성 설계
- [ ] 분산 파일 시스템 설계

### 설계 프레임워크
- [ ] 요구사항 정의 (기능/비기능)
- [ ] 용량 산정 (QPS, Storage, Bandwidth)
- [ ] 고수준 설계
- [ ] 상세 설계
- [ ] 트레이드오프 분석

---

## 진행 상황

| 영역 | 완료 | 전체 | 진행률 |
|------|------|------|--------|
| 분산 시스템 | 0 | 11 | 0% |
| 마이크로서비스 | 0 | 12 | 0% |
| 메시지 큐 | 0 | 9 | 0% |
| 대용량 트래픽 | 0 | 12 | 0% |
| Kubernetes | 0 | 10 | 0% |
| DB 고급 | 0 | 8 | 0% |
| 관측성 | 0 | 11 | 0% |
| 보안 심화 | 0 | 8 | 0% |
| 리더십 | 0 | 12 | 0% |
| 시스템 설계 | 0 | 9 | 0% |
| **총계** | **0** | **102** | **0%** |

---

## 추천 리소스

### 도서
- "Designing Data-Intensive Applications" - Martin Kleppmann
- "Building Microservices" - Sam Newman
- "System Design Interview" - Alex Xu

### 온라인
- [ByteByteGo](https://bytebytego.com/)
- [High Scalability](http://highscalability.com/)
- [Martin Fowler's Blog](https://martinfowler.com/)

---

## 관련 로드맵

- [3년차 백엔드 개발자 로드맵](/backend/backend-3yr-roadmap/)
- [데이터 엔지니어 로드맵](/data-engineering/data-engineer-roadmap/)
