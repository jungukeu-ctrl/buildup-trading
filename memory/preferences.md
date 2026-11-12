---
name: preferences
description: How the user wants Claude to behave in the 빌드업 Trading project (Notion update timing, answer style)
---

(2026-11-12 claude.ai 프로젝트 메모리에서 이전, 2026-11-05 시점까지 반영)

- Notion trading-record pages get updated once after market close (장 종료 후 한번에), not intraday; during market hours help in conversation only
- 실현손익 계산은 Notion에 마지막으로 기록한 날짜의 누적 종목 실현손익을 기준선으로 두고 이후 날짜별 행만 더함. 키움 화면의 누적 합계는 조회 시작일에 따라 바뀌므로 기준으로 쓰지 않음
- 투자 실적 정의: 전체 실적 = 오빌계좌 + 일반계좌 실현손익 합, 계좌 실적 = 그 계좌 모든 종목의 누적 실현손익 합. 종목 이력은 계좌 실적의 구성 내역. 남성 상쇄매도도 계좌 실적에 포함, 기준선은 Notion에 이미 기록된 누적값
- 새 대화에서 "상쇄현황 보여줘"라고 하면 기존 아티팩트 '상쇄 현황' 대시보드(https://claude.ai/artifact/BQBu8oGn2GNrfYA6MAFmua)를 최신 수치로 갱신한 상태로 보여줄 것. 갱신 기준은 Notion 두 계좌 성적 페이지의 최신 값이고, 같은 URL로 재게시해서 링크 유지 (claude.ai 전용 기능 — Claude Code에서는 수치만 요약해 답한다)
- 차트 이미지를 보내면 단순히 "사고 팔라"는 의견이 아니라 실제 주가 흐름(가격대, 박스, 체결 가능성)을 반영한 세부 전략으로 이야기해줄 것. 주가 흐름에 맞지 않는 제안은 발생 확률이 낮다고 봄
- 회전매매용 차트를 보내면 단기 구간과 중기 구간을 나눠서 안내해줄 것 (매도가·재매수가·수량)
- 매매 판단·평가 시 라운드 실현손익보다 계좌 누적 실현손익을 우선 기준으로 삼음 (2026-10-28)
- 키움 평단은 매도해도 안 변하고 낮은 가격에 되사야 내려간다는 설명, 같은 가격 회전만으론 평단이 안 내려간다는 설명, 반등 때 높게 팔고 낮게 되사며 며칠에 나눠 하라는 뻔한 조언은 이미 잘 알고 있으니 앞으로 생략 (2026-11-04)
- 답변은 짧게, 두괄식으로: 질문의 핵심 답 + 매매 방향을 먼저 주고 추가 설명은 사용자가 물을 때만. 장중에는 빠른 판단이 필요하고 답이 길면 읽는 시간이 피로함 (2026-11-06)
- 이 프로젝트의 HISTORY(메모리 기록)는 Claude Code 세션에서만 수정함. claude.ai 챗에서는 HISTORY를 직접 고치지 않고 "HISTORY 반영 요청문"만 작성해 Code 세션에 붙여넣음 (2026-11-12)
