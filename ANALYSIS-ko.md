# beautiful-html-templates 분석 & 활용 정리 (한국어)

> 작성일: 2026-09-18
> 대상 레포지토리: https://github.com/bmshin94/beautiful-html-templates
> 원본(Upstream): https://github.com/zarazhangrui/beautiful-html-templates
> 라이선스: MIT

---

## 목차

1. [이게 뭐하는 프로젝트인가](#1-이게-뭐하는-프로젝트인가)
2. [쉽게 이해하기](#2-쉽게-이해하기)
3. [설치 및 사용법](#3-설치-및-사용법)
4. [플러그인 / 스킬 / MCP 중 무엇인가](#4-플러그인--스킬--mcp-중-무엇인가)
5. [API 토큰이 필요한가](#5-api-토큰이-필요한가)
6. [왜 GitHub에서 유명한가](#6-왜-github에서-유명한가)
7. [로컬 에이전트 구축에 도움이 되는가](#7-로컬-에이전트-구축에-도움이-되는가)
8. [React / PHP로 만들 수 있는가](#8-react--php로-만들-수-있는가)
9. [수익화 아이디어](#9-수익화-아이디어)
10. [핵심 요약](#10-핵심-요약)

---

## 1. 이게 뭐하는 프로젝트인가

### 한 줄 정의

**AI 코딩 에이전트가 읽고 스스로 "예쁜 HTML 슬라이드 덱"을 만들어주도록 설계된
재사용 가능한 템플릿 라이브러리.**

사람이 직접 쓰라고 만든 템플릿 모음이 아니라, **AI 에이전트를 1차 사용자로 상정하고
설계된 "에이전트 네이티브(agent-native)" 리소스 레포지토리**다.

### 실측 폴더 구조

```
beautiful-html-templates/        (총 135MB)
├── AGENTS.md          13KB   AI 에이전트용 작업 매뉴얼 (핵심)
├── README.md          15KB   34개 템플릿 스크린샷 갤러리
├── index.json         41KB   34개 템플릿 메타데이터 카탈로그
├── CLAUDE.md           1KB   Claude Code용 페르소나 설정 (포크에서 추가)
├── LICENSE                   MIT
├── .gitignore                .tools/, previews/, docs/ 등 제외
├── templates/                34개 템플릿 폴더
│   └── <slug>/
│       ├── template.html   ~39KB  슬라이드 12~18장 통짜 HTML
│       ├── template.json   ~1.6KB 템플릿 성격/메타데이터
│       ├── design.md       ~43KB  기계 판독용 디자인 시스템 명세
│       └── deck-stage.js   ~22KB  슬라이드 네비게이션 런타임
├── runtime/
│   └── deck-stage.js  22KB   런타임 마스터 사본
├── screenshots/              102장 PNG (템플릿당 3장)
└── scripts/
    ├── build-index.mjs       index.json 자동 생성
    └── new-template.mjs      새 템플릿 스캐폴딩
```

**주목할 점: `package.json`이 없다.** 즉 npm install도, 빌드 단계도, 번들러도,
외부 의존성도 전혀 없는 순수 HTML/CSS/JS 자산이다.

### 34개 템플릿 목록

`8-bit-orbit`, `biennale-yellow`, `block-frame`, `blue-professional`, `bold-poster`,
`broadside`, `capsule`, `cartesian`, `cobalt-grid`, `coral`, `creative-mode`,
`daisy-days`, `editorial-forest`, `editorial-tri-tone`, `emerald-editorial`, `grove`,
`long-table`, `mat`, `monochrome`, `neo-grid-bold`, `peoples-platform`, `pin-and-paper`,
`pink-script`, `playful`, `raw-grid`, `retro-windows`, `retro-zine`, `sakura-chroma`,
`scatterbrain`, `signal`, `soft-editorial`, `stencil-tablet`, `studio`, `vellum`

### 핵심 부품 4가지

#### (1) `index.json` — 얇은 카탈로그

```json
{
  "slug": "soft-editorial",
  "name": "Soft Editorial",
  "tagline": "Cormorant Garamond serif on warm paper with sage, blush, and lemon accents.",
  "mood": ["literary", "elegant", "quiet", "warm-classical"],
  "occasion": ["editorial feature", "longform brand story", "gallery or museum"],
  "tone": ["literary", "considered", "warm", "magazine"],
  "formality": "high",
  "density": "low",
  "scheme": "light",
  "best_for": "문학적이고 우아하고 여유로운 느낌이 필요한 모든 것...",
  "avoid_for": "시각적 열기나 펀치가 필요한 덱",
  "slide_count": 12
}
```

`scripts/build-index.mjs` 주석에 설계 의도가 명시되어 있다.

> "에이전트는 어떤 템플릿을 쓸지 결정할 때 이 인덱스 파일만 읽는다. 그다음 선택된
> 템플릿의 HTML만 읽는다. 라이브러리가 커져도 컨텍스트 비용이 일정하게 유지된다.
> 템플릿이 100개가 넘어가도 인덱스는 여전히 몇 KB다."

**→ LLM 컨텍스트 예산을 의도적으로 관리하는 아키텍처.**

#### (2) `AGENTS.md` — 에이전트 작업 매뉴얼

| 섹션 | 내용 |
| --- | --- |
| §1 | 6단계 워크플로우 (아래) |
| §2 | index.json 필드 해석 규칙 |
| §3 | 보존할 것 / 교체할 것 |
| §4 | 톤 우선 매칭 철학 |
| §5 | 없는 레이아웃을 직접 설계하는 규칙 |
| §6 | 흔한 실수 7가지 |
| §7 | 출력 계약 |

**6단계 워크플로우**

| 단계 | 내용 |
| --- | --- |
| 1 | "어떤 자리인가 / 어떤 분위기인가" 사용자에게 **반드시** 먼저 질문 |
| 2 | `index.json`을 읽고 서로 충분히 다른 후보 3개 선정 |
| 3 | 각 후보의 표지 슬라이드를 사용자 실제 내용으로 채워 미리보기 생성 |
| 4 | 3개를 브라우저로 열고 경로와 함께 전달, 선택 대기 |
| 5 | 선택된 템플릿 폴더 복사 후 전 슬라이드 내용 교체 |
| 6 | 완성본을 브라우저로 열고 절대경로 + 한 줄 근거 전달 |

**보존 / 교체 규칙 (§3)**

| 절대 보존 (= 디자인 시스템) | 반드시 교체 (= 사용자 콘텐츠) |
| --- | --- |
| 폰트 (`font-family`) | 헤드라인 `h1`~`h3` |
| 색상 팔레트 (`:root` 변수 전체) | 본문, 리스트, 캡션 |
| 레이아웃 그리드 / 절대좌표 | 숫자·통계 (`47%`, `2.4M`) |
| 슬라이드 CSS 클래스 (`.s-cover` 등) | 이름·날짜·출처 |
| 장식 요소 (종이 질감, 코너 브라켓, SVG) | `[Topic]` 등 플레이스홀더 토큰 |
| 네비게이션 런타임 | 이미지 자리 (동일 크기 유지) |

**톤 우선 매칭 (§4)**

> "템플릿은 업종이 아니라 톤을 가진다."
> "자신감 있는 편집 디자인 덱이 기술 발표를 담아도 된다."
> "사용자의 취향이 이긴다 (the user's taste wins)."
> 좋은 질문: "권위 있게? 아니면 따뜻하고 디자인 주도적으로?"
> 나쁜 질문: "금융인가요 테크인가요?"

**흔한 실수 (§6) 중 인상적인 것**

- "Inter도 비슷하잖아 — 아니다. 타이포그래피가 곧 디자인 시스템이다."
- "장식이 노이즈라고 생각해서 빼지 마라. 그게 정체성이다."
- "서로 다른 템플릿의 레이아웃을 섞지 마라. 각 템플릿은 닫힌 시각 체계다."

#### (3) `design.md` — 기계 판독용 디자인 명세 (템플릿당 약 43KB)

YAML 프론트매터 + 31개 섹션으로 구성:

```yaml
colors:
  paper: "#F2EEDF"
  ink: "#2A241B"
  pink: "#E1A4C2"
  lemon: "#D6DD63"
typography:
  display:
    fontFamily: "Cormorant Garamond, Garamond, serif"
    fontSize: 232px
    fontWeight: 500
    lineHeight: 0.92
    letterSpacing: -0.02em
```

섹션 구성: Overview / Colors / Typography / Layout / Depth and Elevation /
Shapes and Treatment / Do's and Don'ts / Responsive Behavior /
**CJK & International Content** / Iteration Guide / Known Gaps

**→ 사람이 읽는 스타일 가이드가 아니라, AI가 파싱해서 없던 슬라이드를 새로
그릴 때 참조하는 디자인 토큰 데이터베이스.**

#### (4) `deck-stage.js` — 웹 컴포넌트 런타임

```html
<deck-stage width="1920" height="1080">
  <section class="slide s-cover">...</section>
  <section class="slide s-quote">...</section>
</deck-stage>
```

제공 기능:

- 키보드 네비게이션 (←/→, Space, PgUp/PgDn, Home/End, 숫자키, `R`=리셋)
- 1920×1080 고정 캔버스를 `transform: scale()`로 뷰포트에 맞춤 (레터박스)
- `noscale` 속성 시 원본 크기 렌더 (PPTX 익스포터용)
- 발표자 노트 (`<script type="application/json" id="speaker-notes">`)
- `@media print` — 슬라이드 1장 = PDF 1페이지
- 비활성 슬라이드는 언마운트하지 않고 `visibility: hidden` (동영상/iframe 상태 보존)
- `slidechange` CustomEvent (index, previousIndex, total, slide, reason)
- 자동 태깅: `data-screen-label`, `data-om-validate`

### 어떤 상황에 쓰는가

| 상황 | 활용 |
| --- | --- |
| 투자 피치덱 | 톤만 정하면 34개 중 매칭 |
| 강의 / 세미나 자료 | PowerPoint 없이 HTML로 |
| 브랜드 매니페스토, 리서치 공유 | 매거진급 편집 디자인 |
| 사내 분기 리뷰 | 시각적 차별화 |
| AI가 만든 덱이 못생겼을 때 | 이 문제를 정면으로 해결 |

---

## 2. 쉽게 이해하기

### 요리 비유

| 레포 구성요소 | 비유 |
| --- | --- |
| `templates/` 34개 | 완성된 요리 34종 (레시피 포함) |
| `index.json` | 메뉴판 — 이름·맛·매운맛만 적힌 얇은 종이 |
| `design.md` | 셰프 레시피북 — 소금 3g, 180도 12분 수준의 정밀도 |
| `AGENTS.md` | 주방 매뉴얼 — "손님 취향을 먼저 물어봐라" |
| `deck-stage.js` | 접시 — 무엇을 담아도 예쁘게 보이게 함 |
| `screenshots/` | 메뉴판의 음식 사진 |
| AI 에이전트 | 요리사 |
| 사용자 | 손님 (취향만 말하면 됨) |

**메뉴판을 따로 둔 이유**: 레시피북 34권(1MB 이상)을 요리사가 전부 읽으면 컨텍스트가
터진다. 얇은 메뉴판(41KB)으로 3개를 먼저 좁히고, 그 3개의 레시피만 정독하게 한 것.

### 실제 사용 흐름

```
사용자: "우리 서비스 투자 피치덱 만들어줘"

AI:    "두 가지만 여쭤볼게요.
        1. 어떤 자리인가요?
        2. 어떤 분위기를 원하세요?"          ← AGENTS.md가 스킵 금지로 강제

사용자: "VC 미팅, 자신감 있고 세련되게"

AI:    (index.json에서 mood:confident + formality:medium-high 매칭)
       "세 가지 후보 표지를 만들어봤어요.
        1. Neo-Grid Bold  /path/previews/01-neo-grid-bold.html
        2. Signal         /path/previews/02-signal.html
        3. Vellum         /path/previews/03-vellum.html"
        ← 실제 회사명/제목이 박힌 표지 3개가 브라우저에 열림

사용자: "1번"

AI:    (폴더 복사 → 12장 전부 교체 → 부족한 레이아웃은 design.md 보고 직접 설계)
       "완성: /path/deck/template.html"
```

### 왜 PowerPoint가 아니라 HTML인가

| 항목 | PowerPoint | 이 레포 (HTML) |
| --- | --- | --- |
| AI가 수정 | 바이너리라 어려움 | 텍스트라 쉬움 |
| 버전 관리 | git diff 불가 | 깔끔한 diff |
| 공유 | 파일 첨부 | URL 링크 |
| 표현력 | 제한적 | CSS 애니메이션·인터랙션 |
| PDF 변환 | 내장 | 브라우저 인쇄 (`@media print` 내장) |
| 설치 | Office 필요 | 브라우저만 |

---

## 3. 설치 및 사용법

### 설치

```bash
git clone https://github.com/bmshin94/beautiful-html-templates
cd beautiful-html-templates
```

`package.json`이 없으므로 추가 설치 단계가 없다. 의존성 0.

```bash
# 템플릿 바로 열어보기
open templates/soft-editorial/template.html       # macOS
xdg-open templates/soft-editorial/template.html   # Linux
start templates/soft-editorial/template.html      # Windows
```

### 사용법 A — AI 에이전트에 위임 (설계 의도)

Claude Code / Cursor / Codex 등에 다음과 같이 지시:

```
https://github.com/bmshin94/beautiful-html-templates 를 클론하고
AGENTS.md 지침을 따라서 HTML 슬라이드 덱을 만들어줘.
```

주의: 이 포크의 `CLAUDE.md`는 페르소나 설정만 담고 있으므로, Claude Code에서
덱 작업을 시킬 때는 `AGENTS.md`를 읽으라고 명시하는 편이 확실하다.

### 사용법 B — 수동 편집

```bash
cp -r templates/neo-grid-bold ~/my-deck
cd ~/my-deck
# template.html 에서 텍스트만 교체
# 건드리지 말 것: :root 색상 변수, font-family, .s-* 클래스, 장식 요소
```

### 사용법 C — 새 템플릿 제작

```bash
node scripts/new-template.mjs my-theme   # templates/my-theme/ 스캐폴딩
# template.html + template.json 작성
node scripts/build-index.mjs             # index.json 재생성
```

주의: `new-template.mjs`의 안내문이 참조하는 `.tools/verify.ts`와
`scripts/shot-slides.mjs`는 `.gitignore`로 제외되어 공개 레포에 없다.
품질 검증 도구는 직접 구현해야 한다.

### 발표 및 배포

| 목적 | 방법 |
| --- | --- |
| 발표 | 브라우저 전체화면(F11) + 방향키 |
| PDF | Cmd/Ctrl+P → PDF로 저장 (1슬라이드 = 1페이지) |
| 웹 배포 | 폴더 통째로 Vercel / Netlify / GitHub Pages |
| 슬라이드 점프 | 숫자키 / Home / End / `R` |

네트워크 주의: 폰트를 Google Fonts CDN에서 로드한다. 완전 오프라인 발표라면
`.woff2`를 내려받아 `@font-face`로 로컬 임베드할 것.

---

## 4. 플러그인 / 스킬 / MCP 중 무엇인가

### 결론: 셋 다 아니다. 평범한 Git 레포지토리다.

| 유형 | 정의 | 이 레포 |
| --- | --- | --- |
| MCP 서버 | 프로토콜 구현체, 서버 프로세스가 도구를 노출 | ❌ 서버 코드·프로토콜 코드 전무 |
| Claude 플러그인 | `.claude-plugin/plugin.json` + 커맨드/훅/에이전트 번들 | ❌ 해당 디렉터리 없음 |
| Skill | `SKILL.md`(YAML 프론트매터) + 참조 리소스 | ⚠️ 개념은 거의 일치하나 `SKILL.md` 없음 |
| 일반 레포 | 클론해서 파일을 읽는 자산 묶음 | ✅ 정답 |

### 정확한 명칭

**에이전트 네이티브 에셋 라이브러리 (agent-native asset library).**

`AGENTS.md`는 [agents.md](https://agents.md) 라는 업계 공용 컨벤션이다. Cursor, Codex,
Copilot 등 여러 도구가 자동으로 읽어주는 약속된 파일명일 뿐, 특정 플랫폼의 공식
기능이 아니다. 따라서 **어떤 AI 도구에도 종속되지 않는다**는 것이 장점이다.

### Skill로 변환하기 (권장)

```
.claude/skills/beautiful-decks/
├── SKILL.md        ← AGENTS.md 내용 + 프론트매터
├── index.json      ← 복사
└── templates/      ← 복사
```

```yaml
---
name: beautiful-decks
description: |
  아름다운 HTML 슬라이드 덱 제작 시 사용. 34개 편집 디자인 템플릿 중
  사용자의 톤/무드에 맞는 것을 골라 실제 내용으로 채워 완성된 덱을 생성한다.
  "발표자료", "슬라이드", "덱", "피치덱" 요청 시 트리거.
---
```

이렇게 하면 매번 "AGENTS.md를 읽어"라고 지시할 필요 없이 자동 발동한다.

### MCP로 만드는 것이 나은가

템플릿이 전부 로컬 정적 파일이라 MCP의 이점(원격 접근, 실시간 데이터, 인증)이
크지 않다. 다만 **템플릿을 유료 서버에서만 배포하려는 경우**에는 MCP가 정답이다
(→ 수익화 아이디어 3).

---

## 5. API 토큰이 필요한가

### 결론: 필요 없다.

레포 전수 확인 결과:

- API 키 관련 코드 없음
- `.env` / `.env.example` 없음
- 인증·네트워크 요청 코드 없음 (`deck-stage.js`는 순수 DOM 조작)
- 외부 서비스 연동 없음
- 트래킹 / 애널리틱스 없음

### 실제로 필요한 것

| 항목 | 설명 |
| --- | --- |
| Google Fonts CDN 접속 | `fonts.googleapis.com` — 토큰 불필요, 무료 |
| 사용 중인 AI 도구 구독 | Claude / Cursor 등 — 레포와 무관하게 원래 쓰던 것 |

### 비용 구조

```
레포 자체       0원 (MIT)
Google Fonts    0원
호스팅          0원 (무료 티어)
──────────────────────────
추가 비용       없음 (AI 도구 구독료는 기존 지출)
```

### 토큰 소모량 참고

`template.html` 1개 ≈ 39KB (약 12k 토큰), `design.md` 1개 ≈ 43KB (약 13k 토큰).
덱 1개 제작에 대략 **50k~150k 토큰** 소모. `index.json`(41KB)만 먼저 읽게 한
설계가 바로 이 비용을 줄이기 위한 것. 후보 수를 3개 → 2개로 줄이면 더 절약된다.

---

## 6. 왜 GitHub에서 유명한가

### (1) 시각적 즉효성

README에 스크린샷 102장이 박혀 있다(템플릿당 3장). 레포를 여는 순간 가치가
증명된다. **README 자체가 랜딩 페이지 역할**을 한다.

### (2) 실제로 아픈 문제 해결

AI에게 "슬라이드 만들어줘"의 결과물은 대체로 흰 배경 + 기본 폰트 + 불렛 3줄이거나,
예쁘게 하라고 하면 그라데이션 떡칠이 된다. "AI는 논리는 잘하는데 미적 감각이 없다"는
보편적 좌절을 **"감각을 가르치지 말고 감각 있는 결과물을 통째로 준다"**는 우회로로 해결.

### (3) 타이밍

`AGENTS.md` / `CLAUDE.md` 컨벤션이 확산되는 시기에, 처음부터 AI를 1차 사용자로
설계한 레포. `index.json` 컨텍스트 예산 관리, `design.md` 기계 판독 토큰 —
**새로운 장르**로 인식되어 학습 목적의 스타를 끌어모은다.

### (4) 진입장벽 제로

npm install, 빌드, 설정, 회원가입, API 키 전부 불필요.

### (5) MIT 라이선스

상업적 사용·수정·재배포·판매 전부 허용. 기업 도입 검토 시 장애물이 없다.

### (6) 인용 가능한 철학

"템플릿은 업종이 아니라 톤을 가진다", "사용자의 취향이 이긴다",
"타이포그래피가 곧 디자인 시스템이다" — SNS 인용 → 트래픽 → 스타.

### (7) 디테일 밀도가 주는 신뢰

커밋 로그에서 드러나는 집착:

```
026de2d Fix CJK font CDN paths — prevent agents from inventing wrong cn-fontsource packages
874d963 Fix CJK em-rule trap — suppress inline face-switching in Chinese
65b0d48 Fix pink-script CJK recommendation — replace ZCOOL XiaoWei with Noto Serif SC 900
419fa21 Add CJK & International Content section to all 34 design.md files
```

"AI가 중국어 폰트 패키지명을 지어내는 버그"까지 잡고 있다.

### 공식

```
예쁜 스크린샷 × 진짜 아픈 문제 × 완벽한 타이밍 × 마찰 제로
  × MIT × 인용 가능한 철학 × 광적인 디테일 = 바이럴
```

---

## 7. 로컬 에이전트 구축에 도움이 되는가

### 결론: 매우 도움이 된다. 다만 "템플릿"보다 "설계 패턴"이 더 값지다.

### 훔쳐올 패턴 5가지

#### (1) 3층 컨텍스트 아키텍처 — 가장 중요

```
Layer 1  index.json     41KB   항상 읽음      → 후보 좁히기용 얇은 색인
Layer 2  template.json  1.6KB  후보 3개만     → 상세 메타
Layer 3  template.html  39KB   최종 1개만     → 무거운 실체
         design.md      43KB   필요 시에만    → 확장용 명세
```

원칙: **에이전트에게 전부 주지 마라. 색인 → 좁히기 → 정밀 읽기.**

응용 예:

- 사내 문서 봇 → `docs-index.json`(제목+요약+태그) → 매칭 문서만 전문 읽기
- 코드 에이전트 → `symbols-index.json` → 해당 파일만
- 고객 응대 봇 → `faq-index.json` → 매칭된 답변만

#### (2) 절차를 프롬프트가 아니라 파일에 고정

6단계 워크플로우를 매 대화에 입력하지 않고 `AGENTS.md`에 박아둠.
→ 버전 관리 가능, PR 리뷰 가능, 팀 공유 가능. **프롬프트 자산화.**

#### (3) Do/Don't 명시 = 가드레일

LLM은 "알아서 잘해"라고 하면 반드시 이상한 창의력을 발휘한다.
**실패 모드를 미리 열거해 막는 것**이 프롬프트를 늘리는 것보다 효과적이다.
`AGENTS.md §6`이 정확히 그 예시.

#### (4) 결정론적 스크립트 + 확률적 LLM 분업

| LLM 담당 | 스크립트 담당 |
| --- | --- |
| 템플릿 선택, 내용 매핑, 없는 레이아웃 설계 | index.json 생성, 폴더 스캐폴딩 |
| (창의 / 판단) | (기계적 / 검증 가능) |

`build-index.mjs`는 slug 불일치를 검출하고 `exit(1)`로 실패시킨다.

#### (5) 출력 계약 (Output Contract)

> §7: 모든 산출물마다 (1) 브라우저로 열고 (2) 절대경로를 보내라.
> "네가 한 모든 단계를 나열하지 마라. 사용자는 결과물 + 경로 + 한 줄 이유만 원한다."

에이전트의 장황함 문제를 구조로 해결.

### 실전 응용

| 만들 것 | 적용 방법 |
| --- | --- |
| 사내 문서 자동생성 봇 | 회사 브랜드 템플릿 3~5종 + 사내 톤 index.json |
| 이메일 / 뉴스레터 에이전트 | `templates/` → 이메일 HTML, `design.md` → 브랜드 가이드 |
| 코드 스캐폴딩 에이전트 | `templates/` → 보일러플레이트, index.json → 스택 태그 |
| 랜딩페이지 생성기 | 동일 구조, 슬라이드 대신 섹션 |
| 디자인 시스템 MCP | design.md 34개를 토큰 DB화 |

### 한계

- 1920×1080 고정 캔버스 + 절대좌표 → 모바일 반응형 아님 (scale 축소만 지원)
- 템플릿이 12~18장 통짜 HTML → 부분 재사용이 어려움
- 34개 커버 시 초기 컨텍스트 로딩 비용 존재
- `.tools/verify.ts` 검증기 미공개 → 품질 자동검증은 직접 구현 필요

---

## 8. React / PHP로 만들 수 있는가

### 결론: 둘 다 가능하며, 각자 잘하는 역할이 다르다.

### React 버전 (난이도 중)

`deck-stage`를 React 컴포넌트로 포팅:

```tsx
export function DeckStage({ width = 1920, height = 1080, children }) {
  const [index, setIndex] = useState(0);
  const [scale, setScale] = useState(1);
  const slides = Children.toArray(children);

  useEffect(() => {
    const fit = () => setScale(Math.min(
      window.innerWidth / width, window.innerHeight / height
    ));
    const key = (e) => {
      if (['ArrowRight', ' ', 'PageDown'].includes(e.key))
        setIndex(i => Math.min(i + 1, slides.length - 1));
      if (['ArrowLeft', 'PageUp'].includes(e.key))
        setIndex(i => Math.max(i - 1, 0));
      if (e.key === 'Home') setIndex(0);
      if (e.key === 'End') setIndex(slides.length - 1);
    };
    fit();
    window.addEventListener('resize', fit);
    window.addEventListener('keydown', key);
    return () => {
      window.removeEventListener('resize', fit);
      window.removeEventListener('keydown', key);
    };
  }, [slides.length]);

  return (
    <div style={{ width: '100vw', height: '100vh', overflow: 'hidden', background: '#1a1a1a' }}>
      <div style={{
        width, height, transform: `scale(${scale})`, transformOrigin: 'center',
        position: 'absolute', top: '50%', left: '50%',
        marginTop: -height / 2, marginLeft: -width / 2
      }}>
        {/* 언마운트하지 않고 visibility만 토글 — 원본 철학 유지 */}
        {slides.map((s, i) => (
          <div key={i} style={{
            position: 'absolute', inset: 0,
            visibility: i === index ? 'visible' : 'hidden',
            opacity: i === index ? 1 : 0, transition: 'opacity .3s'
          }}>{s}</div>
        ))}
      </div>
    </div>
  );
}
```

슬라이드를 데이터 주입형 컴포넌트로:

```tsx
<DeckStage>
  <SoftEditorial.Cover
    eyebrow="2026 Q1" title="제목" subtitle="부제" author="작성자" date="2026.09"
  />
  <SoftEditorial.Numbers stats={[
    { value: "47%", label: "생산성 향상" },
    { value: "2.4M", label: "처리 토큰" },
  ]} />
</DeckStage>
```

**장점**

- `design.md`의 색상/타이포 토큰을 Tailwind config 또는 CSS 변수로 자동 변환
- 실시간 편집기 (좌측 폼 입력 → 우측 즉시 미리보기)
- Next.js `/deck/[id]` 라우팅으로 공유 URL
- `@react-pdf` 또는 Puppeteer로 PDF 내보내기
- Recharts 등으로 차트를 동적으로 (원본은 정적 SVG)
- npm 패키지로 배포 가능

**주의점**

- 34개 × 12슬라이드 = 약 408개 레이아웃의 컴포넌트화는 대규모 작업
  → 현실적으로 인기 템플릿 5개부터 시작
- 절대좌표 CSS는 그대로 이식 가능 (inline style 또는 CSS Module)
- Google Fonts는 `next/font`로 최적화

### PHP 버전 (난이도 낮음)

원본이 "HTML 문자열에 값 채우기" 구조라 PHP와 궁합이 좋다.

```php
<?php
// 가장 단순한 버전
$tpl = file_get_contents("templates/soft-editorial/template.html");
$data = [
  '{{TITLE}}'    => '제목',
  '{{SUBTITLE}}' => '부제',
  '{{AUTHOR}}'   => '작성자',
  '{{DATE}}'     => date('Y.m'),
];
echo str_replace(array_keys($data), array_values($data), $tpl);
```

```php
<?php
// AGENTS.md §4 톤 매칭 로직을 코드로 구현
$index = json_decode(file_get_contents('index.json'), true);

function matchTemplates(array $index, array $wantMood, string $scheme): array {
    $scored = [];
    foreach ($index['templates'] as $t) {
        if ($scheme !== 'any' && $t['scheme'] !== $scheme) continue;
        $score = count(array_intersect($wantMood, array_merge($t['mood'], $t['tone'])));
        if ($score > 0) $scored[] = ['score' => $score, 'tpl' => $t];
    }
    usort($scored, fn($a, $b) => $b['score'] <=> $a['score']);
    return array_slice(array_column($scored, 'tpl'), 0, 3);
}

$top3 = matchTemplates($index, ['confident', 'bold', 'modern'], 'light');
```

```php
<?php
// PDF 내보내기 (Browsershot = headless Chrome)
use Spatie\Browsershot\Browsershot;
Browsershot::url("https://example.com/deck/{$id}")
    ->windowSize(1920, 1080)
    ->waitUntilNetworkIdle()   // Google Fonts 로딩 대기 필수
    ->savePdf("deck-{$id}.pdf");
```

**장점**

- 정적 HTML 문자열 치환은 PHP의 본업
- 저렴한 공유 호스팅에서도 동작
- 워드프레스 플러그인화 시 거대한 시장 접근 가능
- 라라벨 Blade로 컴포넌트화 가능
- 회원 / 결제 / DB 연동이 쉬워 수익화에 직행

**주의점**

- 실시간 편집 UX는 약함 → 프론트 React + 백엔드 PHP 하이브리드 권장
- `deck-stage.js`는 클라이언트 JS이므로 포팅 불필요

### 권장 조합

```
프론트엔드  Next.js (React)   템플릿 갤러리 · 실시간 편집 · 미리보기
백엔드      Laravel (PHP) 또는 Next API Routes   회원/결제 · 덱 저장 · PDF 큐
런타임      deck-stage.js 원본 그대로 재사용
```

**핵심**: `deck-stage.js`는 표준 Web Component다. React / PHP / Vue 어디서든
`<script src="deck-stage.js">` 후 `<deck-stage>` 태그만 쓰면 되므로 포팅이 불필요하다.

### MVP 로드맵 (2~3주)

| 주차 | 작업 |
| --- | --- |
| 1주차 | `index.json` 기반 갤러리 페이지 + 톤 필터 |
| 2주차 | 템플릿 5개를 `{{placeholder}}` 방식으로 변환 + 입력 폼 |
| 3주차 | Browsershot / Puppeteer PDF 내보내기 + 공유 링크 |
| 이후 | 결제 연동 → 유료 템플릿 해금 |

---

## 9. 수익화 아이디어

### 아이디어 1: 한국형 템플릿 팩 (가장 현실적)

**근거**: 원본 34개는 전부 서구 편집 디자인 감성이다. `design.md`의 CJK 섹션은
중국어 중심이며 **"Known CJK Gap"** 이라는 미해결 항목까지 명시되어 있다.
한글 타이포그래피(자간·행간·장평)는 라틴 폰트와 규칙이 완전히 달라
**로컬 지식이라는 진입장벽**이 존재한다.

**제품 구성**

```
한글 슬라이드 템플릿 팩 — Pretendard / 나눔스퀘어네오 / 리디바탕 최적화
├─ 스타트업 IR 팩 (10종)        VC 미팅, 시드/시리즈A
├─ 대학 발표 팩 (10종)          졸업논문, 캡스톤, 학회
├─ 공공/정부과제 팩 (10종)      R&D 보고, 제안서 (한국 특화)
├─ 회사 보고 팩 (10종)          주간/월간/분기 리뷰
└─ 교회/동호회 팩 (10종)
```

**수익 모델**

| 채널 | 가격 |
| --- | --- |
| Gumroad / Lemon Squeezy | 팩당 $29~49 |
| 크몽 / 오투잡 | 3~5만원 |
| 전체 번들 | $99 / 12만원 |
| 구독 (월 신규 5종) | $9/월 |

| 항목 | 값 |
| --- | --- |
| 난이도 | ★★☆☆☆ |
| 초기 투자 | 2~4주 |
| 예상 월수익 | 30만~300만원 |
| 리스크 | 낮음 |

**법적 체크**: MIT는 파생물 상업 판매를 허용하되 **LICENSE 및 저작권 고지 포함**이
조건이다. 원본 재판매가 아니라 한글 최적화 + 신규 디자인으로 부가가치를 붙이고,
원저자 크레딧을 표기하는 것이 바람직하다.

### 아이디어 2: SaaS 웹 서비스 (확장성 최대)

**흐름**

```
① 톤 선택 UI → ② index.json 기반 AI 매칭 (후보 3개 표지)
→ ③ 내용 입력 (텍스트 / 노션·구글독스 URL)
→ ④ LLM이 슬라이드 구조로 분배 → ⑤ 실시간 편집
→ ⑥ HTML / PDF / 공유링크 / PPTX 내보내기
```

**가격 정책**

| 플랜 | 가격 | 내용 |
| --- | --- | --- |
| Free | 0원 | 템플릿 5종, 워터마크, 월 3덱 |
| Pro | ₩9,900/월 | 전체 34종+, 워터마크 제거, 무제한, PDF |
| Team | ₩29,000/월 | 브랜드 커스텀, 공유, 협업 |
| Business | ₩99,000/월 | 전용 템플릿, API, SSO |

**스택**: Next.js + Tailwind · Supabase · Claude API · Puppeteer · Vercel · 토스페이먼츠

**차별점 (Gamma / Tome 대비)**

- AI 생성 특유의 밋밋함 대신 사람이 디자인한 34개 완결 시스템
- 에디터에 갇히지 않고 HTML 소스 통째로 내보내기 가능
- git으로 버전 관리 가능한 덱 → 개발자/디자이너 타겟

| 항목 | 값 |
| --- | --- |
| 난이도 | ★★★★☆ |
| 초기 투자 | 2~3개월 |
| 예상 MRR | 6개월 후 100~1,000만원 |
| 리스크 | 중간 (경쟁, LLM API 비용) |

### 아이디어 3: MCP 서버 / Skill 구독 (가장 트렌디)

```
무료 (GitHub)     기본 템플릿 10종
유료 (MCP 서버)   프리미엄 100종 + 브랜드 커스텀 + 월 신규 5종
```

```jsonc
{
  "mcpServers": {
    "deck-pro": {
      "url": "https://mcp.deckpro.io",
      "headers": { "Authorization": "Bearer ${DECK_PRO_KEY}" }
    }
  }
}
```

노출 도구 예: `list_templates(mood, tone, scheme, formality)`,
`get_template(slug)`, `get_brand_template(org_id, slug)`, `render_preview(slug, content)`

**근거**: 에이전트용 유료 리소스 시장은 아직 비어 있어 선점 기회가 있다. MCP는
인증·과금·원격 제어가 자연스럽게 붙고, 로컬 파일 배포와 달리 복제가 어렵다.
Claude Code / Cursor 사용자는 이미 유료 구독에 익숙한 고객층이다.

| 플랜 | 가격 |
| --- | --- |
| Indie | $9/월 (50종) |
| Pro | $29/월 (100종 + 브랜드 토큰) |
| Team | $99/월 (조직 전용 호스팅) |

| 항목 | 값 |
| --- | --- |
| 난이도 | ★★★☆☆ |
| 초기 투자 | 4~6주 |
| 리스크 | 중간 (MCP 생태계 성장에 베팅) |

### 아이디어 4: B2B 브랜드 커스텀 (객단가 최고)

**납품물**

1. 회사 브랜드 컬러/폰트/로고 적용 템플릿 8~12종
2. 회사 전용 `design.md` (기계 판독 디자인 토큰)
3. 사내 `AGENTS.md` (덱 작성 규칙)
4. 사내 배포 (사내 Git / MCP 서버 / Claude 프로젝트)
5. 임직원 교육 2시간
6. 6개월 유지보수

**구매 이유**: 대기업은 브랜드 가이드 위반에 민감하다. 직원마다 제각각인 PPT는
브랜드 훼손이다. 디자인팀 요청 시 3일 대기하던 것을 직원이 5분에 해결하게 만들고,
"AI 도입 성과"로 보고하기도 좋다.

| 항목 | 금액 |
| --- | --- |
| 초기 구축 | 500만 ~ 3,000만원 |
| 월 유지보수 | 50만 ~ 200만원 |
| 템플릿 추가 | 건당 100만원 |

**타겟**: 스타트업(시리즈B+), 컨설팅펌, 에이전시, 대학/연구소, 공공기관

**시작법**: 지인 회사 1곳에 무료로 구축하고 사례 연구를 작성해 다음 영업에 활용.
첫 레퍼런스 확보가 핵심이다.

### 아이디어 5: 워드프레스 / 노코드 플러그인

```
[beautiful_deck template="neo-grid-bold" id="123"]
```

- 워드프레스 관리자에서 덱 생성/편집, 글에 임베드, 랜딩페이지 활용
- Elementor / Gutenberg 블록 지원
- 워드프레스는 전 세계 웹사이트의 약 40%를 차지하며 유료 플러그인 시장이 성숙

| 플랜 | 가격 |
| --- | --- |
| 무료 (wordpress.org) | 템플릿 5종 |
| Pro | $49/년 (사이트 1개) |
| Agency | $199/년 (무제한) |

변형: Figma 플러그인, 노션 → 덱 변환기, 구글 슬라이드 애드온

### 아이디어 6: 콘텐츠 & 교육 (자본금 0원)

| 상품 | 가격 |
| --- | --- |
| 유튜브 "AI로 3분 만에 발표자료" | 광고 + 제휴 |
| 유료 강의 "에이전트 네이티브 레포 설계법" | 15~30만원 |
| 전자책 "AI 에이전트용 디자인 시스템 만들기" | 2~5만원 |
| 뉴스레터 (주 1회 템플릿 + 팁) | 무료 → 유료 전환 |
| 1:1 컨설팅 | 시간당 15~30만원 |

**가치**: 아이디어 1~5의 마케팅 채널 역할을 겸한다.
유튜브 유입 → 무료 템플릿 → 유료 팩 → SaaS → B2B 컨설팅의 퍼널이 완성된다.

### 아이디어 7: 버티컬(틈새 수직 시장) 특화

| 버티컬 | 특화 내용 | 객단가 |
| --- | --- | --- |
| 의료 / 제약 | 학회 발표, 임상 데이터 차트, 규제 표기 | 높음 |
| 법률 | 변론 자료, 조문 인용 레이아웃 | 높음 |
| 공공 / 정부과제 | R&D 보고서 양식, 평가 대응 포맷 | 중간, 물량 큼 |
| 대학 / 학회 | 논문 발표, 포스터 세션 | 낮음, 물량 큼 |
| 교회 / 종교 | 예배 스크린, 찬양 가사 | 낮음, 충성도 높음 |
| 웨딩 / 이벤트 | 청첩장, 식전영상 배경 | 중간 |
| 부동산 | 매물 브로슈어, 분양 프레젠테이션 | 높음 |

전략: 하나를 골라 해당 업계 1등을 차지한다.

### 종합 비교

| # | 아이디어 | 난이도 | 기간 | 초기비용 | 예상 월수익 | 리스크 | 추천도 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | 한국형 템플릿 팩 | ★★ | 2~4주 | 낮음 | 30~300만 | 낮음 | ★★★★★ |
| 2 | SaaS 서비스 | ★★★★ | 2~3개월 | 중간 | 100~1,000만 | 중간 | ★★★★ |
| 3 | MCP 구독 | ★★★ | 4~6주 | 낮음 | 50~500만 | 중간 | ★★★★ |
| 4 | B2B 커스텀 | ★★★ | 건별 | 낮음 | 건당 500~3,000만 | 낮음 | ★★★★★ |
| 5 | WP 플러그인 | ★★★ | 4~6주 | 낮음 | 50~500만 | 중간 | ★★★ |
| 6 | 콘텐츠 / 교육 | ★★ | 상시 | 0원 | 0~500만 | 매우 낮음 | ★★★★ |
| 7 | 버티컬 특화 | ★★★ | 1~2개월 | 낮음 | 100~800만 | 낮음 | ★★★★ |

### 실행 순서 제안

```
[0~1개월]  씨앗 뿌리기
  · 한국형 템플릿 10종 제작 (Pretendard 기반)
  · GitHub 무료 공개 → 스타 확보
  · 유튜브/블로그 1편
  목표: 신뢰 + 유입 (수익 0원 허용)

[1~3개월]  첫 매출
  · 프리미엄 팩 30종 → Gumroad + 크몽 ($39)
  · 뉴스레터 시작
  목표: 월 50~200만원

[3~6개월]  레버리지
  · 지인 회사 1곳 B2B 무료 구축 → 레퍼런스
  · MCP 서버 베타 ($9/월)
  목표: 월 300~800만원

[6~12개월] 스케일
  · 성과 좋은 축 하나에 집중
  목표: 월 1,000만원+
```

### 리스크

1. **Gamma / Tome 등 기존 강자** → 디자인 품질 + 코드 소유권 + 로컬 우선으로 차별화
2. **AI가 곧 디자인을 잘하게 될 가능성** → 템플릿이 아니라 큐레이션 · 브랜드 일관성 ·
   워크플로우를 판매해야 함
3. **폰트 라이선스** → 상업 배포 시 필수 확인. Pretendard(OFL), 나눔(OFL)은 안전하나
   일부 유료 폰트는 재배포 금지

### 결론

**아이디어 1(한국형 팩) + 6(콘텐츠)을 동시에 시작**하는 것이 자본 대비 위험이 가장 낮다.
여기서 검증되면 2 · 3 · 4번으로 자연스럽게 확장된다. 시간당 수익은 4번(B2B)이 최고이므로
첫 레퍼런스 확보를 중기 목표로 삼는다.

---

## 10. 핵심 요약

| 질문 | 답 |
| --- | --- |
| 뭐하는 건가 | AI 에이전트가 읽고 예쁜 HTML 슬라이드 덱을 만들어주는 템플릿 라이브러리 (34종) |
| 설치 | `git clone` 만. 의존성 0, 빌드 0, `package.json` 없음 |
| 플러그인/스킬/MCP? | 전부 아님. 일반 Git 레포 (= 에이전트 네이티브 에셋 라이브러리) |
| API 토큰 | 불필요. Google Fonts CDN 접속만 필요 |
| 유명한 이유 | 스크린샷 102장 + 실제 아픈 문제 + 타이밍 + 마찰 제로 + MIT + 철학 + 디테일 |
| 로컬 에이전트에 도움? | 매우. 특히 3층 컨텍스트 아키텍처 패턴이 핵심 자산 |
| React/PHP 이식 | 둘 다 가능. `deck-stage.js`는 표준 Web Component라 포팅 불필요 |
| 수익화 | 한국형 팩 → 콘텐츠 → MCP 구독 → B2B 커스텀 순 추천 |

### 핵심 인사이트 3가지

1. **컨텍스트 예산 관리가 곧 아키텍처다.**
   `index.json`(얇은 색인) → `template.json`(메타) → `template.html`/`design.md`(실체)의
   3층 구조는 어떤 AI 에이전트 프로젝트에도 그대로 적용 가능하다.

2. **LLM의 실패 모드를 미리 열거해 막아라.**
   "알아서 잘해"가 아니라 "폰트 바꾸지 마 / 색 바꾸지 마 / 장식 빼지 마"를 명시하는 것이
   프롬프트를 늘리는 것보다 효과적이다.

3. **디자인 시스템을 사람이 아니라 기계가 읽을 수 있게 써라.**
   `design.md`의 YAML 토큰은 AI가 없던 슬라이드를 새로 그려도 톤이 유지되게 만든다.

---

## 참고 링크

- 이 레포 (포크): https://github.com/bmshin94/beautiful-html-templates
- 원본 레포: https://github.com/zarazhangrui/beautiful-html-templates
- 에이전트 매뉴얼: [AGENTS.md](./AGENTS.md)
- 템플릿 갤러리: [README.md](./README.md)
- 템플릿 카탈로그: [index.json](./index.json)
- AGENTS.md 컨벤션: https://agents.md
- 라이선스: [MIT](./LICENSE)
