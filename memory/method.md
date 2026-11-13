---
name: method
description: Trading principles, rebalancing rules, 불타기 R framework, slot evaluation steps, and session workflow
aliases: [비중매매, 리밸런싱, 손익비, 슬랏평가]
---

(2026-11-12 claude.ai 프로젝트 메모리에서 이전, 2026-11-11 시점까지 반영)

## Principles

- Realized losses from rebalancing are structural costs, not failures — they lower 평단 and should be evaluated separately from position quality
- Any purchase below 평단 improves it, regardless of magnitude
- Rebalancing framing: "battle lost, war advantaged" (전투 실패, 전쟁 유리) — short-term realized loss to improve overall position is valid strategy
- Never reuse prior numerical figures without a fresh capture; slot size must be confirmed each session
- HTS "종목실현손익" screen (not the account summary "실현손익" field) is the authoritative source for cumulative realized P&L
- When search cannot confirm a market event's cause, state that directly rather than speculating
- Round end signal: creation of a Discord "비트주식 종료복기" channel for the ticker = official round closure

## Rebalancing strategy (surface proactively each session)

- Active method on 성호전자: 50/50 split-order — 50% immediate re-entry 2 ticks below current price, 50% resting limit at expected support; cancel unfilled orders before close
- Loss zone: 50/50 split fully applicable
- Sideways/횡보: apply only if the box range is sufficiently wide
- Confirmed uptrend/불타기: suspend the 50/50 method and follow 주빌•A directives

## 불타기 정의 (사용자 확정)

- 불타기는 특정 패턴 이름이 아니라 두 조건이 동시에 성립하는 상황: (1) 기준비중(1차 40%) 이상에서 추가매수로 오버비중이 되는 상황, (2) 추가매수로 평단이 올라가는 상황
- 수익 최대화를 위해 오버비중을 감수하는 구조이며, 평단 상승 리스크와 목표가 도달 확률을 함께 판단해야 함
- 불타기 케이스는 Notion 불타기 이론 & 케이스 아카이브에 누적 기록 (실행 안 한 케이스도 포함)

## 불타기 손익비 (R) framework

- R = (익절시수익) ÷ (손절시손실); as add-on quantity increases R always decreases, and the rational upper limit is where R drops below 1.0
- New 평단 = (existing shares × existing 평단 + n × add-on price) ÷ total shares
- Profit if exit = (target − new 평단) × total shares
- Loss if stop = (new 평단 − stop price) × total shares
- R = profit ÷ loss

## 비중매매 philosophy (`knowledge/02_일반매매_비중_차수_가이드.md` — permanent, never delete or summarize)

- The tier system (1차/2차/3차) is both offensive (concentrate when confident) and defensive (reduce to preserve cash and lower 평단 for a future attack) — not merely position size caps
- Key reference for position sizing decisions, uptrend signal identification, re-entry timing, and 3차 commitment decisions
- Living document — append new real cases in the format 실제상황 → 시뮬레이션 → 결론/미해결질문

## Slot evaluation methodology (일반매매 — permanent)

- Always require a fresh capture and confirm slot size with the user each session
- Step 1 — from latest balance capture: 평단 × 보유수량 = 매입금액; 현재가 × 보유수량 = 평가금액
- Step 2 — cumulative realized P&L from the dedicated "종목실현손익" inquiry screen, not the account main screen
- Step 3 — 슬랏 잔여현금 = 슬랏총액 − 현재 매입금액 + 누적 실현손익 (subtract if negative)
- Step 4 — 슬랏 기준 총 평가금 = 보유주식 평가금액 + 슬랏 잔여현금
- Step 5 — 비중(%) = 매입금액 ÷ 슬랏총액, compared against 1차 (40%) / 2차 (45%) / 3차 (65–70%)

## Session workflow

- Fetch the relevant Notion page by ID at session open to confirm current position state
- Interpret 주빌•A's Discord (디코) directive in the context of the current position
- Present a structured operational plan with conditional logic (baseline rebalancing + triggered escalation modes)
- Request an HTS screenshot or Firebase JSON to update position mid/end of session
- Update the Notion page at session end, with KST date in the record header: `최신: YYYY-MM-DD 장종료 캡쳐 / Notion 업데이트: YYYY-MM-DD KST`
- **장 중 세션 역할**: 디코 메시지 단순 요약이 아니라 현재 포지션 상황과 매매 전략·방향 논의가 중점. Notion 업데이트는 장 종료 후 한 번만.

