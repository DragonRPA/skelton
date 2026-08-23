# 계획: Gemini 3.7 Flash 대화록 전수 재분석 및 앙상블 마스터 구축

- **기록 일시**: 2026-08-23 02:53
- **프로젝트**: ConsultParser2 (`d:\GoogleDrive\RPA 개발\01.AntiGravity\ConsultParser2`)
- **작성자**: AntiGravity AI

---

## 1. 맥락 (Context)
- 기존에 로컬 Ollama 모델(Gemma 3 12B, GPT-OSS 120B 등)로 12,986건의 STT 대화록을 파싱하여 `D:\스페이스_테스트\result_json_gemma3_12b` 및 `D:\스페이스_테스트\ensemble_master`에 산출물을 구축하였음.
- 클라우드 최상위 추론 모델인 **Gemini 3.7 Flash**로 12,986건 대화록을 재수행하여 격리된 결과물(`D:\스페이스_테스트\result_json_gemini_37flash_high` 및 `D:\스페이스_테스트\gemini_37flash\ensemble_master`)을 구축하고, 두 모델 간의 파싱 정밀도, 고장 어구 추출력, 통화유형 분류 성능을 다차원 비교 분석하기로 결정함.

---

## 2. 핵심 실행 계획 (Action Plan)

1. **Stage 1 (TXT ➔ JSON 고속 배치 파싱)**:
   - 대상: `D:\스페이스_테스트\stt_texts\*.txt` (12,986건)
   - 모델: `gemini-3.7-flash` (3중 비판 다수결 프롬프트 적용)
   - 결과 저장: `D:\스페이스_테스트\result_json_gemini_37flash_high`

2. **Stage 2 (앙상블 스키마 병합 & 4대 데이터셋 분리)**:
   - `EnsembleDatasetMerger`를 통해 100% 단일 스키마 정규화 및 마스터 데이터셋 구축
   - 결과 저장: `D:\스페이스_테스트\gemini_37flash\ensemble_master`

3. **Stage 3 (온톨로지 사전 군집화 - Taxonomy Clustering)**:
   - `TaxonomyClusterer`를 활용해 청소장비 7대 표준 계통 및 5대 조치 유형으로 어구 매핑
   - `master_taxonomy_dictionary.json` 생성

4. **Stage 4 (팩트테이블 빌드 및 인터랙티브 대시보드 HTML 발행)**:
   - `AnalyticsDatasetBuilder` 및 `InteractiveDashboardGenerator` 실행
   - `consult_analytics_fact.json`, `.csv`, `device_failure_ranking.json`, `consult_analytics_dashboard.html` 생성

5. **Stage 5 (Ollama vs Gemini 3.7 Flash 정밀 비교 분석)**:
   - 통화유형 분포, 증상/조치 추출 건수, 고유 어구 품질 비교 리포트 산출
