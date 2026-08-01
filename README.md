# claude-tidy

[![version](https://img.shields.io/github/v/release/jeongph/claude-tidy?label=version&color=blue)](https://github.com/jeongph/claude-tidy/releases)
[![license](https://img.shields.io/github/license/jeongph/claude-tidy?color=lightgrey)](LICENSE)

세션 마무리 정리와 핸드오프를 위한 Claude Code 플러그인.

작업이 끝난 것 같을 때 "세션 정리해줘" 한마디로 — 커밋 누락, 히스토리 미기록, 문서 누락, 이슈 갱신 누락, 잊힌 작업, 워크트리 유실 위험 문서까지 검사한다. 컨텍스트가 사라지면 "빠뜨렸다는 사실" 자체를 잊는다. 그 전에 잡는다.

## 기능

### `tidy` — 세션 정리

자연어("세션 정리해줘", "마무리 정리") 또는 `/tidy`로 실행. 프로젝트 상태를 보고 **해당되는 검사만** 자동 선별해 수행한 뒤 체크리스트로 보고하고, 승인한 항목만 처리한다.

| 체크 | 감지 조건 | 검사 내용 |
|------|----------|----------|
| `git-uncommitted` | git 레포 | 세션 변경분의 커밋·푸시 누락, stash·임시 파일 잔여물 |
| `history` | 히스토리 체계 사용 | 시스템 변경 작업의 히스토리 기록 누락 |
| `intent` | `docs/intent/` 존재 | 설계 결정·트레이드오프의 intent 문서 누락 |
| `knowledge` | `docs/knowledge/` 존재 | 새 지식의 노드 작성 누락, 기존 노드 staleness |
| `issues` | 이슈 트래커 + 세션에서 이슈 취급 | 이슈 닫기·코멘트·신규 등록 누락 |
| `forgotten-tasks` | 항상 | 세션 초반에 하기로 했다가 컨텍스트에 밀려 잊힌 작업 |
| `worktree-migration` | git 워크트리 | 워크트리 삭제 시 유실될 비추적 문서(`.remember/`, `docs/superpowers/` 등)의 원본 이관 |

### `handoff` — 세션 인수인계

자연어("핸드오프", "다음 세션 위해 정리") 또는 `/handoff`로 실행. compact/clear 직전이나 새 세션으로 작업을 넘길 때 사용한다.

1. tidy 검사를 그대로 수행하고
2. 진행 상태·결정·블로커·부작용·검증 결과를 수집해
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

- **검사가 먼저, 처리는 확인 후** — 발견한 문제를 임의로 수정하지 않는다. 외부로 나가는 것(커밋·이슈 코멘트·기록)은 반드시 draft 확인을 거친다.
- **자동 감지** — 설정 파일 없이 프로젝트 상태(디렉토리·도구 존재)로 검사 적용 여부를 판단한다.
- **대화 컨텍스트가 1급 입력** — git만 보지 않고 세션에서 무엇을 하기로 했는지 되짚는다.

## 라이선스

[MIT](LICENSE)
