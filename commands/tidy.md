---
description: 세션 마무리 정리 (커밋, 히스토리, 문서, 이슈 누락 검사, 잊힌 작업과 워크트리 유실 문서 확인)
allowed-tools: [Bash, Read, Grep, Glob, Write, Edit, Task]
---

# /tidy

`claude-tidy:tidy` 스킬을 호출해 그 절차를 그대로 따른다.

요약: `checks/`의 체크들을 감지 조건에 따라 선별 수행 → 체크리스트로 보고 → 사용자가 승인한 항목만 처리.
