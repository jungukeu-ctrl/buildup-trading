# MyAssetDashBD — 오빌 손실상쇄 추적(OBIL Tracer) 추가 작업지시서

> 이 문서는 MyAssetDashBD 레포(별도 저장소)에 적용할 작업지시서의 보관본이다. 이 레포(buildup-trading)에서 코드를 작성하지 않는다.
> 이 문서를 읽은 Claude Code는 **"코드작성" 메시지 전까지 코드를 작성하지 않는다.**
> MyAssetDashBD의 CLAUDE.md 최우선 규칙(분석·계획·설명만, 작업 전 보고 → 승인 후 진행)을 그대로 따른다.

---

## 0. 시간 인식 규칙

날짜/시간 판단 시 반드시 `TZ='Asia/Seoul' date` 명령으로 KST 실제 시간 직접 확인. 서버는 UTC 기준이므로 항상 KST(UTC+9)로 변환.

---

## 1. 목적

알에프텍 오빌 계좌의 누적 실현손실을, 대체오빌 종목(현재 우리기술, 이전 성호전자)의 누적 실현수익으로 얼마나 "상쇄"했는지 추적하는 독립 기능. 종목은 향후 계속 교체될 수 있으므로 **특정 종목명에 고정되지 않는 구조**로 설계한다.

`sangchu` 노드 구현 패턴(SANGCHU-V1)을 그대로 재사용한다 — 독립 Firebase 노드, 동일한 카드 스타일, 동일한 모달 구조.

---

## 2. Firebase 데이터 구조 (신규, 기존 노드 절대 불변)

경로: `asset-data/obilTracer` (기존 `kiwoom-obil`/`toss-obil` 네이밍 컨벤션과 통일)

### 초기 시드 데이터 (2026-06-19 기준 확정값 — 그대로 입력)

```json
{
  "obilTracer": {
    "trackingStartDate": "2026-03-20",
    "rf": {
      "stock": "알에프텍",
      "qty": 3140,
      "avgPrice": 17557,
      "realizedPnLCum": -2769033,
      "lastUpdated": "2026-06-19"
    },
    "substitute": {
      "currentStock": "우리기술",
      "qty": 4,
      "avgPrice": 14780,
      "realizedPnLCumAllStocks": -29823,
      "lastUpdated": "2026-06-19",
      "history": {
        "성호전자": { "period": "2026-05-08~2026-06-18", "realizedPnL": -30093, "status": "종료" },
        "우리기술": { "period": "2026-06-19~", "realizedPnL": 270, "status": "진행중" }
      }
    },
    "log": {
      "2026-06-19": {
        "rfQty": 3140,
        "rfAvgPrice": 17557,
        "rfRealizedPnLCum": -2769033,
        "subStock": "우리기술",
        "subQty": 4,
        "subAvgPrice": 14780,
        "subRealizedPnLCumAllStocks": -29823
      }
    }
  }
}
```

### 필드 설명

| 필드 | 의미 |
|---|---|
| `rf.realizedPnLCum` | 알에프텍 누적 실현손익 (2026-03-20 추적 시작 기준) |
| `substitute.realizedPnLCumAllStocks` | 대체오빌 종목들의 **합산** 누적 실현손익 (종목이 바뀌어도 계속 누적됨) |
| `substitute.currentStock` | 현재 운용 중인 대체오빌 종목명 (자유 입력, 하드코딩 금지) |
| `substitute.history` | 종료된/진행중인 대체오빌 종목별 기록 |
| `log` | 세션별 스냅샷 1줄 (트렌드 조회용, append만 함) |

### 파생 계산 (저장하지 않고 매번 계산)

```js
offsetAmount = Math.max(0, substitute.realizedPnLCumAllStocks);
offsetRatio  = rf.realizedPnLCum === 0
             ? null
             : (offsetAmount / Math.abs(rf.realizedPnLCum)) * 100;
```

---

## 3. `js/firebase.js` — 함수 추가

`saveSangchuData_` / `fetchSangchuData_` 패턴을 그대로 복제. 이름만 교체.

```js
// ═══════════════════════════════════════════
//  ★ 오빌 손실상쇄 추적 — Firebase (독립 노드)
//  경로: asset-data/obilTracer
// ═══════════════════════════════════════════
function _fbObilTracerUrl() {
  return (typeof FIREBASE_URL !== 'undefined') && FIREBASE_URL &&
         FIREBASE_URL !== 'YOUR_FIREBASE_URL'
    ? FIREBASE_URL.replace(/\/$/, '') + '/asset-data/obilTracer.json'
    : null;
}

async function fetchObilTracerData_() {
  var url = _fbObilTracerUrl();
  if (!url) return null;
  var token;
  try { token = await _getValidToken_(); } catch { return null; }
  const res = await fetch(url + '?auth=' + encodeURIComponent(token));
  if (!res.ok) return null;
  return res.json();
}

async function saveObilTracerData_(data) {
  var url = _fbObilTracerUrl();
  if (!url) return;
  var token;
  try { token = await _getValidToken_(); } catch { return; }
  fetch(url + '?auth=' + encodeURIComponent(token), {
    method:  'PATCH',
    headers: { 'Content-Type': 'application/json' },
    body:    JSON.stringify(data),
  }).catch(function(err) {
    console.warn('오빌Tracer 저장 실패:', err.message);
  });
}
```

