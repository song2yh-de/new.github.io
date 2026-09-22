# 롯데카드 FDS_2026 · 정정사항 & 구현 규칙 (Addendum)

> 원본 스펙: `롯데카드 FDS_2026 · Design System Spec` (design.md)
> 기준 화면: ① 수진자 검사결과 조회 (검색영역 + 수진자 목록 + 검사결과 상세) · ② 가맹점정보 조회 (다중 정보카드 대시보드 + 배분 영역)
> 본 문서는 원본 spec을 실제 화면에 적용하면서 확인된 **정정사항 · 세부 규칙 · 신규 결정**을 정리한 것이다.
> **향후 신규 페이지 작업을 요청할 때는 design.md, 이 문서, 그리고 실제 구현체인 `tokens.css`/`components.css`를 함께 참고 자료로 제공할 것.** 이 문서는 "왜 이렇게 정했는지"를 설명하고, `tokens.css`/`components.css`는 그 결정이 실제로 구현된 소스다 — 새 화면은 코드를 재사용하고, 판단이 필요할 때만 이 문서를 참고한다.

---

## 1. 컴포넌트 분류: Table vs Grid

| 판단 기준 | 분류 |
|---|---|
| 정적 조회, 라벨:값 형태의 요약/메타 정보 패널 (예: 수진자 정보 요약) | **table** |
| 헤더+행으로 구성된 레코드 리스트 — 선택/체크/이력조회 등 상호작용 가능 (예: 수진자 목록, 검사결과 상세) | **grid** |

- `table/*` 토큰은 라벨:값 패널에만 사용
- `grid/*` 토큰은 레코드 리스트(테이블처럼 보여도)에 사용
- 판단이 애매하면 "이 안에서 row를 선택/체크하는가"로 구분 → 그렇다면 grid

## 2. 네이밍 규칙 정정 (카테고리/속성/상태 3단 슬래시 통일)

원본 spec의 border 토큰(`table/border`, `table/border-header`)이 나머지 header 토큰(`table/header/bg`, `table/header/text`)과 구조가 달랐던 것을 통일함.

| 기존(원본 spec) | 정정 | 값 |
|---|---|---|
| `table/border-header` | `table/header/border` | slate/200 |
| `grid/border-header` | `grid/header/border` | gray/150 (`#cecece`) |
| `table/header/top` | `table/header/bg-group` | slate/100 (그룹/병합 헤더 배경) |
| `table/header/top-text` | **삭제** — `table/header/text`로 통합 | 기본/그룹 공통 (값이 같으면 별도 토큰 만들지 않음) |
| — | `table/header/border-group` (신설) | 값 미정, 현재는 `table/header/border`와 동일값 임시 적용 |

**원칙**: 토큰은 "값이 달라질 수 있는 지점"에만 만든다. 구조적 대칭을 맞추기 위해 값이 같은데 변수만 늘리지 않는다(예: `text-group`은 만들지 않음).

## 3. Alias 정리

- `grid/header/border`(`#cecece`)는 원본 spec에 direct hex로 박혀 있었으나(spec 1장에 "정리 대상 후보"로 자체 지목), **`gray/150` primitive와 완전히 동일한 값**이므로 alias로 연결함.
- 코드에서 색상 하드코딩(`#hex`)이 보이면, primitive 팔레트에 동일 값이 있는지 먼저 확인하고 alias로 연결할 것.

## 4. 헤더 보더 적용 범위 규칙

`grid/header/border`(또는 `table/header/border`)는 **헤더와 관련된 모든 보더**에 적용한다:
- 그리드/테이블 바깥 테두리(wrapper)
- 헤더 행 하단 구분선
- **헤더 행 내부의 세로(컬럼) 구분선** ← 처음에 누락했던 부분. 바깥/하단만 적용하고 내부 세로선은 기본 `border`(gray/100)로 남아있으면 정정 대상.

바디(행) 영역은 전부 기본 `grid/border`(또는 `table/border`, gray/100)를 사용.

## 5. 색상값 검증 결과 (오해 해소)

- `table/header/bg → brand/10 (#f4f6fb)`은 값 자체가 흰색에 아주 가까운 톤이며, 원본 spec값과 **정확히 일치**함. "너무 진하다"고 느껴졌던 원인은 대부분 다른 토큰(`slate/100` 계열)과 혼동했거나, 인접한 보더/굵은 텍스트가 만드는 시각적 대비 때문이었음 — 실제로 픽셀을 찍어 확인하는 습관이 필요함(6번 참고).
- 색상 관련 의심이 들면 **렌더링 결과를 직접 픽셀 샘플링해서 hex 값을 비교**한다 (스크린샷 눈대중 X).

