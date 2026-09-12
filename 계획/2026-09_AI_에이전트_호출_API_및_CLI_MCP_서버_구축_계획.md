# 2026-09 AI 에이전트 호출 API 및 CLI / MCP 서버 구축 계획

- **일시**: 2026-09-12 23:15
- **프로젝트**: 매뉴얼 스튜디오 (Manual Studio)
- **카테고리**: 계획 (PLAN)
- **발안자**: 사장님
- **연결 발상**: [2026-09_AI_에이전트_친화적_소프트웨어_설계_및_UIA_MCP_인터페이스_구상.md](../발상/2026-09_AI_에이전트_친화적_소프트웨어_설계_및_UIA_MCP_인터페이스_구상.md)

---

## 1. 계획 수립 배경 및 사장님 지침

> **"에이전트 AI 들이 호출할수 있도록 API 를 설계하고 제공까지 해주게 만들고 싶어졌어. 권고안을 모두 실행해"**

Claude Cowork, Claude Code, Cursor, Antigravity 등 차세대 LLM 에이전트가 매뉴얼 스튜디오를 직접 호출하고 제어할 수 있도록 3대 핵심 인터페이스(AGENTS.md, CLI 헤드리스 엔진, MCP 서버)를 구축하기로 결정함.

---

## 2. 3대 구현 목표 및 범위

1. **`AGENTS.md` 명세서 작성**:
   - AI 에이전트가 리포지토리 접근 시 3초 만에 시스템 구조, CLI 명령어, MCP 툴, 단축키, 에러 복구 지침을 인지하도록 기계 판독형 마크다운 제공.
2. **CLI 헤드리스 엔진 (`manual_cli.py` 및 `manual_capture_studio.py --cli`) 구축**:
   - GUI 창을 띄우지 않고 명령줄 인자만으로 캡처, 주석 삽입, 프로젝트 렌더링, PPT/슬라이드 내보내기를 일괄 수행.
3. **독립형 및 내장형 MCP(Model Context Protocol) 서버 (`mcp_server.py`) 구현**:
   - Claude Desktop 및 Cowork에 직접 등록 가능한 표준 stdio JSON-RPC 2.0 서버 구현.
   - 6대 전용 MCP 도구(`manual_studio_status`, `capture_screen`, `add_annotations`, `render_project`, `export_presentation`, `create_step`) 제공.
   - `ManualStudio.exe --mcp` 단일 바이너리 내장 실행 지원.
4. **전수 단위 테스트 (`test_core_engine.py` Test 44) 및 C-컴파일 빌드 1.4.0.13 연계**.

---

## 3. 실행 단계

- **1단계**: `AGENTS.md` 작성 및 프로젝트 루트 배치
- **2단계**: `manual_cli.py` 모듈 개발 및 `manual_capture_studio.py` 엔트리포인트 CLI/MCP 분기 통합
- **3단계**: `mcp_server.py` 경량 표준 MCP 서버 개발 및 테스트
- **4단계**: `test_core_engine.py` 단위 테스트 44번 구축 및 전수 100% 통과 검증
- **5단계**: RELEASE_NOTES.md 업데이트, git 커밋/푸시, Nuitka C-컴파일 배포