---

## 4. 카드 UI 명세

### 위치

`sangchu` 카드 바로 아래, 독립 섹션.

### 카드 표시 항목

```
┌─────────────────────────────────────────┐
│ 🎯 오빌 손실상쇄 추적          [실현손익입력]│
├──────────────┬──────────────┬──────────┤
│ 알에프텍 누적   │ 상쇄종목누적   │  상쇄율   │
│ -2,769,033원  │  -29,823원   │   0%     │
│               │ (현재: 우리기술)│          │
├──────────────┴──────────────┴──────────┤
│ 알에프텍: 17,557원 × 3,140주              │
│ 우리기술: 14,780원 × 4주                  │
│ 마지막 갱신: 2026-06-19                   │
└─────────────────────────────────────────┘
```

> ⚠️ 라벨은 항상 "상쇄종목누적"이며, 특정 종목명(우리기술/성호전자)을 라벨에 하드코딩하지 않는다. 종목명은 `(현재: {substitute.currentStock})`로만 부기.

### 색상 규칙 (기존 대시보드 스타일 유지)

- 손실(음수): 파란색 / 수익(양수): 빨간색
- 상쇄율 50% 이상: 강조 색상 (선택)

---

## 5. 입력모달 명세 — 캡쳐 → JSON 붙여넣기 방식

### 캡쳐 대상 화면

키움 "종목실현손익" 탭, 종목 필터 **"전체"**, 기간: `lastUpdated 다음날 ~ 오늘`.

### 추출 JSON 포맷 (Gemini/Claude 등 비전 AI에게 이 형식으로 뽑아달라고 지시)

```json
{
  "screenType": "종목실현손익",
  "periodFrom": "2026-06-20",
  "periodTo": "2026-06-25",
  "rows": [
    { "date": "2026-06-22", "stock": "우리기술", "pnl": 1230 },
    { "date": "2026-06-21", "stock": "알에프텍", "pnl": -45210.5 }
  ]
}
```

**규칙**:
- `pnl`은 쉼표·"원" 없는 순수 숫자, 음수는 마이너스 부호 유지
- 화면에 보이는 모든 행을 빠짐없이 포함 (다른 종목 섞여있어도 그대로 포함 — 모달이 필터링함)
- 종목명은 화면 표기 그대로 (축약·번역 금지)

### 모달 안내 문구 (사용자가 비전 AI에 그대로 붙여넣을 지시문 — 모달 상단에 고정 표시)

> "이 키움 종목실현손익 화면 캡쳐를 읽고, 보이는 모든 행을 다음 JSON 형식으로만 출력해줘. 다른 설명 없이 JSON만:
> `{"screenType":"종목실현손익","periodFrom":"YYYY-MM-DD","periodTo":"YYYY-MM-DD","rows":[{"date":"YYYY-MM-DD","stock":"종목명","pnl":숫자},...]}`"

### 모달 UI

```
┌──────────────────────────────────┐
│   오빌 손실상쇄 — JSON 붙여넣기    │
├──────────────────────────────────┤
│ [위 안내 문구 — 복사 버튼 포함]    │
│ ┌──────────────────────────────┐ │
│ │ (JSON 붙여넣기 textarea)      │ │
│ └──────────────────────────────┘ │
│         [파싱]                    │
├──────────────────────────────────┤
│ 파싱 결과 미리보기:                │
│  알에프텍 증분:      -45,210.5원  │
│  우리기술(현재) 증분:  +1,230원   │
│  ⚠ 무시된 행: 성호전자 1건       │
├──────────────────────────────────┤
│ 적용 후:                          │
│  알에프텍 누적: -2,769,033 → -2,814,243.5│
│  상쇄종목누적:  -29,823 → -28,593│
│  상쇄율: 0% → 0%                │
├──────────────────────────────────┤
│ 상쇄종목 교체:  [______________]  │  ← 비워두면 변경 없음
├──────────────────────────────────┤
│         [취소]  [확인]            │
└──────────────────────────────────┘
```

### 파싱/적용 로직

