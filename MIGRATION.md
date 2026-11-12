# 이전 기록 (claude.ai Project "P. 빌드업 Trading" → Claude Code)

이전일: 2026-11-12

## 옮긴 것
- `CLAUDE.md`: 프로젝트 커스텀 지침 (세션 시작 시 자동 로드)
- `memory/`: HISTORY, index, method, preferences, system-and-workflow, tools (2026-11-11까지)
- `knowledge/`: 프로젝트 개요, 오빌 가이드, 일반매매 비중·차수 가이드, 매매 기법·실전팁, 시합장 판정·밸런싱 정석
- 계좌번호는 전부 별칭(오빌계좌 / 연습계좌)으로 치환. 레포에 실제 번호 없음.

## 아직 안 옮긴 것 (필요할 때 요청)
- Discord 자료: `Discord #매매방법 채널`, 빌드업가이드2~5(성호전자·JYP·로보티즈·우리기술), 빌드업 매매 활용방법 → 향후 `knowledge/discord/`
- Google Docs 원본 8종(상추 훈련 매뉴얼, 빌드업 통합 가이드, 6가지 시합장, 되돌림, 볼륨, 소액 검증, 선순환 계좌, 이평선과 파동), 알에프텍가이드 → Drive에 그대로 있으며 Drive 커넥터로 읽을 수 있음
- 분량이 큰 로그: 성호전자_주빌_Discord, #실전고민예시, #실전교육(글), 김현준 대표 인터뷰(Notion PARA Note에도 있음)
- `상추훈련_백테스트.html`, 리스탈 투자 일지 시트, `OBIL_TRACER_작업지시서.md`(MyAssetDashBD 레포용), 템플릿 3종
- claude.ai 프로젝트의 과거 대화는 이전되지 않음 (핵심 맥락은 HISTORY에 정리됨)

## 다른 PC(회사)에서 쓰기 — 클라우드 방식 (기본)
1. 브라우저에서 claude.ai/code 를 열고 `jungukeu-ctrl/buildup-trading` 레포를 선택해 세션 시작 (설치 불필요). `CLAUDE.md`와 `memory/`는 자동으로 읽힌다.
2. Notion 커넥터는 claude.ai 설정의 커넥터에서 켜져 있어야 한다. 세션에서 Notion 도구가 안 보이면 이 설정부터 확인.
3. 클라우드 작업 공간은 세션이 끝나면 사라진다. HISTORY·memory를 수정한 세션은 끝에 반드시 commit + push 한다 ("커밋·푸시해줘").

## (선택) 로컬 PC에서 쓰기
`git clone https://github.com/jungukeu-ctrl/buildup-trading` 후 그 폴더에서 Claude Code 실행, `/mcp`로 Notion 연결. 작업 전 `git pull`, 후 `git push`.
