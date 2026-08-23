# Python Patch 무음 실패와 py_compile의 한계

**날짜**: 2026-08-22
**관련**: 전사 원칙 10.2 (정규식 패치 금지), 10.3 (구문 검사 의무), 10.4 (변경사항 Before/After 검증)

## 1. 현상과 후회
- i_vision_agent.py 에 대규모 UI 코드를 주입하기 위해 str.replace() 기반의 파이썬 패치 스크립트를 사용함.
- 검색 문자열이 원본과 정확히 일치하지 않아 치환이 일어나지 않았으나, 스크립트는 에러 없이 정상 종료됨 (Silent Failure).
- 이후 전사 원칙에 따라 python -m py_compile 구문 검사를 수행했으나 **통과**함.
- 런타임에서 AttributeError: '_add_step_ui' 발생하여 앱이 강제 종료됨.

## 2. 근본 원인
- py_compile은 들여쓰기, 괄호 짝 등 **문법적(Syntax) 무결성**만 검증할 뿐, 런타임에 결정되는 특정 메서드나 변수의 존재 여부(Attribute)를 정적으로 검증하지 못함.
- 패치 스크립트 내에 치환 성공 여부를 검증하는 방어 로직(if search_str not in content: raise Exception)이 누락되어, 변경되지 않은 원본이 그대로 저장되었음.

## 3. 재발 방지 원칙 (강화)
- Python 소스를 스크립트로 패치할 때는 반드시 **치환 대상(Target Content) 존재 여부를 먼저 검증(Assert)** 하고, 실패 시 명시적으로 에러를 던져야(Throw) 함.
- 구문 검사(10.3) 통과를 맹신하지 말고, 변경 전후의 파일 해시나 라인 수 변화, 혹은 grep 등을 통해 실제 코드가 삽입되었는지 Before/After 검증(10.4)을 교차 적용해야 함.
