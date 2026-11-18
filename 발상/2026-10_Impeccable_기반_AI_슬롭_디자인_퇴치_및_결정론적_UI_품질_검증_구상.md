# 2026-10 Impeccable 기반 AI 슬롭(AI Slop) 디자인 퇴치 및 결정론적 UI 품질 검증 구상

- **일시**: 2026-10-07 18:00
- **프로젝트**: 전사 ERP (`Giyuen_Lift`), `DragonRPA`, 스켈레톤 UI 아키텍처 전역
- **카테고리**: 발상 (IDEA)
- **발안자**: 사장님 & AI 어시스턴트
- **연결 문서**:
  - [전사 시스템 개발 표준 헌장 (카테고리 III: UI/UX 표준)](C:/Users/이정용/.gemini/config/AGENTS.md#L39)
  - [전사 표준 헌장 3.1: 무수식어 건조 표준](C:/Users/이정용/.gemini/config/AGENTS.md#L41)
  - [전사 표준 헌장 3.2: 셀/레이블 줄바꿈 방지(No-Wrap)](C:/Users/이정용/.gemini/config/AGENTS.md#L46)
  - [전사 표준 헌장 3.6: 2대 UI 아키타입(카드형 vs 고밀도 그리드)](C:/Users/이정용/.gemini/config/AGENTS.md#L62)

---

## 1. 발상 배경 및 문제 의식 (Inception Context)

1. **AI가 만들어내는 '디자인 슬롭(AI Slop)'의 범람**:
   - AI 에이전트(LLM)에게 프론트엔드 UI 작성을 맡기면, 실무 현장의 맥락과 무관하게 **흔해 빠진 Inter 폰트, 보라색-파란색 그라디언트, 무의미한 카드 중첩(Cards nested in cards), 둥근 모서리 사각 아이콘 타일** 등 전형적인 AI 슬롭 패턴을 반복적으로 찍어냄.
   - 이는 당사 전사 시스템 개발 표준 헌장의 제3.1조(무수식어 건조 표준) 및 제3.6조(고밀도 업무 그리드 원칙)를 정면으로 위배함.

2. **Impeccable(Paul Bakaus)의 솔루션 발견**:
   - AI 에이전트 전용 디자인 언어이자 스킬 시스템인 `Impeccable`(1스킬, 24개 명령, 60개 결정론적 디텍터 룰)의 정밀 분석.
   - LLM/API 키 없이 로컬에서 즉시 구동되는 60개 정적 디텍터(`npx impeccable detect`)를 통해 AI 슬롭을 기계적으로 적발하고 퇴치하는 프레임워크 확보.

---

## 2. 격상된 본질 목표 (Elevated Core Teleology)

> **"AI가 기계적으로 양산하는 겉멋 든 디자인 슬롭(그라디언트, 무의미한 카드 중첩, 흔한 폰트)을 원천 박멸하고,**  
> **[PRODUCT.md / DESIGN.md ➔ 24대 단축 명령 ➔ 60개 정적 디텍터 룰] 파이프라인을 구축하여**  
> **'최소 조작으로 최대 편익을 달성하는 건조하고 단단한 엔터프라이즈 UI'를 전사 시스템에 확립한다."**

---

## 3. 핵심 4대 실무 결합 아키텍처

1. **디자인 SSOT 확립 (`PRODUCT.md` & `DESIGN.md`)**:
   - 프로젝트 루트에 제품의 본질(오디언스, 목적, 제약)과 디자인 토큰(상하 세로 스택, No-wrap, 블루/네이비 톤)을 불변의 마크다운으로 정의하여 세션 간 디자인 편차 원천 차단.
2. **배포 전 `/impeccable harden` 필수 가드**:
   - 텍스트 오버플로우, 비정상 긴 데이터 입력 시 레이아웃 깨짐, 모바일 뷰포트 마찰을 배포 전 1초 만에 자동 검증.
3. **WTT/RWTT 파이프라인에 `npx impeccable detect` 결합**:
   - 정적 린터 게이트로 통합하여, AI 슬롭이나 접근성 결함 발견 시 빌드를 자동 차단.
4. **Google Antigravity 스킬 정식 마운트**:
   - Antigravity 전역 스킬(`~/.gemini/config/skills`)에 Impeccable을 등록하여 에이전트와의 실시간 협업 체계 가동.

---

*기록일: 2026-10-07*
