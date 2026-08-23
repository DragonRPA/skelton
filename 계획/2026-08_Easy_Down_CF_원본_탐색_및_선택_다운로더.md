# 계획 (PLAN): Cloudflare R2 원본 탐색 및 선택 다운로더 (Easy_Down)

- **기록일시**: 2026-08-20 12:15
- **카테고리**: 📋 계획 (skelton/계획)
- **관련 프로젝트**: Easy_Down, Kiyuen_Lift
- **연결 체인**: [2026-08_Easy_Down_CF_원본_탐색_및_선택_다운로더.md (발상)](../발상/2026-08_Easy_Down_CF_원본_탐색_및_선택_다운로더.md) ➔ [계획 완료] ➔ [경험 대기] ➔ [후회 대기]

---

## 1. 계획 개요 및 아키텍처 목표

1. **목표**: Cloudflare R2 버킷 내 전체 폴더 및 파일 트리를 탐색하고 원하는 항목을 선별 다운로드하는 초경량 단일 exe 유틸리티 개발.
2. **핵심 요구사항:
   - 런타임 의존성 없는 순수 네이티브 단일 실행 파일 (.exe)
   - 극소화된 파일 크기 (2~3MB 수준)
   - 전사 표준 헌장 준수 (무수식어 건조한 UI, 상하 스택 배치, 줄바꿈 방지)
   - S3 호환 API 및 Direct CDN 스트리밍 고속 다운로드

---

## 2. 단계별 구현 절차

1. **C# .NET 10 NativeAOT 프로젝트 구조 정립**:
   - Easy_Down.csproj (NativeAOT, Size 최적화, 불필요한 메타데이터 트리밍)
2. **Cloudflare R2 통신 엔진 (R2Client.cs)**:
   - AWS SigV4 서명 REST 구현
   - ListObjectsV2 재귀 페이징 트리 조회
   - Direct CDN / S3 GetObject 스트리밍 다운로드 엔진
3. **설정 관리자 (ConfigManager.cs)**:
   - Kiyuen_Lift 기본 자격증명 및 로컬 저장소 동기화
4. **Win32 네이티브 모던 GUI (MainWindow.cs, Win32Interop.cs)**:
   - 폴더 트리뷰, 파일 리스트뷰, 체크박스 선택, 프로그레스 바
5. **단일 바이너리 빌드 및 기능 검증 (uild.ps1)**:
   - NativeAOT x64 바이너리 컴파일 및 용량 검증
