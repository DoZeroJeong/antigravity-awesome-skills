---
name: git-pr-workflows-git-workflow
description: "코드 리뷰 단계부터 PR 생성까지 아우르는 포괄적인 Git 워크플로우. 전문적인 에이전트를 활용하여 품질 보증, 테스트 및 배포 준비 상태를 점검합니다. 이 워크플로우는 Conventional Commits, 자동화된 테스트 및 체계적인 PR 생성을 포함한 최신 Git 모범 사례를 구현합니다."
risk: unknown
source: community
date_added: "2026-02-27"
---

# 다중 에이전트 오케스트레이션을 통한 완전한 Git 워크플로우

코드 리뷰 단계부터 PR 생성까지 아우르는 포괄적인 Git 워크플로우입니다. 품질 보증, 테스트 및 배포 준비 상태 점검을 위해 전문 에이전트들을 활용합니다. 본 워크플로우는 Conventional Commits(일관된 커밋 메시지 작성법), 자동화 테스트 및 구조화된 PR 생성을 포함하는 최신 Git 모범 사례를 적용합니다.

[심층 분석: 이 워크플로우는 코드가 커밋되기 전 품질을 보장하기 위해 여러 에이전트를 조율합니다. `code-reviewer` 에이전트가 초기 품질 검토를 수행하고, `test-automator`가 모든 테스트 통과 여부를 확인하며, `deployment-engineer`가 프로덕션 배포 준비 상태를 검증합니다. 컨텍스트 전달과 함께 순차적으로 에이전트를 배치함으로써, 빠른 개발 속도를 유지하면서 손상된 코드가 저장소에 유입되는 것을 방지합니다. 또한, 여러 팀의 니즈에 맞게 구성 가능한 옵션으로 Trunk-based 및 Feature-branch 전략 모두를 지원합니다.]

## 이 워크플로우를 사용할 때

- 다중 에이전트 오케스트레이션을 동반한 포괄적 Git 워크플로우 작업 시
- 해당 워크플로우에 대한 가이드, 모범 사례 또는 체크리스트가 필요할 때

## 이 스킬의 사용을 지양할 때

- 작업이 포괄적 Git 워크플로우 및 멀티 에이전트 오케스트레이션과 무관할 때
- 이 범위를 벗어난 다른 도메인이나 도구가 필요할 때

## 지시사항

- 목표, 제약 조건 및 필요한 입력을 명확히 하세요.
- 관련된 모범 사례를 적용하고 결과를 검증하세요.
- 사용자에게 제안할 실행 가능한 명령어(단계)와 검증 방법을 명확히 제공하세요.
- 상세한 예제가 필요하다면 `resources/implementation-playbook.md`를 열어 확인하세요.

## 설정 (Configuration)

**타겟 브랜치 (Target branch)**: `$ARGUMENTS` (명시되지 않을 경우 기본값은 'main')

**지원되는 플래그 (Supported flags)**:

- `--skip-tests`: 자동화 테스트 실행 건너뛰기 (주의해서 사용 요망)
- `--draft-pr`: 작업 진행 중(WIP) 상태를 위한 Draft PR로 생성
- `--no-push`: 모든 분석을 수행하되 리모트 저장소로 푸시하지 않음
- `--squash`: 리모트 푸시 전 커밋 스쿼시(Squash)
- `--conventional`: 엄격한 Conventional Commits 포맷 강제
- `--trunk-based`: 트렁크 기반(Trunk-based) 개발 워크플로우 사용
- `--feature-branch`: 기능 브랜치(Feature branch) 개발 워크플로우 사용 (기본값)

## 1단계: 사전 커밋 리뷰 및 분석 (Phase 1: Pre-Commit Review and Analysis)

### 1. 코드 품질 평가

