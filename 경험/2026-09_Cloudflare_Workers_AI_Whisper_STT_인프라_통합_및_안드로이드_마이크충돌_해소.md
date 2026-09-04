# ☁️ Cloudflare Workers AI Whisper STT 인프라 통합 및 안드로이드 마이크 충돌 해소

- **기록일시**: 2026-09-04 21:15
- **작성버전**: `v1.3.0.Build.136`
- **관련 프로젝트**: `Kiyuen_Lift` (`api/cf-stt.ts`, `walkieTalkieService.ts`, `MobileWalkieTalkieModal.tsx`)

---

## 1. 배경 및 기술적 난관

1. **안드로이드 OS 마이크 독점(Exclusive Access) 제약**:
   - `MediaRecorder`(음성 녹음)와 `SpeechRecognition`(브라우저 음성인식)을 동시에 실행하면, 안드로이드 OS의 Audio HAL 정책에 의해 브라우저 STT가 강제 음소거(Mute)되어 `empty (no speech captured)`가 발생하는 치명적 버그 확인.
2. **비용 0원 원칙 및 인프라 통일**:
   - Gemini 유료 API를 영구 배제하면서도, 회사가 이미 보유한 Cloudflare 인프라(`drcf`)와 일관되게 100% 무료로 고품질 음성 전사를 수행할 필요성 대두.

---

## 2. 해결 방법론 (Cloudflare Workers AI Whisper)

1. **Cloudflare Workers AI (`@cf/openai/whisper`) 도입**:
   - Cloudflare 계정(`35014a2514680107d74e1e68d96e6c32`)에 전용 Workers AI API 토큰을 발급하여 매일 10,000 뉴런(약 1,000건 이상의 무전)을 완전 무료(0원)로 활용.
   - Vercel 서버리스 프록시(`api/cf-stt.ts`)를 통해 클라이언트에 토큰을 노출하지 않고 안전하게 바이너리 오디오를 Whisper 모델로 전달.
2. **2대 업무 시나리오 완전 분리**:
   - **일반 무전기 모드**: 발언 종료 즉시 음성 파일 로컬 등록 및 0초 즉시 브로드캐스트 ➔ 백그라운드로 Cloudflare Whisper 호출 ➔ 1초 뒤 `transcript_update` 이벤트로 발신자와 수신자 화면에 자막 자동 동기화.
   - **독백의뢰 모드 (발주서 음성 작성)**: 음성 녹음기(`MediaRecorder`)를 아예 기동하지 않고 순수 브라우저 음성인식(`SpeechRecognition`)만 단독 구동하여 마이크 충돌 없이 실시간 0초 작성 보장.

---

## 3. 핵심 교훈

- **모바일 브라우저 오디오 공유 불가**: 모바일 크롬에서는 복수의 오디오 캡처 API를 동시에 띄울 수 없으므로, 파일 녹음과 STT가 모두 필요할 때는 `녹음 ➔ 백그라운드 AI Whisper 전사` 파이프라인이 정답임.
- **인프라 통합의 효익**: R2 스토리지와 Workers AI를 동일한 Cloudflare 계정에서 통일 관리함으로써 보안성, 비용 통제(0원 보장), 유지보수성을 극대화함.
