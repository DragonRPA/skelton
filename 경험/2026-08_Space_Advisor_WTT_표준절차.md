# [경험] Space Advisor 고유 WTT(통합 워크스루 테스트) 표준 절차 확립

> **기록일시**: 2026-08-23 16:28 (KST)  
> **프로젝트**: Space Advisor (Space_consult_assist)  
> **참여 SA**: PM SA, 감사 SA  
> **연결 링크**: `docs/wtt_standard_protocol.md`, `wtt_plan.md`, `sa_final_audit_verdict_to_ceo.md`

---

## 1. 맥락 및 배경 (Context)

Space Advisor 프로젝트의 전 구간(Phase 0~5: 백엔드 API, 2,732건 룰 매핑, 모바일 PWA 현장 정비, 부품 재고 차감, 알림톡, 감사 로그) 개발이 완료된 후, 기획서와 헌장(I~X) 기준의 완결성을 실측 검증하기 위해 PM SA와 감사 SA 간의 엄격한 WTT(통합 워크스루 테스트) 체계를 수립하고 집행하였다.

---

## 2. 핵심 경험 및 발견 (Key Learnings & Discoveries)

1. **실측 과정에서의 잠재 스키마 결함 조기 적발**:
   - WTT S-2(LLM Fallback) 테스트 중 `llm_logs` 테이블의 `cache_hit`, `prompt_hash` NOT NULL 제약조건과 엔드포인트 쿼리 간의 미세한 불일치를 실서버 쿼리 실행(`run_wtt_tests.py`)을 통해 즉시 발견하고 수정(`d705277`)하였다.
   - 단순 단위 테스트보다 전사 시나리오 기반의 WTT가 스키마 정합성과 무음 실패(Silent Failure) 방어에 결정적인 역할을 함을 확인하였다.

2. **재고 차감의 원자적(Atomic) 무결성 검증**:
   - 모바일 정비사가 부품 사용 시 `SELECT FOR UPDATE` 락을 통해 `parts.stock` 차감과 `visit_parts` 생성이 단일 트랜잭션으로 체결되는 것을 실시간 데이터 증명으로 완결하였다.

3. **PM SA 실행 ➔ 감사 SA 검토 ➔ 사장님 최종 의결의 3단계 거버넌스 완성**:
   - 개발 주체(PM)가 임의로 완료를 선언하지 않고, 독립된 감사관(Auditor SA)이 헌장 준수 매트릭스를 전수 대조하여 승인한 뒤 사장님께 최종 릴리즈 승인을 상신하는 안정적 거버넌스 프로세스를 정립하였다.

---

## 3. 정립된 5단계 WTT 표준 절차 (Standard Protocol)

1. **WTT 자동화 스크립트 실행 (`run_wtt_tests.py`)**: 5대 비즈니스 시나리오 전수 실측
2. **스키마 및 감사 로그 무누락 검증**: `llm_logs`, `visit_status_history`, `visit_parts`, `audit_log_minimal` 확인
3. **PM SA 결과 보고서 작성 (`wtt_execution_report.md`)**: 실측 로그 및 Before/After 수치 첨부
4. **감사 SA 심층 감사 및 최종 의결 (`sa_final_audit_verdict_to_ceo.md`)**: 헌장 10대 항목 적합성 확인
5. **모의 데이터 SQL 생성 및 배포 (`seed_wtt_mock_data.sql`)**: 승인된 모의 데이터를 멱등성 있게 DB 적재

---

## 4. 재발 방지 및 향후 적용 원칙

- 향후 모든 신규 서브시스템 및 메이저 기능 추가 시, 본 `PROTO-WTT-001` 표준 절차에 따라 WTT 자동화 스크립트와 감사 보고서를 필수 산출물로 작성한다.
