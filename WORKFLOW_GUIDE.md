# Gemini MCP 연계 디자인 파일 구축 by claude (토큰 최적화)

처음 합류하는 팀원이 전체 작업 흐름을 이해할 수 있도록 정리한 문서입니다.

---

## 준비사항

작업 전 아래 3가지가 연결되어 있어야 한다.

| 도구 | 설치/연결 방법 | 확인 |
|------|--------------|------|
| **Claude Code** | CLI 설치 후 프로젝트 폴더에서 실행 | `claude` 명령어 동작 여부 |
| **Gemini MCP** | `claude_desktop_config.json`에 Gemini MCP 서버 등록 | `/analyze` 실행 시 Gemini 응답 여부 |
| **Pencil MCP** | `claude_desktop_config.json`에 Pencil MCP 서버 등록 | `.pen` 파일 열기 가능 여부 |

> Figma MCP 버전은 추후 업데이트 예정. Pencil MCP와 동일한 워크플로우로 교체 적용 가능.

**Claude Code 시작 시 자동으로 로드되는 파일:**
- `CLAUDE.md` — AI 페르소나 및 3대 규칙
- `.claude/settings.json` — PostToolUse 훅 (JSON 사이드카 body 검증)
- `.claude/commands/` — `/capture`, `/analyze`, `/design` 슬래시 명령어

---

## 핵심 도구 이해

### MCP (Model Context Protocol)

Claude Code에 외부 도구를 연결하는 표준 프로토콜이다.  
`claude_desktop_config.json`에 서버를 등록하면 Claude가 해당 도구를 직접 호출할 수 있게 된다.

```json
// claude_desktop_config.json 예시
{
  "mcpServers": {
    "gemini": { ... },    ← Gemini MCP: PNG 분석 담당
    "pencil": { ... }     ← Pencil MCP: .pen 파일 편집 담당
  }
}
```

이 워크플로우에서 MCP가 하는 일:

| MCP 서버 | 담당 단계 | 주요 역할 |
|----------|----------|---------|
| **Gemini MCP** | 1단계 | PNG → JSON 변환 (이미지 분석) |
| **Pencil MCP** | 3단계 | .pen 파일 프레임 생성·수정 |

---

### Skills (슬래시 명령어)

`.claude/commands/` 폴더에 MD 파일로 정의하는 사용자 정의 명령어다.  
Claude Code 세션에서 `/명령어`로 호출하면 해당 MD 파일의 내용을 프롬프트로 실행한다.

| 명령어 | 파일 | 담당 단계 |
|--------|------|---------|
| `/capture` | `.claude/commands/capture.md` | 0단계: Playwright 캡처 실행 |
| `/analyze` | `.claude/commands/analyze.md` | 1단계: Gemini 일괄 분석 |
| `/design [화면명]` | `.claude/commands/design.md` | 3단계: Pencil 프레임 구현 |

명령어에 인자를 붙이면 MD 파일 안의 `$ARGUMENTS`로 전달된다.  
예: `/design 03_결재함` → `design.md` 안에서 `$ARGUMENTS = "03_결재함"`

---

### Hook (훅)

Claude Code가 특정 도구를 실행한 **전후**에 자동으로 쉘 명령을 실행하는 기능이다.  
`.claude/settings.json`에 등록하며, 이 프로젝트에서는 아래 훅 하나가 동작한다.

```
트리거: Write 도구 실행 후 (PostToolUse)
동작:   tools/validate_sidecar.py 실행
목적:   screenshots/*.json 저장 시 components.body가 비어있으면 경고 출력
```

예시 출력:
```
✓  [03_결재함_결재목록.json] 유효 (nav 2개 / body 5개)
⚠  [04_결재함_결재상세.json] 검증 실패:
   - components.body 가 비어있음 → Pencil 작업 전에 반드시 채울 것
```

---

## 전체 흐름 한눈에 보기