## Order safety reminders (include occasionally in 일반매매 sessions)

- Accidental full-position sells have occurred at least twice (2026-07-31 and 2026-08-04) — on days with unusually large losses, consider order error before attributing the result to strategy failure
- Always verify that sell quantity matches intent and that no accidental full-sell/bulk-sell button was used
- 거래시간 기준(2026-09-14~): KRX 시간외 단일가(16:00~18:00)는 폐지됨. 애프터마켓은 접속매매(실시간 체결)로 KRX 16:00~20:00, NXT도 20:00까지 운영. 에프터 종가매수는 20:00 마감 전까지 가능하며, 단일가 구간이라고 안내하지 말 것. 주문은 지정가·최우선·최유리만 가능(시장가 제한), 정규장 미체결 주문은 애프터로 이월 안 됨
- 라운드 정의: 매매 시작부터 보유 전량을 수익실현(정리)할 때까지가 한 라운드이며 임의 구간으로 끊어 평가하지 않음. 비중조절·포지션조정 = 팔고 되사기 / 사고 되팔기. 시작 포지션 대비 벤치마크 비교는 의미 없다고 봄 — 알고 싶은 것은 장중 비중회전으로 평단을 낮출지 지켜볼지의 판단
- 10/07 09:07 전량 매도는 라운드 종료로 확정 (10/07 재진입부터 새 라운드)
- 주빌•A 지침의 "포지션 평단"은 각자 보유 중인 현재 잔고 평단을 뜻하며 실현된 수익금은 포함하지 않음 (라운드 실질 평단과 구분, 2026-11-04)
- 차트 판정 기준: 에프터를 포함해 차트는 장 종료 후에도 연속이고 이평선도 이어지므로, 시간대와 상관없이 이평선 기준으로 시합장(U1~D3)을 판정함. 프레임은 우선 1분봉 주력 + 5분봉 확인으로 운용해 봄 (2026-11-04)
- 공격적 리밸(상승 타이밍)은 팔사가 아니라 사팔 순서: 먼저 사서 비중을 늘리며 평단을 낮추고, 늘어난 비중은 그 추가 매수가보다 위에서 팔아 원래 비중으로 되돌림 (2026-11-06)
- 범퍼 정의: 불타기(추가매수)를 해도 새 평단이 현재 주가 대비 여전히 유리한(아래인) 상태를 뜻함 (2026-11-11, 사용자가 정정)
- 앞으로 불타기·비중 확대·매수/매도 감지 주문 대응은 범퍼를 감안해 설계: 활용할 비중 단계(2차 / 2.5차 / 3차)별로 범퍼가 충분히 확보되는 최소 매수가(또는 그 이상)와, 예상과 다르게 하락해도 정리할 본전컷(새로 형성된 평단)을 미리 계산해 사전표로 제시 (2026-11-11)
- 허용 손실 기준(사용자 확정, 2026-11-13): ① 당일 실현 수익이 있으면 그 금액 이내 ② 당일 수익이 없으면 라운드 누적 수익금 이내 ③ 라운드 누적 수익이 0보다 작으면 슬랏의 0.2% 이내(슬랏 100만원이면 2,000원). 최대 노출 수량 = 허용 손실 ÷ (평단 − 컷 가격)이며, 비중 기준(1차 등) 수량과 비교해 작은 쪽을 따른다
- 디코 실전매매교육 노션 페이지(실전매매교육_SK하이닉스_20261111, https://app.notion.com/p/41568e1fa04c81f3bf5ddc63c7a1a027)를 대응 기준으로 반영: 불리할 땐 정비(팔사)·유리할 땐 수익(불타기), 못 판 물량은 기준비중만 남기고 정리, 유리 완성 후(15번 이후) 추격매수 없이 매도만, 마침표(본전컷/SL 상향/전량매도/종가 불타기 여부)를 미리 정해둠 (2026-11-11)
