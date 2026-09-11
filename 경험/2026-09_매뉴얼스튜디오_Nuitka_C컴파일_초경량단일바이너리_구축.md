# 경험: 매뉴얼 스튜디오 Nuitka C-컴파일 및 초경량 단일 바이너리 구축

## 1. 일시 및 개요
- **일시**: 2026-09-11 13:45
- **프로젝트**: Manual Studio (매뉴얼 스튜디오)
- **주제**: Python GUI 프로그램을 Nuitka를 통해 순수 C 언어로 트랜스파일 및 MinGW64 GCC 15.2.0 네이티브 머신 코드로 컴파일하여 역공학 방지 및 52% 경량화(27.97 MB) 달성

## 2. 해결한 기술적 난관 및 체득 원칙
1. **Windows 한글 사용자 계정명 경로와 depends.exe latin1 깨짐 결함**:
   - Nuitka가 DLL 의존성을 분석할 때 외부 도구(`depends.exe`)의 출력을 `latin1`으로 파싱하면서 `C:\Users\이정용\...` 경로가 `\x04\x04...` 제어문자로 손상되어 크래시 발생.
   - 순수 파이썬 PE 분석기(`--experimental=force-dependencies-pefile`)로 전환하여 외부 프로세스 stdout 인코딩 훼손 위험을 근본 차단함.
2. **MinGW 링커 ld.exe의 비-ASCII 라이브러리 경로 탐색 실패**:
   - `python311.lib`를 참조하는 `-L` 경로에 한글이 포함되면 MinGW ld.exe가 라이브러리를 찾지 못함 (`cannot find -lpython311`).
   - 순수 ASCII 경로(`C:\nuitka_libs`)에 라이브러리를 캐싱하고 Nuitka Scons 빌드 스크립트에 fallback 경로를 주입하여 해결.
3. **GCC LTO의 임시 심볼 파일 경로 결함**:
   - Link-Time Optimization 수행 시 임시 파일이 한글 사용자 `%TEMP%`에 생성되어 `lto1.exe`가 심볼 파일을 열지 못함.
   - `--lto=no` 및 순수 ASCII 임시 폴더(`C:\ManualStudioBuild\temp`)를 지정하여 빌드 속도 향상 및 완전 무결 컴파일 달성.

## 3. 결과 및 효익
- **용량**: PyInstaller 58.7 MB ➔ Nuitka C-컴파일 **27.97 MB** (52% 절감).
- **보안성**: 바이트코드가 아닌 C 기계어로 번들링되어 디컴파일러 무력화.
- **UI/UX**: 콘솔창 없이 미려한 단일 GUI 바이너리로 구동.
