---
layout: single
title: "데이터 엔지니어 로드맵: 도장깨기"
date: 2025-04-07
categories: Data-Engineering
tags: [Roadmap, DataEngineering, Career, BigData]
toc: true
toc_sticky: true
published: true
---

데이터 엔지니어로 성장하기 위한 핵심 역량들입니다.
백엔드 개발 경험을 바탕으로 데이터 엔지니어링으로 전환하거나 역량을 확장하세요.

---

## 1. 프로그래밍 언어

### Python (필수)
- [ ] Python 기본 문법 숙달
- [ ] 데이터 처리 라이브러리 (pandas, numpy)
- [ ] 파일 처리 (CSV, JSON, Parquet)
- [ ] 비동기 프로그래밍 (asyncio)
- [ ] 패키지 관리 (poetry, pip)

### SQL (필수)
- [ ] 기본 CRUD 쿼리
- [ ] JOIN 심화 (INNER, LEFT, FULL OUTER, CROSS)
- [ ] Window Functions (ROW_NUMBER, RANK, LAG, LEAD)
- [ ] CTE (Common Table Expressions)
- [ ] 서브쿼리 최적화
- [ ] 집계 함수 및 GROUP BY 심화

### 추가 언어
- [ ] Scala 기초 (Spark용)
- [ ] Java 기초 (Hadoop 에코시스템)
- [ ] Bash 스크립팅

---

## 2. 데이터베이스

### RDBMS
- [ ] PostgreSQL 심화
- [ ] MySQL 심화
- [ ] 쿼리 최적화 및 실행 계획 분석
- [ ] 인덱스 설계

### NoSQL
- [ ] MongoDB (Document Store)
- [ ] Redis (Key-Value Store)
- [ ] Cassandra (Wide Column Store)
- [ ] HBase 기초

### 분석용 데이터베이스
- [ ] ClickHouse (OLAP)
- [ ] Apache Druid
- [ ] DuckDB (임베디드 분석)

---

## 3. 데이터 웨어하우스

### 클라우드 DW
- [ ] Snowflake 기초 및 심화
- [ ] Google BigQuery
- [ ] Amazon Redshift
- [ ] Azure Synapse

### DW 개념
- [ ] Star Schema vs Snowflake Schema
- [ ] Slowly Changing Dimensions (SCD)
- [ ] Fact Table vs Dimension Table
- [ ] 데이터 모델링 (Kimball, Inmon)

---

## 4. ETL/ELT 파이프라인

### 워크플로우 오케스트레이션
- [ ] Apache Airflow 기초
  - [ ] DAG 작성
  - [ ] Operator 활용 (BashOperator, PythonOperator)
  - [ ] XCom 데이터 전달
  - [ ] 스케줄링 및 Backfill
- [ ] Dagster 기초
- [ ] Prefect 기초
- [ ] AWS Step Functions

### ETL 도구
- [ ] dbt (Data Build Tool)
  - [ ] 모델 작성
  - [ ] 테스트 및 문서화
  - [ ] Incremental Models
  - [ ] Snapshot
- [ ] Fivetran / Airbyte (데이터 수집)
- [ ] Apache NiFi

---

## 5. 빅데이터 처리

### Apache Spark
- [ ] Spark 아키텍처 이해
- [ ] PySpark 기본 API
- [ ] DataFrame vs RDD
- [ ] Spark SQL
- [ ] 파티셔닝 및 최적화
- [ ] Spark Streaming
- [ ] Delta Lake

### Hadoop 에코시스템
- [ ] HDFS 기초
- [ ] YARN 이해
- [ ] Hive 쿼리
- [ ] Hadoop MapReduce 개념

### 스트리밍 처리
- [ ] Apache Kafka
  - [ ] Producer/Consumer 구현
  - [ ] 파티셔닝 전략
  - [ ] Kafka Connect
  - [ ] Kafka Streams
- [ ] Apache Flink 기초
- [ ] AWS Kinesis

---

## 6. 클라우드 플랫폼

### AWS
- [ ] S3 (데이터 레이크 스토리지)
- [ ] Glue (ETL 서비스)
- [ ] Athena (서버리스 쿼리)
- [ ] EMR (관리형 Hadoop/Spark)
- [ ] Redshift
- [ ] Lake Formation
- [ ] IAM 권한 관리

### GCP
- [ ] Google Cloud Storage
- [ ] BigQuery
- [ ] Dataflow (Apache Beam)
- [ ] Dataproc (관리형 Spark)
- [ ] Pub/Sub

### Azure
- [ ] Azure Blob Storage
- [ ] Azure Data Factory
- [ ] Azure Databricks
- [ ] Synapse Analytics

---

## 7. 데이터 품질 & 거버넌스

### 데이터 품질
- [ ] Great Expectations 사용
- [ ] dbt 테스트 활용
- [ ] 데이터 검증 자동화
- [ ] 데이터 프로파일링

### 데이터 카탈로그
- [ ] Apache Atlas
- [ ] AWS Glue Data Catalog
- [ ] DataHub / Amundsen

