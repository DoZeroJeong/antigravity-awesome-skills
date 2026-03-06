# Antigravity Awesome Skills

**Antigravity Awesome Skills**는 AI 에이전트(Antigravity)를 위한 전문 스킬 모음집입니다. 이 저장소는 에이전트가 코딩, 리팩토링, 테스트, 디버깅 등 다양한 작업을 전문가 수준으로 수행할 수 있도록 돕는 지침(Skills)을 제공합니다.

이 프로젝트는 오픈 소스로 공개되어 누구나 자신의 에이전트에 강력한 스킬을 추가하고 확장할 수 있습니다.

## 🚀 특징 (Features)

- **전문가 수준의 가이드라인**: 각 스킬은 해당 도메인의 시니어 엔지니어 수준의 지침을 포함하고 있습니다.
- **다양한 도메인 커버**: Django, Python 테스트, 리팩토링, 코드 리뷰, 디버깅 등 핵심 개발 영역을 다룹니다.
- **확장 가능성**: 표준화된 `SKILL.md` 형식을 통해 새로운 스킬을 쉽게 추가할 수 있습니다.
- **한국어 최적화**: 한국어 사용자 및 개발 환경에 맞춰 최적화된 프롬프트와 가이드를 제공합니다.

## 📦 포함된 스킬 (Available Skills)

현재 스킬들은 기능 및 도메인에 따라 다음과 같이 분류되어 있습니다:

### 1. 🤖 메타 & 에이전트 관리 (Meta & Agent Management)

에이전트의 동작을 제어하고 스킬 자체를 관리, 검증하는 스킬들입니다.

- **`antigravity-orchestrator`**: 여러 스킬들을 효율적으로 조합하고 상황에 맞게 에이전트에게 적용하기 위한 메타 오케스트레이션 수행.
- **`create-new-skill`**: 새로운 에이전트 스킬을 빠르게 생성 가능. 반복적인 작업 자동화 및 도메인 지식 주입 시 사용.
- **`verify-skill`**: 생성된 스킬이 프로젝트 표준을 준수하는지, 구조/내용이 적절한지 검증.
- **`system-investigator`**: 코드베이스를 심도 있게 분석하고 아키텍처를 파악하여 발견한 내용을 구조화된 형식으로 문서화.

### 2. 📝 계획 및 코드 리뷰 (Planning & Code Review)

설계 작업부터 리뷰, PR을 생성하기까지 전반적인 프로세스를 돕습니다.

- **`writing-plans`**: 코드 작성 전, 다단계 작업에 대한 상세한 실행 계획(Implementation Plan)이나 요구사항 문서화에 사용.
- **`code-reviewer`**: 품질, 보안, 그리고 프로젝트별 규칙 준수 여부 등을 바탕으로 수행하는 시니어 레벨의 코드 리뷰.
- **`git-pr-workflows-git-workflow`**: 코드 리뷰 단계부터 PR 생성까지 아우르는 포괄적인 최신 Git 워크플로우(Conventional Commits 등) 적용.

### 3. 🏗️ 백엔드 및 아키텍처 (Backend & Architecture)

백엔드 서버, API 설계, 보안, 이벤트 스트리밍 시스템과 관련된 전문 스킬들입니다.

- **`api-design-principles`**: 직관적이고 확장 가능하며 유지보수가 용이한 REST 및 GraphQL API 설계 원칙.
- **`python-backend-architect`**: 아키텍처 설계, 비동기 처리, 데이터 엔지니어링 및 클라우드 연동 등 파이썬 백엔드 시스템 전문 설계 등 전반적인 가이드 제공.
- **`django-pro`**: Django 3.x+ 기반의 모범 사례, 확장 가능한 아키텍처, 최신 웹 애플리케이션 개발 전문 가이드.
- **`backend-security-coder`**: 인증, 입력 검증, API 보안을 전문으로 하는 안전한 백엔드 코딩 구현 및 취약점 방어.
- **`kafka-streaming-architect`**: Apache Kafka를 활용한 안정적인 이벤트 기반 아키텍처 설계, 구현 및 운영.

