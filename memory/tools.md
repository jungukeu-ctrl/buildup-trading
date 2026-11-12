---
name: tools
description: Notion pages and update pattern, Firebase/web app details, and key project reference files
aliases: [Notion MCP, Firebase, OBIL Tracer, MyAssetDashBD]
---

(2026-11-12 claude.ai 프로젝트 메모리에서 이전. 이전 시점 기준 파일 위치로 갱신)

## Notion MCP

- 오빌계좌 성적 page ID: `3b368e1fa04c81938a1bdb388654056c` — tracks snapshot history and cumulative offset summary
- 성호전자 일반매매 page ID: `3b368e1fa04c813ab14ef8675b1e5e53`
- Top-level 빌드업 Trading 매매기록 page: `https://app.notion.com/p/3b368e1fa04c8123b943e30659e5b2cf`
- 불타기 이론 & 케이스 아카이브 page ID: `3df68e1fa04c81999d90fe92430e3bff` — 실거래 케이스 누적 후 귀납적으로 이론 정리하는 살아있는 문서
- Update pattern: always `notion-fetch` first → copy exact strings → use as `old_str` in `notion-update-page` with `update_content` and `old_str`/`new_str` pairs
- Exact character matching required — preserve escaped chars (e.g. `\\~`), Korean text, whitespace, and callout syntax precisely
- Passing a full Notion URL as the `id` parameter works for fetching page properties

## Firebase / web app

- Firebase project: `my-asset-dashboard-9e6f9`; DB URL: `https://my-asset-dashboard-9e6f9-default-rtdb.firebaseio.com`
- Dashboard: `https://jungukeu-ctrl.github.io/MyAssetDashBD/`
- "Claude에 공유" button copies Firebase state as JSON for pasting into chat
- OBIL Tracer feature spec exists (`specs/OBIL_TRACER_작업지시서.md`, MyAssetDashBD용 보관본); Firebase writes require authenticated tokens from the deployed app

## Key project files (저장소 내 위치)

- `knowledge/02_일반매매_비중_차수_가이드.md` — 비중·차수 프레임워크와 실전 사례 (일반_비중매매_가이드.md 통합본)
- `knowledge/01_오빌_매매_가이드.md` — 오빌 정의 및 매도 트리거, distinct from 상추훈련 rules
- `knowledge/03_매매_기법_및_실전팁.md` — 1-2-3 박스, 보합권 매매, 수익 극대화 팁, 계좌 관리 포인트
- `knowledge/00_프로젝트_개요.md` — 두 계좌의 목적과 매매 방식 3유형
- `knowledge/시합장_판정_및_밸런싱_정석.md` — 시즌4 시합장 U1~D3 판정과 밸런싱
- Discord 교육자료·로그(매매방법 채널, 빌드업가이드2~5, 활용방법, 로그)는 Notion "자료 보관" 페이지 (링크는 `MIGRATION.md`/`memory/index.md`); 알에프텍가이드는 Drive
- `tools/상추훈련_백테스트.html` — 상추훈련 백테스트 도구 (JSON 로드)
- `specs/OBIL_TRACER_작업지시서.md` — MyAssetDashBD용 작업지시서 보관본
- 그 외 Discord 로그 전문과 인터뷰 등 미이전 자료는 `MIGRATION.md` 참고
- `restyle_state.md` (GitHub: `jungukeu-ctrl/MyAssetDashBD`) — 대시보드 쪽 세션 연속성 파일
