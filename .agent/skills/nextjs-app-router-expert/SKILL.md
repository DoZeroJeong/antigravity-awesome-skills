---
name: nextjs-app-router-expert
description: Next.js 14+ App Router, React Server Components (RSC), Server Actions 및 클린 아키텍처 원칙을 활용한 최신의 현대적 프론트엔드 모범 사례 가이드입니다.
---

# Next.js App Router Expert

## 개요 (Overview)
이 스킬은 Next.js App Router (13+ 및 14버전 이상) 환경에서 애플리케이션을 구축하거나 리팩토링할 때 에이전트가 준수해야 할 구조적, 성능적, 보안적 원칙과 가이드를 제공합니다. 클라이언트 사이드 렌더링(CSR)과 서버 사이드 렌더링(SSR), 서버 컴포넌트(RSC)의 명확한 분리를 지향하며, 상태 관리 및 비동기 데이터를 취급하는 최적의 패턴을 정의합니다.

## 사용 시점 (When to use)
- 사용자로부터 Next.js (App Router 기반) 프로젝트의 컴포넌트나 페이지 생성을 요청받았을 때
- Server Action을 사용하여 API 라우트 없이 데이터 변이(Mutation) 로직을 작성해야 할 때
- `use client`와 서버 컴포넌트의 경계선이 모호하여 리팩토링이 필요할 때
- 메타데이터(SEO), Suspense를 활용한 스트리밍 처리, 캐싱 전략 등을 적용할 때

## 지침 (Instructions)

1. **컴포넌트 렌더링 전략 결정 (RSC vs Client Component)**
   - **기본값은 서버 컴포넌트 (Server Components)입니다.** 특별한 이유(상태, 생명주기, 브라우저 API 필요)가 없는 한 서버 컴포넌트로 작성합니다.
   - 상태 변화(`useState`, `useReducer`), 생명주기(`useEffect`), 이벤트 리스너(`onClick` 등), 브라우저 전용 API가 필요한 경우에만 파일 최상단에 `"use client";`를 선언하여 클라이언트 컴포넌트로 만듭니다.
   - 클라이언트 컴포넌트는 트리의 말단(Leaf)에 최대한 가깝게 배치하여 JS 번들 크기를 줄이세요.

2. **데이터 페칭 (Data Fetching) 및 캐싱 전략**
   - 기존의 `getServerSideProps`나 `getStaticProps`는 사용하지 마세요. 대신 App Router의 확장된 `fetch` API를 서버 컴포넌트 내부에서 비동기(`async/await`)로 사용하세요.
   - 캐싱과 재검증이 필요한 경우 `fetch` 옵션(`next: { revalidate: 60 }`, `cache: 'force-cache'` 등)을 명시적으로 사용하세요.
   - 병렬 페칭이 가능한 경우 `Promise.all`을 사용하여 워터폴(Waterfall) 현상을 피하세요.

3. **서버 액션 (Server Actions) 활용**
   - 데이터 변이(생성, 수정, 삭제)는 `/api` 등의 엔드포인트를 따로 만들지 말고 Server Actions를 활용하세요.
   - 서버 전용 파일(`actions.ts` 등)에 `"use server"` 지시어를 넣고 비동기 함수로 작성하여 사용합니다.
   - 클라이언트에서 Server Action을 호출할 때는 폼의 경우 `action` prop이나, `useTransition` / `useFormStatus`와 결합하여 진행 상태(loading) 처리를 매끄럽게 하세요.

4. **로딩 UI와 에러 핸들링 (Streaming & Error Boundaries)**
   - 긴 서버 페칭을 보여주어야 하는 경우, 라우트 파일 경로에 `loading.tsx`를 배치하거나 특정 부분만 `<Suspense fallback={<Loading />}>`로 감싸 스트리밍 렌더링을 적용합니다.
   - 오류 처리를 위해 `error.tsx` (클라이언트 컴포넌트 필수)와 예기치 못한 전체 에러를 잡는 `global-error.tsx`를 적재적소에 배치하세요.

5. **클린 아키텍처 및 폴더 구조 (Colocation & Private Folders)**
   - App Router는 라우팅과 무관한 파일의 동일 폴더 내 콜로케이션(Colocation)을 지원합니다 (예: `app/dashboard/page.tsx`와 `app/dashboard/button.tsx`).
   - `_components`, `_lib` 처럼 파파라미터(`_`)를 붙이면 해당 폴더를 프라이빗 폴더로 만들어 라우팅에서 제외시킬 수 있습니다.
   - 비즈니스 로직과 UI 컴포넌트, 서버 액션을 분리하여 결합도를 낮추세요.

6. **SEO와 메타데이터 (Metadata)**
   - 정적 메타데이터는 레이아웃이나 페이지 파일 내에서 `export const metadata: Metadata = { ... }` 형태로 정의합니다.
   - 동적 데이터에 의존하는 메타데이터는 `export async function generateMetadata({ params }): Promise<Metadata>`를 활용하세요.

## 모범 사례 (Best Practices)

- **보안 (Security):** 서버 액션이나 데이터 페칭 시 사용자의 권한/인증(Authentication/Authorization) 상태를 반드시 검증해야 합니다. 외부 입력값은 zod와 같은 스키마 검증 라이브러리로 방어하세요.
- **성능 (Performance):** 서버 컴포넌트에서 클라이언트 컴포넌트를 직접 `import` 할 수 있지만, 서버 컴포넌트를 클라이언트 컴포넌트 안으로 `import`하면 그 서버 컴포넌트 역시 클라이언트 컴포넌트로 취급됩니다. 만약 둘을 혼합해야 한다면, 부모인 클라이언트 컴포넌트에서 `children` prop으로 서버 컴포넌트를 전달받도록 설계하세요.
- **SEO & a11y:** 시맨틱 HTML 태그를 사용하며, 이미지 최적화를 위해 Next.js의 내장 `<Image />` 컴포넌트와 올바른 `alt` 속성을 반드시 사용하세요.
- **PEP8 및 파이썬 연관 규칙 주의사항:** 사용자 규칙으로 PEP8 준수 등이 강제되어 있는 환경이더라도, Next.js와 Node.js 기반의 프론트엔드/BFF 코드를 다룰 때는 Prettier나 ESLint와 같은 JS/TS 생태계의 엄격한 클린 코드 및 린트 룰을 최우선으로 적용해야 합니다.
