# [경험] 최고관리자 계정 스위치(Impersonation) 패턴

## 배경
권한별 화면과 기능을 테스트할 때마다 최고관리자가 매번 로그아웃 후 해당 임직원 계정으로 재로그인해야 하는 극심한 번거로움 발생.

## 개념 및 원칙
- **개발/운영 편의성 확보**: 다중 권한 시스템을 설계할 때는 초기 설계 단계부터 개발/운영 편의성을 위해 최고관리자용 계정 스위치(Impersonation) 아키텍처를 표준(Skelton)으로 포함하여야 한다.
- **세션의 분리**: 원래의 관리자 권한을 영구 상실하지 않도록 물리 세션(Original Admin)과 논리 세션(Current View User)을 분리 저장하는 것이 핵심이다.
- localStorage('auto_user')는 물리적 로그인을 유지하는 쿠키 역할, sessionStorage('user')와 currentUser는 현재 탭의 논리적 뷰포트로 정의.

## 구현 패턴 (React Context 기반)
1. **상태 관리**: switchUser 함수 호출 시, 현재 사용자가 ADMIN일 경우 전환 전 original_admin_user를 sessionStorage에 백업.
2. **UI 노출**: currentUser.role === 'ADMIN' 또는 original_admin_user가 존재하는 경우, 글로벌 헤더 프로필 영역에 사용자를 선택할 수 있는 <select> 드롭다운 제공.
3. **즉각적 반영**: 사용자 선택 시 페이지 새로고침 없이 setCurrentUser를 통해 React Context 하위 트리 전역에 권한 및 화면 뷰를 즉시 반영.