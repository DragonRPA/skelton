# 2026-09 매뉴얼 스튜디오 Cloudflare R2 원클릭 자동 릴리즈 및 웹사이트 버전 동기화 경험

- **일시**: 2026-09-17 10:40
- **프로젝트**: 매뉴얼 스튜디오 (Manual Studio) & www.dragonrpa.co.kr
- **카테고리**: 경험 (EXPERIENCE)
- **발안자**: 사장님
- **연결 문서**: [2026-09_AI_에이전트_도구_진화_및_웹_GEO_검색엔진최적화_경험.md](2026-09_AI_에이전트_도구_진화_및_웹_GEO_검색엔진최적화_경험.md)

---

## 1. 당면 과제 및 배경

1. **대용량 바이너리 배포 및 트래픽 부담**:
   - 기존에는 빌드된 `ManualStudio.exe`(약 61MB)를 Next.js 공식 웹사이트의 `public/downloads/`에 직접 커밋 및 푸시하여 웹 호스팅 서버에서 서빙함.
   - 새 버전 빌드 시마다 60MB가 넘는 실행 파일이 Git 이력에 누적되어 저장소 비대화, 웹 서버 대역폭 낭비, 다운로드 속도 저하 문제 발생.
2. **버전 정보 파편화 및 수동 작업**:
   - `version.json`, `manual_capture_studio.py` (`APP_VERSION`), `build_c.bat` (윈도우 PE 리소스 버전), `updater_engine.py`, 그리고 공식 홈페이지 Neon PostgreSQL DB(`products` 테이블)가 제각각 분리되어 있어 버전 업그레이드 시 누락 위험 발생.
   - 사장님의 요구사항: 개발 프로젝트에 변화가 발생하면 버전 번호를 기억 및 자동 증분하고, Cloudflare R2로 무중단 업로드하며, 공식 홈페이지와 앱 내 자동 업데이트가 항상 최신 버전을 다운로드할 수 있는 완전 자동화 파이프라인 구축.

---

## 2. 구축 성과 및 아키텍처

1. **Cloudflare R2 다이렉트 릴리즈 엔진 구축 (`release_to_cf.py`)**:
   - S3 호환 boto3 라이브러리를 활용하여 Cloudflare R2 버킷(`dragonrpa`)에 단일 스크립트로 직접 업로드 파이프라인 구축.
   - **자동 버전 기억 및 동기화 (SSOT)**:
     - `version.json`을 기준으로 `--bump patch|minor|major` 또는 명시적 버전 지정 시 자동 갱신.
     - `manual_capture_studio.py`의 `APP_VERSION`, `build_c.bat`의 컴파일 리소스 버전, `updater_engine.py`의 버전 체크 엔드포인트를 파일 수정 없이 원클릭 정규식 동기화.
   - **바이너리 SHA-256 해시 및 파일 크기 자동 계산**:
     - 릴리즈 메타데이터(`version.json`)에 SHA-256 체크섬을 자동 기록하여 무결성 검증 보장.
   - **이원화 캐시 정책 업로드**:
     - `releases/ManualStudio_latest.exe`: `Cache-Control: no-cache, no-store, must-revalidate`를 적용하여 사용자가 언제 다운로드해도 즉시 최신 바이너리를 수신.
     - `releases/ManualStudio_v{version}.exe`: `Cache-Control: public, max-age=31536000, immutable`을 적용하여 영구 버전 아카이브 제공.
     - `releases/version.json`: `Cache-Control: no-cache`로 앱 내 오토업데이터가 0초 지연으로 신규 버전을 감지하도록 설정.
2. **원격 Neon PostgreSQL DB 실시간 릴리즈 동기화 (`sync_release_db.js`)**:
   - R2 업로드 완료 즉시 공식 홈페이지 DB(`products` 테이블)의 `MANUAL_STUDIO` 행에 최신 버전(`v1.9.2`)과 R2 영구 다운로드 URL을 즉시 자동 업데이트.
   - 공식 웹사이트(`/products`, `/manual-studio`)를 다시 빌드하거나 배포하지 않아도 사용자 화면에 실시간으로 최신 버전이 표출됨.
3. **Next.js 15 웹사이트 경량화 및 302 영구 리다이렉트**:
   - `public/downloads/` 폴더에서 거대한 `ManualStudio.exe` 바이너리를 전면 제거하여 Git 저장소와 Next.js 빌드 번들 크기 경량화.
   - `next.config.ts`에 Next.js path-to-regexp 표준 정규식 기반 리다이렉트 라우팅 탑재:
     - `/downloads/ManualStudio.exe`, `/downloads/ManualStudio_latest.exe`, `/downloads/:file(ManualStudio.*)` 요청 시 Cloudflare R2 초고속 CDN 다운로드 URL로 302 즉시 포워딩.
4. **바탕화면/탐색기 원클릭 배치 파일 제공 (`배포_Cloudflare업로드.bat`)**:
   - 개발자가 코드 수정 후 마우스 더블클릭 한 번으로 버전 증분 ➔ R2 업로드 ➔ DB 동기화 ➔ 다운로드 검증을 10초 만에 완결.

---

## 3. 기술적 교훈 및 주의사항

- **Cloudflare R2 토큰 권한 범위(Scoping)**:
  - 버킷 단위로 세분화된 R2 API 토큰은 `s3.list_buckets()`(전체 버킷 목록 조회) 권한이 없으므로 `ClientError (403 Forbidden)`가 발생할 수 있음.
  - 따라서 R2 연동 스크립트에서는 전체 버킷 리스트를 조회하지 않고 대상 버킷(`dragonrpa`)을 직접 지정하여 `put_object`, `head_object`를 수행해야 함.
- **Cloudflare WAF와 기본 파이썬 User-Agent**:
  - Python `urllib.request`의 기본 User-Agent(`Python-urllib/3.x`)는 Cloudflare의 자동 봇 차단 WAF에 의해 403 에러가 반환될 수 있음.
  - 업로드 완료 검증(GET 요청) 시 반드시 표준 브라우저 User-Agent(`Mozilla/5.0...`)를 헤더에 명시해야 정상 200 OK 응답을 수신함.
- **Next.js `next.config.ts`의 `redirects` 파라미터 문법**:
  - `path-to-regexp` v6/v7 규격에서는 `_Setup_:path*`처럼 파라미터 앞에 슬래시(`/`) 구분자가 없으면 `TypeError: Can not repeat "path" without a prefix and suffix` 빌드 에러를 유발함.
  - 임의 패턴이나 파일명을 매칭할 때는 `/:file(ManualStudio.*)`와 같이 명시적 정규식 그룹 문법을 사용해야 32개 전체 라우트 빌드가 정상 통과함.
