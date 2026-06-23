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
