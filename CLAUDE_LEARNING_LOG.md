# CLAUDE LEARNING LOG — 운영실장 시스템 line-level 인벤토리

> 목적: 대화 모델 Claude가 운영실장(운영실장)의 전사 운영 시스템을 정확히 이해하도록, 클로드 코드가 **코드·시트에서 직접 추출**한 line-level 인벤토리 + 데이터 흐름 + 운영 시나리오를 **누적**한다.
> 사용법(대화 Claude): 이 파일을 web_fetch로 읽고, ❓ 항목은 운영실장에게 질문, ⚠ 항목은 검증 요청.
> 누적 규칙: 덮어쓰지 않고 Day N 섹션 append. 정정은 해당 항목에 `[정정 Day N]` 표기.

## 범례 (모든 항목 필수 마킹)
- ✅ **확인**: 코드/시트/DB에서 직접 읽음
- ⚠ **추측**: 코드/데이터 + 추론 (운영실장 검증 필요)
- ❓ **질문**: 코드/데이터에 없음 — 운영실장 답 필요

---

# 16-노드 매핑표 (재구성 — PDF 미접근, ⚠/❓)

❓ **Q0. "PDF OS 구조도"가 작업환경에 안 들어왔습니다.** Drive 검색(구조/OS/아키텍처+pdf)에도 없음. → PDF를 Drive에 업로드하거나 **16개 노드 이름 목록**을 주시면 아래 재구성과 1:1 대조하겠습니다. 지금은 작업지시서 a~i 분류 + 기존 인벤토리(MASTER_PLAN/SYSTEM_INVENTORY/SCRIPT_IDS)로 재구성.

| 그룹 | 노드(시스템) | Apps Script ID | 핵심 시트 ID | 상태 | 담당(추정) |
|---|---|---|---|---|---|
| a | 운영실 대시보드 (P1~P19 + KPI Snapshot) | `1UlH7sYMBcUf…`(18파일) ✅ | BI통합 `1tKr70…` ✅ | 🟢 라이브(06-22 수정) ✅ | 운영실장 ⚠ |
| b | VOC + 리뷰 인사이트 | VOC주간 `1yLUyT…`(95줄)✅ / 리뷰 `1IqYaH…`(5파일)✅ / FIC본체 `1P18V…` ❓미확보 | VOC SZI `1Ve6…`✅ CON `1RL…`✅ / 리뷰소스 `1CbmPsm…` ⚠ | VOC sync 🟢(19,769행) / 리뷰 ❌미이주 | 운영실장/CS ⚠ |
| c | 회계 센트럴 + 지점별 회계 | CON센트럴 v5.2 `bwyu-1`(3)✅ / SZI v4 `bwyu-5`(1)✅ / 지점 푸드코스트 v4.2 `bwyu-3`(4)✅ | 9 회계시트 ✅ | 추적로그 🟢(8,090행) / 센트럴 ❌ | 회계담당(회계담당) ✅(코드주석) |
| d | HR센트럴 + 지점로스터 + 급여계약 | HR `1hEq11…`(7)✅ / 로스터 `1VD6p0p…`(18,5886줄)✅ | HR 2시트 / 로스터 18시트 ✅ | ❌미이주 | HR/회계담당 ⚠ |
| e | 매출대시보드 + 지점OP + 시간별 | (a 대시보드가 렌더) | 9 OP시트 ✅ + BI `1tKr70…` | 🟢 fact_sales_daily 820행+7신규컬럼 ✅ | 매장매니저 입력 ✅ |
| f | 운영실 비용결산 + 인력배치 | ❓미식별 | ❓ | ❓ | 운영실장 ⚠ |
| g | 위생 HACCP PWA + SOP + 매뉴얼 | 위생 `1URkxkZb…`(24)✅ / 매뉴얼 `1PSQGdGZ…`(2)✅ / 라운드3 `1QwUURch…`(9)✅ | 매뉴얼 `1apm9eR9…`✅ | ❌미이주 | 매니저/운영실장 ⚠ |
| h | 레시피+이론원가 + 판매량 | 판매량 `1HbgElbaivhU…`(17)✅ | 판매량 `1Gf2POwr…`✅ | ❌미이주 | 셰프/회계 ⚠ |
| i | 마케팅 13시트 | (마케팅 BI 설계 `1ll3vT…`) ⚠ | 13시트(KPI/META/카카오/시딩/CRM/상위노출)✅ | ❌미이주 | 마케팅팀장 ⚠ |

❓ **Q1. 노드 f(운영실 비용결산 + 인력배치 모듈)** — 별도 Apps Script/시트가 있나요? 코드/Drive에서 아직 못 찾음. scriptId 또는 시트 ID 알려주세요.
❓ **Q2. VOC FIC 본체(`1P18V_JfgbTG…`, MASTER_PLAN상 6파일)** — 현재 VOC 시트엔 95줄 Slack 스크립트만 바운드. 이 6파일 본체는 폐기됐나요, 아니면 별도 위치인가요?

---

# Day 1 — 노드 a: 운영실 대시보드 (P1 line-level)

## a-1. 시스템 개요 ✅
- scriptId `1UlH7sYMBcUfTOap72kcaODkPv7zW8gbBVkD3_0Oww1Q55uN4JPq4ZBfk` (clasp clone 완료) ✅
- 성격: **HTML 단일 페이지 BI 출력** (Apps Script 웹앱 + Drive 캐시). 25 챕터 + Chart.js 다수. ✅
- 파일 18개 ✅: `Code.js`(22,014줄, ctx 빌더+라우팅+sync+AI) / 렌더챕터 `gd_p1_overall`(707)·`ge_p5~p10`(945)·`gb_p11_reviews`(463)·`gc_p13_scorecard`(390)·`gf_p18~m12`(1910)·`gh_m0~m9_marketing`(1544)·`gi_exec_summary`(160)·`ga_render_helpers`(74) / 브리지(Supabase) `bridge_a_sync_sales`·`bridge_a_sync_accounting`·`bridge_c_sync_voc_haccp_discount`·`bridge_z_helpers`·`bc_sync_helpers`·`be_sync_op`·`bf_sync_monthly` / `bb_health`·`bd_triggers`
- ⚠ **챕터→파일 매핑**(파일명 기준): P1=gd / P5~P10=ge / P11=gb / P13=gc / P18·M12=gf / M0~M9=gh / ExecSummary=gi. **P2·P3·P4·P7·P8·P9·P14·P15·P19·P20·P21은 어느 파일인지 ❓** (Code.js 내부 또는 미식별 — Day 1 범위 밖, 추후 확인)

## a-2. 데이터 흐름 ✅ (코드 확인)
```mermaid
flowchart LR
  OP["9 OP 시트<br/>(매장 매니저 매일 입력)"] -->|syncOpData| BI["BI 통합시트 1tKr70<br/>매출_일별 27컬럼"]
  ACC["9 회계 시트<br/>(추적로그)"] -->|syncMonthlyData| BI
  BI -->|"_renderChapterP1 등<br/>(ctx 빌더가 BI read)"| DASH["운영실 대시보드<br/>HTML 출력 + Drive 캐시 3벌"]
  BI -->|"bridge_a_sync_sales<br/>(매시간)"| SUPA[("Supabase<br/>fact_sales_daily")]
  ACC -->|bridge_a_sync_accounting| SUPA2[("fact_account_log")]
  OP -->|bridge_c (할인/VOC)| SUPA3[("fact_discount / fact_voc")]
  DASH -->|매일 06:30 캐시 / 13:00 syncAll 후 재캐시| USER["CEO · 운영실장<br/>(매일 아침 5분)"]
```
- ✅ 대시보드 렌더는 **BI 통합시트(`1tKr70`)를 ctx로 읽음** (Supabase 아님). Supabase는 **별도 신규 시스템**(우리가 구축 중)이 같은 BI를 브리지로 받는 것.
- ✅ 캐시: 매일 06:30 자동 + 13:00 syncAll 후 재캐시 (P1 methodology 명시).