- `subagent_type="code-reviewer"`인 Task 도구를 사용합니다.
- 프롬프트: "코드 품질 문제에 대해 아직 커밋되지 않은 모든 변경 사항을 검토하세요. 1) 코드 스타일 위반, 2) 보안 취약점, 3) 성능 우려 사항, 4) 누락된 예외 처리, 5) 불완전한 구현을 확인하세요. 심각도 수준(critical/high/medium/low)과 라인별 구체적인 피드백이 담긴 상세 보고서를 생성하세요. 출력 포맷: JSON 형식 `{issues: [], summary: {critical: 0, high: 0, medium: 0, low: 0}, recommendations: []}`"
- 예상 출력: 다음 단계를 위한 구조화된 코드 리뷰 보고서

### 2. 의존성 및 하위 호환성 침해(Breaking Change) 분석

- `subagent_type="code-reviewer"`인 Task 도구를 사용합니다.
- 프롬프트: "다음 사항에 대해 변경 분을 분석하세요: 1) 새로운 의존성이나 버전 변경, 2) Breaking API 변경, 3) 데이터베이스 스키마 수정, 4) 환경 설정 변경, 5) 하위 호환성 문제. 이전 리뷰에서 생성된 컨텍스트: [이슈 요약 입력]. 마이그레이션 스크립트나 문서 업데이트가 필요한 변경 사항을 식별하세요."
- 이전 단계로부터의 컨텍스트: Breaking Change를 암시할 수 있는 코드 품질 문제
- 예상 출력: Breaking change 평가 및 마이그레이션 요구사항 목록

## 2단계: 테스트 및 검증 (Phase 2: Testing and Validation)

### 1. 테스트 실행 및 커버리지

- `subagent_type="unit-testing::test-automator"`인 Task 도구를 사용합니다.
- 프롬프트: "수정된 코드에 대해 사용자에게 테스트 스위트 실행 명령어를 제안하고, 실행 결과를 전달받아 분석하세요. 1) 유닛 테스트, 2) 통합 테스트, 3) End-to-end 테스트(해당 시)를 실행할 것을 권고합니다. 커버리지 보고서를 생성하고 테스트되지 않은 코드 경로를 식별하세요. 리뷰에서 나온 문제점들 [critical/high 이슈 입력] 을 참고하여 리스크 영역을 테스트가 커버하는지 확인하십시오. 테스트 결과를 다음 형식으로 제공하세요: `{passed: [], failed: [], skipped: [], coverage: {statements: %, branches: %, functions: %, lines: %}, untested_critical_paths: []}`"
- 이전 단계로부터의 컨텍스트: 테스트 커버리지가 필요한 치명적 코드 리뷰 이슈
- 예상 출력: 전체 테스트 결과 및 커버리지 메트릭

### 2. 테스트 권장 사항 및 갭(Gap) 분석

- `subagent_type="unit-testing::test-automator"`인 Task 도구를 사용합니다.
- 프롬프트: "테스트 결과 [요약 입력] 및 코드 변경을 기반으로 다음을 식별하세요: 1) 누락된 테스트 시나리오, 2) 커버되지 않은 예외(Edge) 케이스, 3) 검증해야 할 통합 포인트, 4) 필요한 성능 벤치마크. 리스크 우선순위에 따라 추가적 테스트 구현 권장 사항을 생성하세요. 파악된 Breaking Change를 고려하십시오: [Breaking changes 입력]."
- 이전 단계로부터의 컨텍스트: 테스트 결과, Breaking Changes, 미테스트 경로 등
- 예상 출력: 우선순위가 정해진 추가 테스트 필요 목록

## 3단계: 커밋 메시지 생성 (Phase 3: Commit Message Generation)

### 1. 변경 요약 및 범주화 (Categorization)

