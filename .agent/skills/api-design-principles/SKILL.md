---
name: api-design-principles
description: "직관적이고 확장 가능하며 유지보수가 용이한 REST 및 GraphQL API 설계 원칙. 새로운 API 설계, API 사양 검토 또는 팀 내 설계 표준 확립이 필요할 때 사용하세요."
risk: unknown
source: community
date_added: "2026-02-27"
---

# API 설계 원칙

개발자가 사용하기 편리하고 오랜 기간 유지보수할 수 있는 직관적이고 확장 가능한 REST 및 GraphQL API 설계 원칙을 마스터하세요.

## 이 스킬을 사용할 때

- 새로운 REST 또는 GraphQL API를 설계할 때
- 더 나은 사용성을 위해 기존 API를 리팩토링할 때
- 팀을 위한 API 설계 표준을 확립할 때
- 구현 전 API 사양을 검토할 때
- API 패러다임 간 마이그레이션을 진행할 때 (REST에서 GraphQL 등)
- 개발자 친화적인 API 문서를 작성할 때
- 특정 사용 사례를 위해 API를 최적화할 때 (모바일, 서드파티 통합 등)

## 이 스킬의 사용을 지양할 때

- 특정 프레임워크에 대한 단순 구현 가이드만 필요할 때
- API 계약 없이 인프라 관련 작업만 수행할 때
- 공개된 인터페이스를 변경하거나 버전을 지정할 수 없을 때

## 지시사항

1. API 소비자, 사용 사례 및 제약 조건을 정의하세요.
2. API 스타일을 선택하고 리소스 또는 타입을 모델링하세요.
3. 에러 처리, 버저닝, 페이지네이션 및 인증 전략을 구체화하세요.
4. API 명세 자동화(OpenAPI, drf-spectacular 등)를 통해 프론트엔드 등 소비자와의 협업 인터페이스(문서)를 구축하세요.
5. 예제를 통해 검증하고 일관성을 검토하세요.

상세한 패턴, 체크리스트, 템플릿은 `resources/implementation-playbook.md`를 참고하세요.

## 리소스

- `resources/implementation-playbook.md` (상세 패턴, 체크리스트, 템플릿 제공)
