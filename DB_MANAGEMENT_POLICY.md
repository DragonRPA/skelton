# 🏛️ 전사 데이터베이스 운용 표준 및 자격증명 관리 대장 (DB_MANAGEMENT_POLICY.md)

> **시스템 개발 표준 헌장 연계 문서**  
> 전사 프로젝트에서 사용하는 모든 데이터베이스(DB) 및 클라우드 스토리지 서비스의 운용 표준, 프로젝트별 매핑, 무료/유료 과금 정책, 마이그레이션 기준, 그리고 제어용 보안 자격증명(API Key, Connection String)을 통합 관리합니다.

---

## 📌 1. 전사 프로젝트별 DB 운용 현황 및 매핑표

| 프로젝트명 | 담당 업무 | 사용 DB / 스토리지 | 현재 플랜 | 상태 |
| :--- | :--- | :--- | :--- | :--- |
| **ImageScan** | 바코드 스캔, 라벨 인쇄, 자산 관리 | **Supabase (PostgreSQL)** | Free Tier (무료) | 🟢 활성 (Slot 1/2) |
| **Kiyuen_Lift** | 리프트 견적/렌탈 계약 관리 | **Supabase (PostgreSQL)** | Free Tier (무료) | 🟢 활성 (Slot 2/2) |
| **UBUS_contract** | UBUS ERP 계약서 PDF OCR & RPA | **Neon (PostgreSQL)** | Free Plan (무료) | 🟢 활성 (1/100) |
| **Universal_RPA** | 범용 RPA 스크립트 IDE & 녹화기 | **Neon (PostgreSQL)** | Free Plan (무료) | 🟢 활성 (2/100) |
| **전사 공통 자원** | 클라우드 원본 파일, 배포판 보관 | **Cloudflare R2 (`drcf`)** | Free Tier (무료) | 🟢 상시 운용 |

---

## 🔐 2. 프로젝트별 DB 제어 보안 자격증명 (Master Credentials)

### 1) ImageScan (Supabase Slot 1)
- **서비스**: Supabase Cloud
- **Project Ref**: `tfgbpgutxxlhqbzewkyt`
- **Region**: AWS Seoul (`ap-northeast-2`)
- **API URL**: `https://tfgbpgutxxlhqbzewkyt.supabase.co`
- **Anon Public Key**: `sb_publishable_wruJQfp3Op-ISvVwb4ZdmA_2OqMUJeQ`
- **용도**: 자산 마스터(`asset`), 라벨 인쇄 큐(`print_queue`), 실시간 스캔 로그

---

### 2) Kiyuen_Lift (Supabase Slot 2)
- **서비스**: Supabase Cloud
- **Project Ref**: `wywgkikkjgbnlljkkmnz`
- **Region**: AWS Seoul (`ap-northeast-2`)
- **API URL**: `https://wywgkikkjgbnlljkkmnz.supabase.co`
- **Anon Public Key**: `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Ind5d2draWtramdibmxsamtrbW56Iiwicm9sZSI6ImFub24iLCJpYXQiOjE3ODQzNjcxMzgsImV4cCI6MjA5OTk0MzEzOH0.gSftxhQjFmWUQzikx-Q5UsdgNKSZISZqJvUGeLBOCqU`
- **용도**: 고객사, 리프트 견적서, 계약 내역, 장비 이력 관리

---

### 3) UBUS_contract & Universal_RPA_Recorder (Neon PostgreSQL)
- **서비스**: Neon Serverless PostgreSQL
- **Project Name**: `rpa-script-editor`
- **Region**: AWS Asia Pacific 1 (Singapore - `ap-southeast-1`)
- **Host**: `ep-small-firefly-az3rp5ve.c-3.ap-southeast-1.aws.neon.tech`
- **Database**: `neondb`
- **User**: `neondb_owner`
- **Password**: `npg_W0LzlYBckKp1`
- **Connection String (URI)**:
  ```
  postgresql://neondb_owner:npg_W0LzlYBckKp1@ep-small-firefly-az3rp5ve.c-3.ap-southeast-1.aws.neon.tech/neondb?sslmode=require
  ```
- **주요 테이블**: `rpa_contract_logs`, `rpa_batch_runs`
- **용도**: PDF OCR 추출 결과, ERP RPA 실행 성공/실패 로그, 배치 통계

---

### 4) Dragon Cloudflare R2 (`drcf` 전사 글로벌 스토리지)
- **별칭**: `drcf`
- **Account ID**: `35014a2514680107d74e1e68d96e6c32`
- **Bucket Name**: `dragonrpa`
- **Public CDN Domain**: `https://pub-4bd1b65a7bcc4eef8993da27e7362727.r2.dev`
- **S3 API Endpoint**: `https://35014a2514680107d74e1e68d96e6c32.r2.cloudflarestorage.com/dragonrpa`
- **용도**: 계약서 원본 PDF 아카이브, PyInstaller EXE 빌드 배포본, 백업 데이터

