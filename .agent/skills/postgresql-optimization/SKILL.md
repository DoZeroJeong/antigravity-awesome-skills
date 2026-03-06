---
name: postgresql-optimization
description: "SQL 쿼리 튜닝, 인덱싱 전략, 성능 분석 및 프로덕션 데이터베이스 관리를 위한 PostgreSQL 데이터베이스 최적화 워크플로우입니다."
category: granular-workflow-bundle
risk: safe
source: personal
date_added: "2026-02-27"
---

# PostgreSQL 최적화 워크플로우

## 개요

쿼리 튜닝, 인덱싱 전략, 성능 분석, Vacuum 관리 및 프로덕션 데이터베이스 관리를 포함하는 PostgreSQL 데이터베이스 최적화에 특화된 워크플로우입니다.

## 이 워크플로우를 사용할 때

다음과 같은 상황에서 이 워크플로우를 사용하세요:

- 느린 PostgreSQL 쿼리를 최적화할 때
- 인덱싱 전략을 설계할 때
- 데이터베이스 성능을 분석할 때
- PostgreSQL 설정을 튜닝할 때
- 프로덕션 데이터베이스를 관리할 때

## 워크플로우 단계별 진행

### 1단계: 성능 평가 (Phase 1: Performance Assessment)

#### 호출할 스킬

- `database-optimizer` - 데이터베이스 최적화
- `postgres-best-practices` - PostgreSQL 모범 사례

#### 명령어 제안 및 분석 항목

1. 데이터베이스 버전 확인
2. 설정 검토
3. 느린 쿼리 분석
4. 리소스 사용량 확인
5. 병목 현상 파악

#### 복사-붙여넣기 프롬프트

`Use @database-optimizer to assess PostgreSQL performance`

### 2단계: 쿼리 분석 (Phase 2: Query Analysis)

#### 호출할 스킬

- `sql-optimization-patterns` - SQL 최적화
- `postgres-best-practices` - PostgreSQL 패턴

#### 명령어 제안 및 분석 항목

1. EXPLAIN ANALYZE 명령어 제안 및 결과 분석
2. 스캔 타입 식별
3. 조인 전략 확인
4. 실행 시간 분석
5. 최적화 기회 포착

#### 복사-붙여넣기 프롬프트

`Use @sql-optimization-patterns to analyze and optimize queries`

### 3단계: 인덱싱 전략 (Phase 3: Indexing Strategy)

#### 호출할 스킬

- `database-design` - 인덱스 설계
- `postgresql` - PostgreSQL 인덱싱

#### 명령어 제안 및 분석 항목

1. 누락된 인덱스 파악
2. B-tree 인덱스 생성
3. 복합(Composite) 인덱스 추가
4. 부분(Partial) 인덱스 고려
5. 인덱스 사용 현황 검토

#### 복사-붙여넣기 프롬프트

`Use @database-design to design PostgreSQL indexing strategy`

### 4단계: 쿼리 최적화 (Phase 4: Query Optimization)

#### 호출할 스킬

- `sql-optimization-patterns` - 쿼리 튜닝
- `sql-pro` - SQL 전문가

#### 명령어 제안 및 분석 항목

1. 비효율적인 쿼리 재작성
2. 조인 최적화
3. 도움이 되는 경우 CTE(Common Table Expressions) 추가
4. 페이지네이션 구현
5. 개선 사항 테스트

#### 복사-붙여넣기 프롬프트

`Use @sql-optimization-patterns to optimize SQL queries`

### 5단계: 설정 튜닝 (Phase 5: Configuration Tuning)

#### 호출할 스킬

- `postgres-best-practices` - 설정
- `database-admin` - 데이터베이스 관리

#### 명령어 제안 및 분석 항목

1. `shared_buffers` 튜닝
2. `work_mem` 설정
3. `effective_cache_size` 설정
4. Checkpoint 설정 조정
5. Autovacuum 설정

#### 복사-붙여넣기 프롬프트

`Use @postgres-best-practices to tune PostgreSQL configuration`

### 6단계: 유지보수 (Phase 6: Maintenance)

#### 호출할 스킬

- `database-admin` - 데이터베이스 유지보수
- `postgresql` - PostgreSQL 유지보수

#### 명령어 제안 및 분석 항목

1. VACUUM 스케줄링
2. ANALYZE 명령어 제안 및 통계 결과 분석
3. 테이블 Bloat(공간 낭비) 확인
4. Autovacuum 모니터링
5. 통계 검토

#### 복사-붙여넣기 프롬프트

`Use @database-admin to schedule PostgreSQL maintenance`

### 7단계: 모니터링 (Phase 7: Monitoring)

#### 호출할 스킬

- `grafana-dashboards` - 모니터링 대시보드
- `prometheus-configuration` - 메트릭 수집

#### 명령어 제안 및 분석 항목

1. 모니터링 설정
2. 대시보드 생성
3. 알림(Alerts) 구성
4. 주요 메트릭 추적
5. 트렌드 검토

#### 복사-붙여넣기 프롬프트

`Use @grafana-dashboards to create PostgreSQL monitoring`

## 최적화 체크리스트

- [ ] 느린 쿼리 식별 완료
- [ ] 인덱스 최적화 완료
- [ ] 설정 튜닝 완료
- [ ] 유지보수 스케줄링 완료
- [ ] 모니터링 활성화 완료
- [ ] 성능 개선 확인

## 품질 게이트 (Quality Gates)

- [ ] 쿼리 성능이 향상되었는가?
- [ ] 인덱스가 효과적으로 작동하는가?
- [ ] 설정이 최적화되었는가?
- [ ] 유지보수가 자동화되었는가?
- [ ] 모니터링이 적절히 구성되었는가?

## 관련 워크플로우 번들

- `database` - 데이터베이스 운영
- `cloud-devops` - 인프라스트럭처
- `performance-optimization` - 성능
