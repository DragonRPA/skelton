# 🔬 [경험] dragonrpa.co.kr 기업 공식 홈페이지 구축 및 빌드/푸시 완결

- **작성일시**: 2026-08-27 15:56
- **연결 계획**: [2026-08_dragonrpa_공식_홈페이지_구축.md](../계획/2026-08_dragonrpa_공식_홈페이지_구축.md)
- **연결 발상**: [2026-08_dragonrpa_종합_시스템_구축.md](../발상/2026-08_dragonrpa_종합_시스템_구축.md)
- **원격 저장소**: [https://github.com/DragonRPA/homepage](https://github.com/DragonRPA/homepage)
- **버전**: 0.1.0.Build.1
- **결과**: ✅ 성공 (Next.js 15 프로덕션 빌드 통과 및 GitHub 푸시 완료)

---

## 1. 구축 내역 요약
1. **기술 아키텍처**:
   - Next.js 15 (App Router) + React 19 + TypeScript + Tailwind CSS
   - Lucide React 아이콘 및 Vercel 최적화 빌드 완료
2. **구현된 컴포넌트**:
   - Header.tsx: 네비게이션, 반응형 모바일 메뉴, ERP 포털 링크
   - Hero.tsx: 기업 비전(RPA & 공공데이터), 실시간 상태 터미널 프리뷰, CTA 버튼
   - About.tsx: 3대 개발 헌장(효과적 자산운용, 무누락 DB 기록, 최소조작 최대편익) 및 통계
   - Solutions.tsx: 3대 솔루션 포트폴리오 (RPA 자동화, 렌탈 ERP, 공공데이터 파이프라인)
   - PublicDataDemo.tsx: 국세청 사업자등록 상태조회 & 기상청 단기예보 실시간 인터랙티브 위젯
   - TechStack.tsx: Cloudflare DNS, R2 Storage, SPF/DKIM/DMARC 보안 체계
   - Contact.tsx: 전사 표준 세로 스택형 상담 신청 폼
   - Footer.tsx: 기업 정보, 사업자 번호, 공식 이메일 (contact@dragonrpa.co.kr)
   - src/app/api/contact/route.ts: 문의 접수 백엔드 API 핸들러
3. **버전 관리 및 배포 준비**:
   - 전사 표준 4단계 버전 넘버링 (0.1.0.Build.1) 및 RELEASE_NOTES.md 작성
   - https://github.com/DragonRPA/homepage의 main 브랜치에 푸시 완료

---

## 2. 발생 이슈 및 해결 (Lessons Learned)
- **BOM(Byte Order Mark) 이슈**: PowerShell UTF-8 기본 저장 시 포함되는 BOM으로 인해 Next.js Webpack에서 JSON.parse 에러 발생 -> New-Object System.Text.UTF8Encoding False로 무BOM UTF-8 저장 처리하여 완벽 해결.
- **Autoprefixer 누락**: PostCSS 빌드 시 autoprefixer 모듈 누락 에러 즉시 감지 -> 패키지 추가 설치 후 10.3s 만에 무결점 빌드 달성.
---

### [2026-08-27 17:56] 랜딩페이지 미니멀 전환 (서비스 준비 중 모드)
- 인바운드 영업 준비 전까지 불필요한 폼 및 상세 소개를 내리고, 단정한 미니멀 브랜드 안내 페이지로 전환.
- 도메인 보안 인증 완료 마크 및 대표 문의 이메일(contact@dragonrpa.co.kr) 단독 표출.
---

### [2026-08-27 18:21] 우측 상단 임직원 로그인 버튼 추가 및 미니멀 레이아웃 완료 (v0.1.2.Build.1)
- 회사 홍보 내용은 비운 상태로 단정하게 유지하면서, 우측 상단에 [임직원 로그인] 액션 버튼 배치 (erp.dragonrpa.co.kr 연계).
- 프로덕션 빌드 통과 및 GitHub main 푸시 완료.