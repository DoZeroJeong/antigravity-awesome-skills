---
name: react-best-practices
description: Vercel 엔지니어링 룰 기반의 React 및 Next.js 성능 최적화 가이드입니다. React/Next.js 코드를 작성, 리뷰, 리팩토링할 때 사용하여 최적의 성능 패턴을 보장합니다.
---

# React Best Practices

## 개요 (Overview)
Vercel에서 권장하는 React 및 Next.js 애플리케이션에 대한 종합적인 성능 최적화 가이드입니다. 워터폴 방지, 번들 크기 최적화, 서버/클라이언트 사이드 렌더링 성능 최적화 및 불필요한 재렌더링 방지 등의 지침을 포함합니다.

## 사용 시점 (When to use)
- 새로운 React 컴포넌트나 Next.js 페이지를 작성할 때
- 클라이언트 또는 서버 사이드 데이터 페칭을 구현할 때
- 성능 문제로 인해 코드를 검토하거나 리팩토링할 때
- 초기 로딩 시간(LCP)이나 번들 크기를 최적화할 때

## 지침 (Instructions)

### 1. 워터폴 제거 (Eliminating Waterfalls - CRITICAL)
- **`async-defer-await`**: `await`는 실제로 값이 사용되는 분기(branch) 내부로 미루어 호출하세요.
- **`async-parallel`**: 서로 독립적인 비동기 작업은 `Promise.all()`을 사용하여 병렬 처리하세요.
- **`async-suspense-boundaries`**: `<Suspense>`를 사용하여 스트리밍을 활성화하고 컨텐츠를 비동기적으로 보여주세요.

### 2. 번들 크기 최적화 (Bundle Size Optimization - CRITICAL)
- **`bundle-barrel-imports`**: Barrel 파일(단순 export만 하는 index.ts/js) 사용을 피하고 컴포넌트를 직접 import 하세요.
- **`bundle-dynamic-imports`**: 무거운 컴포넌트나 클라이언트 전용 라이브러리는 `next/dynamic` 또는 `React.lazy`를 사용하여 필요할 때 로딩하세요.
- **`bundle-defer-third-party`**: 분석, 로깅 등 Third-party 스크립트는 하이드레이션(Hydration) 이후로 지연 로딩하세요.

### 3. 서버 사이드 성능 (Server-Side Performance - HIGH)
- **`server-cache-react`**: 요청당 중복된 데이터를 방지하기 위해 `React.cache()`를 사용하세요.
- **`server-serialization`**: 클라이언트 컴포넌트로 전달하는 프로퍼티(props)의 크기를 최소화하여 불필요한 직렬화(Serialization) 오버헤드를 줄이세요.
- **`server-after-nonblocking`**: 사용자에게 응답을 보낸 후 뒷단에서 로깅이나 추적 작업을 할 때는 Next.js 캐싱을 방해하지 않는 비차단(non-blocking) 메서드(`after()`)를 사용하세요.

### 4. 클라이언트 사이드 데이터 페칭 (Client-Side Data Fetching - MEDIUM-HIGH)
- **`client-swr-dedup`**: 데이터 요청에 SWR(또는 React Query)을 사용하여 자동 요청 중복 제거(deduplication) 및 캐싱의 이점을 활용하세요.

### 5. 재렌더링 최적화 (Re-render Optimization - MEDIUM)
- **`rerender-memo`**: 렌더링 비용이 큰 작업은 `React.memo` 또는 `useMemo`를 이용하여 추출하세요.
- **`rerender-dependencies`**: `useEffect`나 `useCallback` 옵션 배열에는 항상 원시 타입(Primitive types) 등 불변의 값을 넣어 최적화하세요.
- **`rerender-functional-setstate`**: 상태 콜백의 의존성을 줄이고 싶다면 함수형 `setState(prev => prev + 1)`를 활용하세요.
- **`rerender-transitions`**: 긴급하지 않은 상태 업데이트는 `useTransition(startTransition)`을 사용하여 UI 프리징을 방지하세요.

### 6. 자바스크립트 성능 (JavaScript Performance - LOW-MEDIUM)
- **`js-set-map-lookups`**: 반복문 내에서 단순 배열의 검색(`includes`, `find`) 대신 `[Set, Map]` 객체를 사용해 O(1) 조회 속도로 최적화하세요.
- **`js-early-exit`**: 불필요한 함수형 스코프 진입을 막기 위해 함수 실행 시작 시 Early Return 패턴을 적극 활용하세요.

## 모범 사례 (Best Practices)
코드 최적화는 '가독성'과 '유지보수성'을 해치지 않는 선에서 진행하는 것이 중요합니다. 모든 항목을 무리하게 적용하기보다, 가장 병목이 되기 쉬운 "Data Fetching의 병렬 처리"와 "무거운 종속성 동적 로딩"을 우선 순위로 점검하세요. (예: `React.memo` 남용으로 가독성이 저하되는 것은 피해야 합니다.)