```
HTML 목업 파일
    ↓
[0단계] /capture — Python(Playwright)으로 전체 화면 캡처
        산출물: PNG + JSON 뼈대 + _manifest.json
    ↓
[1단계] /analyze — Gemini MCP로 PNG 분석
        산출물: JSON 사이드카 상세 내용 완성 (nav + body)
    ↓
[2단계] JSON 검토 — 훅이 자동 검증, 이상 있으면 수동 보완
        산출물: 검증 완료된 JSON 사이드카
    ↓
[3단계] /design — Pencil MCP로 프레임 디자인 구현
        산출물: .pen 파일 (버전 rN)
    ↓
[4단계] 디자인토큰 + 컴포넌트 MD 문서화
        산출물: DesignTokens.md, COMPONENT_MAP.md
    ↓
[5단계] 파일 정리 및 버전 저장
        산출물: git commit
```

> **슬래시 명령어 없는 단계(2·4·5단계)** 는 수동 검토 또는 별도 작업이 필요하다.

---

## JSON 사이드카(Sidecar)란?

> 이 워크플로우의 핵심 개념이다. 먼저 이해하고 진행하라.

**JSON 사이드카**는 PNG 스크린샷과 1:1로 쌍을 이루는 텍스트 명세 파일이다.  
파일명과 위치를 동일하게 유지해, AI가 이미지를 직접 열지 않고 JSON 텍스트만 읽어도 화면의 구조·컴포넌트·디자인 토큰을 파악할 수 있게 한다.

```
screenshots/
  [NN]_[화면명].png       ← 원본 이미지 (사람이 보는 것)
  [NN]_[화면명].json      ← 사이드카 (AI가 읽는 것)
```

**사이드카 파일 구조:**
```json
{
  "id": "03_결재함_결재목록",
  "screen_number": 3,
  "gnb": "결재함",
  "menu": "결재 목록",
  "screen_type": "list",
  "components": {
    "nav": ["GNB 탭(결재함 활성)", "LNB 메뉴(결재목록 활성)"],
    "body": [
      "페이지 타이틀(결재 목록)",
      "필터 바(기간 선택 드롭다운, 결재상태 드롭다운, 조회 버튼)",
      "데이터 테이블(결재일시, 신청자, 금액, 상태 배지, 상세보기 링크)",
      "테이블 하단 페이징",
      "하단 버튼 영역(승인 버튼, 반려 버튼)"
    ]
  },
  "layout": "GNB(48px) + LNB(200px) + 본문 영역: 타이틀 → 필터 바 → 테이블 → 페이징 → 버튼",
  "tokens_used": ["color.primary", "color.danger", "badge.blue", "badge.gray", "table.top-border"],
  "notes": "테이블 상단 2px 강조 보더 적용. 상태 배지: 대기=gray, 승인=blue, 반려=red"
}
```

> **중요**: `components.body`는 반드시 채워야 한다. GNB/LNB는 모든 화면에 공통이므로 Pencil 작업의 핵심 기준은 **본문(body) 영역의 UI 구성**이다. body가 비어있으면 AI가 해당 화면의 고유 레이아웃을 알 수 없다.

**왜 필요한가:**

| 방식 | 토큰 소비 | 비고 |
|------|-----------|------|
| PNG 직접 처리 | ~2,000 토큰/장 | 매번 이미지를 다시 읽음 |
| JSON 사이드카 처리 | ~100 토큰/장 | 텍스트만 읽음 |
| **37장 기준 절감** | **약 70,000 토큰** | **95% 절감** |

**핵심 규칙**: 사이드카 JSON이 이미 존재하면 Gemini를 다시 호출하지 않는다. 한번 만들어두면 이후 모든 작업에서 재활용한다.

---

## Claude Code 안에서 구현하는 이유 (토큰 절약)

이 워크플로우는 **Claude Code CLI 안에서 모든 도구를 한 세션으로 실행**한다.  
외부 도구를 오가거나 대화를 새로 시작하지 않기 때문에 두 가지 이점이 생긴다.

### 컨텍스트 재사용

