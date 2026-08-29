# [계획] MiniMax H3 로컬 독립실행 영상 생성 스튜디오 구축 계획서

- **기록일시**: 2026-08-29 14:05
- **연결문서**:
  - 발상: [2026-08_MiniMax_H3_로컬_독립실행_영상생성_스튜디오.md](../발상/2026-08_MiniMax_H3_로컬_독립실행_영상생성_스튜디오.md)

---

## 1. 개요 및 추진 목표
- **목표**: MiniMax H3 비디오 생성 DiT 엔진을 로컬 환경에서 프롬프트 자동화부터 렌더링, 오디오 후처리까지 원클릭으로 완결하는 독립실행 어플리케이션 구축.
- **주요 파이프라인**:
  1. `prompt_refiner.py`: Ollama / 로컬 SLM 연동 MiniMax H3 마크다운 프롬프트 자동 변환기.
  2. `comfy_workflow_engine.py`: Headless ComfyUI API 통신, SageAttention v2 노드 주입 및 FP8 Scaled 모델 오프로딩.
  3. `post_processor.py`: FFmpeg 오디오 추출/재합성 및 H.264/AV1 최적화 인코딩.
  4. `task_queue_manager.py`: SQLite 기반 비동기 태스크 큐 및 오케스트레이션 상태머신.
  5. `app_gui.py`: 전사 시스템 개발 표준 헌장 [카테고리 III] 준수 데스크톱 UI.

---

## 2. 단계별 추진 로드맵
1. **[1단계] 코어 엔진 및 프롬프트 변환기 검증 (완료)**:
   - MiniMax H3 마크다운 템플릿 정립 및 Ollama SLM 변환 테스트 완료
   - FFmpeg 기반 오디오 분리/재합성 및 썸네일 생성 모듈 구현 완료
2. **[2단계] 워크플로우 오케스트레이션 및 큐 매니저 연동 (완료)**:
   - ComfyUI API 통신 클라이언트 및 SageAttention v2 노드 그래프 생성기 구현
   - SQLite 기반 태스크 대장 및 백그라운드 워커 스케줄러 구현
3. **[3단계] 데스크톱 UI 및 CLI 통합 (완료)**:
   - 카테고리 3.1, 3.2, 3.4 표준을 100% 준수한 모던 UI 및 통합 실행 진입점 `main.py` 구축
