---
name: qgis-collect
description: 공공데이터 API 수집기를 의존 순서대로 돌려 00.origins 원본을 받는다. "수집해줘" · "데이터 받아줘" · "collect" · "P3 데이터 수집" 일 때 쓴다. .env 키 이름만 점검하고 드라이런 뒤 본 실행한다. 전처리는 qgis-preprocess 가 한다.
license: CC-BY-NC-ND-4.0
compatibility: 실습 패키지(`C:\qgis_mcp_class`)와 Python 3 가 필요하다. QGIS·MCP 연결은 쓰지 않는다. 수집기 전체를 돌리려면 requests · geopandas · pandas · pyproj 가 있어야 하고, P9 토지피복만(`--only 6`) 돌릴 때는 requests · pyproj 로 된다.
---

## 출력 규칙

- 이 스킬로 답할 때 **첫 줄에 `[qgis-collect 2.1.0]` 을 쓴다.** 예외 없다.
- 수집 결과는 **파일명 · 건수 · CRS** 를 그대로 적는다. "받았습니다" 로 값을 대신하지 않는다.
- **키 값을 적지 않는다.** 키는 이름까지만 쓴다.

## 이 스킬이 하는 일

실습 데이터 중 재배포가 금지된 것은 파일이 아니라 **수집 스크립트**로 배포된다.
이 스킬은 수집 스크립트 6개를 러너(`scripts/collect/collect_all.py`)로 의존 순서대로 돌려
각 프로젝트의 `00.origins` 를 채운다.

이 스킬은 **QGIS 를 쓰지 않는다.** MCP 연결도 필요 없다. 수집이 끝난 뒤
`00.origins` → `01.preprocess` 는 `qgis-preprocess` 가 한다. 여기서 전처리하지 않는다.

**받은 건수가 강의 수치와 다를 수 있다.** 상가업소정보와 아파트 실거래가는 갱신되는
데이터라 건수가 달라진다. 슬라이드 수치와 정확히 대조되는 것은 토지피복(P9)과
DEM(P5·P7)뿐이고, 그 둘은 패키지에 파일로 들어 있어 **수집 없이 바로 실습된다.**

## 단계 6개 — `collect_all.py` 의 `STEPS`

실행 순서는 `--only` 를 어떻게 주든 항상 1→6 이다.

| 단계 | 스크립트 | 받는 것 | 필요한 키 | 선행 |
|---|---|---|---|---|
| 1 | `collect_vworld_admin.py` | 대상지 시군구 경계 8지역 (다른 수집기의 클립 마스크) | `VWORLD_KEY` | — |
| 2 | `collect_vworld_layers.py` | P1 용도지역·도로·하천 / P4 도시계획 공간시설 | `VWORLD_KEY` | 1 |
| 3 | `collect_sgis.py` | P3 전국 시도 17 · P3 광주 · P4 청주 경계+인구 | `SGIS_SERVICE_ID` · `SGIS_SECURITY_KEY` | 1 |
| 4 | `collect_store.py` | P2 대전 서구 상가업소정보 | `DATA_GO_KR_KEY` | — |
| 5 | `collect_realprice.py` | P6 대구 수성구 아파트 실거래가 + 지오코딩 | `DATA_GO_KR_KEY` · `VWORLD_KEY` | — |
| 6 | `collect_landcover_wcs.py` | P9 세종 토지피복 대분류 4시점 | **불필요** | — |

**1 을 가장 먼저 돌린다.** 2 와 3 이 1 의 `_shared\admin\regions_5186.gpkg` 를 클립 마스크로 읽는다.
선행 단계를 빼고 `--only` 를 주면 러너가 "`[2]` 은 `[1]` 의 산출물을 씁니다" 로 경고하고,
그 파일이 있는지(`있음`/`없음`)까지 알려 준다. 실행 여부는 사람이 정한다.

### 프로젝트로 지목받았을 때 — 어느 단계인가

