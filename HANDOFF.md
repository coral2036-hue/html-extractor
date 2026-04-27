# 🎯 인수인계 — Branch+ — 통합 목업 (5개 역할)

> **Claude Code / 개발팀 / 디자이너용 진입 문서**
> 이 폴더 통째로 받았다면 이 파일을 가장 먼저 읽으세요.

---

## 📋 이 폴더는 무엇인가요?

원본 HTML `index__2_.html` 1개를 자동 분석해 만들어진 **디자인 + 개발 인수인계 패키지**입니다.

- **페이지 분류**: `list-with-filter`
- **추출된 디자인 토큰**: 색상 27, 폰트 2, 사이즈 10, 간격 10
- **추론된 컴포넌트**: 106개

## ⚠️ 정직 고지 — 이 폴더의 성격

**이 패키지는 "디자인 핸드오프 자동화" 결과물입니다. "즉시 머지 가능한 React 코드" 가 아닙니다.**

- `react/*.tsx` 는 `@ts-nocheck` + `document.getElementById` + 전역 뮤터블 `_g` 싱글턴을 사용합니다.
- **"렌더는 됨" 을 증명하는 프로토타입** 이며, 실무 프로젝트 재사용은 권장하지 않습니다.
- 개발팀은 `docs/spec.md` + `design-tokens/tokens.css` + `_source.html` 을 받아 **자기 프로젝트 패턴으로 재구현** 하는 것이 맞는 사용법입니다.

## 📂 폴더 가이드 (어디부터 봐야 하나요?)

| 역할 | 먼저 볼 파일 | 그 다음 |
|------|------------|--------|
| **개발자 (재구현)** | `docs/spec.md` | `design-tokens/tokens.css` → `_source.html` (브라우저로 열어 실제 동작 확인) |
| **개발자 (프로토타입 참고만)** | `react/FIDELITY_NOTES.md` | `react/Page.tsx` (⚠️ 재사용 금지) |
| **디자이너** | `docs/components.md` | `figma/figma-import-instructions.md` |
| **Figma 작업자** | `figma/figma-frame.json` + `design-tokens/tokens.figma.json` | Tokens Studio 플러그인으로 import |
| **Claude Code** | 이 `HANDOFF.md` | `CLAUDE_PROMPTS.md` 의 프롬프트 복사 → 실행 |
| **PM / 기획자** | `docs/spec.md` | `_source.html` (원본 HTML 미리보기) |

## 🧩 추론된 컴포넌트

- `Button` × 80 — 예: "로그인", "회원가입", "로그아웃"
- `Input` × 75
- `Card` × 42
- `DataTable` × 10

각 컴포넌트의 상세 스펙은 `docs/components.md` 참고.

## 🎨 디자인 토큰 사용법

| 포맷 | 위치 | 어디에 쓰나요? |
|------|------|---------------|
| CSS Variables | `design-tokens/tokens.css` | React/HTML 프로젝트 import |
| W3C Design Tokens | `design-tokens/tokens.w3c.json` | Style Dictionary, 다른 토큰 도구 |
| Tailwind | `design-tokens/tokens.tailwind.cjs` | tailwind.config.js extend |
| Figma Tokens | `design-tokens/tokens.figma.json` | Tokens Studio 플러그인 |
| TypeScript | `design-tokens/tokens.ts` | TS/TSX 프로젝트에서 import |

## ⚠️ 충돌 검토 (중요)

`conflicts/conflicts.md` 에 **기존 디자인 시스템과의 차이점이 후보로만** 정리되어 있습니다.
- ✅ 자동 반영되지 않음
- ✅ 사람이 검토 후 결정 필요
- 신규 토큰을 그대로 쓸지, 기존 토큰으로 매핑할지 결정하세요

## 🚀 다음 단계

1. **개발 진행**: `CLAUDE_PROMPTS.md` 의 #1 프롬프트를 Claude Code에 복붙
2. **디자인 검수**: `figma/` 폴더 가이드대로 Figma에 import
3. **충돌 정리**: `conflicts/conflicts.md` 검토 → 디자인시스템 버전 업데이트

---

**원본 파일**: `_source.html`
**생성 시각**: 2026-04-20T06:40:02.044Z
**문의**: branch@webcash.co.kr
