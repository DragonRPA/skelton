# 📋 [계획] dragonrpa.co.kr 기업 공식 홈페이지 구축 계획

- **작성일시**: 2026-08-27 15:44
- **연결 발상**: [2026-08_dragonrpa_종합_시스템_구축.md](../발상/2026-08_dragonrpa_종합_시스템_구축.md)
- **목표**: B2B RPA 및 공공데이터 솔루션 전문 기업의 신뢰도 높은 표준 공식 홈페이지 구축

---

## 1. 홈페이지 주요 구성 요소
1. **네비게이션 (Header)**: 로고, 메뉴(회사소개, 솔루션, 공공데이터 연동, 문의), ERP 시스템 바로가기
2. **히어로 (Hero)**: 기업 아이덴티티 및 핵심 가치 전달, 즉시 문의 CTA
3. **회사 소개 (About Us)**: 미션, 핵심 역량, 기술력
4. **솔루션 소개 (Solutions)**:
   - RPA 업무 자동화 솔루션
   - 렌탈 자산/배차 관리 ERP 솔루션
   - 국가 공공데이터포털(data.go.kr) API 연동 솔루션
5. **실시간 인터랙티브 데모 (Showcase)**: 국세청 사업자등록 상태조회 등 공공데이터 연동 쇼케이스
6. **고객 문의 (Contact)**: 실시간 상담 신청 폼 및 이메일 연동
7. **푸터 (Footer)**: 기업 정보, 사업자등록번호, 공식 연락처, 저작권 표기

---

## 2. 기술 스택
- 프레임워크: Next.js 15 (App Router, TypeScript)
- 스타일링: Tailwind CSS
- 아이콘: Lucide-React
- 배포 대상: Vercel (도메인: www.dragonrpa.co.kr)

---

## 3. 실행 단계
1. Next.js 프로젝트 생성 및 Tailwind CSS/Lucide 아이콘 환경 세팅
2. 기업형 시맨틱 컴포넌트(Header, Hero, About, Solutions, PublicDataShowcase, Contact, Footer) 구현
3. 문의 폼 API 핸들러 및 메일 연동 준비
4. 빌드 및 반응형 테스트 검증