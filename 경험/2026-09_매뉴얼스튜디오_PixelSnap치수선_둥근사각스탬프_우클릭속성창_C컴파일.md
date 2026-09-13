# 매뉴얼 스튜디오: PixelSnap 치수선, 둥근 사각 스탬프, 우클릭 속성창 및 C-컴파일 최적화

- **일시**: 2026-09-13 13:05
- **버전**: v1.4.0.Build.21
- **레포지터리**: `DragonRPA/Manual-Studio`

---

## 1. 개요 및 배경

UI/UX 매뉴얼 작성 시 요소 간 여백과 크기 규격을 직관적으로 표현하는 PixelSnap 스타일의 정밀 치수선(`DimensionLineItem`) 도구를 구축하고, 기존 원형 일변도였던 숫자 스탬프에 모서리가 둥근 사각형(Rounded Rect) 형태를 추가했습니다. 또한 캔버스에 삽입된 모든 객체를 우클릭하여 속성(좌표, 크기, 글꼴, 선색, 배경색, 글자색)을 열람·수정하고 전역 기본 설정으로 즉시 동기화할 수 있는 통합 다이얼로그(`ItemPropertiesDialog`)를 전사 13개 언어로 완성했습니다.

컴파일 과정에서는 Nuitka가 보조 OCR 라이브러리의 정적 `import`를 타고 PyTorch 등 수 기가바이트의 머신러닝 생태계를 번들링하려던 병목을 진단하고, 동적 로딩(`importlib`) 및 `--nofollow-import-to` 플래그로 격리하여 1분 30초 초고속 C-컴파일을 확립했습니다.

---

## 2. 핵심 구현 및 발견 사항

### 2.1 PixelSnap 스타일 치수선 (`DimensionLineItem`)
- **자동 직교 판정 및 Shift 잠금**: 시작점과 끝점의 가로/세로 차이를 비교하여 수평/수직 치수선을 자동 결정하며, `Shift` 키 입력 시 1:1 직교 라인으로 강제 잠금.
- **양끝 수직 틱(`├ ─ ┤`) & 중앙 캡슐 뱃지**: 양 끝에 직각 틱 바운더리를 렌더링하고, 중앙에 둥근 캡슐 뱃지로 거리 수치(`[ 320 px ]`, `[ 16 dp ]`)를 가독성 높게 표시.

### 2.2 숫자 스탬프 "모서리가 둥근 사각형" 바탕 추가
- `shape: "circle" | "rounded_rect"` 지원: 둥근 모서리 사각형 본체와 은은한 드롭 섀도우를 QPainterPath로 정밀 렌더링.
- 원거리 클릭 오차를 방지하는 사각 바운딩 히트테스트 확장.

### 2.3 캔버스 객체 우클릭 컨텍스트 메뉴 & 속성 편집기 (`ItemPropertiesDialog`)
- **우클릭 컨텍스트 메뉴**: 기존 즉시 삭제 동작을 안전하게 개편하여 `속성... (P)`, `맨 앞으로`, `맨 뒤로`, `삭제 (Del)` 메뉴 제공.
- **통합 속성 편집**: 객체 좌표(X, Y), 크기(Width, Height, 선두께), 스탬프 모양(원형 vs 사각), 글꼴(서체, 크기, 굵기), 텍스트 내용, 선색, 배경색, 글자색을 다이얼로그 1곳에서 인라인 편집.
- **"기본 설정에 반영" 체크박스**: 수정된 스타일 속성을 애플리케이션 전역 `config.json`의 기본값으로 즉시 영구 저장.

### 2.4 C-컴파일(Nuitka) 지연 병목 진단 및 해결 (E-098)
- **증상**: Nuitka 빌드 시 5GB 메모리 점유 및 20분 이상 컴파일 중단.
- **원인**: `from rapidocr_onnxruntime import RapidOCR` 정적 구문으로 인해 Nuitka가 시스템의 PyTorch(38개 DLL), OpenCV, ONNXRuntime을 재귀 분석.
- **해결**: `importlib.import_module()` 동적 로딩으로 전환하고 `build_c.bat`에 `--nofollow-import-to=torch,torchvision,cv2,onnxruntime,rapidocr_onnxruntime,matplotlib,pandas,scipy`를 추가.
- **결과**: 메모리 5GB ➔ 328MB 급감, 1분 30초 만에 48MB 단일 실행 파일 빌드 성공.

---

## 3. 검증 결과

- **단위 테스트**: 54개 전 항목 100% PASS (`test_core_engine.py`).
- **바이너리 빌드**: `ManualStudio.exe` (48.6MB) C-컴파일 완료 및 `--cli status` 정상 구동 확인.
- **Git 형상 관리**: 원격 저장소(`origin/main`) 푸시 완료 (`5718f08`).