## a-3. P1 "전사 통합" 챕터 — 14섹션 인벤토리 ✅ (gd_render_chapter_p1_overall.js 707줄 전부 정독)

| # | 섹션 | 유형 | 줄 | 데이터(ctx) | 마크 |
|---|---|---|---|---|---|
| 0 | 방법론 헤더 | doc | 40–49 | — | ✅ |
| 1 | KPI Row 6개 | KPI | 54–92 | c.last7Rev·c.wow·c.revM·c.targetM·c.pace·c.ach·c.cust·c.ticket·c.anomalyCount / 신규월1~3일 swap | ✅render ⚠calc(Code.js) |
| 2 | 시스템 health 배지 | card | 95–134 | ctx.freshness·ctx.triggerHealth | ✅ |
| 3 | 데이터 무결성 검증 | table | 137–162 | ctx.전사.dataIntegrity (A~H 7검증, 09:25) | ✅render ⚠검증로직 |
| 4 | 같은 요일 비교 | table | 164–231 | ctx.전사.wowByDay/last7ByDay/prev7ByDay (런치·디너 WoW) | ✅render ⚠calc |
| 5 | 지점별 일별 매출 9×7 | table | 233–297 | loc.last7ByDay | ✅ |
| 6 | 오늘의 안건 Top3 | table | 299–385 | 자동 4룰(매출위기-15%/비용임계+5%p/부정리뷰3건&30%/진척<80%) | ✅ |
| 7 | 매출 위기 자동감지 | table | 387–421 | 직전7일 일평균 vs 전월 -15%↓ | ✅ |
| 8 | 매장 수익성 4분면 | table | 423–488 | s.prev.revM/마진율/laborPct/foodPct, 중앙값 4분면 | ✅render ⚠calc |
| 9 | 런치/디너 매트릭스 | table | 490–561 | s.last7LunchCust/last7DinnerCust | ✅ |
| 10 | 예약/워크인 매트릭스 | table | 563–669 | loc.reserveMonthly (R_S/워크인) | ✅render ❓예약시트소스 |
| 11 | 전사 매출 종합차트 | canvas×3 | 672–677 | p1_brand·p1_weekly·p1_monthly | ✅placeholder ❓chart config |
| 12 | 연간 YoY 흐름 | canvas×10 | 679–696 | p1_brand_monthly_trend + p1_store_yoy_0~8 | ✅placeholder ❓config |
| 13 | 요일별 평균 + 주목지점 | canvas×1+list | 698–702 | p1_dow + c.anomalies Top5 | ✅render ❓config |

**P1 = Chart.js canvas 14개 + 표/매트릭스 7개 + KPI 6개 + 시스템카드 2개.** (참고: 신규 web-admin `/sales` 골격은 매장카드+차트2개뿐 — P1의 ~10%)

### P1 계산식 (methodology L44, ✅ 명시):
- 이번달 매출 = 매출_일별 전산_합계 1일~refDate 합산
- 전월 매출 = 매출_월별 직전월 raw(마감)
- 진척률 = 현재매출 ÷ (목표매출 × 경과일/월총일)
- 7일 매출 = 직전 완료 주(월~일) 합산 (이번주 진행중 제외)
- WoW = (직전주−그전주)÷그전주
- ⚠ 위 계산의 **정확한 집계 코드는 Code.js ctx 빌더** — Day 1엔 render만 정독, calc는 다음 단계.

### 🚩 발견사항 (P1):
1. ⚠ **섹션6 "오늘의 안건" 룰①(매출위기 -15%)와 섹션7 "매출위기 자동감지"가 동일 로직 중복** — 화면 2회 등장. 옛 코드 누적. ❓ 1:1 재현 시 그대로 둘지 통합할지?
2. ✅ **런치/디너·예약/워크인은 "포스 시간대 분류"만** — 배달(배민/쿠팡/캐치) 시간대 정보 없어 미포함. **전체매출 ≠ 런치+디너 합**(차이=배달). (코드 주석 L200 명시)
3. ✅ 신규월 1~3일엔 헤드라인을 직전월 마감으로 swap (당월 0 표시 방지, L51/56).

## a-4. 핵심 사실 / 불변량 / 용어집 (누적)
- ✅ **불변**: 운영실 대시보드는 **BI 통합시트(1tKr70)를 진실원천으로 렌더**. Supabase(신규)는 같은 BI를 브리지로 복제하는 별도 라인.
- ✅ **불변**: 매출 = POS raw 실측 합산. 진척률·WoW·페이스는 산출치.
- ✅ **제약**: 진행 중 당월=일별, 마감월=월별 → 두 소스 차이로 ~1% 오차 가능.
- ✅ **용어**: "페이스" = 달성률÷시간진행률(배수). 100%=시간에 딱 맞음, 80%미만=위험. "달성률" 아님.
- ✅ **용어**: "직전 7일" = 어제부터 7일 전까지 rolling (이번 주 아님).
- ✅ **회계**: "선결제 회수" = 전월 인식 매출의 당월 차감(이중계산 방지) → **할인으로 분류 유지**(prepayment_redemption). 운영실수 아님.
- ✅ **용어**: VOC는 티켓 큐가 아니라 **기록**(GOOD/BAD 감정).

## ❓ Day 1 질문 (운영실장 답 필요)
- Q0: PDF 16노드 목록 (위)
- Q1: 노드 f(비용결산/인력배치) 위치 (위)
- Q2: VOC FIC 본체 1P18V 폐기 여부 (위)
- ❓ Q3: P1 섹션6/7 중복 — 통합 vs 그대로 재현?
- ❓ Q4: 운영실장이 매일 아침 P1에서 **가장 먼저 보는 섹션**은? (KPI Row? 오늘의 안건? 매출위기?) — 신규 페이지 우선순위 결정용.
- ❓ Q5: P1 canvas 14개 중 **실제로 매일 보는 차트** vs 거의 안 보는 차트? (1:1 재현 우선순위)

## 다음 (Day 2 예정)
- P1 calc 추적: Code.js ctx 빌더에서 c.revM/last7Rev/pace/last7ByDay/reserveMonthly 계산 line-level
- P1 canvas 14개 chart config (Scripts.html/Code.js) 정독 — 데이터변환·차트유형·색상
- 이어서 P2~P19 챕터 인벤토리

---

# Day 2 — 노드 a: P1 계산 로직(Code.js ctx 빌더) + 챕터 매핑 + Q0~Q5 반영