Claude Code 한 세션 안에서 Gemini MCP → Pencil MCP 순서로 이어서 실행하면,  
앞 단계에서 읽은 JSON 내용이 **컨텍스트에 그대로 남아 있다**.  
Pencil 작업 시 JSON을 다시 읽지 않아도 되므로 중복 토큰 소비가 없다.

```
[한 세션 안]
Gemini MCP가 PNG → JSON 생성      ← 이미지 읽는 비용 1회만 발생
  ↓ (컨텍스트 유지)
Claude가 JSON 내용 기억한 채로
  ↓
Pencil MCP로 .pen 파일 수정        ← JSON 재읽기 없음
```

새 대화를 시작하면 `.pen` 파일 스키마 로딩 + 노드 탐색 + 이미지 재로딩으로  
**한 세션에 ~18,000 토큰**이 추가 소비된다.

### MCP 도구들이 Claude Code에서 바로 동작

| MCP 도구 | 하는 일 |
|----------|---------|
| `mcp__gemini__ask-gemini` | PNG 파일 경로를 주면 JSON 분석 결과 반환 |
| `mcp__pencil__open_document` | `.pen` 파일 열기 |
| `mcp__pencil__batch_design` | 프레임/컴포넌트 삽입·수정 |
| `mcp__pencil__get_screenshot` | 작업 결과 PNG로 확인 |

터미널 하나에서 Claude Code를 실행하면 이미지 분석 → 디자인 구현 → 결과 확인까지 한 화면에서 끝난다.

---

## 원스톱 실행 순서

MCP 2개가 연결된 상태에서 Claude Code를 실행하면 슬래시 명령어만으로 핵심 단계가 돌아간다.

```
/capture              → 0단계: HTML 목업 전체 화면 캡처 + JSON 뼈대 + manifest 생성
/analyze              → 1단계: JSON 없는 PNG만 Gemini 분석 → body 상세 채움
                        (2단계: 훅이 자동으로 body 검증 → 경고 있으면 수동 보완)
/design [화면명]       → 3단계: 해당 화면 JSON 읽고 Pencil 프레임 구현 + 결과 스크린샷
```

예시:
```
/capture
/analyze
/design 03_결재함_결재목록
```

새 화면이 추가될 때는 `/capture` → `/analyze` → `/design` 순서만 반복하면 된다.  
4단계(토큰 문서화), 5단계(버전 저장)는 작업 완료 후 수동으로 진행한다.

---

## 각 단계 상세

### 0단계. /capture — 화면 자동 캡처

**목적**: HTML 목업 파일을 브라우저로 열어 모든 화면을 자동으로 PNG로 저장하고, JSON 사이드카 뼈대와 manifest까지 한번에 생성한다.

**스크립트**: `tools/capture_screens_v3.py`

**실행 방법:**
```bash
pip install playwright
playwright install chromium
python tools/capture_screens_v3.py
```

**산출물 (자동 생성):**
```
screenshots/
  [NN]_[GNB]_[메뉴명].png
  [NN]_[GNB]_[메뉴명].json      ← JSON 뼈대 (1단계에서 상세 채움)
  ...
  _manifest.json                 ← 전체 화면 목록 인덱스
```

**v2 vs v3 차이:**

| 버전 | PNG 캡처 | JSON 사이드카 | _manifest.json |
|------|----------|---------------|----------------|
| v2 | O | X | X |
| **v3** | **O** | **O (자동)** | **O (자동)** |

v3를 쓰면 0단계에서 JSON 뼈대가 생성되고, Gemini는 1단계에서 `components.body`, `layout`, `tokens_used` 같은 상세 내용만 채운다.

**_manifest.json 구조:**
```json
{
  "folder": "screenshots/",
  "total": 26,
  "screens": [
    { "id": "[NN]_[GNB]_[메뉴명]", "file": "[NN]_[GNB]_[메뉴명].png", "screen_number": 1, "gnb": "GNB탭명", "menu": "메뉴명" }
  ]
}
```

---

### 1단계. /analyze — Gemini MCP 이미지 분석

