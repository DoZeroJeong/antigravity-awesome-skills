---
name: writing-plans
description: 코드를 만지기 전, 다단계 작업에 대한 명세나 요구사항이 있을 때 사용합니다.
---

# Writing Plans

## 개요 (Overview)

엔지니어가 우리 코드베이스에 대한 맥락이 전혀 없고 취향이 모호하다고 가정하고 포괄적인 구현 계획을 작성하세요. 각 작업에 대해 수정해야 할 파일, 코드, 테스트, 확인해야 할 문서, 테스트 방법 등 엔지니어가 알아야 할 모든 것을 문서화하세요. 전체 계획을 한 입 크기(Bite-sized)의 작업으로 제공하세요. DRY, YAGNI, TDD, 빈번한 커밋을 준수하세요.

그들이 숙련된 개발자라고 가정하되, 우리의 도구 세트나 문제 도메인에 대해서는 거의 모른다고 가정하세요. 또한 좋은 테스트 설계를 잘 모른다고 가정하세요.

**시작 시 알림:** "구현 계획을 생성하기 위해 writing-plans 스킬을 사용하고 있습니다."

**컨텍스트:** 이 스킬은 전용 워크트리(브레인스토밍 스킬로 생성됨)에서 실행되어야 합니다.

**계획 저장 위치:** `docs/plans/YYYY-MM-DD-<feature-name>.md`

## 한 입 크기 작업 세분화 (Bite-Sized Task Granularity)

**각 단계는 하나의 액션(2-5분)입니다:**

- "실패하는 테스트 작성" - 단계
- "실패하는지 확인하기 위해 실행" - 단계
- "테스트를 통과시키기 위한 최소한의 코드 구현" - 단계
- "테스트를 실행하고 통과하는지 확인" - 단계
- "커밋" - 단계

## 계획 문서 헤더 (Plan Document Header)

**모든 계획은 반드시 다음 헤더로 시작해야 합니다:**

```markdown
# [기능명] 구현 계획

> **Claude용:** 필수 서브 스킬: 이 계획을 작업별로 구현하려면 superpowers:executing-plans를 사용하세요.

**목표:** [이것이 무엇을 만드는지 설명하는 한 문장]

**아키텍처:** [접근 방식에 대한 2-3 문장]

**기술 스택:** [주요 기술/라이브러리]

---
```

## 작업 구조 (Task Structure)

```markdown
### 작업 N: [컴포넌트 이름]

**파일:**
- 생성: `exact/path/to/file.py`
- 수정: `exact/path/to/existing.py:123-145`
- 테스트: `tests/exact/path/to/test.py`

**단계 1: 실패하는 테스트 작성**

```python
def test_specific_behavior():
    result = function(input)
    assert result == expected
```

**단계 2: 테스트가 실패하는지 확인**

실행: `pytest tests/path/test.py::test_name -v`
예상: "function not defined"와 함께 실패(FAIL)

**단계 3: 최소한의 구현 작성**

```python
def function(input):
    return expected
```

**단계 4: 테스트가 통과하는지 확인**

실행: `pytest tests/path/test.py::test_name -v`
예상: 통과(PASS)

**단계 5: 커밋**

```bash
git add tests/path/test.py src/path/file.py
git commit -m "feat: add specific feature"
```

```

## 기억할 점 (Remember)
- 항상 정확한 파일 경로 사용
- 계획에 완전한 코드 포함 ("유효성 검사 추가"와 같이 모호하게 작성 금지)
- 예상 출력과 함께 정확한 명령어 포함
- @ 구문으로 관련 스킬 참조
- DRY, YAGNI, TDD, 빈번한 커밋

## 실행 이관 (Execution Handoff)

계획을 저장한 후, 실행 옵션을 제안하세요:

**"계획이 완료되어 `docs/plans/<filename>.md`에 저장되었습니다. 두 가지 실행 옵션이 있습니다:**

**1. 서브에이전트 주도 (현재 세션)** - 작업당 새로운 서브에이전트 파견, 작업 간 검토, 빠른 반복

**2. 병렬 세션 (별도)** - executing-plans로 새 세션 열기, 체크포인트가 있는 일괄 실행

**어느 방식을 선택하시겠습니까?"**

**서브에이전트 주도 선택 시:**
- **필수 서브 스킬:** superpowers:subagent-driven-development 사용
- 현재 세션 유지
- 작업당 새로운 서브에이전트 + 코드 리뷰

**병렬 세션 선택 시:**
- 워크트리에서 새 세션을 열도록 안내
- **필수 서브 스킬:** 새 세션은 superpowers:executing-plans 사용