## Q0~Q5 답 반영 (운영실장 확인 ✅)
- **Q0 PDF 16노드** ✅: 최상위=운영 전략 대시보드 / 3대 전략=비용개선·메뉴개발·고객만족 / 11 시스템(운영실 비용결산·VOC·매뉴얼·인력배치모듈·HR센트럴·회계센트럴·리뷰크롤러·FIC오프라인·위생관리·SOP·매출대시보드·지점별로스터·급여계약서모듈·레시피+이론원가·지점별회계·판매량·지점별OP·시간별매출). 점선 흐름 3: 시간별매출→매출대시보드+메뉴개발 / 회계센트럴→비용결산+메뉴개발 / 매출대시보드→인력배치+비용결산.
- **Q1 운영실 비용결산** ✅: 별도 시스템 아님 = **9 지점 회계 시트 + 각자 자체 Apps Script**(9쌍, 아래 c-1). ❓ 9개 집계 통합본이 따로 있는지 Day3 확인.
- **Q2 FIC 본체 1P18V** ✅: 폐기 아님 = **리뷰 아카이브 크롤러 + 자동분류 + V2 액션보드** (시트 `1CbmPsmObHVAwcCT_RzwmPp9TzW1z1N0KrGWDNBYBEok` "심퍼티쿠시 리뷰 아카이브", 19탭). 우리가 sync한 `1Ve6…`=FIC 오프라인 부분만. Day2~3 두 시트 관계 확인.
- **Q3** ✅: P1 섹션6/7 **통합**(매출위기 -15% 화면 1회만).
- **Q4** ✅: P1 = 운영실장 매일 첫 화면.
- **Q5** ✅: P1 차트 14개 전부 매일 봄 → **1:1 재현 필수**.
- 마케팅 M0~M9(gh) **보류**(마케팅 팀장 영역, 별도 라운드).

## a-3b. P1 계산 로직 — line-level ✅ (Code.js 전사 ctx 빌더 7660~9116 정독, 모두 코드 직접 확인)
데이터 소스: `dailyData`=매출_일별, `monthlyData`=매출_월별, `reserveRows`=예약_주차별. (refDate=어제, today=오늘)

| 지표 (P1 표시) | 변수 | 공식 (Code.js 줄) | 마크 |
|---|---|---|---|
| 시간진행률 | requiredRatio | `max(0,(d-1)/monthDays)` — 어제까지 경과일/월총일 (7664) | ✅ |
| 이번달 매출 | revM | `sumF(thisMoDaily,'전산_합계')` — 당월 1일~today 일별 합 (7788) | ✅ |
| 목표 | targetM | `sumF(thisMo,'목표매출')` — 매출_월별 (7789) | ✅ |
| 달성률 | ach | `revM/targetM` (7790) | ✅ |
| 월 페이스 | pace | `ach/requiredRatio` (7791) | ✅ |
| 이번달 객수 | cust | `sumF(thisMoDaily,'일_객수')` → fallback 런치+디너객수 → 월객수×매출비율 → 일수비례 (CON fallback) (7793–7808) | ✅ |
| 객단가 | ticket | `revM/cust` (7809) | ✅ |
| 직전7일 매출 | last7Rev | `sumF(last7,'전산_합계')`, last7=inRange(w_start,w_end) (7822–23) | ✅ |
| WoW | wow | `(last7Rev−prev7Rev)/prev7Rev` (7830) | ✅ |
| 같은요일 비교 | wowByDay | 요일별 `_byDay()`(매출=전산_합계, 런치매출=전산_런치, 디너=전산_디너, 일수=distinct date) → wowRev/wowCust/wowLunch/wowDinner (7834–7894) | ✅ |
| 런치비중 | lunchPct | last28 `전산_런치/(전산_런치+전산_디너)` (7896–98) | ✅ |
| 배달비중 | deliveryPct | last28 `(배민+쿠팡)/전산_합계` (7899–7902) | ✅ |
| 캐치비중 | catchpayPct | last28 캐치페이런치+디너 / 전산_합계. **CON/SEL은 일별 캐치페이 0 하드코딩(syncOpData L294)→매출_월별 '캐치페이' 직전3개월 비율 fallback** (7903–09) | ✅ |
| 객단가 분포 | ticketDist | last28 일별객단가 median/p25/p75/mean/std/cv/iqr (n≥7) (7864–78) | ✅ |
| 예약비율 | 예약비율 | `예약객수/월객수` (매출_월별, 7819–21) | ✅ |
| 인사이트 건수 | anomalyCount | ⚠ anomalies 배열 길이 (계산 위치 별도 — Day3 확인) | ⚠ |

**자동 4룰**(gd L299~385, 값은 ctx 기반): ①매출위기 = 직전7일 일평균 < 전월 일평균 −15% ②비용임계 = laborPct/foodPct > 임계+5%p (ctx.raw.thresholds 브랜드별) ③부정리뷰 = reviewNeg7≥3 & reviewNegRate7≥30% ④진척률 = pace<80%. ✅ render·룰 / ⚠ reviewNeg7·thresholds 계산위치 Day3.

**4분면(섹션8) s.prev**: ⚠ `s.prev.revM/마진율/laborPct/foodPct` = 매출_월별 직전월 마감 기반 (ctx.지점 빌더에서 계산 — 정확 줄 Day3).

## a-2b. 챕터 매핑 완성 ✅ (작업4)
- **P1**=gd_render_chapter_p1_overall ✅ / **P11**=gb ✅ / **P13**=gc ✅ / **P18·M12**=gf ✅ / **M0~M9**=gh(보류) ✅ / **ExecSummary**=gi ✅
- **P2·P3·P4·P5·P6·P7·P8·P9·P10·P14·P15·P19·P21 = Code.js 인라인 템플릿 (`<div class="page" id="pN">`, 18072~)** ✅
- 사이드바 노출 순서(L12311~12327) ✅: 전사(p1)·인사이트(p8)·위기매장(p18)·심퍼티쿠시(p2)·콘피에르(p3)·셀렉션(p4)·전년대비(p9)·객수객단가(p7)·비용마진(p5)·할인서비스(p10)·음료(p6)·메뉴성과(p15)·리뷰평판(p11)·스코어카드(p13)·매장위생(p19)·인사HR(p14)·상세매출(p21)

## 작업3 — 예약/워크인 소스 ✅
`'예약_주차별'` 탭(L3597/20744) → `data.reserveWeekly`(L9052) → `reserveRows`. 컬럼: 지점·월·기준일·R_L·R_S·예약율·워크인·캐치테이블·네이버·방문객수·전주대비. ctx.지점에 **지점명 부분매칭**으로 부착(L9086~9114). 워크인비율 = 1−예약비율. ❓ '예약_주차별' 탭을 품은 스프레드시트가 BI(1tKr70)인지 별도 예약율 대시보드(1Ag37/1mxY)인지 Day3 확정.

