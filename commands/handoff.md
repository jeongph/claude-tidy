---
description: 세션 인수인계 (정리 검사 후 진행 상태를 기록하고 다음 세션 인계 노트 생성)
allowed-tools: [Bash, Read, Grep, Glob, Write, Edit, Task]
---

# /handoff

`claude-tidy:handoff` 스킬을 호출해 그 절차를 그대로 따른다.

요약: 핸드오프 필요 판단 → tidy 검사 재사용 → 세션 상태 수집 → 프로젝트 기록 체계(OKF, tasks.md, 트래커, 개인 메모리)에 상황 기록 → "▶ 여기서 시작" 인계 노트 출력.
