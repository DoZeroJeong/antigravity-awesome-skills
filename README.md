# Antigravity Awesome Skills

**Antigravity Awesome Skills**는 AI 에이전트(Antigravity)를 위한 전문 스킬 모음집입니다. 이 저장소는 에이전트가 코딩, 리팩토링, 테스트, 디버깅 등 다양한 작업을 전문가 수준으로 수행할 수 있도록 돕는 지침(Skills)을 제공합니다.

이 프로젝트는 오픈 소스로 공개되어 누구나 자신의 에이전트에 강력한 스킬을 추가하고 확장할 수 있습니다.

## 🚀 특징 (Features)

- **전문가 수준의 가이드라인**: 각 스킬은 해당 도메인의 시니어 엔지니어 수준의 지침을 포함하고 있습니다.
- **다양한 도메인 커버**: Django, Python 테스트, 리팩토링, 코드 리뷰, 디버깅 등 핵심 개발 영역을 다룹니다.
- **확장 가능성**: 표준화된 `SKILL.md` 형식을 통해 새로운 스킬을 쉽게 추가할 수 있습니다.
- **한국어 최적화**: 한국어 사용자 및 개발 환경에 맞춰 최적화된 프롬프트와 가이드를 제공합니다.

## 📦 포함된 스킬 (Available Skills)

현재 포함되어 있는 스킬들은 다음과 같습니다:

| 스킬 이름 (Skill Name) | 설명 (Description) |
| :--- | :--- |
| **`code-refactoring-refactor-clean`** | 클린 코드(Clean Code) 및 SOLID 원칙에 입각하여 코드를 분석하고 리팩토링합니다. 유지보수성과 가독성을 높이는 데 중점을 둡니다. |
| **`code-reviewer`** | 시니어 레벨의 코드 리뷰를 수행합니다. 보안 취약점, 성능 문제, 코드 품질을 사전에 점검하고 개선안을 제시합니다. |
| **`django-pro`** | Django 3.x 기반의 웹 애플리케이션 개발, 아키텍처 설계, ORM 최적화 등을 지원하는 전문가 스킬입니다. |
| **`python-testing-patterns`** | `pytest`, `mock`, TDD 등 Python 테스트 자동화 및 테스트 전략 수립을 돕습니다. 견고한 테스트 슈트 작성을 지원합니다. |
| **`systematic-debugging`** | 체계적인 디버깅 절차(원인 분석 -> 가설 수립 -> 검증)를 통해 버그의 근본 원인을 찾아 해결합니다. |
| **`writing-plans`** | 복잡한 구현 작업 전에 상세한 실행 계획(Implementation Plan)을 작성하여 작업의 정확도와 효율성을 높입니다. |

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