## c-1. 노드 c 회계 9쌍 (작업5 착수 — scriptId/sheetId 확보 ✅, line-level은 Day3)
| 매장 | 시트 ID | Apps Script ID |
|---|---|---|
| 심퍼티쿠시 용산 | 1y65z8rQ-twKxy69gpXJWmLdGl6QLtalRBP47sd_kJFM | 1bVKiefUapg4xa2FJ3R-CSzvzk51p7WPld_TXnNKeJHxcBx9gWsKjNNpr |
| 심퍼티쿠시 서울역 | 1KIQ5kQNWwSxrVqS8mRbd6J1RN-KnQe9rRU07pWr_Tr8 | 1X1-Vz-eTCpJKNWgxRYD76KY307MMSaPvrtbYnzu7wGZjPbH85A7O0950 |
| 심퍼티쿠시 성수 | 1f8xnGaFVm9EkBEJcI4ljULY7s3lq-UmY19dhJq12ufE | 1-KQb12AhYV9w1EH5ytijAMevE2mdGUwv_vzjo80bKfw7TegGhaFEpIhI |
| 심퍼티쿠시 여의도 | 1DQiv7UlNLr2GufvEBm4E8PbZO_VfRVxFBW9UTWk_ClI | 1e-vEptzXZ9LMigK86Uv58SuttSQcW5Hz6bUzdOSu9pGRtRIE1ITcI_0i |
| 심퍼티쿠시 이스트폴 | 13zs0tXoOaRkcA_kTsszbu_K5l7NO3qIPet7T4IA8ZFY | 1A5ACebz8cz-6YXakcGgrvJsuB0r1BGGzvmknfqtW-yvy74m5XDAMA0b3 |
| 심퍼티쿠시 광화문 | 1JwQgAho-H01wMvtFFtAWB8c_hPRw2vFhUWG-eOluJCc | 1QdJnOOttkOGbHa1UCEr6qkpkLz-dhHZ4sdc1VAVB3KKAjtBMFaT2KiYd |
| 콘피에르 서울역 | 1xV_1HKH7ISP5XZAjzP9xoEG2VnebOOAAK3kgbJsnNLQ | 1eX03rVLAYAgfDIomDJygIxMKr48VIF0It-QGN0JU91Mj8qlrM_vt38bv |
| 콘피에르 셀렉션 잠실 | 1ePAXdQOsuXq7xZFx1KPQ9RfVCOAp580Dcc_aZhCPdSw | 1gM2YFQfAYiVOjGahthgSGS4GFt-OcsZ_oLqqSvGl00L2r1xhYf_N4GkB |
| 콘피에르 셀렉션 그랑서울 | 1-MYOSReVIyVpoh1_D3gB-v9o3znQtZX6sPlNiU5Uzas | 1A5fnlORcYz9tfX9jgr2b8KWrM8EAW03__S4JZYl8CB_4lZ5_MwDU6R89 |
✅ 위 9 시트 = 기존 인벤토리 '지점 회계 9시트'와 동일. AppsScript ID는 신규 확보(이전 "bound 무제목" 추정 정정).

## Day 2 발견사항
1. ⚠→✅ **CON/SEL 일별 캐치페이 0 하드코딩**(syncOpData L294) → 월별 시트 캐치페이 직전3개월 비율 fallback. CON/SEL 캐치 수치는 추정치임 (운영실장 인지 필요).
2. ✅ cust(객수)는 4단 fallback — CON은 일별 객수 없을 때 월별 비례 추정 → CON 일별 객수/객단가는 추정 가능성.
3. ✅ 회계 9쌍 AppsScript ID 신규 확보 — 이전 인벤토리의 "회계담당 bound 무제목" 추정을 정정(매장별 독립 스크립트).

## Day 2 질문 (❓)
- ❓ Q6: '예약_주차별' 탭 호스트 스프레드시트 = BI(1tKr70) vs 예약율 대시보드(1Ag37/1mxY)?
- ❓ Q7: anomalyCount / reviewNeg7 / thresholds 계산 위치 — Code.js 어느 함수? (Day3에 직접 찾되, 운영실장이 알면 빠름)
- ❓ Q8: 회계 9개 AppsScript가 BI 통합시트(1tKr70)로 어떻게 집계되나? (각자 push? 중앙이 pull?) — Day3 코드로 확인 예정, 운영실장 설명 있으면 가속

## 다음 (Day 3)
- P1 chart config 14개 정독 (Code.js 차트 init 블록: p1_brand/weekly/monthly/brand_monthly_trend/store_yoy_0~8/dow — 데이터변환·유형·색상·축)
- 회계 9쌍 line-level (1쌍 대표 정독 → 공통 구조 → 9개 차이) + BI 집계 경로
- VOC 1CbmPsm(19탭) vs 1Ve6 관계 + 리뷰 크롤러/자동분류 로직

---

# Day 3 (2026-06-23) — P1 차트 config + 회계 9쌍 line-level + VOC 두 시트 관계 + anomaly/캐치 추정

## 작업1. P1 차트 14개 config 정독 (Code.js 차트 init 블록) ✅
> 모두 `new Chart(el, {...})` 직접 호출. **글로벌 `Chart.defaults` 미변경**(안전룰 준수). 색 토큰: 초록 `#1DB954`(SZI/긍정), 분홍 `#ec4899`(CON), 주황 `#f59e0b`(SEL/현재월/진행중), 보라 `#a855f7`(주말).

| canvas id | type | 데이터 소스 | 변환 | 색/스타일 |
|---|---|---|---|---|
| `p1_brand` | bar | `Object.values(브랜드).revM` | 브랜드별 당월매출 | `COLOR` 팔레트 ✅ |
| `p1_weekly` | bar | `전사.weeklyTrend`(주차/매출) | — | `#1DB954` ✅ |
| `p1_dow` | bar | `전사.dowAvg`(요일/평균매출) | — | 주말 `#a855f7` / 평일 `#1DB954` ✅ |
| `p1_monthly` | bar | `전사.last12m`(월/목표/매출) | 목표=`monthlyTargetStyle()` + 실매출 2-dataset | 현재월 `#f59e0b` else `#1DB954`, borderRadius4 ✅ |
| `p1_brand_monthly_trend` | line | `브랜드[*].yearlyMonthly.byYear` | 1~12월 라벨 고정, 브랜드별 작년+올해 2라인 | 작년=브랜드컬러+`88`알파+점선`[6,4]`, 올해=실선 2.8px+현재월 노란점`#fbbf24`(r7)+미래월 null ✅ |
| `p1_store_yoy_0~8` | line ×9 | `allLocs[i].yearlyMonthly` | 매장별 작년 vs 올해 1~12월 미니차트 | 작년=`#a3a3a380` 점선`[5,3]`, 올해=`storeColors[지점명]` 실선 fill, 현재월 노란점 ✅ |

- ✅ **isFuture/isCurrent 플래그**: yearlyMonthly 각 월에 `isFuture`(미래월→null로 라인 끊김), `isCurrent`(진행중월→노란 강조점) 부착. `spanGaps:true`로 결측 월 건너뜀.
- ✅ **작년 라인 0/null 처리**: `x.매출>0 ? x.매출 : null` — 매출 0(미오픈/휴업)월은 라인에서 제외.
- ✅ P1 외 P2~P4 브랜드 차트(L13067~): `loc_rev`/`weekly`/`monthly`/`meal`(런치디너 도넛)/`bv`(Food음료 도넛)/`delivery`(매장/배민/쿠팡 도넛)/`ticket`(객단가 line). P5(L13080~)는 v50.10에서 19→5 차트 축소 + 차트별 `animation:false`.
- 🔗 신규시스템 매핑: 이 차트들이 [[월별 YoY·목표대비]] 시각화 핵심. fact_sales_daily에 `sales_lunch/dinner`, `beverage_sales/ratio`, `guest_lunch/dinner` 이미 적재됨(Day1~2) → React에서 재현 가능. **단 `목표매출`·`yearlyMonthly` 작년치는 BI `매출_월별` 탭 의존 → 미이주** ⚠.

## 작업2. 회계 9쌍 line-level (용산 대표 clone: scriptId `1bVKief…`) ✅
> 15파일 11,787줄. **매장마다 독립 스크립트**(공통 코드 복제본). "푸드코스트 통합 v4.5".