| 요청 | 돌릴 단계 |
|---|---|
| P1 입지 | `--only 1,2` |
| P2 상권 | `--only 4` (`DATA_MANIFEST.md` §3 은 경계용 `VWORLD_KEY` 를 함께 적는다 → 경계가 없으면 `1` 도) |
| P3 인구 | `--only 1,3` |
| P4 공원 | `--only 1,2,3` |
| P6 실거래가 | `--only 5` (같은 이유로 경계가 없으면 `1` 도) |
| P9 토지피복 | `--only 6` — **키 불필요**. 다만 파일이 이미 패키지에 있다 |
| P5 지형 · P7 침수 | 수집 없음. DEM 은 패키지 포함 |
| P8 네트워크 | 수집 없음. 표준노드링크는 **직접 다운로드** (`DATA_MANIFEST.md` §2-8) |
| P10 캡스톤 | 수집 없음. 앞 프로젝트 산출물을 쓴다 |

## 절차

### 1. 실습 루트와 `.env` 존재 확인

| 보는 것 | 기준 |
|---|---|
| 실습 루트 | `C:\qgis_mcp_class` — `scripts\collect\common.py` 의 `DATA_ROOT` 에 고정돼 있다 |
| 수집기 | `C:\qgis_mcp_class\scripts\collect\collect_all.py` |
| 키 파일 | `C:\qgis_mcp_class\.env` — 러너가 보는 경로다(`collect_all.py` 의 `ENV_PATH`) |

- 루트가 다른 경로면 **스크립트가 동작하지 않는다.** 폴더를 `C:\qgis_mcp_class` 로 옮기도록
  안내한다(`DATA_MANIFEST.md` §0). 경로에 한글·공백을 넣지 않는다.
- `.env` 가 없으면 **만들지 말고** 같은 폴더의 `.env.example` 을 복사하라고 안내한다.

  ```
  copy .env.example .env
  ```

  그다음 키 발급은 `handouts\API_KEYS.md` 로 보낸다 — 발급처·절차는 §1(data.go.kr) ·
  §2(SGIS) · §3(V-World) 에 있고, `.env` 작성 규칙은 §5 에 있다.
- 키 이름은 **4개**다. 이름이 정확히 같아야 읽힌다.

  ```
  DATA_GO_KR_KEY   SGIS_SERVICE_ID   SGIS_SECURITY_KEY   VWORLD_KEY
  ```

> **`.env` 를 열어 내용을 보여 주지 않는다.** 파일이 있는지와 키 **이름**까지만 확인한다.
> 값은 화면·로그·보고에 어디에도 적지 않는다.

### 2. 드라이런 — 순서·경로·키 이름 점검

```
python scripts\collect\collect_all.py --dry-run
```

이 모드는 **API 를 호출하지 않는다.** 러너가 하는 일은 세 가지다(`collect_all.py` 의
`show_plan()` · `check_env()`).

1. **실행 계획을 찍는다** — `DATA_ROOT`, 스크립트 폴더, 러너 로그 경로, 단계마다
   스크립트명·설명·필요한 키 이름·선행 단계, 그리고 주요 산출 경로와 `(이미 있음)` 표시.
2. **선행 누락을 경고한다** — 고른 단계가 다른 단계의 산출물을 쓰는데 그 단계가 선택에
   없으면 그 사실과 파일 유무를 적는다.
3. **키를 이름으로만 검사한다** — 고른 단계가 쓰는 키마다 `OK (값 있음)` 또는 `비어 있음`
   을 찍는다. `.env` 에 있지만 이름이 4개 중 어느 것도 아닌 항목은 **오타 탐지용으로 이름만**
   표시한다. 값은 출력되지 않는다.

판정은 종료코드로 갈린다.

| 결과 | 뜻 |
|---|---|
| `드라이런 통과` (코드 0) | 키가 채워졌다. `--dry-run` 을 빼고 다시 실행한다 |
| `드라이런: 키가 부족합니다 -> ...` (코드 0) | 비어 있는 **이름**을 알려 준다. `API_KEYS.md` 해당 절로 보낸다 |

