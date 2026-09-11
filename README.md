# claude-tidy

[![version](https://img.shields.io/github/v/release/jeongph/claude-tidy?label=version&color=blue)](https://github.com/jeongph/claude-tidy/releases)
[![license](https://img.shields.io/github/license/jeongph/claude-tidy?color=lightgrey)](LICENSE)

작업을 마칠 때 빠진 커밋, 문서, 이슈 처리를 확인하고 다음 세션에 넘길 내용을 정리합니다.

"세션 정리해줘"라고 요청하면 커밋, 작업 이력, 문서, 이슈 갱신에서 빠진 항목을 검사합니다. 대화에서 약속한 미완료 작업과 워크트리 삭제 시 사라질 수 있는 문서도 함께 확인합니다.

## 기능

### `tidy` — 세션 정리

자연어("세션 정리해줘", "마무리 정리") 또는 `/tidy`로 실행. 프로젝트 상태를 보고 **해당되는 검사만** 자동 선별해 수행한 뒤 체크리스트로 보고하고, 승인한 항목만 처리한다.

| 체크 | 감지 조건 | 검사 내용 |
|------|----------|----------|
| `git-uncommitted` | git 레포 | 세션 변경분의 커밋·푸시 누락, stash·임시 파일 잔여물 |
| `history` | 히스토리 체계 사용 | 시스템 변경 작업의 히스토리 기록 누락 |
| `intent` | `docs/intent/` 존재 | 설계 결정·트레이드오프의 intent 문서 누락 |
| `knowledge` | `docs/knowledge/` 존재 | 새 지식의 노드 작성 누락, 기존 노드의 오래된 내용 |
| `issues` | 이슈 트래커 + 세션에서 이슈 취급 | 이슈 닫기·코멘트·신규 등록 누락 |
| `forgotten-tasks` | 항상 | 세션 초반에 하기로 했다가 컨텍스트에 밀려 잊힌 작업 |
| `worktree-migration` | git 워크트리 | 워크트리 삭제 시 유실될 비추적 문서(`.remember/`, `docs/superpowers/` 등)의 원본 이관 |

### `handoff` — 세션 인수인계

자연어("핸드오프", "다음 세션 위해 정리") 또는 `/handoff`로 실행. compact/clear 직전이나 새 세션으로 작업을 넘길 때 사용한다.

1. tidy 검사를 그대로 수행하고
2. 진행 상태·결정·진행을 막는 문제·부작용·검증 결과를 수집해
3. 프로젝트가 쓰는 기록 체계(OKF `session-state.md`, `docs/tasks.md`, 이슈 트래커, 개인 메모리)에 맞게 기록한 뒤
4. **"▶ 여기서 시작"** 한 줄이 맨 위에 오는 인계 노트를 출력한다

OKF(`docs/knowledge/`) 프로젝트에서는 History 노드 마감·resume 스냅샷·트래커 동기화 절차([okf-handoff.md](skills/handoff/references/okf-handoff.md))를 수행한다.

## 설치

```
/plugin marketplace add jeongph/claude-plugins
/plugin install claude-tidy@jeongph-claude-plugins
```

## 검사 추가하기

`skills/tidy/checks/<이름>.md` 파일 하나를 추가하면 된다. 각 체크는 **감지 조건 / 검사 절차 / 처리** 세 섹션으로 구성된다. 감지 조건 덕분에 프로젝트에 해당 없는 체크는 자동으로 건너뛴다.

## 설계 원칙

- **검사가 먼저, 처리는 확인 후** — 발견한 문제를 임의로 수정하지 않는다. 커밋·이슈 코멘트·기록은 반드시 초안을 확인받은 뒤 반영한다.
- **자동 감지** — 설정 파일 없이 프로젝트 상태(디렉토리·도구 존재)로 검사 적용 여부를 판단한다.
- **대화 내용도 확인** — Git 상태와 함께 세션에서 하기로 한 작업을 확인한다.

## 라이선스

[MIT](LICENSE)