**파일 구성** (용산 기준, 9개 동일 추정 ⚠):
| 파일 | 줄 | 역할 |
|---|---|---|
| Code.js | 3,887 | 코어: SETTING/입력-N월 시트 생성·발주입력·계정과목 |
| 발주입력팝업.js | 1,959 | 발주 입력 모달 UI |
| 데이터 보호 패치.js | 1,065 | 시트 보호/권한 |
| 소모품입력.js | 922 | 소모품내역 입력 |
| 코스트시뮬레이션.js | 834 | 원가 시뮬레이터 |
| 로스터동기화.js | 593 | `syncTeamFromRoster_` — 로스터→인건비 |
| 운영대시보드.js / 예산대시보드.js | 464/444 | 매장 자체 대시보드 |
| 거래처관리.js | 441 | 거래처(vendor) CRUD |
| 정산보고서.js | 344 | `runMonthlySettlement` 월말 대조 |
| bulk_settlement_report.js / bulk_labor_sync.js | 170/119 | **9지점 일괄 실행(용산=허브)** |
| 토탈수식보호.js / 발주섹션_시트보호.js | 81/464 | 수식·범위 보호 |

- ✅ **시트 구조** (각 회계운영 시트): `입력-N월` 탭(`📦 BOH 발주 입력`/`📦 FOH 발주 입력` 섹션, A열=계정과목, D열=토탈, 섹션당 35행) + `추적로그` 탭(12컬럼: idx0=시트명, idx3=userTeam, idx4=금액, idx8=계정과목, idx11=inputSection) + `소모품예산 전용 관리`(또는 `소모품내역`) + `SETTING`(H3=BOH ROSTER_ID, H4=FOH, U~W=사용자, BOH거래처 4~38행, FOH 40~74).
- ✅ **계정과목 8종**: 식자재/소모품/용역/기타/프로젝트/음료자재/회식/채용 예산.
- ✅ **`runMonthlySettlement(sheetName)`**(정산보고서.js L224): 입력시트 D열 토탈 합산(계정×팀) **vs** 추적로그 금액 합산(계정×팀) → `diff>1`이면 mismatch. + 소모품 status `완료` 여부 검증 → `{allMatch, mismatchCount, supplyComplete, supplyPending}`. 이것이 매장 회계의 **자체 정합성 검증 로직**(우리 reconcile와 동일 사상).
- ✅ **Q8 답** [정정 Day3]: 회계 9개는 BI(1tKr70)로 push **안 함**. 용산 회계운영 시트가 **허브** — `bulk_labor_sync.js`/`bulk_settlement_report.js`가 `ORI_LABOR_BRANCHES`(9 sheetId 하드코딩)를 `openById`로 직접 열어 `setActiveSpreadsheet` 후 각 지점 함수 호출(fan-out). BI 매출 집계(`syncOpData`)와는 **완전 별개 경로** — 회계운영 시트는 발주·인건비·소모품(비용)이고, BI는 OP 시트 1.SA 매출만 pull.
- 🔗 신규시스템: `추적로그`=`fact_account_log` 원천. `ORI_LABOR_BRANCHES` 9 sheetId = bridge OP_SHEETS와 **다른 시트**(회계운영 ≠ OP). ⚠ 현재 우리가 sync하는 건 OP쪽이지 회계운영 9시트 추적로그 직접 sync 여부 재확인 필요.
- ⚠ ADMIN_EMAILS = 회계담당 + 매장관리자 2명 (공개본 redact).

## 작업3. VOC 두 시트 관계 ✅
- ✅ `1Ve6…` = **"(SZI)_전지점 Brand FIC"** (13.3MB, 2024-10 생성, 폴더 `1HzYty…` 내, 활발히 수정 2026-06-23). = 우리가 sync한 SZI VOC 원천. **오프라인 고객 접수 VOC/피드백**.
- ✅ `1CbmPsm…` = **"심퍼티쿠시 리뷰 아카이브"** (2.5MB, 2026-04 생성, 19탭). = **온라인 리뷰 크롤러/자동분류** (네이버 등 외부 리뷰).
- 🟰 **관계**: 둘은 **다른 데이터 소스**. FIC(1Ve6)=매장 접수 VOC(불만/요청), 리뷰아카이브(1CbmPsm)=외부 플랫폼 리뷰. 대시보드 P-챕터(리뷰분석 reviewNeg7 등)는 **리뷰아카이브 계열** 데이터 사용, VOC페이지는 FIC 계열. 
- ❌ **미이주**: 리뷰 아카이브(1CbmPsm) → Supabase 미이주. reviewDaily(부정수/SOP위반수/평균별점)의 원천이 이 시트 계열일 가능성 높음 → Day4 크롤러/자동분류 로직 + reviewDaily 원천 탭 확정 필요.

## 작업4. anomaly / review / thresholds 계산 위치 ✅ (Q7 답)
- ✅ **thresholds**: `ctx.raw.thresholds = readTab('임계값_정보')`(L7588) — BI의 `임계값_정보` 탭. 브랜드별 비용 임계. `data.thresholds.find(x=>x.브랜드===s.브랜드)`(L8427)로 매장점수 계산 시 매칭.
- ✅ **reviewNeg7**(L8179) = `reviewLast7`(직전 완료주 7일) 부정수 합. `reviewNegRate7 = reviewNeg7/reviewCount7`(L8181). `reviewWeightedScore7`=리뷰수 가중 평균별점. `reviewSop7`/`reviewHighSop7`=SOP·High위반 합. 12주 트렌드(L8185~)=주별 집계 `reviewWeeklyTrend`.
- ✅ **anomalyCount**(L8400) = `이상치탐지(data, 지점)` 함수 결과 `anomalies.length`. `전사.anomalies`에 배열 저장.
- ✅ **매장점수**(L8426~) = csuite 합의 v2.0 6축: 매출10/고객만족20/인건비20/식자재20/운영품질20/위생10. `costRatioScore(actual,threshold)`(L8409) piecewise(임계 80%↓=100점 … 135%↑=0점). 위생 미시작 시 5축 가중치 비례 재분배.

## 작업5. CON/SEL 캐치 객수 추정 로직 + 진짜 측정값 경로 (Q9 답) ✅
- ✅ **cust(객수) 4단 fallback**(ctx L7793): ①`일_객수` 합 → 0이면 ②`런치_객수+디너_객수` → 0이면 ③`월객수 × (당월매출/월매출)` 비례 → ④`월객수 × (경과일/월일수)`. **CON은 일별 객수 결측 → ②③④ 추정치**일 수 있음 ⚠.
- ✅ **catchpayPct**(ctx L7903): `(캐치페이_런치+캐치페이_디너)/last28총매출`. **CON/SEL은 일별 캐치페이 0 하드코딩**(`syncOpData` L294, BI-bound 스크립트) → `catchpaySum===0`이면 `매출_월별` 시트 `캐치페이` 컬럼 직전 3개월 비율로 fallback(L7907~7913). → **CON/SEL 캐치 수치 = 월별 추정치**.
- ✅ **Q9 답 (진짜 측정값 받는 법)**: 캐치테이블 **실측 예약/결제**는 이미 마케팅 빌더에 설계돼 있음 — `마케팅_캐치예약_상세` 시트(`source: 캐치테이블 API + 네이버 예약`, 컬럼: 예약일시/방문일시/채널/인원수/상태(확정·노쇼·취소·방문완료)/결제금액/캐치페이여부, Code.js L4705~4710) + `마케팅_매장_검색유입_KPI`의 `캐치테이블실예약`. → **캐치테이블 API를 이 마케팅 raw 시트로 적재 → 일별 캐치 객수/매출 실측 가능**. 현재 운영 대시보드는 이 마케팅 데이터를 매출 ctx에 **연결 안 함**(P12 마케팅 통합 시 연결 대상). 즉 "진짜 측정값"의 파이프는 존재하나 **운영 대시보드 매출 경로엔 미연결** = 추정 유지 중.

