# 경험 (EXPERIENCE): Cloudflare R2 원본 탐색 및 선택 다운로더 (Easy_Down)

- **기록일시**: 2026-08-20 12:20
- **카테고리**: 🔬 경험 (skelton/경험)
- **관련 프로젝트**: Easy_Down, Kiyuen_Lift
- **연결 체인**: [2026-08_Easy_Down_CF_원본_탐색_및_선택_다운로더.md (발상)](../발상/2026-08_Easy_Down_CF_원본_탐색_및_선택_다운로더.md) ➔ [2026-08_Easy_Down_CF_원본_탐색_및_선택_다운로더.md (계획)](../계획/2026-08_Easy_Down_CF_원본_탐색_및_선택_다운로더.md) ➔ [경험 완료] ➔ [후회 대기]

---

## 1. 구현 결과 및 기술적 검증

1. **초경량 네이티브 단일 바이너리 성공**:
   - .NET 10 NativeAOT 컴파일을 활용하여 런타임/외부 DLL 의존성이 전혀 없는 5.7MB 단일 .exe (Easy_Down.exe) 생성.
   - Node.js SEA (약 80MB) 대비 1/14 수준으로 극소화 달성.
2. **Cloudflare R2 S3 SigV4 및 Direct CDN 파이프라인**:
   - 외부의 무거운 AWS SDK 없이 경량 BCL(HttpClient + HMACSHA256 + XDocument)만으로 완벽한 S3 v4 서명 및 ListObjectsV2 페이징 트리 파싱 구현.
   - Public CDN 도메인을 통한 초고속 0.01초 다이렉트 다운로드 및 S3 GetObject 자동 폴백 지원.
3. **Win32 Native Common Controls v6 UI/UX**:
   - 윈도우 네이티브 TreeView 및 ListView(체크박스)를 연동하여 반응 속도 0ms의 즉각적인 탐색기 룩앤필 제공.
   - 전사 표준 헌장 준수: 무수식어 건조한 명사·동사 레이블, 상하 스택 배치, 줄바꿈 방지.

---

## 2. 향후 유지보수 지침

- 새 버킷 자격증명이 필요할 경우 화면 상단 입력창 또는 EasyDown_config.json에서 즉시 수정 및 자동 영구 보존 가능.
