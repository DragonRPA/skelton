# [계획] PC Setup Automation - 순수 C 언어(Win32 Native) 전면 전환 계획

- **기록 일시**: 2026-08-29 14:12
- **프로젝트**: PC Setup Automation (HanWha)
- **기준 파이썬 버전**: 2.4.4 (Git Tag: 2.4.4-python-final)

---

## 1. 전환 배경 및 목적
1. **백신(V3) 및 EDR 오탐 0% 달성**: 파이썬 인터프리터 언패커(Unpacker) 시그니처 및 임시 폴더 해제 행위를 원천 제거하고, 정통 Win32 PE 단일 바이너리로 전환.
2. **폐쇄망 및 클린 설치 윈도우 무의존성 구동**: .NET Framework, Python, VC++ 재배포 패키지가 없는 상태에서도 실행되는 CRT 정적 링크(/MT) 독립 바이너리 구축.
3. **탐색/입력 반응 속도 극대화**: GDI 직접 캡처 및 C 레벨 바이트 슬라이딩 템플릿 매칭(5ms 이내) 탑재.

---

## 2. 모듈 분기 구조
- engine_scenario: cJSON 기반 시나리오 저장/로드/병합 및 스텝 관리
- engine_input: Win32 SendInput 및 RegisterHotKey
- engine_vision: Win32 GDI 캡처 + stb_image + 고속 픽셀 오차 분석
- engine_process: 2단계 하이브리드 프로세스/창 안전 종료
- engine_network: Native WLAN API 및 IP Helper 네트워크 제어
- ui_main: Win32 Native SysTreeView32 UI 및 다크 테마

---

## 3. 실행 단계
- Step 1: 디렉터리 분리 (Python_Version vs C_Version) 및 파이썬 베이스라인 태그 발행
- Step 2: cJSON & stb_image 기반 C 기초 라이브러리 및 공통 헤더 구성
- Step 3: 핵심 모듈 순차 개발 및 단위 셀프 테스트
- Step 4: Win32 Native GUI 통합 및 단일 PC_Setup_Automation.exe 컴파일
