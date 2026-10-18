# OKF 프로젝트 핸드오프 절차

`docs/knowledge/`를 쓰는 OKF(Open Knowledge Format) 프로젝트에서 handoff 단계 3(상황 기록)이 따르는 절차다. (claude-okf 플러그인의 handoff에서 이관됨)

## 3층 모델 (섞지 않는다)

| 층 | 성격 | 위치 | 갱신 |
|---|---|---|---|
| **완료 작업** | 영구·append-only | `docs/history/` (`type: History`) | 작업 단위마다 새 노드 추가 |
| **진행+남은 작업** | 살아있는 스냅샷 | `docs/knowledge/session-state.md` (`type: Handoff`) | 매 핸드오프마다 **덮어씀** |
| **외부 트래커** | 프로젝트별 SSOT | issue·notion·linear·file | 상태 전이 + 상태 코멘트 |

원칙: **완료는 아카이브(History), 진행은 스냅샷(resume), 진짜 작업목록은 트래커.** resume 노드는 "지금 상태 사진"이지 작업 대장이 아니다.

## 1) 완료 작업 마감 → History 노드

이번 세션에 완료된 작업 단위 중 `docs/history/`에 노드가 없는 것을 찾아 초안을 작성한다.

- 파일명, frontmatter, 본문(Why→How→What)은 프로젝트의 히스토리 컨벤션을 따른다. 없으면 OKF authoring 스킬의 `type: History` 양식을 쓴다.
- `timestamp`는 실제 시각을 조회해 기록한다: `date '+%Y-%m-%dT%H:%M:%S%z'`
- 초안을 작성한 뒤 커밋한다 (완료 작업이 여럿이면 작업 단위로 분리).

## 2) resume 노드 작성 / 갱신

`docs/knowledge/session-state.md`(없으면 생성, 있으면 **덮어씀**):

```markdown
---
type: Handoff
title: <프로젝트> 세션 상태
description: 진행 중·남은 작업 스냅샷 (매 핸드오프 갱신)
timestamp: <ISO8601>   # 실제 시각 조회해 기록
resource: <branch> @ <git SHA 7자리>
tags: [handoff, session-state]
status: in-progress | blocked | ready-to-resume
---

# <프로젝트> 세션 상태

## ▶ 여기서 시작
<다음 단일 액션 한 줄 — 다음 세션이 제일 먼저 읽는 곳>

## 진행 중
- <현재 작업 + 어디까지 했는지>

## 남은 작업
- [ ] <할 일> — <왜/맥락>

## 블로커 · 열린 루프
- <기다리는 것 + 어디서 확인하는지>

## 이번 세션 결정 (근거)
- <결정>: <이유>

## 함정 · 주의
- <발견한 트랩>

## 부작용 (이 세션이 만든 것)
- 브랜치/PR/태그/배포/외부 전송: <고아 방지용 목록>

## 검증 상태
- build / test / deploy: <실제 결과>

## 완료 (History 링크)
- [<작업>](../history/<파일>.md)
```

- `resource`의 `<branch> @ <SHA>`가 staleness 감지 근거다.
- 저장 시 OKF validate/update-index 훅이 있으면 자동으로 검증되고 색인된다.

## 3) 태스크 트래커 동기화

1. **감지**: `.okf/handoff.yml` 설정이 있으면 그대로 따른다. 없으면 추정한다: `gh`+GitHub 원격이면 github-issues, Notion MCP 연결 시 notion, `docs/tasks.md` 있으면 file, 아니면 none.
2. 추정 결과를 사용자에게 한 줄로 확인받는다.
3. **동기화**: 진행 중 항목의 상태 전이 + 상태 코멘트(완료분과 다음 액션 요약).
4. **외부로 나가는 코멘트는 초안을 먼저 보여주고 확인받는다** (설정 `auto: true`면 생략).

## 개인 메모리와의 경계

resume 노드(커밋·팀 공유 대상)와 개인 메모리 도구는 의존이 아니라 레이어다. 개인 메모리가 있으면 resume 노드 포인터 한 줄만 얹는다. **하드 의존을 만들지 않는다.** resume 노드가 SSOT다. 자격증명과 개인정보는 resume 노드에 넣지 않는다.
