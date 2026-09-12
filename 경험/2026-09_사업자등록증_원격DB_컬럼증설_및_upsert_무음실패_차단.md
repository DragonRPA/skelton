# 사업자등록증 원격 DB 컬럼 일괄 증설 및 upsert 무음실패 원천 차단 경험

## 1. 개요 및 배경
- 사업자등록증 Vision AI 모달 및 국세청 휴폐업 검증을 통해 신규 고객을 등록했으나, 실제 원격 DB(Supabase)에 저장되지 않고 새로고침 시 데이터가 증발하는 결함 발생.
- 사용자 질문: "사업자 등록증 넣고 정상 확인 돼서 고객등록을 눌렀는데 고객 등록이 되지 않았어(저장되지 않았어) 등록이 된건데 안보이는건가?"

## 2. 문제 원인
1. **원격 PostgreSQL DB(Supabase) DDL 미동기화**:
   - TypeScript 모델에 추가된 izItem, izType, 	axType, usinessStatus 등의 컬럼이 원격 customers 테이블에 미생성되어 PGRST204 에러로 저장이 거부됨.
2. **CUD 계층의 무음 실패(Silent Swallow)**:
   - insertRow에서 에러 발생 시 고정 컬럼만 제거하는 fallback이 실패하고 최종적으로 eturn null을 반환하여, 상위 UI는 저장이 성공한 것으로 착각하고 성공 토스트를 띄움 (헌장 5.2 위반).
3. **UI 시각적 확인 동선 단절**:
   - 신규 고객이 수백 개 목록 맨 아래에 위치함에도 검색창과 필터가 리셋되지 않아 실무자가 화면에서 즉시 찾지 못함.

## 3. 해결 조치
1. public.dev_exec_ddl RPC를 통해 원격 customers 및 endors 테이블에 26개 누락 컬럼을 ALTER TABLE ... ADD COLUMN IF NOT EXISTS로 일괄 증설.
2. schema.sql 단일 진실의 원천(SSOT)에 100% 동기화.
3. db.ts 내 insertRow/updateRow에 미반영 컬럼명 정규식 동적 감지 및 제거 후 재시도 엔진을 탑재하고, 최종 실패 시 무조건 	hrow new Error로 즉각 에러 모달 표출 강제 (헌장 5.2 무음 실패 방지).
4. Customers.tsx에서 등록 완료 즉시 검색창(searchTerm)에 고객사명을 자동 기입하여 화면 최상단에 1순위로 즉각 노출하고 스크롤을 동기화.

## 4. 체득 원칙
- DB 인터페이스 확장 시 로컬 schema.sql과 원격 DBMS DDL의 선행 동기화가 필수적임.
- 저장 레이어는 어떠한 경우에도 실패를 무음 삼킴(eturn null)하지 않고 반드시 throw하여 사용자에게 알려야 함.
- 등록 완료 직후에는 검색어 자동 포커스를 통해 사용자의 시각적 확인 비용을 0으로 만들어야 함.
