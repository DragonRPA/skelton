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