- 키를 하나도 안 넣었어도 **`--only 6` 은 통과한다.** 그 단계는 키가 없고, 러너가
  "선택한 단계에 API 키가 필요하지 않습니다" 로 답한다.
- `--only` 에 단계 번호가 아닌 값을 주면 러너가 **쓸 수 있는 값을 적어 주고 즉시 멈춘다.**
  임의로 고쳐 넣지 않고 그 메시지를 그대로 전한다.

### 3. 본 실행

전체를 돌릴 때.

```
python scripts\collect\collect_all.py
```

프로젝트를 지목받았으면 위 "프로젝트로 지목받았을 때" 표대로 단계를 좁힌다.

```
python scripts\collect\collect_all.py --only 1,3
```

> **전체 수집은 10~30분 걸린다**(`START_HERE.md` §4). **백그라운드로 돌리고 종료를 기다린다.**
> 같은 명령을 반복 호출해 진행 상황을 들여다보지 않는다 — 끝나면 종료 알림이 온다.
> 기다리는 동안 알고 싶은 것은 로그 파일에 쌓인다(아래 4절).

한 단계가 실패하면 러너는 **기본적으로 거기서 멈춘다.** 멈춘 자리, 다음 조치,
끝난 단계, 안 돌린 단계, 그리고 실패 단계에 의존해 지금 돌려도 실패하는 단계를 찍는다.
`--continue-on-error` 를 주면 실패해도 다음 단계로 넘어가고 끝에 실패 목록을 모아 보여 준다.

| 종료코드 | 뜻 |
|---|---|
| 0 | 전 단계 성공 (또는 드라이런) |
| 1 | 한 단계에서 멈췄다 / `--continue-on-error` 로 끝까지 갔지만 실패가 있다 |
| 2 | `.env` 키가 부족해 **시작 전에** 중단했다 |

**`00.origins` 의 기존 원본을 지우거나 덮어쓰기 전에 사람에게 묻는다.** 드라이런 계획표의
`(이미 있음)` 표시가 그 판단 근거다. 수집을 다시 돌리면 같은 경로에 다시 쓴다.

### 4. 산출 확인 — 기대 결과와 대조

러너가 남기는 로그 두 벌을 먼저 본다. 둘 다 한 단계가 끝날 때마다 즉시 flush 된다.

| 로그 | 컬럼 |
|---|---|
| `scripts\_logs\collect_all_<YYMMDD>.csv` (러너 요약) | `ts, step, script, status, seconds, returncode, note` |
| `scripts\_logs\collect_<YYMMDD>.csv` (수집기 자체) | `ts, dataset, source, rows_or_size, crs, status, note` |

그다음 생성 파일을 `DATA_MANIFEST.md` §2 의 기대 결과와 **표로 대조해 보고한다.**
아래가 그 기대값이다(강의 제작 시점 2026-09-11~12 실측).

