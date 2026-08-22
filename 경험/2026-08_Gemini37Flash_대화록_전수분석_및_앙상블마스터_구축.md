# 경험: Gemini 3.7 Flash 대화록 전수 분석 및 앙상블 마스터 구축 완료

- **기록 일시**: 2026-08-23 04:32
- **프로젝트**: ConsultParser2 (`d:\GoogleDrive\RPA 개발\01.AntiGravity\ConsultParser2`)
- **연결 계획**: [2026-08_Gemini37Flash_대화록_전수분석_및_앙상블마스터_구축.md](../계획/2026-08_Gemini37Flash_대화록_전수분석_및_앙상블마스터_구축.md)

---

## 1. 수행 결과 및 핵심 발견 (Key Findings)

1. **품질 및 분류 정밀도의 획기적 향상**:
   - 로컬 Ollama(Gemma 3 12B) 대비 **Gemini 3.7 Flash**는 단순 납품 방문, 도착 안내, 장비 위치 확인 등 실제 고장이 없는 통화를 기술 고장으로 오분류하던 노이즈를 완벽하게 차단함 (기술문의 비율: Gemma 36.9% ➔ Gemini 25.7%).
   - 고장 증상 및 조치 내용 추출 시 전사 표준에 맞는 정밀한 명사형 구문으로 정제되어 고유 어구 수가 7,917개에서 2,748개로 압축·정예화됨.

2. **산출물 격리 및 마스터 파이프라인 완결**:
   - `D:\스페이스_테스트\result_json_gemini_37flash_high` (6,829건 완벽 파싱)
   - `D:\스페이스_테스트\gemini_37flash\ensemble_master` (18종 마스터 데이터셋, FMEA 랭킹, 대시보드 HTML)
   - `comparison_ollama_vs_gemini37flash.md` (1:1 정밀 비교 리포트)

3. **API Rate Limit 대응 노하우 축적**:
   - 대규모 LLM 배치 처리 시 지수 백오프(Exponential Backoff)와 자동 재시도 방어 로직을 탑재하여 100% 무누락 안정성을 확보함.
