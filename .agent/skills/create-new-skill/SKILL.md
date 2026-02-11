---
name: create-new-skill
description: 새로운 에이전트 스킬을 생성하는 스킬입니다. 반복적인 작업을 자동화하거나 특정 도메인 지식을 에이전트에게 주입하고 싶을 때 사용하세요.
---

# Create New Skill

## 개요 (Overview)

이 스킬은 `.agent/skills/` 디렉토리 아래에 새로운 스킬을 생성하는 과정을 가이드합니다. 스킬은 에이전트가 특정 작업을 수행하는 데 필요한 지식, 절차, 템플릿을 포함하는 패키지입니다.

## 사용 시점 (When to use)

- 프로젝트에 특화된 반복 작업 절차를 정의하고 싶을 때
- 특정 라이브러리나 프레임워크 사용에 대한 모범 사례를 에이전트에게 교육하고 싶을 때
- 복잡한 디버깅이나 리팩토링 절차를 표준화하고 싶을 때

## 절차 (Steps)

1. **스킬 이름 결정:**
   - 영문 소문자와 하이픈(`-`)을 사용한 kebab-case로 이름을 정합니다 (예: `python-backend-architect`, `debug-django-orm`).
   - 이름은 스킬의 목적을 명확하게 드러내야 합니다.

2. **디렉토리 생성:**
   - `.agent/skills/<skill-name>/` 디렉토리를 생성합니다.

3. **SKILL.md 파일 생성:**
   - 생성한 디렉토리 안에 `SKILL.md` 파일을 생성합니다.
   - 아래 템플릿을 복사하여 내용을 채워 넣습니다.

## SKILL.md 템플릿 (Template)

```markdown
---
name: <skill-name>
description: <스킬에 대한 한 줄 요약 설명>
---

# <Skill Title>

## 개요 (Overview)
이 스킬이 무엇을 하는지, 어떤 문제를 해결하는지 상세히 설명합니다.

## 사용 시점 (When to use)
- 이 스킬을 언제 사용해야 하는지 구체적인 상황을 나열합니다.
- 예: "새로운 API 엔드포인트를 추가할 때", "데이터베이스 마이그레이션이 실패했을 때"

## 지침 (Instructions)
에이전트가 따라야 할 단계별 지침을 적습니다.

1. **단계 1:**
   - 구체적인 행동 지침

2. **단계 2:**
   - 코드 예시나 명령어 포함 가능

## 모범 사례 (Best Practices)
- 이 작업과 관련된 팁이나 주의사항을 적습니다.
```

## 예시 (Example)

만약 `fastapi-endpoint-creator`라는 스킬을 만든다면:

1. 폴더 생성: `.agent/skills/fastapi-endpoint-creator/`
2. 파일 생성: `.agent/skills/fastapi-endpoint-creator/SKILL.md`
3. 내용 작성:

```markdown
---
name: fastapi-endpoint-creator
description: FastAPI 프로젝트에서 새로운 API 엔드포인트를 생성하는 표준 절차를 가이드합니다.
---

# FastAPI Endpoint Creator

## 개요
Clean Architecture를 준수하며 FastAPI 엔드포인트를 생성합니다.

... (중략) ...
```