## 6. Chip 컴포넌트 (수진자 목록 "검사진행" 상태)

원본 spec의 `chip/*` 토큰(브랜드색 vs 회색 2종)을 상태 표시에 사용:

| 상태 | 토큰 | 값 |
|---|---|---|
| 검사중 | `chip/bg` + `chip/fc` | brand/50 + brand/600 |
| 완료 | `chip/g-bg` + `chip/g-fc` | gray/100 + gray/600 |

(주의: `chip/*`은 상태별 시맨틱 컬러(성공=초록 등)가 아니라 브랜드/회색 2종뿐. 상태 의미를 색으로 구분하고 싶다면 `feedback/*` 토큰을 쓸지 여부를 매번 확인할 것 — 이번엔 chip 토큰 그대로 쓰기로 결정함.)

## 7. 버튼 컴포넌트 적용 규칙

| 용도 | 토큰 | 비고 |
|---|---|---|
| "조회" 버튼 | `button/search` (action/search=brand/800, hover brand/900) | `button/primary`(brand/700)와 다른 전용 토큰이 spec에 있음 — 검색성 액션엔 이걸 쓸 것 |
| 카드 상단 액션 버튼 그룹(이력조회/출력/추가 등) | `button/default` | 특별히 강조할 이유가 없으면 전부 default로 통일 |
| 엑셀 다운로드 등 단일 기능 버튼 | `button/icon` (bg: brand/10, fg: gray/600, border: gray/200) | 텍스트 없이 아이콘만, 버튼 그룹 내 위치는 보통 맨 끝(우측) |

브랜드 컬러 아이콘(예: Excel 로고)을 아이콘 버튼에 넣을 땐 `button/icon`의 line-icon 사이즈 규칙(14px)을 그대로 적용하지 말고, 아이콘 자체가 요구하는 사이즈로 별도 클래스를 만들어 오버라이드할 것.

## 8. 레이아웃 구조 — 메인/서브 화면 분리 패턴 ⭐핵심

"목록에서 row를 선택하면 결과가 뜬다"는 흐름을 가진 화면(좌: 목록 / 우: 상세)은 아래 구조를 기본으로 한다:

```
페이지 배경: 흰색
├─ 좌측 (메인 화면 레벨)
│   - 카드/보더 없이 흰 배경에 바로 노출
│   - 자체 padding만 가짐 (예: 20px)
└─ 우측 (row 선택 결과 = 서브 화면)
    - 배경: 회색(bg/subtle), 라운드 없음, 화면 끝까지 꽉 채움(풀블리드)
    - 그 안에 흰 카드(그림자 있음, 라운드 있음)가 콘텐츠를 담음
    - "메인 화면 위에 뜬 하위 레이어"라는 느낌을 시각적으로 줌
```

구현 포인트:
- `body`를 `min-height:100vh; display:flex; flex-direction:column;`으로 구성
- 좌/우 컬럼은 `gap:0`인 flex row로 배치 (흰색↔회색이 바로 맞닿아야 "면분할"처럼 보임)
- 우측 회색 컨테이너는 `border-radius:0`, 좌/우 각각 자체 padding만으로 내부 여백 조정 (바깥 padding 금지)

## 9. Spacing 규칙

### 9-1. 타이틀 → 콘텐츠 간격
- 메인 타이틀(`.tit`)과 서브 타이틀(`.subtit`) **모두 `space/8`로 통일**.
- **중요 버그 패턴**: `<h2>`/`<h3>` 태그에 `.tit`/`.subtit` 클래스를 입히면 브라우저 기본 UA 마진(예: h2는 `0.83em` ≈ 13px)이 남아있어서, 의도한 spacing 값 위에 그대로 더해진다. **heading 태그를 쓸 땐 반드시 `margin:0`으로 리셋한 뒤 의도한 margin만 명시할 것.**
- 의심되면 눈대중하지 말고 실제 DOM에서 `getBoundingClientRect()`로 실측할 것 (12번 참고).

### 9-2. 전체 스케일
- Number 스케일: `4 / 8 / 12 / 16 / 20 / 24`만 사용.
- 이번 화면 감사 결과 스케일 밖 값(6, 7, 10, 14, 18, 22px 등)이 다수 발견됨 — **아직 일괄 정정 보류 상태**. 신규 화면 작업 시 처음부터 스케일 값만 쓰도록 주의할 것.
- 아이콘 내부 조립용 1~2px 같은 초미세 간격은 스케일 예외로 허용 가능(컴포넌트 간 spacing이 아니므로).