- `subagent_type="code-reviewer"`인 Task 도구를 사용합니다.
- 프롬프트: "Conventional Commits 사양에 따라 모든 변경을 분석하고 범주화하세요. 기본 변경 유형(feat/fix/docs/style/refactor/perf/test/build/ci/chore/revert)과 범위를 식별하세요. 변경 대상: [파일 리스트 및 요약 입력]. 이것이 단일 커밋이어야 하는지 원자적(Atomic) 다수 커밋이어야 하는지 결정하세요. 다음 테스트 결과를 고려하세요: [테스트 요약 입력]."
- 이전 단계로부터의 컨텍스트: 테스트 결과, 코드 리뷰 요약
- 예상 출력: 커밋 구조 제안(단일 커밋 vs 다중 커밋)

### 2. Conventional 커밋 메시지 작성

- `subagent_type="llm-application-dev::prompt-engineer"`인 Task 도구를 사용합니다.
- 프롬프트: "할당된 범주 분류 결과 [분류 결과 입력] 에 따라 Conventional Commits 포맷 메시지들을 생성하세요. 포맷: `<type>(<scope>): <subject>` 다음 빈 줄 1개 추가 후 작업에 대한 내용과 이유를 `<body>`에 적고, 필요한 경우 `<footer>`에 `BREAKING CHANGE:` 를 기입합니다. 다음을 포함하세요: 1) 명확한 제목 (50자 이하), 2) 이유가 설명된 상세 본문, 3) 관련된 이슈나 티켓 레퍼런스, 4) 해당되는 경우 공동 작성자(Co-authors). 파급 효과를 고려하십시오: [있는 경우 breaking changes 입력]."
- 이전 단계로부터의 컨텍스트: 변경 분류 내용, Breaking changes 정보
- 예상 출력: 포맷에 맞춰 올바르게 작성된 커밋 메시지

## 4단계: 브랜치 전략 및 푸시 준비 (Phase 4: Branch Strategy and Push Preparation)

### 1. 브랜치 관리

- `subagent_type="cicd-automation::deployment-engineer"`인 Task 도구를 사용합니다.
- 프롬프트: "워크플로우 타입(`--trunk-based` 또는 `--feature-branch`)에 따라 브랜치 전략을 준비하세요. Feature branch인 경우 브랜치 이름 규칙 `(feature|bugfix|hotfix)/<ticket>-<description>`을 지켰는지 확인합니다. Trunk-based의 경우 필요한 경우 피처 플래그 통제 전략과 함께 main 브랜치 다이렉트 푸시를 준비합니다. 현재 브랜치: [현재 브랜치 입력], 대상(Target): [대상 브랜치 입력]. 브랜치 충돌이 없는지 확인하세요."
- 예상 출력: 브랜치 준비 커맨드 및 충돌 발생 여부 상태

### 2. 푸시(Push) 사전 검증

- `subagent_type="cicd-automation::deployment-engineer"`인 Task 도구를 사용합니다.
- 프롬프트: "마지막 푸시 전 사용자에게 다음 점검 항목을 확인하고 실행하도록 권고하세요: 1) 모든 CI 체크 통과 여부 검증, 2) 커밋에 민감 데이터가 없는지 확인, 3) (필요시) 커밋 서명(commit signatures) 검증, 4) 브랜치 보호 규칙 점검, 5) 리뷰 커멘트 모두 반영되었는지 확인. 테스트 요약: [테스트 결과 입력]. 리뷰 상태: [리뷰 요약 입력]."
- 이전 단계로부터의 컨텍스트: 이전에 수행된 모든 검증 결과
- 예상 출력: 푸시 준비 완료 승인 여부 및 진행 불가능(Blocking) 항목 안내

## 5단계: Pull Request 생성 (Phase 5: Pull Request Creation)

### 1. PR 설명문 생성