## Day 3 발견사항 요약
1. ✅ P1 14차트 전부 글로벌 defaults 미변경 + isFuture/isCurrent 플래그 기반 YoY 라인 — 안전룰 준수 확인.
2. ✅ 회계 9쌍 = 매장별 독립 "푸드코스트 v4.5" 스크립트, 용산=일괄 허브. 추적로그=fact_account_log 원천, 자체 월말대조 로직 보유.
3. ✅ Q8: 회계→BI push 없음(fan-out openById 허브 구조). Q7: thresholds=임계값_정보 탭/anomaly=이상치탐지(). Q9: 캐치 실측 파이프(마케팅_캐치예약_상세)는 설계됐으나 매출 경로 미연결.
4. ✅ VOC = FIC(오프라인 접수) + 리뷰아카이브(온라인 크롤러) 2계열, 후자 미이주.

## Day 3 질문 (❓)
- ❓ Q10: 회계운영 9시트(`ORI_LABOR_BRANCHES`)의 `추적로그`를 Supabase로 직접 sync 중인가, 아니면 OP 시트 경유만인가? (fact_account_log 완전성 영향)
- ❓ Q11: reviewDaily(부정수/SOP위반/평균별점) 원천 탭 = 리뷰아카이브(1CbmPsm) 계열 맞나? BI에 sync돼 들어오나?
- ❓ Q12: 캐치테이블 API 키/연동은 현재 살아있나(마케팅_캐치예약_상세 실제 적재 중인가), 아니면 설계만인가?

## 다음 (Day 4)
- 리뷰 시스템: 1CbmPsm 크롤러/자동분류 Apps Script clone + reviewDaily 원천 확정 (Q11)
- 회계운영 시트 추적로그 → Supabase sync 경로 확인 (Q10)
- P6~P11 챕터 ctx 빌더 line-level (HR/위생/예약율)

---

# Day 3 보강 (2026-06-23) — 예약_주차별 호스트 확정(작업5) + 운영실장 답변 반영(Q9·Q10)

## 작업5. 예약_주차별 탭 호스트 확정 ✅ (Q6 답)
- ✅ **호스트 = BI(1tKr70) 자체의 `예약_주차별` 탭**. 대시보드는 `readTab('예약_주차별')`(L7622)로 **bound BI 시트**에서 읽음(.length 0이면 `마케팅_예약_주차별` fallback). → ctx `data.reserveWeekly` → `reserveRows`(L9052) → 지점 부분매칭 부착.
- ✅ **원천 = 별도 예약율 대시보드 2개** (BI 아님):
  - `SZI_RESERVE 1mxY-t7979v2YTD9PdzgLfbMem8jZJp6NEs8LiU1Mjjw` = "(SZI) 예약율 대시보드", `심퍼티쿠시_26` 탭, 6매장×14컬럼.
  - `CON_RESERVE 1Ag37YRjsrQ0vefGUyFxEtdksKtnFXyiMT8Y7bTSy7ws` = "(CON) 예약율 대시보드", `콘피에르_26` 탭, 3매장×12~14컬럼.
- ✅ **sync 함수 `syncReserveData()`**(L3595): `getActiveSpreadsheet()`(=BI) `예약_주차별` 탭을 clear→재생성, 헤더 12컬럼 `[브랜드, 지점, 월, 기준일, R_L, R_S, 워크인, 캐치테이블, 네이버, 예약율, 전주대비, 방문객수]`. 두 예약율 대시보드를 `openById`로 읽어 가로 매장블록 → flat 통합. (L3592 주석: "P7 객수/객단가 챕터 확장")
- 🟰 **결론**: Q6의 두 후보(BI vs 예약율 대시보드)는 **둘 다 맞음** — 원천은 1mxY/1Ag37, 대시보드가 읽는 호스트는 BI `예약_주차별`(syncReserveData가 중계). reserveWeekly 미이주 ❌ → 예약율/워크인비율(P7) React 재현하려면 이 sync 경로 복제 필요.

## Q9 보강 (운영실장 답) ✅→🟰
- 운영실장: "CON 객수 fallback은 **일별 미입력이 있을 때** 그런 것일 것." → CON 객수 4단 fallback(ctx L7793)은 **상시 추정이 아니라 일별 객수 결측 시에만** ②③④로 진입. 평소엔 ①`일_객수` 실측. [정정 Day3보강] = "CON 일별 객수는 추정 가능성" ⚠ → **"입력 누락분에 한해 추정"** 으로 한정. catchpay(작업5/Day3)는 하드코딩이라 별개로 항상 추정 유지.

## Q10 답 (운영실장: "코드 그대로") — 사이드바 챕터 **표시 순서** ✅
> ⚠ 중요: 챕터 **번호(p1·p8·p18…)는 표시 순서와 다름**. 아래는 운영실장이 매일 보는 **실제 우선순위 순서**(L12311~12327, nav L12342~ 동일).

**운영 그룹 (17개, 순서대로)**:
1. p1 전사 · 2. p8 **인사이트** · 3. p18 **위기 매장 진단** · 4. p2 심퍼티쿠시 · 5. p3 콘피에르 · 6. p4 콘피에르 셀렉션 · 7. p9 전년 대비 · 8. p7 객수/객단가 · 9. p5 비용/마진 · 10. p10 할인/서비스 · 11. p6 음료 · 12. p15 메뉴 성과 · 13. p11 리뷰&평판 · 14. p13 매장 스코어카드 · 15. p19 매장 위생 · 16. p14 인사(HR) · 17. p21 상세 매출

**마케팅 그룹 (8개, showMkt 시)**:
m0 마케팅 전사 · m1 이번 주 요약 · m3 META 광고 · m9 고객관리(CRM) · m7 검색노출(SEO) · m8 인플루언서 시딩 · m10 카카오 친구 · m2 검색→예약

- ✅ **인사이트(p8)·위기 매장 진단(p18)이 브랜드별 매출(p2~)보다 앞** = 운영실장은 "문제 먼저" 본다. React 신규 대시보드 IA(정보구조)도 이 우선순위 따라야 함.
- ✅ `showOps`/`showMkt` 플래그로 audience별 노출 분기(운영/마케팅 그룹). nav(상단 flex-wrap 2줄)와 sidebar 동일 순서.

## Day 3 보강 발견 요약
1. ✅ 예약_주차별: 원천=예약율 대시보드 2개(1mxY/1Ag37) → syncReserveData가 BI 탭으로 중계 → 대시보드가 읽음. 미이주.
2. ✅ Q9: CON 객수 추정은 결측분 한정(상시 아님). catchpay는 상시 추정(하드코딩).
3. ✅ Q10: 운영 17챕터 표시순서 = 우선순위순(전사→인사이트→위기진단→…), 번호순 아님. 신규 IA 기준.

## 다음 (Day 4) — 갱신
- 리뷰 시스템: 1CbmPsm 크롤러/자동분류 clone + reviewDaily 원천 확정 (Q11)
- 회계운영 시트 추적로그 → Supabase sync 경로 (Q10 기존/이제 Q-acct)
- P7 예약율·P8 인사이트·P18 위기진단 ctx 빌더 line-level (운영실장 최우선 3챕터)

---

# Day 4 (2026-06-23) — 회계 9쌍 완결 + P5/P11/P13 챕터 line-level + VOC 두 시트 + 정정 3건