**목적**: PNG를 AI가 읽을 수 있는 텍스트(JSON)로 변환하고, `components.body`를 상세히 채운다.

**입력**: `screenshots/` 폴더 내 JSON 사이드카가 없는 PNG 파일

**도구**: Gemini MCP

**출력**: JSON 사이드카 상세 내용 (`components.nav`, `body`, `layout`, `tokens_used`, `notes`)

**Gemini에게 줄 프롬프트:**
```
이 PNG 스크린샷을 분석해서 아래 JSON 형식으로 출력해줘:
{
  "id": "[화면번호_GNB_메뉴명]",
  "screen_number": [번호],
  "gnb": "[GNB 탭명]",
  "menu": "[메뉴명]",
  "screen_type": "[list|form|dashboard|split-list]",
  "components": {
    "nav": ["GNB에서 활성화된 탭명", "LNB에서 활성화된 메뉴명"],
    "body": [
      "본문 상단 타이틀 또는 서브탭",
      "필터/검색 영역 구성 요소",
      "주요 콘텐츠 영역 (테이블 컬럼명, 카드 구성 등)",
      "하단 버튼 또는 페이징 여부"
    ]
  },
  "layout": "GNB → LNB → 본문 구조를 순서대로 설명 (예: 타이틀 → 필터 바 → 테이블 → 페이징)",
  "tokens_used": ["사용된 색상/컴포넌트 토큰"],
  "notes": "퍼블리싱 시 주의할 특이사항 (예: 배지 색상, 고정 컬럼, 팝업 존재 여부)"
}

body는 이 화면 고유의 UI 구성이므로 반드시 상세히 작성할 것.
nav는 GNB/LNB 활성 항목만 간략히 기재.
```

---

### 2단계. JSON 검토 (자동 + 수동 보완)

**목적**: Gemini가 채운 JSON을 검증하고 저장한다.

**자동 처리**: JSON 저장 시 훅(`validate_sidecar.py`)이 자동 실행되어 `components.body` 누락 여부를 검사한다.

**수동 보완이 필요한 경우**:
- 훅에서 `⚠ body 비어있음` 경고가 출력된 경우 → `/analyze`로 해당 화면 재분석
- Gemini가 컴포넌트를 잘못 파악한 경우 → JSON 파일 직접 수정

**규칙**: PNG와 JSON은 **같은 파일명, 같은 위치**에 쌍으로 존재해야 한다.

---

### 3단계. /design — Pencil MCP 프레임 구현

**목적**: JSON 명세를 바탕으로 Claude가 설계 판단을 내리고, Pencil MCP로 `.pen` 파일에 실제 디자인 프레임을 만든다.

**버전 관리 규칙:**
```
r1 → r2 → r2.2 → r3 → r4
마이너 수정: r2 → r2.1
메이저 완성: r2.2 → r3
```

**핵심 Pencil 명령어:**

| 명령 | 용도 |
|------|------|
| `I("parent", {...})` | 노드 삽입 (버튼, 텍스트 추가) |
| `R("nodeId", {...})` | 노드 교체 (화면 본문 전체 교체) |
| `U("nodeId", {...})` | 노드 속성 수정 (텍스트, 색상 변경) |
| `D("nodeId")` | 노드 삭제 |

---

### 4단계. 디자인토큰 + 컴포넌트 MD 문서화

**목적**: 화면 분석에서 확인된 색상·폰트·간격·컴포넌트 스펙을 문서로 정리해, 향후 React 개발의 기준으로 삼는다.

**포함 내용:**
- Color (Primary, Text, Surface, Border, Status)
- Typography (Font Family, Size, Weight)
- Layout (GNB 높이, LNB 너비, 콘텐츠 너비)
- Spacing, Border Radius, Shadow
- 컴포넌트별 토큰 적용 예시 (GNB, LNB, Table, Button 등)

---

### 5단계. 파일 정리 및 버전 저장

**파일 명명 규칙:**
```
[날짜]_[프로젝트명]_[버전].pen
예: 20260408_[프로젝트명]_r2.2.pen
```

