# 2026-09 AI 에이전트 호출 API 및 CLI / MCP 단일 바이너리 통합 경험

- **일시**: 2026-09-12 23:30
- **프로젝트**: 매뉴얼 스튜디오 (Manual Studio) v1.4.0.Build.13
- **카테고리**: 경험 (EXPERIENCE)
- **발안자**: 사장님
- **연결 계획**: [2026-09_AI_에이전트_호출_API_및_CLI_MCP_서버_구축_계획.md](../계획/2026-09_AI_에이전트_호출_API_및_CLI_MCP_서버_구축_계획.md)

---

## 1. 달성 성과

1. **초고밀도 기계 판독형 AGENTS.md 수립**:
   - Claude Cowork, Claude Code, Cursor, Antigravity 등 차세대 LLM이 리포지토리에 진입했을 때 단 3초 만에 전체 시스템 구조, CLI 명령어 체계, MCP 서버 툴, 단축키 맵, .mcs.json 스키마를 습득할 수 있는 공식 명세서 완결.
2. **헤드리스 CLI 엔진 (manual_cli.py) 구축**:
   - GUI를 띄우지 않고 명령줄 인자(`--rect`, `--monitor`, `--fixed`, `--stamp`, `--box`, `--arrow`, `--callout`, `--text`, `--export-ppt`, `--export-slides`)만으로 백그라운드 캡처, 주석 합성, 프로젝트 복원 렌더링 및 프레젠테이션 주입을 완벽 수행.
3. **표준 stdio JSON-RPC 2.0 MCP 서버 (mcp_server.py) 탑재**:
   - 외부 의존성(pip install) 없이 순수 Python 표준 라이브러리만으로 Anthropic MCP (2024-11-05) 표준 프로토콜 완벽 구현.
   - 6대 핵심 도구(`manual_studio_status`, `capture_screen`, `add_annotations`, `render_project`, `export_presentation`, `create_step`) 등록.
4. **단일 바이너리(ManualStudio.exe) 하이브리드 진입점 통합**:
   - `--windows-console-mode=attach` 및 `AttachConsole(-1)`을 통해 동일 바이너리가 더블클릭 시에는 무콘솔 네이티브 GUI 스튜디오로, CLI/MCP 인자 전달 시에는 즉시 터미널 콘솔에 부착되어 JSON-RPC / CLI 엔진으로 지능형 분기 실행.
5. **단위 테스트 44개 전수 100% 통과 및 Nuitka C-컴파일 배포 완결**.

---

## 2. 기술적 발견 및 교훈

- **Windows GUI Subsystem과 CLI 콘솔 부착 (AttachConsole)**:
  - 데스크톱 GUI 앱(.exe)을 CLI나 MCP 서버로도 동작하게 만들 때, 컴파일러 플래그(`--windows-console-mode=attach`)와 함께 런타임에서 `ctypes.windll.kernel32.AttachConsole(-1)`을 선행 호출해야 PowerShell/CMD에서 출력 버퍼가 유실되지 않고 표준 스트림에 즉시 연결됨.
- **MCP 표준 통신과 Stdout/Stderr 분리 철칙**:
  - MCP 서버는 `stdout`을 JSON-RPC 전용 데이터 파이프로 사용하므로, 디버그 로그나 경고는 예외 없이 `stderr`로 송출해야 프로토콜 파싱 에러를 원천 예방할 수 있음.