### 데이터 거버넌스
- [ ] 데이터 리니지 추적
- [ ] 메타데이터 관리
- [ ] 데이터 분류 및 태깅
- [ ] PII 데이터 처리

---

## 8. 컨테이너 & 오케스트레이션

### Docker
- [ ] Dockerfile 작성
- [ ] Docker Compose
- [ ] 멀티스테이지 빌드

### Kubernetes
- [ ] 기본 개념 (Pod, Deployment, Service)
- [ ] Spark on Kubernetes
- [ ] Airflow on Kubernetes
- [ ] Helm Charts

---

## 9. 데이터 레이크 & 레이크하우스

### 데이터 레이크
- [ ] 데이터 레이크 아키텍처 이해
- [ ] 파일 포맷 (Parquet, ORC, Avro)
- [ ] 파티셔닝 전략
- [ ] 데이터 레이크 vs 데이터 웨어하우스

### 레이크하우스
- [ ] Delta Lake 심화
- [ ] Apache Iceberg
- [ ] Apache Hudi
- [ ] ACID 트랜잭션 on Data Lake

---

## 10. 모니터링 & 운영

### 파이프라인 모니터링
- [ ] Airflow 모니터링 설정
- [ ] 데이터 품질 대시보드
- [ ] 알림 설정 (Slack, PagerDuty)
- [ ] SLA 관리

### 로깅 & 메트릭
- [ ] 파이프라인 로깅 전략
- [ ] 처리 시간 메트릭
- [ ] 데이터 볼륨 추적
- [ ] 비용 모니터링

---

## 11. AI 도구 활용

### Claude Code 통합
- [x] Claude Code MCP/Skills/Hooks 개념 이해 - [[Claude Code로 데이터 엔지니어링 워크플로우 통합하기](/ai-engineering/claude-code-for-data-engineers/)]
- [ ] dbt Agent Skills 설정 및 활용
- [ ] MCP Data Toolbox로 DB 연결
- [ ] Airflow MCP 연동
- [ ] OpenMetadata MCP로 영향 분석

### 품질 자동화 (Hooks)
- [ ] pytest PreCommit Hook 설정
- [ ] sqlfluff SQL 린팅 Hook 설정
- [ ] dbt test 자동 실행 Hook 설정

### Docs-as-MCP
- [ ] Context7 설정
- [ ] gitingest 활용

---

## 12. 소프트 스킬

### 협업
- [ ] 데이터 분석가와 협업
- [ ] 비즈니스 요구사항 이해
- [ ] 데이터 모델 문서화
- [ ] 기술 문서 작성

### 문제 해결
- [ ] 데이터 파이프라인 디버깅
- [ ] 성능 병목 분석
- [ ] 데이터 불일치 해결
- [ ] 장애 대응 및 복구

---

## 진행 상황

| 영역 | 완료 | 전체 | 진행률 |
|------|------|------|--------|
| 프로그래밍 언어 | 0 | 14 | 0% |
| 데이터베이스 | 0 | 12 | 0% |
| 데이터 웨어하우스 | 0 | 8 | 0% |
| ETL/ELT | 0 | 13 | 0% |
| 빅데이터 처리 | 0 | 17 | 0% |
| 클라우드 | 0 | 15 | 0% |
| 데이터 품질 | 0 | 11 | 0% |
| 컨테이너 | 0 | 7 | 0% |
| 레이크하우스 | 0 | 8 | 0% |
| 모니터링 | 0 | 8 | 0% |
| AI 도구 활용 | 1 | 10 | 10% |
| 소프트 스킬 | 0 | 8 | 0% |
| **총계** | **1** | **131** | **1%** |

---

## 추천 학습 순서

### Phase 1: 기초 (1-3개월)
1. Python + SQL 심화
2. PostgreSQL 또는 MySQL
3. 기본 ETL 개념

### Phase 2: 핵심 도구 (3-6개월)
1. Apache Airflow
2. dbt
3. 클라우드 서비스 (S3, BigQuery 등)

### Phase 3: 빅데이터 (6-12개월)
1. Apache Spark
2. Kafka
3. 데이터 웨어하우스

### Phase 4: 심화 (12개월+)
1. 데이터 레이크하우스
2. 스트리밍 처리
3. 데이터 거버넌스

---

## 추천 리소스

### 도서
- "Fundamentals of Data Engineering" - Joe Reis
- "Designing Data-Intensive Applications" - Martin Kleppmann
- "The Data Warehouse Toolkit" - Ralph Kimball

### 온라인
- [DataEngineering.wiki](https://dataengineering.wiki/)
- [Start Data Engineering](https://www.startdataengineering.com/)
- [Data Engineering Zoomcamp](https://github.com/DataTalksClub/data-engineering-zoomcamp)

---

## 관련 로드맵

- [3년차 백엔드 개발자 로드맵](/backend/backend-3yr-roadmap/)
- [5년차 백엔드 개발자 로드맵](/backend/backend-5yr-roadmap/)