**React 컴포넌트 버전 규칙:**
```
초안:   Button_v1.tsx
수정:   Button_v2.tsx
확정:   Button.tsx (개발자가 병합)
```

**프로젝트 폴더 구조:**
```
design_react_css/
├── CLAUDE.md                               ← AI 시스템 지침 (페르소나)
├── README.md                               ← 전체 로드맵
├── .claude/
│   ├── settings.json                       ← PostToolUse 훅 (JSON body 검증)
│   └── commands/
│       ├── capture.md                      ← /capture : Playwright 캡처 실행
│       ├── analyze.md                      ← /analyze : Gemini 일괄 분석
│       └── design.md                       ← /design [화면명] : Pencil 프레임 구현
├── docs/
│   ├── HANDOVER_GUIDE.md                   ← 도구별 역할 + 협업 규약
│   ├── VIBE_CODING_PLAYBOOK.md             ← 도구 사용법 + 프롬프트 예시
│   └── WORKFLOW_GUIDE.md                   ← 이 문서
├── project_global/
│   ├── DESIGN_TOKENS.md                    ← 글로벌 디자인 토큰
│   ├── COMPONENT_MAP.md                    ← 컴포넌트 명세
│   └── MASTER_PROMPT.md                    ← Claude 초기화용 프롬프트
├── project_branch/
│   ├── [프로젝트명]_DesignTokens_v1.md     ← 프로젝트 전용 디자인 토큰 (선택)
│   └── [프로젝트명]_YYYYMMDD_rN.pen        ← Pencil 작업 파일
├── tools/
│   ├── capture_screens_v3.py               ← Playwright 캡처 스크립트
│   └── validate_sidecar.py                 ← 훅용 JSON 검증 스크립트
└── screenshots/
    ├── [NN]_[GNB]_[메뉴명].png
    └── [NN]_[GNB]_[메뉴명].json            ← Gemini가 생성한 사이드카
```

---

## 도구별 역할 요약

| 도구 | 역할 | 비유 |
|------|------|------|
| **Gemini MCP** | PNG → JSON 변환 (이미지 분석) | 눈 |
| **JSON 사이드카** | 화면 명세서 보관 및 재활용 | 설계도면 |
| **Claude** | JSON 읽고 설계 판단 → Pencil 명령 생성 | 건축가 |
| **Pencil MCP** | `.pen` 파일 실제 수정 | 시공사 |
| **DESIGN_TOKENS** | 색상/폰트/간격 기준 | 자재 규격서 |

---

## 예시 프롬프트 (슬래시 명령어 대신 직접 입력 시)

슬래시 명령어(`/capture`, `/analyze`, `/design`)를 쓰지 않고 직접 프롬프트를 입력하고 싶을 때 사용하라.

### A. 단일 화면 전체 처리

```
다음 순서로 작업해줘:

1. Gemini MCP로 screenshots/[화면명].png를 분석해서
   screenshots/[화면명].json으로 사이드카를 저장해줘.
   JSON 스키마: { id, screen_number, gnb, menu, screen_type, components{nav,body}, layout, tokens_used, notes }

2. 생성된 JSON과 프로젝트 디자인 토큰(MD 파일 또는 .pen 내 변수)을 참조해서
   이 화면의 Pencil 프레임 구조를 설계해줘.

3. Pencil MCP로 .pen 파일에 해당 프레임을 구현해줘.
```

### B. 복수 화면 일괄 처리

```
screenshots/ 폴더의 PNG 파일 중 JSON 사이드카가 없는 것만 골라서
Gemini MCP로 일괄 분석하고 각각 [화면명].json으로 저장해줘.
완료되면 생성된 파일 목록을 알려줘.
```

### C. 기존 JSON으로 바로 Pencil 작업

```
screenshots/ 폴더의 JSON 사이드카 파일들을 읽고,
프로젝트 디자인 토큰(MD 파일 또는 mcp__pencil__get_variables)을 적용해서
[화면명] 화면의 Pencil 프레임을 만들어줘.
```