```js
function applyObilTracerJson(json) {
  const RF_STOCK = obilTracer.rf.stock; // "알에프텍"
  const SUB_STOCK = obilTracer.substitute.currentStock;

  const rfRows  = json.rows.filter(r => r.stock === RF_STOCK);
  const subRows = json.rows.filter(r => r.stock === SUB_STOCK);
  const ignored = json.rows.filter(r => r.stock !== RF_STOCK && r.stock !== SUB_STOCK);

  const rfDelta  = rfRows.reduce((s, r) => s + r.pnl, 0);
  const subDelta = subRows.reduce((s, r) => s + r.pnl, 0);

  // ── 미리보기 표시 후 [확인] 클릭 시에만 실제 적용 ──
  obilTracer.rf.realizedPnLCum += rfDelta;
  obilTracer.rf.lastUpdated = json.periodTo;
  obilTracer.substitute.realizedPnLCumAllStocks += subDelta;
  obilTracer.substitute.lastUpdated = json.periodTo;

  if (ignored.length) {
    showWarning(`무시된 행 (현재 추적 종목 아님): ${ignored.map(r => r.stock).join(', ')}`);
  }

  // log에 한 줄 append
  obilTracer.log[json.periodTo] = {
    rfQty: obilTracer.rf.qty,
    rfAvgPrice: obilTracer.rf.avgPrice,
    rfRealizedPnLCum: obilTracer.rf.realizedPnLCum,
    subStock: SUB_STOCK,
    subQty: obilTracer.substitute.qty,
    subAvgPrice: obilTracer.substitute.avgPrice,
    subRealizedPnLCumAllStocks: obilTracer.substitute.realizedPnLCumAllStocks,
  };

  saveObilTracerData_(obilTracer);
  renderObilTracer();
}
```

### 상쇄종목 교체 로직

```js
function switchSubstituteStock(newStockName) {
  const old = obilTracer.substitute.currentStock;
  if (obilTracer.substitute.history[old]) {
    obilTracer.substitute.history[old].status = '종료';
    obilTracer.substitute.history[old].period += `~${todayKST()}`;
  }
  obilTracer.substitute.currentStock = newStockName;
  obilTracer.substitute.history[newStockName] = {
    period: `${todayKST()}~`,
    realizedPnL: 0,
    status: '진행중',
  };
  // realizedPnLCumAllStocks는 그대로 유지 (전체 누적이므로 리셋하지 않음)
}
```

---

## 6. 유효성 검사

- JSON 파싱 실패 시: "JSON 형식이 올바르지 않습니다" 에러 표시, 적용 차단
- `rows`가 빈 배열인 경우: "추출된 거래 내역이 없습니다" 경고, 적용은 허용(증분 0)
- `periodFrom`이 `obilTracer.rf.lastUpdated`보다 이전이면: "이미 반영된 기간과 겹칩니다 — 중복 계산 위험" 경고 표시 (막지는 않고 경고만)

---

## 7. 제약 조건 (반드시 준수)

- 기존 `sangchu`, `state`, `kiwoom` 노드 데이터 구조 변경 금지
- 외부 라이브러리 추가 금지
- 기존 카드 디자인 스타일 일관성 유지
- 모바일 반응형 유지
- Firebase PATCH 방식 그대로 사용 (PUT 금지 — 다른 노드 삭제 위험)

---

## 8. 구현 순서

**Step 1** — Firebase `obilTracer` 노드 초기 시드 데이터 입력 (섹션 2)
**Step 2** — `js/firebase.js`에 `_fbObilTracerUrl`/`fetchObilTracerData_`/`saveObilTracerData_` 추가
**Step 3** — `js/obilTracer.js` 신규 파일 생성 (상태 로드/저장, JSON 파싱, 계산 로직)
**Step 4** — `index.html`에 오빌Tracer 카드 HTML 추가
**Step 5** — `js/modal.js`에 JSON 붙여넣기 모달 추가
**Step 6** — `css/style.css`에 카드 스타일 추가 (기존 sangchu 카드 스타일 재사용)
**Step 7** — 테스트 (아래 검증 시나리오) 후 GitHub Pages 배포
**Step 8** — `PLAN.md` 업데이트: 완료 작업 테이블에 `OBIL-TRACER-V1` 행 추가, Firebase 구조 섹션에 `obilTracer` 노드 기록

---

## 9. 검증 시나리오 (수정 후 반드시 예시 숫자로 검증)

입력 JSON:
```json
{
  "periodFrom": "2026-06-20", "periodTo": "2026-06-25",
  "rows": [
    {"date":"2026-06-22","stock":"우리기술","pnl":1230},
    {"date":"2026-06-21","stock":"알에프텍","pnl":-45210.5},
    {"date":"2026-06-20","stock":"성호전자","pnl":500}
  ]
}
```

예상 결과:
- 알에프텍 누적: -2,769,033 → **-2,814,243.5** ✅
- 상쇄종목누적: -29,823 → **-28,593** ✅ (우리기술 +1,230만 반영)
- 성호전자 행은 무시 처리, 경고 표시 ✅
- 상쇄율: `max(0,-28593)=0` → 여전히 **0%** ✅

---

## 10. 작업 시작 멘트

이 파일을 읽은 Claude Code는 이렇게 시작한다:

> "OBIL Tracer 추가 작업 파악했습니다.
> 베이스라인: 알에프텍 -2,769,033원 / 상쇄종목(우리기술) -29,823원 / 상쇄율 0%
> Step 1부터 시작하겠습니다 — 먼저 js/firebase.js의 sangchu 함수 패턴 확인할게요."