---

## 💰 3. 무료 플랜 비교 및 한도 요약 (현재 운영)

| 제공사 | 무료 프로젝트 수 | 스토리지 한도 | 컴퓨팅 / 대역폭 | 절전(Sleep) 정책 |
| :--- | :---: | :---: | :---: | :--- |
| **Supabase** | **2개 (소진됨)** | 500 MB / 프로젝트 | 2 Core, 1 GB RAM 공유 | 7일 무접속 시 일시정지 |
| **Neon** | **100개 (여유 98개)** | 500 MB / 프로젝트 | 100 CU-시간 / 월 | 5분 무접속 시 자동 Scale-to-Zero |
| **Cloudflare R2** | 1개 (멀티버킷) | 10 GB (월간 무료) | 클래스A 100만회 / 클래스B 1000만회 | 상시 활성 (트래픽 송출비 $0) |

---

## 📈 4. 향후 유료 전환 가이드 및 과금 정책 비교

### 1) 서비스별 유료 플랜 구조

```
[비용 최적화 계층 구조]
1. 간헐적/배치 작업 (RPA, 크롤링, OCR)  ➔ Neon 종량제 ($1 ~ $3 / 월)
2. 24시간 상시 웹 서버 + DB 필요 시       ➔ Railway / Aiven ($5 / 월)
3. Auth + Storage + Realtime 통합 필요 시 ➔ Supabase Pro ($25 / 월)
```

| 서비스 | 유료 플랜명 | 기본 요금 | 포함 스펙 | 초과 종량 과금 |
| :--- | :--- | :--- | :--- | :--- |
| **Neon** | **Launch / Scale** | **$0 기본 + 종량제** (사용량만 청구) | • 프로젝트 무제한<br>• Scale-to-Zero 자동 절전 | • Compute: $0.106 / CU-시간<br>• Storage: $0.35 / GB-월 |
| **Railway** | **Hobby / Pro** | **$5 / 월** (Hobby)<br>**$20 / 월** (Pro) | • $5 크레딧 포함<br>• 8GB RAM, 8 vCPU | • RAM: $0.000231/GB-시간<br>• vCPU: $0.000463/vCPU-시간 |
| **Aiven** | **Developer** | **$5 / 월** (정액) | • 1 vCPU, 1 GB RAM<br>• 5 GB Storage | 정액제 플랜 업그레이드 |
| **Supabase** | **Pro** | **$25 / 월** (정액) | • 프로젝트 100개<br>• 8 GB DB 스토리지<br>• 100 GB 파일 스토리지<br>• 10만 MAU Auth | • DB Storage: $0.125 / GB-월<br>• File Storage: $0.021 / GB-월 |

---

### 2) 우리 회사 최적 유료 전환 전략 (Cost-Benefit Roadmap)

1. **단기 (현재 ~ 50개 프로젝트 확장기)**:
   - **Neon 무료 티어 적극 활용**: 최대 100개 프로젝트까지 무료 운용 가능하므로 신규 RPA, 파서, 도구는 모두 Neon으로 신규 생성.
   - 비용: **0원**.
2. **중기 (RPA 실시간 대량 처리 및 용량 500MB 초과 시)**:
   - **Neon Launch 플랜 유지**: RPA 특성상 야간이나 비가동 시 5분 만에 Scale-to-Zero로 잠들기 때문에, 실질 월 청구액은 **$1 ~ $3 (약 1,500 ~ 4,500원)** 수준 유지.
3. **장기 (전사 웹 ERP 백엔드 통합 시)**:
   - 자사 포털 및 인증(Auth)이 전사 통합될 때만 **Supabase Pro ($25/월)** 1개 계정으로 일원화.

---

## 🛡️ 5. 보안 및 백업 헌장 준수 수칙

1. **자격증명 하드코딩 금지**: 모든 DB 비밀번호와 API Key는 소스코드에 하드코딩하지 않고 `.env` 또는 `.gitignore`된 `config.json`을 통해서만 주입한다.
2. **SSOT 원칙**: 본 `DB_MANAGEMENT_POLICY.md` 문서를 전사 DB 자격증명의 단일 진실의 원천(SSOT)으로 관리하며, 신규 프로젝트 추가 시 즉시 갱신한다.
3. **일일 백업**: 중요 트랜잭션 데이터는 매일 자정 Cloudflare R2(`drcf`) 버킷으로 자동 덤프 백업을 수행한다.
