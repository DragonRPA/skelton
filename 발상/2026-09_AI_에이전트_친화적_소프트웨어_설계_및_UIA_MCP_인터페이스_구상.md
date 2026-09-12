# 2026-09 AI 에이전트 친화적 소프트웨어 설계 및 UIA / MCP 인터페이스 구상

- **일시**: 2026-09-12 23:05
- **프로젝트**: 매뉴얼 스튜디오 (Manual Studio) 및 전사 소프트웨어 표준
- **카테고리**: 발상 (IDEA)
- **발안자**: 사장님
- **연결 경험**:
  - [전사 시스템 개발 표준 헌장 (카테고리 I~VIII)](../README.md)

---

## 1. 발상 배경 및 사장님 핵심 화두 (Inception Context)

> **"이 프로그램이 인간한테도 친화적이겠지만, 클로드코웍 같은 에이전트 AI에도 친화적이게 하려면 무엇을 제공해주면 좋을까? 예를들면 기능을 설명하는 AI용 설명서를 준다던가 우리 기능버튼들에 대한 UIA 명세서를같은 정보들을 모두 제공하는 MD 파일을 준다던가"**

전통적인 소프트웨어는 **'인간의 눈(시각 UI)'**과 **'인간의 손(마우스/키보드)'**만을 상정하고 설계되었다.
그러나 Claude Cowork, Claude Computer Use, OpenAI Operator, OSWorld 등 **'소프트웨어를 직접 관찰하고 조작하는 AI 에이전트'**가 차세대 주류 사용자로 등장함에 따라, 소프트웨어 설계의 패러다임이 **Human-Friendly**를 넘어 **Agent-Native & Agent-Friendly**로 진화해야 한다는 통찰이다.

---

## 2. 격상된 본질 목표 (Elevated Core Teleology)

> **"인간 사용자에게는 직관적이고 미려한 GUI(리본 메뉴, 알약 탭, 단축키)를 제공함과 동시에,**
> **AI 에이전트에게는 픽셀 좌표 추측이나 착오 없이 0ms 만에 정확한 인지와 조작을 가능케 하는**
> **① UIA 시맨틱 트리 및 명세서, ② AGENTS.md / llms.txt 기계 판독형 카탈로그, ③ MCP(Model Context Protocol) 툴 브릿지, ④ JSON 선언적 조작 파이프라인을 전면 제공하여**
> **'인간과 AI 에이전트가 동시에 협업하는 최고의 소프트웨어'로 거듭난다."**

---

## 3. 에이전트 AI 친화적 소프트웨어 4대 계층 설계 기둥 (4 Architecture Pillars)

### ① 🎯 제1계층: UIA (UI Automation) 시맨틱 트리 완성 및 UIA 명세서 (`uia_spec.json` / `UIA_SPEC.md`)
- **문제점**: 에이전트가 스크린샷 픽셀만 보고 버튼을 클릭하면 해상도/DPI 차이로 인해 빗나갈 확률이 높음.
- **해결책**:
  - 모든 위젯에 명확한 `AutomationId` (`objectName`), `AccessibleName`, `AccessibleDescription`을 100% 부여.
  - 이를 집대성한 `uia_spec.json` 및 `UIA_SPEC.md`를 동봉하여, 에이전트가 버튼 이름, 단축키, 제어 패턴(Invoke, Value, Toggle 등)을 사전에 완벽히 인지하도록 지원.

### ② 📑 제2계층: 기계 판독형 에이전트 설명서 (`AGENTS.md` 및 `llms.txt`)
- 인간용 사용자 설명서와 별도로, LLM의 토큰 효율성과 추론에 최적화된 마크다운 설명서 제공.
- 프로그램의 상태 전이 다이어그램(State Machine), 단축키 맵, 명령 인자 스키마, 오류 복구 가이드 수록.
- Claude Code / Cowork가 프로젝트에 접근하는 즉시 `AGENTS.md`를 읽고 전체 조작법을 자율 습득.

### ③ 🔌 제3계층: MCP (Model Context Protocol) 로컬 서버 내장
- Anthropic 주도의 표준 프로토콜인 MCP 서버를 스튜디오 내부에 탑재하거나 사이드카로 제공.
- 에이전트가 마우스를 움직이지 않고도 Tool Use를 통해:
  - `manual_studio.capture_rect(x, y, w, h)`
  - `manual_studio.add_annotation(item_type, params)`
  - `manual_studio.export_presentation(target="powerpoint"|"slides")`
  직접 API 함수 호출로 1초 만에 매뉴얼 제작을 자동 완결.

### ④ 📋 제4계층: 선언적 데이터 파이프라인 (JSON-Driven Automation)
- 이미 구축된 `.mcs.json` 프로젝트 파일 포맷을 에이전트 입력 규격으로 공식화.
- AI 에이전트가 GUI를 클릭하는 대신 JSON 파일 하나를 작성하여 `ManualStudio.exe --load-and-render plan.json` 형태로 호출하면 완벽한 매뉴얼 슬라이드가 생성.

---

## 4. 기대 효익 (Expected Value)

1. **에이전트 조작 성공률 99.9% 달성**: 픽셀 기반 Vision 에이전트의 실패율(미스클릭, 타이밍 이슈)을 UIA/MCP 기반 0%로 수렴.
2. **Claude Cowork / RPA 연동 극대화**: 에이전트에게 "ERP 화면 캡처해서 매뉴얼 만들어줘"라고 지시하면 에이전트가 Manual Studio를 직접 드라이빙하여 10초 만에 완결.
3. **전사 시스템 개발 표준으로의 수평 전파**: 매뉴얼 스튜디오뿐만 아니라 당사의 모든 전사 시스템(기연리프트 ERP 등)에 에이전트 친화 표준을 동일 적용 가능.
