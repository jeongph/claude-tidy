# claude-tidy 설계

날짜: 2026-07-23

## 목적

세션 마무리 시점의 반복 작업 — 누락 검사(커밋·히스토리·문서·이슈·잊힌 작업·워크트리 문서)와 인수인계 — 를 자연어/커맨드로 실행하는 Claude Code 플러그인. 검사 항목은 계속 늘어날 예정이므로 확장 비용이 낮아야 한다.

## 구조: 체크 모듈 방식

```
claude-tidy/
├─ .claude-plugin/plugin.json
├─ commands/
│  ├─ tidy.md          # /tidy — 스킬 호출 래퍼
│  └─ handoff.md       # /handoff — 스킬 호출 래퍼
├─ skills/
│  ├─ tidy/
│  │  ├─ SKILL.md      # 오케스트레이션: 감지 → 실행 → 보고 → 승인 후 처리
│  │  └─ checks/       # 검사 항목 1개 = 파일 1개
│  └─ handoff/
│     ├─ SKILL.md      # tidy 재사용 + 상태 수집 + 기록 + 인계 노트
│     └─ references/okf-handoff.md   # OKF 전용 절차 (claude-okf에서 흡수)
└─ README.md
```

검토한 대안: (A) 스킬 2개 단일 파일 — 체크가 늘수록 비대해지고 tidy/handoff 간 중복. (C) 커맨드 인자 방식 — 자연어 트리거 불가. B(체크 모듈)를 선택: 새 검사 추가 = 파일 하나 추가.

## 체크 파일 계약

각 `checks/*.md`는 **감지 조건 / 검사 절차 / 처리** 세 섹션. tidy 스킬은 감지 조건을 평가해 해당되는 체크만 수행한다 (자동 감지 — 설정 파일 없음). 해당 없는 체크는 보고서에서 생략한다.

## handoff와 claude-okf의 관계

claude-okf의 handoff 스킬·커맨드를 이 플러그인이 **흡수**한다:

- 범용 절차(필요 판단 → tidy → 상태 수집 → 기록 → 인계 노트)는 `skills/handoff/SKILL.md`
- OKF 전용 절차(3층 모델, History 노드 마감, `session-state.md` resume 스냅샷, 트래커 동기화)는 `references/okf-handoff.md`로 이관하고, `docs/knowledge/` 감지 시에만 수행
- claude-okf에서는 handoff 스킬·커맨드를 제거하고 버전 범프

## 안전 원칙

- 검사가 먼저, 처리는 사용자 승인 후. 외부로 나가는 내용(커밋·이슈 코멘트·기록)은 draft 확인 필수
- 커밋되는 기록에 개인·비공개 정보 금지
- 워크트리 이관 시 원본과 충돌하는 파일은 덮어쓰지 않고 diff 보고
