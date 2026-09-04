# 📋 [계획] Google Gemini GEMS 기반 렌탈 4대 업무서식 음성 AI 비서 구축 계획

- **일시**: 2026-09-04 15:26
- **수행자**: 이수용 사장님 & AI 어시스턴트
- **연결 문서**:
  - 💡 [발상](../발상/2026-09_Gemini_Gems_기반_업무서식_자동완성_및_ERP_연동_에이전트.md)

---

## 1. 목표 및 범위 (Goal & Scope)
- **목표**: 영업사원이 운전 중이거나 현장에서 스마트폰 화면을 타이핑하지 않고, **자연어 음성 대화만으로 4대 핵심 렌탈 업무(출고·회수·교환·현장AS)를 접수하고, AI가 누락 정보를 교차 검증하여 ERP 배차·계약 대장에 직결하는 '기연 렌탈 전용 인앱 GEMS 에이전트'** 구축.
- **핵심 원칙**:
  - 헌장 1.1 최우선 개발 사명 (최소 노력으로 최대 편익 달성).
  - 헌장 2.1 영업 부서와 출고/자산 부서 간 R&R 엄격 준수 (영업은 의뢰만, 자산 초이스는 출고부서).
  - 헌장 2.2 대차/교체 시 계약 속성 100% 자동 상속.
  - 헌장 2.3 대차/교체 배차 의뢰 단일 'EXCHANGE' 1건 발행.

---

## 2. 세부 개발 단계 (Implementation Phases)

### Phase 1: Gemini Function Calling 서비스 모듈 (`geminiGemsService.ts`)
- Gemini 1.5 Flash API 통신 레이어.
- 4대 도구 정의 (`submitDispatchOrder`, `submitReturnOrder`, `submitExchangeOrder`, `submitFieldAsIntake`).
- 기연리프트 마스터 데이터(장비 규격표, 건설 은어 사전) 시스템 프롬프트 탑재.
- 대화 히스토리 및 누락 필드 자동 질의 로직.

### Phase 2: 모바일 대화형 UI 컴포넌트 (`MobileGemsAgentModal.tsx`)
- 모바일 무전기 스타일의 슬림 상태바 + 대화 피드.
- 실시간 STT 음성 인식 타이핑 버블.
- 완성된 서식 미리보기 카드 및 원터치 전송 버튼.
- 헤더 및 출고요청 페이지 진입 숏컷.

### Phase 3: ERP DB 파이프라인 연동 (`AppContext.tsx`)
- AI Tool Call 수신 시 Supabase DB에 `deliveries` / `repairs` 자동 생성.
- 단일 'EXCHANGE' 1건 배차 처리.
- 배차 대장 및 출고 담당자 실시간 알림 연동.

---

## 3. 검증 계획 (Verification)
- 출고/회수/교환/AS 4대 시나리오 음성 발화 테스트.
- 누락 필드 발생 시 AI 역질의 멀티턴 대화 검증.
- `cmd /c "npm run build"` 정적 컴파일 무결성 검증.
