# 변경 이력

버전 규칙은 semver 입니다. `README.md` 의 버전 표를 보십시오.

## 2.0.0 — 2026-10-06

**플러그인 식별자를 개명했습니다. 구버전에서 자동 갱신되지 않습니다.**

- **식별자 `qgis-mcp-course` → `upwise-qgis-mcp-toolkit`.** `plugin.json` 과 `marketplace.json` 의
  `name` 이 둘 다 바뀌었다. 식별자가 바뀌면 `claude plugin update` 가 같은 플러그인으로 보지 않으므로
  **MAJOR** 다. `README.md` 버전 표의 MAJOR 행에 "플러그인 식별자(`name`)가 바뀔 때" 를 넣었다
- **표시명 `QGIS + Claude MCP 완전 정복` → `UPWISE QGIS MCP Toolkit`.** 강의명은 그대로이고
  플러그인 표시명만 바꿨다. 탐색 목록과 설치 경고 팝업에 이 이름이 나온다.
  README 의 경고 팝업 인용문도 새 표시명으로 적었다 — **v2.0.0 재설치 때 실물 문구를 재확인한다**
- **저장소 주소를 `upwise-edu/upwise-qgis-mcp-toolkit` 으로 바꿨다.** `plugin.json` 의
  `homepage` · `repository`, `README.md` 의 설치·갱신 명령, `LICENSE-docs` 의 출처 표시 형식이 대상이다
- **스킬 호출 접두가 `/qgis-mcp-course:` → `/upwise-qgis-mcp-toolkit:` 으로 바뀐다.**
  스킬 폴더명 4종(`qgis-connect-check` · `qgis-preprocess` · `qgis-verify` · `qgis-style`)은 그대로다
- **마이그레이션 절을 신설했다.** 구버전 설치자는 `claude plugin uninstall qgis-mcp-course` 와
  `claude plugin marketplace remove qgis-mcp-course` 로 지운 뒤 새 이름으로 다시 설치한다
- `keywords` 에 `toolkit` 을 넣었다. `author` · `license` 는 그대로다

실측으로 남아 있던 백로그 세 건을 스킬 본문에 반영했습니다.

- **`qgis-connect-check` 의 서버 켜는 위치를 정정했다.** "QGIS MCP 도크 위젯에서 서버를 켜야" 는
  **구버전 UI 서술**이었다. 0.15.0 설치본에는 도크 위젯이 없다 — `QDockWidget` · `addDockWidget` 가
  전수 탐색 0건이고, Run MCP 는 `iface.pluginToolBar()` 의 툴버튼 액션이다(`plugin.py:72-75,125`).
  켜지면 **툴바 아이콘이 초록**으로 바뀌고 `MCP :9876` 문자열은 **툴팁과 플러그인 메뉴 항목 이름**에만
  보인다(툴버튼은 `setToolButtonStyle(ICON_ONLY)` 로 아이콘만 그린다 — `plugin.py:124,127,135,355-370`).
  근거는 강의 저장소 장부 `scripts/verify_ledger.csv` 의 **V18-1 · V18-9**(설치본 소스 실측)
- **`qgis-connect-check` 4절에 함정 20 의 행동 한 줄을 넣었다.** 스킬 4종에는 프로젝트를 열거나 닫는
  호출이 없어 열려 있는 프로젝트가 무엇이든 거기에 레이어가 쌓인다.
  **경고가 보이면 그 프로젝트를 닫고 빈 프로젝트나 실습 프로젝트에서 다시 시작한다**
- **`qgis-style` 3절에 함정 19 의 한 줄을 넣었다.** 되읽기 대조는
  `qgis/renderer-v2/symbols/symbol[N]/layer`(래스터는 `rasterrenderer`) **하위로 경로를 한정**한다.
  QML 을 앞에서부터 훑어 첫 `SimpleFill` 을 잡으면 QGIS 3.44 가 모든 내보내기 QML 에 붙이는
  `qgis/elevation/profileFillSymbol` 심볼이 걸려 DIFF 오탐이 난다