### 4. 🎨 프론트엔드 (Frontend)

최신 프론트엔드 프레임워크와 UI 아키텍처에 대한 스킬을 포괄합니다.

- **`frontend-developer`**: React 19, Next.js 15 환경에서 아키텍처 및 퍼블리싱, 성능/접근성 최적화.
- **`nextjs-app-router-expert`**: Next.js 14+ App Router, React Server Components (RSC) 시스템 적용 등 최신 클린 아키텍처의 프론트엔드 개발 시 활용.
- **`react-best-practices`**: Vercel 엔지니어링 룰을 기반으로 하는 React 및 Next.js 성능/최적화 가이드 제공.
- **`react-ui-patterns`**: 로딩, 에러 핸들링, Empty states 및 폼 제출 등에 활용되는 모범적인 최신 React UI 패턴 지원.

### 5. 🗄️ 데이터베이스 및 인프라 (Database & Infrastructure)

데이터베이스의 최적화, 모니터링 시스템, 컨테이너 환경 운용과 관련된 지침입니다.

- **`postgresql-optimization`**: SQL 쿼리 튜닝, 인덱싱 전략 분석 및 성능 향상을 위한 PostgreSQL 최적화 워크플로우.
- **`sql-optimization-patterns`**: EXPLAIN 등의 분석 기법으로 느린 쿼리를 제거하고 데이터베이스 성능 및 스키마 구조 개선.
- **`docker-expert`**: Docker 생태계 최적 운용(컨테이너화, Docker Compose 등). 인프라 최적화 구성 도모.
- **`datadog-expert`**: Python(Django/FastAPI) 프로젝트용 Datadog 세팅, 로그 및 트레이싱과 메트릭 수집 효율 증대 관리.

### 6. 🛠️ 유지보수, 리팩토링 및 테스트 (Maintenance, Refactoring & Testing)

기존 시스템 향상 및 안정성 보장을 위한 코드 정리, 마이그레이션, 자동화 테스트 등의 가이드를 포함합니다.

- **`code-refactoring-refactor-clean`**: 클린 코드(Clean Code), SOLID 원칙, 모범 사례에 기반하여 코드의 유지보수성 및 성능 리팩토링 진행.
- **`legacy-modernizer`**: 레거시 코드의 안전한 리팩토링, 구형 프레임워크 마이그레이션 등 점진적인 코어의 모던화 지원.
- **`python-testing-patterns`**: `pytest`, `mock`, TDD를 활용하여 포괄적인 테스트 전략 도입 및 작성 지원.
- **`systematic-debugging`**: 예상치 못한 동작, 실패 등 발생 시 체계적인 디버깅 절차로 근본 원인 분석 후 수정안 제안.

## 🛠️ 사용 방법 (Usage)

이 스킬들을 Antigravity 에이전트 설정에 추가하여 사용할 수 있습니다.

1. 이 저장소를 로컬 머신에 클론합니다:

   ```bash
   git clone https://github.com/your-username/antigravity-awesome-skills.git
   ```

2. 에이전트 설정(예: `.cursorrules`, 사용자 설정 등)에서 해당 스킬 디렉토리를 참조하도록 설정합니다.
   - 경로: `antigravity-awesome-skills/.agent/skills/`

3. 에이전트에게 작업을 요청할 때, 필요한 스킬을 명시하거나 에이전트가 상황에 맞춰 자동으로 스킬을 활용하도록 합니다.

## 🤝 기여하기 (Contributing)

새로운 스킬을 추가하거나 기존 스킬을 개선하고 싶다면 언제든 환영합니다!

1. 이 저장소를 포크(Fork)합니다.
2. 새로운 브랜치를 생성합니다 (`git checkout -b feature/new-skill`).
3. `.agent/skills/<new-skill-name>/SKILL.md` 형식으로 스킬을 작성합니다.
4. 변경 사항을 커밋하고 푸시합니다.
5. Pull Request를 제출합니다.

## 📄 라이선스 (License)

이 프로젝트는 MIT 라이선스 하에 배포됩니다. 자세한 내용은 `LICENSE` 파일을 참조하세요.
