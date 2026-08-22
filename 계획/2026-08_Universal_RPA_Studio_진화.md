# Universal RPA Studio 진화 계획

**날짜**: 2026-08-22
**연결**: [발상](../발상/2026-08_Universal_RPA_Studio_진화.md) | [경험](../경험/2026-08_Universal_RPA_Studio_진화.md)

---

## 승인된 구현 Phase 목록

| Phase | 내용 | Build | 상태 |
|---|---|---|---|
| 1 | DB 스키마 5테이블 (neon_db.py) | 38 | ✅ 완료 |
| 2 | 시작 모달 (project_startup.py 신규) | 38 | ✅ 완료 |
| 3 | 프로젝트 컨텍스트 바 (상단 고정) | 38 | ✅ 완료 |
| 4 | 지문 헤더 + DB 자동 저장 | 38 | ✅ 완료 |
| 5 | Keep 리스트 기반 자연어 빌더 | 37 | ✅ 완료 |
| 6 | 요소 타입별 액션 팝업 | 39 | ✅ 완료 |
| 7 | 데이터 소스 패널 (Excel/CSV/JSON) | 40 | ✅ 완료 |
| 8 | DB 관리 탭 (탐색기 + DDL 패치 실행기) | 54 | ✅ 완료 |

---

## 핵심 설계 결정

### 기술 결정
- `python -c "..."` 인라인 실행 영구 금지 → `.py` 파일 저장 후 `& python script.py` 실행
- 정규식 패치 스크립트 금지 → `replace_file_content` 직접 편집 우선
- PW 평문 DB 저장 (Neon TLS 전제, 추후 Fernet 암호화 고려)
- 오프라인 Graceful Fallback 유지 (DB 없어도 앱 작동)

### UX 결정
- `[연결 확인]` 버튼 삭제 → DOM 수집 시 자동 CDP 상태 갱신
- 브라우저 수동 선택 UI 삭제 → HWND 창 핸들로 특정
- `[선택]` 버튼 → `[Keep ★]` 완전 대체 (둘 다 남기지 않음)
- 이미지 강제 입력 해제 → DOM 텍스트만으로도 코드 생성 가능

### DB 스키마 핵심
```
rpa_projects
  └── rpa_targets (URL / 윈도우 앱)
  └── rpa_keep_elements (Keep된 DOM 요소)
  └── rpa_tasks (자연어 + 생성 스크립트 + 지문 9개 컬럼)
        └── rpa_task_elements (태스크 ↔ Keep 요소 M:N)
```

### 지문 컬럼 (rpa_tasks)
`build_type` / `version_tag` / `ai_engine` / `ai_model` / `generated_at`
/ `sys_os` / `sys_hostname` / `sys_python` / `sys_user` / `sys_playwright`

---

## 미완성 항목 (다음 세션 예정)

- Phase 5: 프로젝트 관리 탭 (project_manager_tab.py 신규)
  - 대상 관리 UI, 태스크 목록, status 변경, 내보내기
- 바인딩 테이블 UI (Keep 요소 ↔ 데이터 컬럼 명시적 매핑)
- 실행 진행률 모니터 (배치 처리 실시간 행/행 진행 표시)
- 결과 파일 자동 저장 위치 설정
