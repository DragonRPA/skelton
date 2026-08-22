# Universal RPA Studio 진화 경험

**날짜**: 2026-08-22
**연결**: [발상](../발상/2026-08_Universal_RPA_Studio_진화.md) | [계획](../계획/2026-08_Universal_RPA_Studio_진화.md) | [후회](../후회/2026-08_Universal_RPA_Studio_진화.md)

---

## 버전 이력 및 교훈

| Build | 내용 | 핵심 교훈 |
|---|---|---|
| 30 | CDP 전략 0순위 탑재 | 9222~9230 포트 순회, --user-data-dir 격리 |
| 31~32 | CDP 바로가기 생성 | COM WshShortcut 한글 경로 오류 → ASCII 파일명 |
| 33 | `disconnect()` → `close()` 핫픽스 | Playwright Browser API는 `.close()` 사용 |
| 34 | 브라우저 수동 선택 UI 제거 | HWND 창 핸들로 브라우저 특정 → UI 불필요 |
| 35 | 연결확인 버튼 제거 | 버튼 최소화 원칙: 기능은 다른 액션에 녹여라 |
| 36 | 이미지 강제 입력 해제 | 이미지 없이도 텍스트 전용 AI 요청 가능 |
| 37 | Keep 리스트 기반 자연어 프롬프트 빌더 | {변수명} 패턴의 강력함 |
| 38 | 프로젝트 중심 진화 | DB 지문 9컬럼이 스크립트 추적성을 완전히 보장 |
| 39 | Keep 칩 타입별 액션 팝업 | element_type → Playwright method 자동 매핑 |
| 40 | 데이터 소스 패널 | 배치 RPA 패러다임 정립, pandas + iterrows 루프 |
| 46~48 | UI 3분할 및 폰트 11pt 개편 | 공간 활용도 극대화 및 가독성 최적화 |
| 50 | 프로젝트별 대상 URL 저장/로드 | `rpa_targets` DB 연동 및 ComboBox 바인딩 |
| 53 | 수직 구분선 CTkFrame 200px 버그 해결 | `CTkFrame` 기본 `height=200` 주의, `height=1` 명시 필수 |
| 54 | DB 관리 탭 (탐색기 + DDL 패치 실행기) | Treeview 다크 그리드 + thread-safe Queue 비동기 DB 인트로스펙션 |
| 55 | Keep 요소 DB 자동 로드 & 중복 방지 | 레거시 테이블 전면 정리, 프로젝트 로드 시 Keep 자동 복원 및 DB 양방향 동기화 |
| 56 | DOM 카드 우측 액션 버튼 잘림 방지 | Tkinter Pack 순서: 고정 우측 버튼 선패킹(pack right first)으로 100% 가시성 확보 |
| 57 | 단계별 조립 제거 & Keep 더블클릭/하이라이트 | 복잡한 스텝 빌더 폐지(백업 격리), Keep 더블클릭 즉시 삽입 및 노란색 토큰 하이라이트 |
| 58 | rpa_targets 대상 URL Keep 리스트 연동 | 프로젝트 URL을 `[URL] {url_...}` 로 Keep에 자동 로드 및 AI 시작 접속 프롬프트 주입 |
| 59 | 데이터소스 헤더 팝업 Keep 버튼 & 일괄 추가 | 창 폭 확장(740px), 개별 `[Keep ★]` + `[✓ 전체 컬럼 Keep 추가]` 1클릭 지원 |
| 60 | 폴더 파일명 배열 변수 Keep + 5단 컬러 하이라이트 | 폴더+확장자 ➔ `{file_list}` 파일 배열 Keep 등록 및 5W1H 멀티컬러 토큰 하이라이트 |
| 61 | Keep 행 직접 [조작 ▾] 버튼 통합 & 하단 칩 바 제거 | 대규모 객체 스케일링 대응, 행 단위 팝업 호출 및 화면 수직 공간 극대화 |
| 62 | 입력창(text/password) 액션 매핑 버그 수정 | element_type(`text`, `password` 등)과 `input` 액션 카탈로그 1:1 완벽 매핑 |
| 63 | 셀렉트(select) <option> 실시간 자동 추출 & 스마트 액션 | DOM <option> 파싱 ➔ `[조작 ▾]`에 실제 옵션 항목 나열 ➔ AI 프롬프트 옵션 주입 |
| 64 | 입력창 [조작 ▾] Keep 데이터 변수({row...}, {file...}) 주입 연동 | Where+What+How 삼위일체 완성, 1클릭 변수 주입 문장 자동 조립 및 스크롤 팝업 |
| 65 | 변수명 수정 DB 영구 저장 & 비밀번호 Fernet 암호화 체계 | `[✏]` 수정값 DB 100% 영구 동기화 및 `login_pw` Fernet 양방향 암호화 보호 |
| 66 | 변수명 수정 시 PK ID 기반 순수 UPDATE 보장 | 기존 행 덮어쓰기(UPDATE) 엄격화, INSERT 중복 생성 원천 차단 |
| 67 | Ollama 비이미지 코드생성 NoneType 버그 수정 & 기본 프롬프트 표준화 | `image_path=None` 안전 처리(순수 텍스트 Keep 추론) 및 표준 프롬프트 문구 적용 |
| 68 | Ollama 엔진 목록 비전 모델(vl) 제외 & 코더 모델 우선 정렬 | `vl`, `vision`, `llava` 등 무거운 멀티모달 제외, `qwen2.5-coder` 등 코드 특화 모델 우선화 |
| 69 | 자연어 프롬프트 중간 저장(draft) & 태스크 불러오기/관리 팝업 | 요구사항 헤더 `[저장]`/`[불러오기 ▾]` 탑재, `rpa_tasks` draft 실시간 동기화 & 원클릭 복원 |
| 70 | Tkinter 스레드 비동기 콜백 소멸 예외(_safe_after) 방어 | 모달/창 종료 후 백그라운드 DB 스레드 완료 시 `RuntimeError: not in main loop` 원천 차단 |
| 71 | 수집된 DOM 조작 객체 목록 실시간 텍스트 검색/필터 바 구축 | 텍스트·경로·셀렉터 실시간 필터링, 대규모 수집 시 수초 만에 타깃 객체 발견 |
| 72 | Local Ollama 미실행(WinError 10061) 친절 안내 모달 제공 | 연결 거부 시 원인과 해결책(Gemini 전환 또는 ollama serve)을 명확히 제시 |
| 73 | [스크립트로 전송] & [봇 등록] 액션 파이프라인 연동 정상화 | 지문 헤더(#)로 인한 유효성 검사 차단 버그 수정, 탭 자동 전환 및 모듈 체이닝 완성 |

---

## 확립된 기술 패턴

### CDP 연결 구조
```python
# 포트 9222~9230 순회
for port in range(9222, 9231):
    browser = pw.chromium.connect_over_cdp(f"http://localhost:{port}")
    # 성공 시 _cdp_last_port 캐시
```

### Keep 요소 데이터 구조
```python
{
    "var_name": "버튼_검색",       # {변수명} 토큰
    "label": "검색",
    "selector": "button:has-text('검색')",
    "element_type": "button",
    "path": "검색폼 > 버튼"
}
```

### 지문 헤더 형식 (확립)
```
# ═══════════════════════════════════════════════════════════
# [RPA Script Fingerprint]
# Project   : {프로젝트명}
# Task      : {태스크 제목}
# Build     : DEBUG | RELEASE
# ───────────────────────────────────────
# AI Engine : Google Gemini | Ollama
# AI Model  : gemini-2.5-flash
# Generated : YYYY-MM-DD HH:MM:SS
# ───────────────────────────────────────
# OS        : Windows 11 Pro 23H2
# Hostname  : {PC명}
# User      : {사용자명}
# Python    : 3.11.9
# Playwright: 1.47.0
# ═══════════════════════════════════════════════════════════
```

### AI 확장 프롬프트 구조 (확립)
```
[고정 참조 객체]
  {var} = selector: "..."

[데이터 소스]
  파일: xxx.xlsx | N행 | 컬럼: col1, col2...
  각 행의 값은 {row.컬럼명} 으로 참조

[요구사항]
  (사용자 자연어)

지침:
  - [고정 참조 객체] 변수명 → Playwright 셀렉터 대응
  - 데이터 소스 있으면 pandas + iterrows() 루프 처리
  - 결과를 컬럼으로 기록 후 결과 파일 저장
```

### 요소 타입 → 액션 매핑 (확립)
```python
_ELEM_ACTIONS = {
    "input":    [("값 입력", "{var}에 '{값}'을 입력"), ...],
    "button":   [("클릭", "{var}을 클릭"), ("더블클릭", ...), ...],
    "select":   [("옵션 선택", "{var}에서 '{옵션}'을 선택"), ...],
    "checkbox": [("체크", ...), ("체크 해제", ...), ...],
    "table":    [("데이터 추출", ...), ("행 수 확인", ...), ...],
    "_default": [("클릭", ...), ("텍스트 확인", ...), ...]
}
```

---

## 환경 정보
- Python: `C:\ProgramData\anaconda3\python.exe`
- Universal 경로: `d:\GoogleDrive\RPA 개발\01.AntiGravity\Universal_RPA_Recorder\`
- 프로젝트 경로: `d:\GoogleDrive\RPA 개발\01.AntiGravity\UBUS_contract\`
- Git remote: `https://github.com/DragonRPA/RPA_Script_Editor.git` (branch: master)
- CDP Chrome 포트: 9222 / Edge 포트: 9223