## 10. 상세검색(토글) 패턴

"상세검색" 같은 접이식 필드 그룹은:
- 토글 트리거는 실제 `<button>` + `aria-expanded` + `aria-controls`로 구현 (div 아님)
- 펼쳐지는 영역은 `hidden` 속성으로 표시/숨김
- 쉐브론 아이콘은 `aria-expanded="true"`일 때 180도 회전
- 펼쳐진 필드 그룹도 기존 `field-row` 패턴(라벨 64px + 필드 body) 그대로 재사용

## 11. 폰트 대체

- `NanumSquare Neo`는 Google Fonts CDN에 없음 → **`Noto Sans KR`로 대체**하여 사용 중 (실제 폰트 확보 시 교체 필요).

## 12-1. 공유(components.css) vs 화면 전용(로컬 &lt;style&gt;) 판단 기준

새 화면을 만들다가 CSS를 어디에 적을지 애매하면 아래 기준으로 판단한다:

| 판단 기준 | 위치 |
|---|---|
| 두 화면 이상이 구조적으로 동일한 영역을 쓴다 (예: 조치영역, 배분영역, 카드 시스템) | **components.css** |
| 이 화면에만 있는 특수한 예외 처리 (예: 특정 카드의 폭을 다르게) | 화면 파일의 로컬 `<style>` |
| "다른 화면에서도 곧 재사용될 것 같다"는 예상만 있고 아직 2번째 사용처가 없다 | 일단 로컬에 두고, 실제로 2번째 화면이 그 패턴을 쓰게 되는 시점에 components.css로 승격 |

**승격 규칙**: 로컬 `<style>`에 있던 규칙을 components.css로 옮길 땐, 화면 파일에 남기는 자리에 "공통 스타일은 components.css에 있다 — 여기서 재정의하지 말 것" 주석을 남긴다 (조치영역/배분영역에 이미 적용된 패턴 — components.css 470행, 301행 부근 참고). 옮긴 뒤에는 그 컴포넌트를 쓰는 화면들을 전부 다시 렌더링해서 회귀가 없는지 확인한다.

**새 컴포넌트를 components.css에 추가할 때**: 파일 맨 위 목차에 위치를 추가하고, `component-catalog.html`에도 렌더링 예시를 함께 추가한다 — 둘 중 하나라도 빠지면 다음에 같은 패턴을 또 새로 만드는(재발명) 원인이 된다.

## 12. 검증 방법론 (중요 — 눈대중 금지)

색상/간격 관련 이슈는 스크린샷을 눈으로 보고 판단하지 않고, 아래 방식으로 실측 후 답변한다:
1. 헤드리스 Chromium으로 로컬 HTML을 렌더링 (`--headless --screenshot`)
2. 색상 확인: 스크린샷을 픽셀 좌표로 샘플링해서 실제 hex 값 비교 (Python PIL)
3. 간격/레이아웃 확인: Playwright로 실제 DOM을 열어 `getBoundingClientRect()` / `getComputedStyle()`로 실측 (스크린샷보다 정확)
4. 수정 전/후 스크린샷을 바이트 비교(`cmp`)해서 의도치 않은 변화가 없는지 확인 후 배포

---

## 부록: 지금까지 확정된 CSS 커스텀 프로퍼티 전체 목록

```css
/* Table (라벨:값 패널) */
--table-header-bg: brand/10;          --table-header-bg-group: slate/100;
--table-header-text: text/secondary;  --table-header-required: error/500;
--table-header-border: slate/200;     --table-header-border-group: slate/200(임시);
--table-row-bg: bg/base;              --table-row-bg-selected: feedback/warning-bg;
--table-row-text: text/secondary;     --table-border: gray/100;

/* Grid (레코드 리스트) */
--grid-header-bg: slate/100;          --grid-header-text: gray/800;
--grid-header-border: gray/150(alias);--grid-border: gray/100;
--grid-row-bg-selected: orange/50;

/* Chip (상태 표시) */
--chip-fc: brand/600;  --chip-bg: brand/50;
--chip-g-fc: gray/600; --chip-g-bg: gray/100;

/* Button */
--btn-search-bg: action/search(brand/800); --btn-search-bg-hover: brand/900;
--btn-icon-bg: brand/10; --btn-icon-fg: gray/600; --btn-icon-border: gray/200;
```
