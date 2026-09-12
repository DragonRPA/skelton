# [경험] Supabase 전사 스키마 100% 일치 DDL 패치 및 자가 진단 정합성 검증 완비

- **일자**: 2026-09-12 20:45
- **프로젝트**: 기연고소작업대 통합 ERP (Giyuen_Lift)
- **버전**: `v1.14.0.Build.83`
- **관련 표준 헌장**:
  - 카테고리 I: 1.1 시스템 최우선 사명 (최대 편익 달성)
  - 카테고리 V: 5.2 전 스토리지/DB 저장 성공 검증 및 무음 실패 방지 정책
  - 카테고리 V: 5.3 단일 진실의 원천(SSOT) 및 로컬 DB 스키마 정합성 자가 검증
  - 카테고리 VI: 6.2 커밋/푸시 정책 및 단축어 "ㄹㅇ" 일괄 실행 규칙

---

## 1. 배경 및 당면 문제

사용자가 `DevDataUploader.tsx` (개발자 도구 - DB 데이터 업로더 / 스키마 정합성 검증) 화면에서 스키마 진단을 실행한 후 다음 2가지 본질 질문을 제시함:
1. **"이 기능이 현재도 기능 본질 목적을 달성하고 있나?"**
2. **"현재 점검 했더니 이런 상태(470개 DDL 구문 패치 필요, standard_options 테이블 부재, billings 컬럼 누락 등)로 나오는데 조치해야하는가?"**
3. **"검증하고 DDL 패치 수행하고 ㄹㅇ"**

---

## 2. 진단 및 본질 목적 검증

### (1) 기능 본질 목적 달성 여부 (헌장 5.3)
- `DevDataUploader.tsx`의 스키마 검증 엔진은 PostgREST의 스키마 캐시 왜곡을 완전히 우회하기 위해 PostgreSQL 내부 시스템 카탈로그(`information_schema.columns`)를 직접 1:1 대사(Reconciliation)하도록 설계됨.
- 로컬 `schema.sql`의 최신 테이블/컬럼 정의와 원격 Supabase DB의 실제 컬럼 구성을 비교하여, 배포 전후 발생할 수 있는 스키마 드리프트(Schema Drift)를 단 1컬럼의 오차도 없이 검출함.
- 따라서 **런타임 42703(컬럼 없음), 42P01(테이블 없음) 에러로 인한 무음 실패(Silent Failure)를 사전에 차단하는 핵심 헌장 5.3 도구로서 본질 목적을 100% 충실히 달성**하고 있음을 검증함.

### (2) 조치 필요성 판단
- 검증 결과 실제 원격 DB에 10개 신규 테이블(`standard_options`, `corporate_vehicles`, `vehicle_operation_logs`, `vehicle_fuel_logs`, `equipment_manuals`, `print_stations`, `print_queue`, `legal_notice_logs`, `legal_notice_templates`, `bank_initial_balances`)이 미생성 상태였음.
- 또한 19개 기존 핵심 테이블(`billings`의 `billingType`, `rejectReason`, `details`, `assets`의 `maintenanceScore` 등 13개 컬럼, `deliveries`의 `waivedAmount` 등 12개 컬럼, `todos`의 17개 컬럼 등)에 필수 컬럼이 누락되어 있었음.
- 미조치 시 청구서 반려 처리, 표준 옵션 선택, 배차비 감면, 법인차량 운행일지 등록 시 DB 저장 거부 에러가 즉시 발생하므로 **반드시 조치해야 하는 정당하고 긴급한 조치**로 판단됨.

---

## 3. DDL 패치 집행 및 완전 정합성 수렴

1. **`dev_exec_ddl` RPC를 통한 468개 DDL 구문 일괄 집행**:
   - 10개 신규 테이블 `CREATE TABLE IF NOT EXISTS` + RLS 허용 정책 일괄 생성.
   - 19개 테이블 `ALTER TABLE ADD COLUMN IF NOT EXISTS` + RLS 허용 정책 일괄 갱신.
   - 19개 배치(Batch)로 분할 실행하여 **Total Success: 468, Total Failed: 0**으로 100% 무결 집행 완결.
2. **`standard_options` 전사 표준 옵션 마스터 16종 시드 적재**:
   - 유상 옵션 10종 + 보양 작업 6종 기본 데이터 원격 DB 적재.
3. **스키마 재진단 완결**:
   - `Missing: 0, Mismatch: 0, OK: 69` (전체 69개 테이블 100% 일치 수렴).
4. **UI 메타데이터 보강**:
   - `DevDataUploader.tsx`의 `TABLE_LABEL_MAP`에 신설 13개 테이블의 한글 명칭 메타데이터 반영.
