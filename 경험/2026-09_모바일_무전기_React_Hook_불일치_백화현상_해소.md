# 🔬 [경험] 모바일 무전기 React Hook 불일치 백화현상(WSOD) 해소 및 전사 ErrorBoundary 복원 아키텍처 정립

- **작성일자**: 2026-09-05 12:05
- **프로젝트**: 기연리프트 차세대 웹 ERP (Kiyuen_Lift)
- **관련 파일**: `src/mobile/components/MobileWalkieTalkieModal.tsx`, `src/components/ErrorBoundary.tsx`, `src/main.tsx`, `src/mobile/MobileApp.tsx`
- **관련 경험 ID**: E-055

---

## 1. 배경 및 현상 (Context & Symptom)
- 스마트폰 모바일 화면에서 상단 헤더의 [무전] 버튼을 터치하여 무전기를 여는 순간, 전체 화면이 순백색으로 변하고 아무것도 표시되지 않는 백화 현상(White Screen of Death / WSOD)이 발생함.
- 데스크톱/모바일 전체 화면이 언마운트되어 F5 새로고침 외에는 일체의 조작이 불가능한 상태에 빠짐.

## 2. 기술적 진단 및 근본 원인 (Root Cause Analysis)
1. **React Rules of Hooks (훅의 규칙) 정면 위반**:
   - `MobileWalkieTalkieModal.tsx` 컴포넌트 내부 246행에 `if (!isOpen) return null;`이라는 조건부 조기 종료문(Early Return)이 존재했음.
   - 모바일 초기 로드 시 `isWalkieModalOpen`은 `false`이므로, 246행 이전까지의 38개 Hook(`useState`, `useRef`, `useEffect`)만 등록되고 조기 리턴됨.
   - 이후 사용자가 [무전] 버튼을 클릭하여 `isOpen`이 `true`로 전환되면 246행을 통과하여 405행에 배치된 채널 자동 전환 `useEffect`가 비로소 실행됨.
   - React는 직전 렌더링 시 38개였던 훅 목록과 이번 렌더링의 39번째 훅(`useEffect`) 간의 불일치를 감지하여 치명적인 React 내부 인바리언트 에러(`Error: Rendered more hooks than during the previous render`, React Error #310)를 발생시킴.
2. **전사 Root ErrorBoundary 부재**:
   - React 트리에 에러 바운더리가 없어, 보조 모달 컴포넌트 1개의 렌더링 예외가 최상위 루트 `<div id="root">` 전체를 언마운트시켜 백화 현상을 초래함.
3. **모바일 브라우저 예외 방어 미비**:
   - `localStorage.getItem('walkie_show_debug')` 호출 시 Safari 프라이빗 모드나 웹뷰 환경에서 예외를 던질 수 있는 방어막(try-catch) 부재.

## 3. 체득한 원칙 및 최종 해결책 (Core Principle & Resolution)
1. **모든 Hook은 조건부 return 이전 컴포넌트 최상단에 100% 무조건 선언**:
   - `MobileWalkieTalkieModal.tsx`에서 246행의 조기 리턴을 제거하고, 405행의 `useEffect` 내부에서 `if (!isOpen) return;`으로 실행을 제어함.
   - 조기 종료 `if (!isOpen) return null;`은 모든 Hook 선언이 완료된 후 JSX `return (` 바로 직전으로 이동함.
   - 이를 통해 `isOpen`이 `false`이든 `true`이든 무조건 동일한 39개의 Hook이 동일한 순서로 호출되도록 보장함.
2. **전사 Root 및 모달 전용 `ErrorBoundary` 구축 및 탑재**:
   - `src/components/ErrorBoundary.tsx`를 신규 구축하여 `main.tsx`의 루트 `<App />` 및 `MobileApp.tsx`의 `MobileWalkieTalkieModal`, `MobileGemsAgentModal`을 에러 바운더리로 격리함.
   - 모달이나 자식 컴포넌트에서 예외가 발생하더라도 메인 화면이 꺼지지 않고, "화면 일시 오류 복구" 카드와 `[화면 새로고침]`, `[무전기 캐시 초기화]` 원클릭 복구 버튼을 제공함.
3. **안전한 시간 포맷터 및 로컬스토리지 방어막**:
   - `formatSafeTime` 헬퍼 함수를 적용하여 `Date.toLocaleTimeString` 예외를 원천 방어하고, `localStorage` 접근에 try-catch 래퍼를 적용함.

---