> ⚠ **정정 3건 먼저**:
> 1. **[정정 Q12]** 캐치테이블은 **API 연동 자체가 불가능**(운영실장 확정). Day3의 "진짜 측정 파이프(`마케팅_캐치예약_상세`←캐치테이블 API)는 설계됨, 활성화 가능" 결론은 **틀림**. CON/SEL 캐치 객수/매출은 **영구 추정**(월별 비율 fallback)이 유일한 방법. "파이프 활성화 검토"는 불가능하므로 폐기.
> 2. **[정정 P5 source]** P5 비용·마진은 **회계 9시트가 아니라 OP 매니저 입력 기원**(브랜드 대시보드 경유). 회계 9쌍은 **별개 결산 시스템**(회계담당 영역). 상세는 작업2.
> 3. **[정정 Q11]** 대시보드 `reviewNeg7`/`reviewDaily`는 **내가 만든 게 아니라 기존 Apps Script**(`syncReviewData`)가 1CbmPsm에서 BI로 sync. 리뷰는 **Supabase 미이주** = 내 migration 산출물 아님(정직 보고). "너가 만든거"는 오해 — 내가 만든 건 회계/매출/할인/VOC sync뿐.

## 작업1. 회계 9쌍 line-level 완결 ✅
- ✅ **8개 매장 = 용산의 동일 복제본**: 공유 12파일 중 **11개 byte-identical(md5 일치)**, 정산보고서.js 포함. 유일 차이 = `Code.js` **정확히 2줄**(L98-99 허브 메뉴 와이어링). 8개 매장 Code.js는 서로도 md5 동일(단일 변종).
- ✅ **허브 전용 2파일**: `bulk_labor_sync.js`(119)·`bulk_settlement_report.js`(170)는 **용산에만** 존재. 8개 매장엔 `ORI_LABOR_BRANCHES`/`runOri*` 참조 0건.
- ✅ **fan-out 체인**: `runOriLaborSyncAllBranches`(bulk_labor_sync L28) → `ORI_LABOR_BRANCHES`(9 sheetId) 루프 → `openById`+`setActiveSpreadsheet` → `silentSyncLaborFromRoster_`(L88) → `syncTeamFromRoster_`(로스터동기화.js L146). **말단 함수는 8개 매장에도 동일 존재** → 각 매장 로컬 단독 동기화 가능, 9지점 동시 트리거만 용산 독점.
- ✅ **`runMonthlySettlement` 자체 reconcile**(정산보고서.js L224, 9개 byte 동일): 입력시트 BOH/FOH D열 토탈 합산(`sheetTotals[team|account]`) **vs** `추적로그` 12열 동월 합산(`logTotals`, '시스템' 제외, `inputSection||userTeam` 키) → `Math.round` 후 `|diff|>1`=mismatch + `소모품예산 전용 관리` 완료/대기 카운트 → `{allMatch, mismatchCount, supplyComplete}`.
- ✅ **Q10 답 (추적로그 → Supabase)**: **내 bridge `bridge_a_sync_accounting.js`**가 처리. `syncAccountLogsAll()`(L82) → 9 `ACCT_SHEETS`(L21) 각 `추적로그` 탭(12컬럼: 시트명/작성자(이름·이메일)/팀/금액/입력시간/거래처/결제방식/계정과목/재무팀확인/품목/입력섹션) read → `fact_account_log`로 ingest. 매핑(L9~18): `occurred_on←입력시간`, `amount←금액`, `category_id←계정과목`(한글→영문 `BR_CAT_MAP` 8분류: food/labor/rent/utility/supplies/marketing/equipment/etc, L35~63), `status←재무팀확인`(Y/O/확인/완료→verified else pending), `source_ref={sheetId}/{rowIdx}`(idempotent UQ). → **OP 경유 아님, 회계 9시트 직접 sync**.
- 🟰 **결론**: 회계 9쌍 = 발주·인건비·소모품 **결산/검증** 시스템(회계담당). 추적로그→fact_account_log 직접 파이프 존재. **P5 비용(작업2)과 완전 별개 source**.

## 작업2. P5 비용·마진 챕터 line-level ✅ ([정정] source = OP 기원)
- ✅ **렌더 구조**(`ge_render_chapters_p5_p10.js` L28~93): 헤더(전월 마감) → Methodology(source=`매출_월별`+`임계값_정보`) → KPI 6카드(인건비율/푸드/음료/소모품/변동비합계/공헌이익률 + 전전월 cmpPP) → **5종 비용표 3개**(전사/브랜드/지점, v50.10에서 19→5 축소) → 12개월 추이 6차트(브랜드×인건비/푸드 + 임계 점선) → 마케팅비 2카드.
- ✅ **계산식**(`calcMonthCosts` Code.js L7706~7737): `rev=sumF(rows,'실매출')`; `laborPct=labor/rev`, `foodPct=food/rev`, `bevPct`, `consPct`, `nonPct` 모두 **분모=실매출**; `마진율=(rev-5종합계)/rev`. 5종 금액 = `인건비_금액`·`식자재비`·`음료자재비`·`소모품비`·`비경상비`.
- ✅ **[정정] source 재추적**: `매출_월별`은 **`syncMonthlyData()`**(`bf_sync_monthly.js` L14)가 채움 → **`SZI_DASHBOARD_ID`/`CON_DASHBOARD_ID`(브랜드 대시보드 시트)** 의 매장 탭에서 read(L76~85). 컬럼 `인건비율`/`푸드코스트`/`인건비`/`식자재비`/`음료자재비`/`소모품비`/`비경상비`(findCols L55~68). 이 브랜드 대시보드 값은 **매장 매니저 OP 입력 기원** — **회계 9시트 추적로그가 아님**. (※ 파일 주석 L2가 "회계시트(SZI/CON _대시보드)"로 헷갈리게 명명했으나 실체는 브랜드 대시보드, 회계 결산 9시트와 무관) → **운영실장 정정 코드로 확정** ✅.
  - 흐름: OP 매장 매니저 입력 → 브랜드 대시보드(SZI/CON _대시보드) 월별 탭 → `syncMonthlyData` → BI `매출_월별` → `calcMonthCosts` → P5.
- ✅ **4룰 ② (비용 경보)**: 임계 초과(`s.prev.laborPct > t.인건비_임계값`, `foodPct > t.푸드코스트_임계값`)=**medium**(L11841~45); **공헌이익률 전전월 대비 -5%p 하락**(`(s.prev.마진율 - s.pp.마진율) < -0.05`)=**high**(L11847~50). 즉 "+5%p"의 실체 = "마진율 5%p **악화**". 임계값=`readTab('임계값_정보')` 브랜드별(심퍼티쿠시 인건비≤27%·푸드≤23% / 콘피에르 인건비≤28%·푸드≤24%).