- `subagent_type="documentation-generation::docs-architect"`인 Task 도구를 사용합니다.
- 프롬프트: "다음 항목을 포함해 포괄적인 PR 설명문(Description)을 작성하세요: 1) 변경 요약 (무엇을 왜 바꿨는지), 2) 변경 유형 체크리스트, 3) 수행한 테스트 요약 [테스트 결과 입력], 4) UI 변경이 있을 시 스크린샷/녹화본 첨부, 5) 배포 시 고려 사항 [배포 고려사항 입력], 6) 관련된 이슈/티켓, 7) (해당 시) Breaking changes 리스트: [breaking changes 입력], 8) 리뷰어 확인 체크리스트. GitHub Flavored Markdown 포맷으로 작성하세요."
- 이전 단계로부터의 컨텍스트: 모든 검증 결과, 테스트 결과, Breaking changes
- 예상 출력: Markdown 형식으로 완성된 완벽한 PR 설명문

### 2. PR 메타데이터 및 자동차 설정

- `subagent_type="cicd-automation::deployment-engineer"`인 Task 도구를 사용합니다.
- 프롬프트: "PR 메타데이터 설정: 1) CODEOWNERS에 명시된 적절한 리뷰어 지정, 2) 라벨 달기(type, priority, component), 3) 관련된 이슈 링크, 4) 마일스톤 할당, 5) Merge 전략 채택(squash/merge/rebase), 6) 모든 검사 통과 시 Auto-merge 설정(해당 시). Draft 상태 여부를 고려할 것: [`--draft-pr` 플래그 확인]. 테스트 상태 포함: [테스트 요약 입력]."
- 이전 단계로부터의 컨텍스트: PR 설명, 테스트 결과, 리뷰 상태
- 예상 출력: PR 설정/생성 관련 명령 및 자동화 규칙 등록

## 성공 기준 (Success Criteria)

- ✅ 심각/높음(Critical/High) 수준의 코드 이슈가 모두 해결되었음
- ✅ 테스트 커버리지 유지 또는 상승 (목표: >80%)
- ✅ 모든 테스트 통과 (Unit, Integration, E2E)
- ✅ 커밋 메시지가 Conventional Commits 포맷을 준수함
- ✅ 메인 대상 브랜치와의 병합(Merge) 충돌이 없음
- ✅ PR 설명문에 필수 섹션이 전부 기재됨 properly
- ✅ 브랜치 보호(Branch Protection) 정책 준수
- ✅ 보안 스캐닝 통과 및 치명적 취약점 없음
- ✅ 성능 벤치마크 결과가 합격 가능한 기준 내에 속함
- ✅ API 수정 사항이 있다면 관련된 문서가 전부 업데이트됨

## 롤백 절차 (Rollback Procedures)

Merge 이후 버그가 발생한 경우:

1. **즉각적 롤백 (Immediate Revert)**: `git revert <commit-hash>` 를 통해 롤백 PR 생성
2. **기능 비활성화 (Feature Flag Disable)**: 피처 플래그 사용 중일 경우 스위치를 바로 끔
3. **핫픽스 브랜치 (Hotfix Branch)**: 심각한 이슈라면 main 브랜치에서 분기하여 Hotfix 브랜치 생성
4. **연락 체계 (Communication)**: 정해진 슬랙 채널 등으로 다른 팀에 알림
5. **사건 원인 규명 (Root Cause Analysis)**: 담당 포스트모템(Postmortem) 템플릿에 장애 문서를 작성

## 베스트 프랙티스 참고사항

- **커밋 주기**: 자주, 일찍 커밋하되, 각각의 커밋 내용들은 원자성(Atomic)이어야 합니다.
- **브랜치 네이밍**: `(feature|bugfix|hotfix|docs|chore)/<ticket-id>-<간단한-설명>`
- **PR 사이즈 (크기)**: 효과적인 코드 리뷰를 위해 400라인(Lines of code) 이하로 유지합니다.
- **리뷰 응답 속도**: 코드 리뷰 등 피드백에 대해 24시간 안에 응답합니다.
- **Merge 전략 (Merge Strategy)**: 기능 구현용 브랜치에서는 Squash merge, 릴리즈 등은 기본 병합(Merge)을 씁니다.
- **승인 갯수 (Sign-Off)**: Main 브랜치 변경 사항은 코드 리뷰어 최소 2명의 Approve가 있어야 합니다.
