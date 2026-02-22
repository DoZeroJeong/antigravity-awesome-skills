---
name: react-ui-patterns
description: 로딩 상태, 에러 처리, 빈 화면(Empty states), 폼 제출 등에 대한 Modern React UI 패턴 가이드입니다. UI 컴포넌트를 구축하거나 비동기 데이터를 처리할 때 반드시 참고하세요.
---

# React UI Patterns

## 개요 (Overview)
사용자 경험(UX)을 향상시키기 위해 일관된 컴포넌트 상태 관리를 제시하는 핵심 React UI 패턴 지침서입니다. 결함이 있거나 지연이 발생하는 상황에서도 사용자가 불안해하지 않도록, 우아한 실패(Graceful degradation)와 점진적 공개(Progressive disclosure)의 원칙을 따릅니다.

## 사용 시점 (When to use)
- 새로운 UI 컴포넌트를 설계하고 개발할 때
- 비동기 데이터(API 통신 등)를 처리하거나 로딩/에러/빈 상태를 관리해야 할 때
- 사용자 입력(Form 전송)이나 버튼 액션 등을 다룰 때

## 핵심 원칙 (Core Principles)
1. **Never show stale UI:** 데이터 로딩이 실제로 발생할 때만 스피너(Loading spinner)를 표시합니다.
2. **Always surface errors:** 무언가 실패했을 때는 사용자가 항상 인지할 수 있도록 에러를 반드시 노출하세요.
3. **Optimistic updates:** UI가 즉각적으로 반응하는 것처럼 느껴지게 낙관적 업데이트를 적용하세요.
4. **Progressive disclosure:** 콘텐츠가 준비되는 대로 점진적으로 사용자에게 보여주세요.
5. **Graceful degradation:** 완벽한 데이터가 아니더라도 빈 화면보다는 부분적인 데이터라도 보여주는 것이 중요합니다.

## 지침 (Instructions)

### 1. 로딩 상태 패턴 (Loading State Patterns)
**절대 규칙:** 화면에 표시할 데이터가 전혀 없을 때만 로딩 인디케이터를 보여주세요. 이미 캐시된 데이터가 있다면 스피너를 보여주어 화면을 깜빡이게(Flicker) 만들지 마세요.

**선택 가이드:**
- **Skeleton 사용:** 보여줄 콘텐츠의 구조(형태)를 알 수 있을 때 (리스트, 카드 레이아웃, 최초 페이지 로드 등)
- **Spinner 사용:** 콘텐츠의 형태를 예측하기 어려울 때 (모달 액션, 버튼 제출, 인라인 요소 조작 등)

### 2. 에러 핸들링 패턴 (Error Handling Patterns)
오류는 침묵해서는 안 됩니다. 심각도에 맞춰 계층을 나누어 적절히 노출하세요.
- **인라인 에러 (Inline error):** 폼 검증과 같은 필드 레벨 오류 (예: 이메일 형식 오류)
- **토스트 알림 (Toast notification):** 사용자가 다시 시도할 수 있는 복구 가능한 네트워크/호출 오류
- **에러 배너 (Error banner):** 페이지 전체엔 영향을 주지 않지만, 데이터 일부만 사용할 수 없는 상태일 때
- **전체 에러 화면 (Full error screen):** 사용자 액션이 반드시 필요하거나 치명적인 실패일 때

*(팁: `onError` 핸들러에서 에러를 콘솔에만 찍지 말고, 반드시 `toast.error()` 등으로 사용자 알림을 추가하세요.)*

### 3. 버튼 상태 패턴 (Button State Patterns)
비동기 작업(API 전송 등)이 진행 중일 때는 여러 번 전송되는 것을 막기 위해 **반드시 트리거(버튼 등)를 비활성화(`disabled=true`)**해야 합니다.
```tsx
// 모범 사례
<Button disabled={isSubmitting} isLoading={isSubmitting} onClick={handleSubmit}>
  Submit
</Button>
```

### 4. 빈 상태 (Empty States)
목록(List)이나 컬렉션을 렌더링할 때는 요소가 없을 경우에 대해 반드시 텅 빈 목록을 대체할 Empty State를 제공해야 합니다. (예: "검색 결과가 없습니다", "항목을 처음으로 생성해보세요"). 단순히 빈 화면을 렌더링하면 버그로 인식될 수 있습니다.

### 5. 폼 제출 패턴 (Form Submission Pattern)
폼 관련 처리를 할 때는 아래 사항을 준수하세요.
- 유효성(Validity) 검증을 통과하지 못하면 폼을 전송하지 않고 토스트 알림을 띄우거나 명확한 인라인 에러를 표시합니다.
- 제출 버튼은 폼이 '유효하지 않거나(invalid)' '전송 중(loading)'일 때 반드시 비활성화(`disabled`) 시킵니다.

## 모범 사례 (Best Practices)
- **일관된 UX 컴포넌트화:** `<ErrorState />`, `<LoadingState />`, `<EmptyState />`와 같은 상태 전용 공통 컴포넌트를 만들어 프로젝트 전반에 일관되게 사용하세요.
- **예측 가능성:** 상태가 변함에 따라 레이아웃이 출렁이는 현상(Layout Shift)을 방지하기 위해 로딩/에러 컴포넌트의 높이/단위를 고정하거나 비슷하게 맞추는 것이 좋습니다.
