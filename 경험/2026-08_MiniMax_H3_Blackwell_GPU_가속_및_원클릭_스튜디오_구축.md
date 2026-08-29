# 경험: MiniMax H3 비디오 스튜디오 Blackwell GPU 가속 및 백엔드 원클릭 자동화

- **기록일시**: 2026-08-29 19:30
- **프로젝트**: `Video_Create_Studio` (MiniMax H3 비디오 생성 스튜디오)
- **버전**: `v1.0.1.Build.2`

---

## 1. 배경 및 맥락
차세대 플래그십 그래픽 카드인 NVIDIA GeForce RTX 5080 Laptop GPU (16GB VRAM, Blackwell `sm_120`) 환경에서 로컬 MiniMax H3 영상 생성 엔진(ComfyUI 백엔드)을 구축하는 과정에서, 기존 PyTorch 2.13.0+cu126 안정화 릴리즈가 `sm_120` 바이너리 커널 이미지를 미지원하여 연산 오류(`CUDA error: no kernel image is available for execution on the device`)가 발생함.

또한 사용자가 ComfyUI 서버와 GUI 앱을 개별 실행해야 하는 번거로움과 작업 실패 시 오류 원인을 알 수 없는 문제를 해결하기 위해 시스템을 통합 개편함.

---

## 2. 핵심 구현 및 해결 방법
1. **Blackwell (`sm_120`) PyTorch CUDA 13.2 생태계 구축**:
   - Compute Capability 12.0을 정식 지원하는 PyTorch 2.15+cu132 nightly 휠과 동기화된 `torchvision`, `torchaudio`를 결합하여 커널 누락 오류를 완벽히 해소하고 16GB VRAM 최대 대역폭 연산 확보.
2. **백엔드 원클릭 전자동 오케스트레이터 (`backend_orchestrator.py`)**:
   - GUI 앱 실행 시 백그라운드에서 ComfyUI 데몬을 자동 감지/기동하고, 앱 종료 시 `atexit`을 통해 리소스를 깨끗이 회수.
3. **작업 실패 진단 및 원클릭 재시도 UI**:
   - 트리뷰 및 상세창에 실패 사유, 원인 진단, 조치 가이드 3단 박스를 렌더링하고 더블클릭/버튼을 통한 원클릭 재시도 구현.
4. **공식 3단 블록 마크다운 헌장 및 실전 샘플 툴바**:
   - 공식 문서 규격 가이드(`docs/MINIMAX_H3_GUIDE_AND_RESOURCES.md`) 및 샘플 4종 프리셋, 1클릭 즉시 실행 기능 탑재.

---

## 3. 체득한 원칙 및 교훈
- **차세대 하드웨어 생태계 호환성**: 최신 아키텍처 GPU(Blackwell 등)에서는 드라이버 및 PyTorch의 CUDA 호환성(`sm_xxx`)을 사전에 철저히 검증하고, 연관 패키지(`torch`, `torchvision`, `torchaudio`)의 CUDA 릴리즈 인덱스를 100% 일치시켜야 함.
- **사용자 편익 극대화 (카테고리 1.1)**: 복잡한 백엔드 서버 기동 및 포트 관리를 사용자에게 전가하지 않고, 단일 진입점(원클릭)에서 백그라운드 데몬으로 완전 자동화할 때 시스템 효익이 극대화됨.

---

## 4. [v1.0.2.Build.3] MiniMax H3 KSampler 플로우 샘플링 및 폴링 정상화
- **진단 및 이슈**: 렌더링 32% 시점에서 KSampler가 `'ModelSamplingAdvanced' object has no attribute 'audio_scale'` 예외를 발생시키고, 폴링 루틴이 `/history`의 빈 `outputs: {}`를 완료로 오인하여 `산출물 파일 없음`으로 조기 실패 처리함.
- **해결**:
  1. 외부 `ModelSamplingSD3` 노드를 제거하고 MiniMax H3 모델 자체 내장 Discrete Flow 스케줄러(`audio_scale`)를 사용하도록 `UNETLoader` -> `KSampler` 직접 연결.
  2. `poll_execution` 조건식을 `status.completed == True` 및 `status_str != 'error'` 검증으로 강화.
  3. RTX 5080 환경에서 5-Step 실제 비디오 추론 완주 및 MP4 생성(1.04MB) 100% 검증.

---

## 5. [v1.0.3.Build.4] 77% 진행률 상한 및 WebSocket 실시간 스텝 연동
- **진단 및 이슈**: 이전 테스트 프로세스 종료 시 백엔드 단절이 발생했을 때, 폴링 루틴이 예외를 무시하고 30분 타임아웃까지 무한 대기하며 77% 상한선에 멈춰 있는 현상 규명.
- **해결**:
  1. `websocket-client`를 통해 ComfyUI WebSocket(`ws://127.0.0.1:8188/ws`) 실시간 스텝(`12/25 단계`) 및 VAE 디코딩 노드 상태 1:1 연동.
  2. 백엔드 연결 단절 시 4회 연속 실패 감지 즉시 `ConnectionError`를 표출하는 Fail-Fast 기제 도입.
  3. `imageio-ffmpeg`를 자동 연동하여 FFmpeg 후처리 엔진 가용성 100% 확보.

---

## 6. [v1.0.4.Build.5] 실시간 작업 상세 모니터 동적 파이프라인 시각화 및 자동 갱신
- **진단 및 이슈**: 영상 생성 중 하단 작업 상세 모니터에 정적 텍스트만 고정 표시되어 현재 파이프라인 진행 단계를 직관적으로 확인하기 어려운 문제 해결.
- **해결**:
  1. `app_gui.py`: 1초 주기 실시간 자동 동기화 타이머(`_tick_live_monitor`)를 탑재하여 백그라운드 진행 상태를 능동적으로 반영.
  2. 28칸 ASCII 실시간 프로그레스 바(`[████████░░░░░░░░░░░░] 45%`) 및 5단계 파이프라인(마크다운 변환 -> Qwen3-VL 인코딩 -> MiniMax DiT KSampler -> VAE 디코딩 -> MP4 최적화) 체크리스트 동적 렌더링.
  3. `[작업 삭제]`, `[작업 중단]`, `[전체 비우기]` 대장 제어 풀세트 완비.