### D. 토큰 절약 모드 (JSON 우선 확인)

```
작업 전에 먼저 확인해줘:
- screenshots/ 폴더에서 JSON 사이드카가 없는 PNG 목록
- JSON이 이미 있는 화면 목록

JSON이 없는 것만 Gemini MCP로 분석하고, 있는 건 기존 JSON을 재활용해줘.
분석 완료 후 어떤 화면을 Pencil로 작업할지 물어봐줘.
```

---

## 트러블슈팅

### /capture 실패

| 증상 | 원인 | 해결 |
|------|------|------|
| `ModuleNotFoundError: playwright` | Playwright 미설치 | `pip install playwright && playwright install chromium` |
| `FileNotFoundError: HTML 파일` | `HTML_FILE` 경로 오류 | `tools/capture_screens_v3.py` 내 `HTML_FILE` 경로 수정 |
| 캡처됐는데 빈 화면 | 로그인 버튼 텍스트 불일치 | 스크립트 내 로그인 버튼 텍스트가 실제와 일치하는지 확인 |
| 일부 화면만 캡처됨 | 탭 10개 제한 | 배치 크기(`BATCH_SIZE = 9`) 줄이거나 순서 확인 |

---

### /analyze 실패 (Gemini MCP)

| 증상 | 원인 | 해결 |
|------|------|------|
| `HTTP 429` 오류 | Gemini API 분당 한도 초과 (무료: 15회/분) | 잠시 대기 후 재시도 |
| Gemini 응답 없음 | MCP 연결 안 됨 | `claude_desktop_config.json`에 Gemini MCP 등록 여부 확인 |
| JSON 저장 안 됨 | `screenshots/` 폴더 없음 | 폴더 직접 생성 후 재시도 |
| body가 비어있다는 훅 경고 | Gemini가 body를 분석 못 함 | 해당 PNG를 `/analyze`로 재분석. 프롬프트에 "body는 상세히" 강조 |

---

### /design 실패 (Pencil MCP)

| 증상 | 원인 | 해결 |
|------|------|------|
| JSON 파일 목록만 나오고 작업 안 함 | `$ARGUMENTS` 미입력 | `/design 03_결재함_결재목록` 처럼 화면명 함께 입력 |
| "JSON 파일을 찾을 수 없음" | 사이드카 파일 없음 | `/analyze` 먼저 실행 |
| `.pen` 파일 열기 실패 | Pencil MCP 연결 안 됨 | `claude_desktop_config.json`에 Pencil MCP 등록 여부 확인 |
| `fill_container` 경고 | 노드 교체 후 width 초기화 | `U(newId, {width:"fill_container"})` 추가 실행 |
| 디자인 토큰 색상이 틀림 | 구버전 토큰 참조 | MD 파일이면 해당 파일 업데이트, `.pen` 안에 있으면 `mcp__pencil__get_variables`로 확인 |

---

### 훅(Hook)이 동작 안 함

| 증상 | 원인 | 해결 |
|------|------|------|
| JSON 저장해도 검증 출력 없음 | `settings.json` 미로드 | `.claude/settings.json` 존재 여부 확인 후 Claude Code 재시작 |
| `python: command not found` | Python 경로 문제 | `settings.json`의 command를 `python3 tools/validate_sidecar.py`로 수정 |

---

## 작업 시작 전 체크리스트

- [ ] `screenshots/` 폴더에 PNG 준비
- [ ] JSON 사이드카 존재 여부 확인 (있으면 Gemini 재호출 불필요)
- [ ] 디자인 토큰 최신 상태 확인
  - 토큰이 **MD 파일**에 있으면 → 해당 파일 내용이 현재 디자인과 일치하는지 확인
  - 토큰이 **`.pen` 파일 안**에 있으면 → `mcp__pencil__get_variables`로 현재 값 확인
- [ ] Pencil MCP에서 `.pen` 파일 열려있는지 확인
- [ ] 작업 후 git commit으로 버전 저장