| 단계 | 파일 | 레이어·건수 | CRS |
|---|---|---|---|
| 1 | `_shared\admin\regions_5186.gpkg` | `daejeon_yuseong`·`daejeon_seo`·`cheongju`·`wonju`·`jeonju`·`daegu_suseong`·`sejong`·`gwangju` 각 1 + `sigungu_all` 16 | EPSG:5186 |
| 2 | `proj01_suitability\00.origins\vworld_yuseong_5186.gpkg` | `zoning_urban` 303 · `zoning_manage` 2 · `zoning_conserve` 1 · `road` 1,845 · `stream` 14 | EPSG:5186 |
| 2 | `proj04_park\00.origins\vworld_cheongju_5186.gpkg` | `upis_space_facility` 2,278 (경계 클립·단일부 분해 후) | EPSG:5186 |
| 3 | `proj03_population\00.origins\sgis_sido_5186.gpkg` + `sido_population.csv` | `sido_boundary` 17 (인구 조인 17/17, 총인구 51,774,521) | 5179 → EPSG:5186 |
| 3 | `proj03_population\00.origins\sgis_gwangju_5186.gpkg` + `sigungu_pop_population.csv` · `emd_pop_population.csv` | `sigungu_pop` 5 · `emd_pop` 97 (인구 조인 95) | 5179 → EPSG:5186 |
| 3 | `proj04_park\00.origins\sgis_cheongju_5186.gpkg` | `emd_pop` 43 (인구 조인 43/43, 총 870,358명) | 5179 → EPSG:5186 |
| 4 | `proj02_trade_area\00.origins\store_daejeon_seogu.csv` + `store_daejeon_seogu_5186.gpkg` | `store` 27,753 (좌표 결측 0) · 기준시점 `stdrYm` = `202606` | 4326 → EPSG:5186 |
| 5 | `proj06_realprice\00.origins\apt_trade_suseong_raw.csv` · `apt_addr_geocode.csv` · `apt_trade_suseong_5186.gpkg` | 거래 2,072 → 좌표 부여 `apt_trade` 2,070 (고유주소 262개 중 지오코딩 실패 1개 = 거래 2건 손실) · 기준 2026-03~2026-08 | 응답에 좌표 없음 → EPSG:5186 부여 |
| 6 | `proj09_landcover\00.origins\lv1_{1980,1990,2000,2010}_3857.tif` | 4장 · 각 1521 × 2033 px · 30 m · uint8 · nodata 15 | EPSG:3857 |

**건수가 다를 수 있는 항목을 보고에 분명히 적는다.**

- **상가업소(4)** — 분기마다 갱신된다. 응답의 `stdrYm` 을 확인해 함께 적는다.
- **실거래가(5)** — 계속 신고·정정된다. 같은 6개월을 요청해도 2,072건이 그대로 나오지 않는다.
  슬라이드·검증표의 수치는 **강의 제작 시점 스냅샷**이다.
- **용도지역·도시계획시설(2)** — 고시가 갱신되면 건수가 변한다.
- **인구(3)** — `YEAR="2023"` 고정이라 같아야 한다. 다르면 SGIS 가 기준연도를 개편한 것이다.
- **토지피복(6)** — 파일로 배포되므로 변하지 않는다. 단 패키지 동봉본은 EPSG:3857 태그가
  기록된 상태라 **각 3,146,755 B** 이고, 다시 수집하면 응답 원본인 **3,146,213 B** 가 된다.
  값은 같고 좌표계 정보만 다르다(`DATA_MANIFEST.md` §2-9 주의 ②).
- **`zoning_agri`(농림 `uq113`) 레이어가 없는 것은 오류가 아니다.** 유성구 범위에 0건이라
  스크립트가 건너뛴다.
- **읍면동 2개의 인구가 조인되지 않는 것도 실측 결과다.** 분석은 `IS NOT NULL` 로 유효분만 쓴다.

**건수가 0 이면 정상이 아니다.** 키 문제이거나 대상지 코드가 바뀐 것이다 → 5절.

### 5. 실패했을 때 — 어디를 보나

러너가 단계마다 "다음 조치" 를 찍는다. 그 문장을 그대로 전하고, 아래 포인터를 덧붙인다.
**원인을 추측해 지어내지 않는다.**