- **1.1.0 항목의 "도크는 `Server: not running` 표시" 는 오기였다 (아래에서 정정).**
  그 라벨은 도크가 아니라 플러그인 메뉴의 `MCP Setup Configurator` 대화상자 안에 있다
  (`configurator.py:592`/`595`)
- **`skills/qgis-preprocess/SKILL.md` 는 이번 판에서 손대지 않았다.** 자동 생성물이라
  헤더의 버전 리터럴이 아직 `1.1.0` 이다. 강의 저장소에서 `python scripts/build_skills.py` 를 돌리면
  `plugin.json` 의 2.0.0 으로 맞춰진다. 다른 스킬 3종과 `README.md` 의 버전 줄은 같은 값으로 미리 맞췄다

## 1.1.0 — 2026-09-30
- 서버 켜는 버튼 라벨을 실물대로 **Run MCP** 로 통일(업스트림 v0.15.0 `plugin.py` 툴바 액션 원문). 이전 문서의 `Start Server` 는 오기
  — ~~도크는 `Server: not running` 표시~~ **2.0.0 정정: 0.15.0 에는 도크 위젯이 없다.** 그 라벨은 플러그인 메뉴의 `MCP Setup Configurator` 대화상자 안에 있다
- 표준 스타일 25개(QML 22 + 치환 템플릿 3). `p9_landcover_major_7.qml` 신설 — 토지피복 대분류(값 1~7) 래스터용, 색은 환경부 「토지피복지도 작성지침」 별표1 원문 RGB(law.go.kr flSeq=158209827)

기준 환경을 승격하고 설치 절을 다시 짰습니다. 스킬 4종의 절차 본문과 동봉 자산은 그대로입니다.

- **기준 환경을 QGIS MCP 플러그인 0.15.0 + 서버 `v0.15.0` 태그로 승격했다.**
  QGIS 플러그인 관리자가 이제 0.15.0 을 주므로 수강생은 **0.14.0 을 고를 수 없다.**
  업스트림 0.14.0 → 0.15.0 대조에서 **삭제된 도구 0건**, 스킬 4종이 참조하는 도구 28개 전부 존재,
  시그니처 변경 9건은 **전부 선택 매개변수 추가**라 기존 호출이 그대로 동작한다.
  `.mcp.json` 의 서버 인자를 `refs/tags/v0.15.0.zip` 로 올렸다 (HTTP 200 확인).
  스킬 4종의 `compatibility` 와 `README` 요건 표도 같은 값으로 맞췄다
- **`qgis-connect-check` 의 플러그인 버전 판정을 세 갈래로 바꿨다.** 일치·불일치 두 갈래로는
  "기준보다 높다" 를 실패처럼 보이게 만들었다. 이제 `기준 0.15.0 / 일치` ·
  `0.15.0 보다 높음: 강의 검증 범위 밖, 결과가 이상하면 버전 차이를 먼저 의심` ·
  `0.15.0 보다 낮음: QGIS 플러그인 관리자에서 업그레이드` 중 하나를 그대로 적는다.
  값을 못 읽으면 여전히 "확인 불가" 이고, 어느 갈래든 실패 사유가 아니다
- **설치 절을 일곱 절로 다시 짰다** — 1 사전 요건 / 2 기본 경로(앱) / 3 설치 확인 /
  4 탐색에 안 보일 때 자기진단 / 5 대안 로컬 폴더 / 6 터미널 CLI / 7 Codex · Cursor.
  기존의 별도 "요건" 절은 1 로 흡수했다
- **사전 요건에 Git for Windows 를 넣었다.** git 이 없으면 저장소를 클론할 수 없어
  마켓플레이스 등록이 실패하고, 등록만 남은 상태에서는 카탈로그를 못 받아
  **탐색·내 항목에 플러그인이 안 뜬다** (2026-09-30 실측)