## 작업3. P11 리뷰 & 평판 챕터 line-level ✅
- ✅ **렌더**(`gb_render_chapter_p11_reviews.js` 463줄): 자체설문 섹션(브랜드별 KPI+매장 만족도표+코멘트) → 4×2 KPI(7일 리뷰/별점/호평/부정/SOP/전월 부정율) → 3차트(브랜드·지점 12주 추이+별점) → 카테고리별 부정 4주(8카테 + 지점×카테 매트릭스) → SOP/매뉴얼 → 최근 부정리뷰+SOP 매핑(15건)+Streamlit 링크.
- ✅ **reviewDaily 출처**: `readTab('리뷰_일별')`(Code.js L7590) ← BI `1tKr70`. 원천 = `REVIEW_SHEET_ID='1CbmPsm…'`(L284) `통합_분류`+`부정리뷰_SOP매핑` 탭을 **`syncReviewData()`**(L792~)가 일별 집계 → BI `리뷰_일별`/`리뷰_부정` write(매일 06시).
- ✅ **6컬럼**: 날짜/리뷰수/부정수(전체감정=='부정' 카운트)/평균별점(전체점수 가중)/SOP위반수(부정리뷰_SOP매핑 매치)/High위반수. `reviewNeg7`=직전7일 부정수 합(L8179).
- ✅ **Supabase 리뷰 미이주 확정**: bridge_*.js 전부 review/리뷰 0건, `syncAllSheetsToSupabase`에 review 미포함. → 리뷰는 Apps Script 내부 BI 순환만.

## 작업4. P13 매장 스코어카드 line-level ✅
- ✅ **6축 + 가중치**(`매장점수(s)` Code.js L8426~8555): 매출10 / 고객만족20 / 인건비20 / 식자재20 / 운영품질20 / 위생10.
| 축 | 라인 | 공식 핵심 |
|---|---|---|
| 매출 | 8429~39 | 달성률(ach) 3-tier piecewise: ≥100%→95~100 / 90~99→85~94 / 80~89→70~84 / 70~79→50~69 / 50~69→20~49 / <50→0~20 |
| 고객만족 | 8479~528 | 외부70%(`호평율×60 + 별점/5×40`) + 자체설문30%(Bayesian: n≥200 그대로, 30~200 `(avg·n+4.5·50)/(n+50)`, n<30 제외). 둘 다 없으면 50 |
| 인건비 | 8441~50 | `diff=(임계-laborPct)×100`(양수=양호) piecewise: ≥+3%p→95~100 / 0~3→70~95 / -2~0→50~70 / -5~-2→20~50 / <-5→0~20 |
| 식자재 | 8452~61 | 인건비와 동일 구조(임계=푸드코스트) |
| 운영품질 | 8463~77 | 부정율(negPct) piecewise(≤5%→90~100…>30%→0~25) − SOP −2점/건 − HighSOP −5점/건 |
| 위생 | 8530~45 | `todayStr>='2026-06-01'` & 시니어점수>0일 때만. `시니어×0.7 + (100-min(50,미해결×10))×0.2 + (100-min(30,유통임박×5))×0.1`. **Critical 미해결≥1 → max 50 GATE** |
- ✅ **종합**(L8547~55): 6축 가중평균. 위생 미가동(6/1 전) 시 5축 `/0.90` 비례 재분배. 현재(6/23)는 6축 활성.
- ✅ **렌더**(`gc_…` 390줄): 점수공식 accordion → KPI요약 → 매장 종합표(80↑초록/60~80주황/<60빨강) → 매장별 6축 레이더+근거+AI액션 → 12개월 heatmap → 4개월 순위변화.
- ⚠ `costRatioScore`(L8409)는 **정의만, 실제 미사용** — 매장점수는 위 `diff` piecewise를 직접 씀(주석/csuite 잔재).

## 작업5. VOC 두 시트 관계 ✅ + 크롤러 ❓(Issue #1)
- ✅ **1Ve6 "(SZI)_전지점 Brand FIC"** = 수기 오프라인 VOC. `브랜드 FIC` 메인탭(기간/지점/채널/날짜/접점/분류/성향/내용/FOH·BOH 파트장 체크) + 지점별 5탭(용산/서울역/성수/여의도/이스트폴). = 우리가 fact_voc로 sync한 원천.
- ✅ **1CbmPsm "심퍼티쿠시 리뷰 아카이브"** = 자동 크롤+분류(19탭 중 4탭 코드확인): `통합_분류`(크롤 raw + **Claude 자동분류**: model=claude-sonnet-4-6, 프롬프트 v3, 채널=catchtable/google/naver_place, 8카테고리[음식·음료·서비스·환경·가격·접근성·예약·특별한날]×[감정·긍정·부정]+신뢰도+검토필요) / `V2_액션보드`(매장×SOP: 항목ID/도메인FOH·BOH/우선순위/위반건수/책임자/핵심표준/개선제안/샘플리뷰) / `V2_항목별빈도` / `부정리뷰_SOP매핑`.
- ✅ **reviewNeg7 연결 확정**(가설 아님): 1CbmPsm `통합_분류`/`부정리뷰_SOP매핑` → `syncReviewData()`(매일06시) → BI `리뷰_일별`/`리뷰_부정` → `readTab` → P11 `reviewNeg7` → P1/P8 이상치(`reviewNeg7≥3 & negRate≥30%` → 알림, `gd_…` L346).
- ⚠ **리뷰 파이프 혼재**: `syncReviewData`가 채우는 건 `리뷰_일별`+`리뷰_부정` 2개뿐. 나머지 4시트(`리뷰_카테고리/키워드/SOP빈도/개선제안`)는 **별도 Python/Streamlit `review_project`**가 채움(P11 L323 `review_project/dashboard.py`). → 리뷰 파이프라인 = **Apps Script sync + 외부 Python 2경로**.
- ❓ **크롤러/자동분류/V2생성 내부 로직 미확보** → **GitHub Issue #1 등록**: bound scriptId 미확보(시트 owner ≠ clasp 로그인 계정, Sheets API 비활성). 운영실장이 아카이브 시트 → 확장프로그램 → Apps Script → scriptId 알려주면 해소.

## Day 4 발견 요약
1. ✅ 회계 9쌍 = 8개 용산 복제본 + 허브 fan-out. 추적로그→fact_account_log 직접 sync(내 bridge, BR_CAT_MAP 8분류). 결산 시스템 = P5와 별개.
2. ✅ [정정] P5 비용 = 브랜드 대시보드(OP 매니저 입력)→매출_월별, 회계 아님. pct 분모=실매출. 4룰②=마진 -5%p high.
3. ✅ P11 reviewNeg7 = 1CbmPsm 자동분류→syncReviewData→BI. Supabase 미이주. 리뷰 파이프 Apps Script+Python 혼재.
4. ✅ P13 6축 piecewise 전체 확보. 위생 6/1 활성, Critical GATE max50.
5. ✅ VOC = 1Ve6(수기 FIC) + 1CbmPsm(자동 크롤+Claude분류). 크롤러 내부는 Issue #1.

## Day 4 질문 (❓)
- ❓ Q13: 외부 Python/Streamlit `review_project`(리뷰_카테고리/키워드/SOP빈도/개선제안 생성)는 어디서 도는가(로컬/서버/GCP)? 이주 대상인가?
- ❓ Q14 (Issue #1): 리뷰 아카이브(1CbmPsm) 크롤러 bound scriptId — 알려주시면 크롤/분류/V2 로직 line-level 확보.
- ❓ Q15: P5 비용(브랜드 대시보드/OP 기원)과 회계 추적로그(fact_account_log)는 **같은 비용을 두 경로로 입력**하는 구조인가? 둘이 reconcile되는가, 독립인가? (이중입력/불일치 리스크 점검)

## 다음 (Day 5)
- P7 예약율 / P8 인사이트(이상치탐지 함수) / P18 위기진단 ctx 빌더 line-level — 운영실장 최우선 3챕터
- Q15 비용 이중경로 reconcile 점검 (P5 OP비용 vs fact_account_log 회계비용 동월 대조)