| 증상 | 보낼 곳 |
|---|---|
| `SERVICE_KEY_IS_NOT_REGISTERED_ERROR` · 결과코드 `30` | `API_KEYS.md` §1-3. 그 API 의 **활용신청**이 승인됐는지 본다. 키는 계정당 하나지만 활용신청은 API 별로 따로다(§1-1) |
| `API 오류: <메시지>` 로 멈춤 | `API_KEYS.md` §1-3. 포털이 준 원문 메시지다. 그 문장으로 포털 오류코드를 찾는다 |
| 인증키를 넣었는데 계속 키 오류 | `API_KEYS.md` §1-3 — **Decoding 키**를 넣는다 |
| `SGIS auth 실패` | `API_KEYS.md` §2-3. 서비스 ID 와 보안키를 **한 쌍으로** 다시 확인한다. 한쪽만 바꿔 넣는 실수가 흔하다 |
| `청주 adm_cd 탐색 실패` · 엉뚱한 지역 | `API_KEYS.md` §2-3. SGIS `adm_cd` 는 행정표준코드와 다른 자체 체계다 |
| `WFS exception:` · 인증·도메인 의심 | `API_KEYS.md` §3-1 3번(서비스 URL 등록) · §3-2 |
| `INVALID_RANGE` | `API_KEYS.md` §3-2 — WFS `maxFeatures` 1000 상한. 스크립트가 타일 분할로 우회한다 |
| `sig_cd 검증 실패` 로 아무것도 저장하지 않고 멈춤 | V-World 행정코드 개편이다(`API_KEYS.md` §3-2 마지막 행). `collect_vworld_admin.py` 의 `REGIONS` 를 갱신해야 한다 |
| 2 또는 3 이 선행 산출물을 못 찾음 | `--only 1` 로 1 단계부터 다시 돌린다 |
| 6(P9) 실패 | **EGIS WCS 는 키가 필요 없다**(`API_KEYS.md` §4). 서버 점검이거나 `coverageId` 개편이다. **이 데이터는 패키지에 파일로 들어 있어 다시 받지 않아도 실습된다** |

**막혀 있어도 할 수 있는 실습이 있다는 것을 알린다.**

- **P9 토지피복** — 키가 하나도 없어도 바로 된다(파일 포함).
- **P5 지형 · P7 침수** — DEM 이 패키지에 들어 있다. 키 없이 바로 실습된다.
- 키를 손으로 하나씩 확인해 보고 싶으면 `API_KEYS.md` §8 의 `api_demo\api_demo.py` 를 쓴다.
  `requests` 와 표준 라이브러리만 쓰므로 geopandas 설치 전에도 돌아간다.

### 6. 보고

```
[qgis-collect 2.1.0]
실행: <명령 그대로>            (종료코드 <n>)
루트: C:\qgis_mcp_class       .env: 있음 / 없음
키(이름만): <검사한 이름과 OK·비어 있음>
단계별: <step / status / seconds / returncode — 러너 로그 그대로>
대조표: <파일 | 받은 건수 | 기대 건수 | CRS | 일치·불일치·갱신형>
갱신형 항목: <건수가 달라도 정상인 항목과 그 이유>
남은 것: <안 돌린 단계, 직접 받아야 하는 자료>
```

대조표에서 **일치하지 않은 행을 지우거나 기대값으로 바꿔 적지 않는다.** 받은 값을 그대로 적고
갱신형인지 아닌지로 가른다.

## 하지 않는 것

- **키 값을 출력하지 않는다.** 보고·로그·터미널 어디에도 적지 않는다. 키는 이름까지만 쓴다.
- **`.env` 의 내용을 `echo` · `type` · `cat` 으로 찍지 않는다.** 파일 존재와 키 이름은
  `--dry-run` 이 값 없이 알려 준다.
- **`.env` 를 대신 만들어 채우지 않는다.** 발급과 입력은 사람이 한다.
- **`00.origins` 의 기존 원본을 확인 없이 지우거나 덮어쓰지 않는다.** 재수집 전에 묻는다.
- 수집기 코드를 임의로 고치지 않는다. 행정코드 개편처럼 갱신이 필요하면 그 사실을 보고하고 묻는다.
- 전처리하지 않는다. `00.origins` 까지가 이 스킬의 일이고 `01.preprocess` 는 `qgis-preprocess` 다.
- 받지 못한 데이터를 다른 자료로 대체하지 않는다. 비었으면 비었다고 적는다.
- 건수를 확인하지 않은 채로 "수집됐다" 고 보고하지 않는다.