- **팝업 함정을 적었다.** "저장소에서" 칸에 입력하면 아래 목록에 후보가 바로 보이는데,
  **오른쪽 "추가" 버튼을 한 번 더 눌러야 등록된다.** 그대로 창을 닫으면 등록만 남는다.
  칸 안내 문구가 "GitHub owner/repo 형식 또는 Git 저장소 URL" 이고 raw json 주소는 실패한다는 것도 적었다
- **설치 확인 절을 신설했다.** 연결 확인 스킬보다 **먼저** 내 항목(Installed)의 `qgis-mcp-course` 와
  새 세션의 `/qgis-mcp-course:` 자동완성을 본다. 스킬이 안 붙은 세션도 헤더 없이 답을 내놓기 때문에,
  순서를 바꾸면 QGIS 문제인지 설치 문제인지 갈리지 않는다
- **자기진단 절을 신설했다.** 탐색에 안 보이면 ① "추가" 버튼 누락 ② git 없음 순으로 의심한다
- **대안 경로로 로컬 폴더 설치를 적었다.** "저장소에서" 칸에 강의 패키지의 `plugin\` 폴더 경로를
  넣으면 **git 없이** 설치된다 (2026-09-30 실측). 대신 `claude plugin update` 로 갱신되지 않는다
- Codex `config.toml` 예시의 서버 태그도 `v0.15.0` 으로 올렸다
- **작성자 표기를 UPWISE 로 변경(이력 재작성 예정).** `plugin.json` 의 `author` 는 이미 UPWISE 이고,
  커밋 작성자 표기를 맞추는 이력 재작성은 별도로 수행한다

## 1.0.3 — 2026-09-26

문서 정정판입니다. 스킬 본문의 절차·동봉 자산·서버 태그는 그대로입니다.

- **설치 절을 앱 경로로 고쳤다.** Desktop 앱 Code 탭에서 `/plugin` → 플러그인 관리 팝업 →
  **마켓플레이스 추가 ▸ 저장소에서** → `upwise-edu/qgis-mcp-course` 순서다.
  1.0.2 까지 적혀 있던 "앱 안에는 마켓플레이스 추가 화면이 없다" 와 `+ ▸ Plugins ▸ Add plugin` 경로는
  실측과 달라 지웠다. 앱 경로를 CLI 절보다 앞에 둔다 — 수강생 기본 경로다
- `/plugin` 뒤에 인자를 붙이면 **인자가 무시되고 팝업만 열린다**는 주의를 넣었다
- **헤더 예시에서 버전 번호를 뺐다.** `[qgis-connect-check <버전>]` 처럼 적는다.
  `README.md` 에 버전 리터럴은 "버전" 절 한 줄만 남기고, 그 한 줄도 빌드가 `plugin.json` 에서 맞춘다
- **동봉 스크립트 요건 목록을 실제 import 로 통일했다** — Python 3 와
  fiona · geopandas · numpy · pandas · rasterio. `pandas` 가 빠져 있었다
- **공개 저장소에서 볼 수 없는 내부 문서·로그 경로 참조를 지웠다.** 스킬 본문과 동봉 스크립트
  두 개의 docstring 을 자기완결 문장으로 고쳤다 (로그 컬럼은 문서를 가리키지 않고 그 자리에 적는다)
- **업스트림 라이선스를 정정했다.** 저장소 `nkarasiak/qgis-mcp` 와 QGIS 플러그인은 GPL v2+,
  `uvx` 로 띄우는 서버 패키지 `src/qgis_mcp` 는 MIT 다. 이 플러그인은 그 코드를 포함하지 않고
  실행 명령만 참조한다
- **Codex 스킬 확인법을 실측으로 고쳤다.** `$qgis-connect-check` 로 지목해 첫 줄 헤더로 본다.
  `codex` CLI 0.145.0 에는 `/skills` 서브커맨드가 없다
- 이중 등록 제거 명령에 범위를 붙였다 — `claude mcp remove qgis -s user`
- 라이선스 표에 `*.qml.tmpl`(문서 자산)과 `.gitattributes`(설정, 라이선스 대상 아님) 를 적었다

## 1.0.2 — 2026-09-26

라이선스를 제한형으로 교체(MIT→PolyForm NC, CC BY→CC BY-NC-ND). 기능 변경 없음.

- 코드 — MIT 를 **PolyForm Noncommercial 1.0.0** 으로 교체했다. `LICENSE` 에 공식 원문 전문을 넣었다
- 문서·스킬 본문·스타일 자산 — CC BY 4.0 을 **CC BY-NC-ND 4.0** 으로 교체했다.
  `LICENSE-docs` 에 공식 legal code 전문과 한국어 요약을 넣었다
- 허용되는 것은 개인 학습·연구·사적 수정이고, 금지되는 것은 상업적 이용·재배포·개작본 공개다.
  출처 표시는 필수다. **강의 수강생에게도 같은 조건이 적용된다** (`README.md` 의 "라이선스" 절)
- 스킬 4종의 프론트매터 `license:` 를 `CC-BY-NC-ND-4.0` 으로 바꿨다
- **스킬 본문·동봉 자산·서버 태그는 그대로다.** 이 판은 라이선스 교체만 한다

## 1.0.1 — 2026-09-25

QGIS 3.44.14 로 실측한 결과를 반영한 문구 수정판입니다. 스킬 이름과 동봉 자산은 그대로입니다.

- `qgis-preprocess` — 로그 CSV 값 안에 쉼표가 있으면 그 칸을 큰따옴표로 감싸고 열 수를 센다는 규칙을 넣었다.
  값에 쉼표가 든 행 하나가 8열이 아니라 9열로 밀려 나온 일이 있었다 (정본 `PREPROCESS.md` 수정 후 재생성)
- `qgis-preprocess` · `qgis-verify` — 동봉 스크립트가 프로젝트 경로를 `C:\qgis_mcp_class\projNN_*` 로
  고정해 두었다는 경고를 넣었다. 실습 폴더가 다르면 스크립트를 쓰지 않고 MCP 도구만으로 수행한다
- `qgis-style` — `get_layer_style` 이라는 도구는 없다. 적용 결과 되읽기는 `save_style_qml` 또는
  `render_map` 으로 한다는 한 줄을 넣었다
- `README.md` — Codex · Cursor 스킬 폴더는 **여는 폴더 바로 아래** `.agents\skills\` 여야 한다.
  상위 폴더는 스캔되지 않는다. 스킬이 안 붙으면 조용히 자기 방식으로 처리하므로 첫 줄 헤더로 판별한다
- `README.md` — 이미 `qgis` MCP 서버를 직접 등록해 둔 경우 서버가 두 벌 뜬다. 플러그인을 쓰면
  직접 등록한 항목을 지우라는 안내를 넣었다
- MCP 서버 태그는 `v0.14.0` 을 유지한다. 실측에서 이 태그 하나로 전 항목이 성립했고,
  `0.15.0` 쪽은 QGIS 플러그인 0.14.0 과 불일치를 스스로 보고했다

## 1.0.0 — 2026-09-25

첫 배포판입니다.

- 스킬 4종 신설 — `qgis-connect-check` · `qgis-preprocess` · `qgis-verify` · `qgis-style`
- `qgis-preprocess/SKILL.md` 는 강의 저장소의 `handouts/PREPROCESS.md` 에서 자동 생성한다. 손편집하지 않는다
- 표준 스타일 24개 동봉 — QML 21 + 치환 템플릿 3. 수강생에게 처음 배포되는 자산이다
- 동봉 스크립트 2개 — `verify_preprocess.py` · `verify_outputs.py`. 둘 다 선택 실행이다
- `.mcp.json` 은 QGIS MCP 서버를 `v0.14.0` 태그로 고정한다. `main` 을 쓰지 않는다
- 라이선스 분리 — 코드 MIT, 문서와 스타일 자산 CC BY 4.0
- `qgis-project`(프로젝트 10선 플레이북)는 1.1 로 미뤘다
