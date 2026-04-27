# AI 하나로 — 분석 원본 (Raw Analysis Document)

> CSS 변수, 실제 측정값, 클래스명 원본 데이터 중심의 기술 참조 문서
> 분석 일자: 2026-04-03

---

## A. CSS Variables 원본 (:root)

```css
:root {
  /* 레이아웃 토큰 */
  --topbar-height: 4rem;          /* 64px */
  --sidebar-1-width: 4.5rem;      /* 72px */
  --sidebar-width: 20.5rem;       /* 328px */
  --searchbar-height: 3.5rem;     /* 56px */
  --right-modal-width: 30rem;     /* 480px */

  /* 브랜드 컬러 */
  --aicfo-purple: #4f63d2;
  --aicfo-purple-accent: #5267dd;
  --aicfo-text: #0f0f0f;

  /* 배경 */
  --background-primary: #ffffff;
  --background-secondary: #f5f5f5;
  --background-tertiary: #edeef0;
  --background-sidebar: #f5f5f5;
  --background-gray: #f9fafb;

  /* 텍스트 */
  --text-primary: #0f0f0f;
  --text-primary-accent: #0f0f0f;
  --text-secondary: #6e6f72;
  --text-tertiary: #a0a0a0;
  --text-disabled: #4f4f4f;
  --text-hover: #005df9;

  /* UI 요소 */
  --placeholder: #cdcdcd;
  --border: #d9d9d9;
  --button-disabled: #eaeaea;
  --background-disabled: #767676;

  /* 폰트 */
  --font-family: "Pretendard Variable", -apple-system, BlinkMacSystemFont,
    system-ui, Roboto, "Helvetica Neue", "Segoe UI", "Apple SD Gothic Neo",
    "Malgun Gothic", "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol",
    sans-serif;

  font-size: 16px;
  height: 100%;
}
```

---

## B. Tailwind 커스텀 클래스 원본

```
.bg-aicfo                    → background-color: var(--aicfo-purple)
.text-aicfo                  → color: var(--aicfo-purple)
.border-aicfo                → border-color: var(--aicfo-purple)
.accent-aicfo                → accent-color: var(--aicfo-purple)
.outline-aicfo               → outline-color: var(--aicfo-purple)
.bg-background-sidebar       → background-color: var(--background-sidebar)
.w-sidebar-width             → width: var(--sidebar-width)
.w-[var(--sidebar-1-width)]  → width: 4.5rem
.h-searchbar-height          → height: var(--searchbar-height)
.h-topbar-height             → height: var(--topbar-height)
.text-[var(--aicfo-text)]    → color: var(--aicfo-text)
```

---

## C. 컴포넌트별 실제 클래스명 원본

### C-1. 전체 레이아웃 구조

```
<html>
  body (font-size: 16px, font-family: Pretendard Variable)
  ├─ <header class="fixed top-0 left-0 right-0 h-topbar-height shrink-0 md:flex z-[9900]">
  │    height=64px, position=fixed, z-index=9900
  │    └─ <nav class="mx-auto flex h-full w-full items-center justify-between px-4">
  │         height=64px, padding=0 16px
  ├─ <aside class="shrink-0 z-50 flex justify-between h-screen absolute md:relative whitespace-nowrap">
  │    ├─ 아이콘 사이드바: class="flex z-20 relative h-full border-r border-border bg-background-sidebar"
  │    │    width=72px | bg=rgb(245,245,245) | border-right=1px solid #d9d9d9
  │    └─ 최근질문 패널: class="relative" — width: 0 <-> 328px 토글
  └─ <main class="w-full h-full pt-[var(--topbar-height)]">
       padding-top=64px
```

### C-2. Header / TopBar

```
헤더:
  class="fixed top-0 left-0 right-0 h-topbar-height shrink-0 md:flex z-[9900]"
  실측: height=64px | position=fixed | z-index=9900 | bg=rgba(0,0,0,0)

내비:
  class="mx-auto flex h-full w-full items-center justify-between px-4"
  실측: height=64px | padding=0 16px

문의하기 버튼:
  class="bg-white p-2 w-10 h-10 border border-gray-300 rounded-full hover:bg-gray-50"
  실측: bg=rgb(255,255,255) | 40x40px | border=1px solid #d1d5db | border-radius=9999px

알림 버튼:
  class="relative outline-none"
  실측: bg=rgba(0,0,0,0)
```

### C-3. Sidebar (아이콘 사이드바)

```
외부 컨테이너:
  class="flex z-20 relative h-full border-r border-border bg-background-sidebar"
  실측: bg=rgb(245,245,245) | border-right=1px solid #d9d9d9 | width=72px

내부 컬럼:
  class="flex flex-col items-center text-disabled w-[var(--sidebar-1-width)] justify-between"
  실측: color=rgb(79,79,79)

메뉴 그룹:
  class="flex flex-col gap-y-4 px-4"
  실측: gap=16px | padding=0 16px

개별 아이템:
  class="group relative flex items-center justify-center flex-col text-left cursor-pointer text-xs"
  실측: font-size=12px | color=rgb(79,79,79) (비활성)

로고 영역:
  class="flex justify-center z-40 items-center p-5 pb-7 shrink-0"
  실측: padding=20px 20px 28px
```

### C-4. Recent Panel (최근 질문 패널)

```
전체 삭제 버튼:
  class="bg-white text-gray-500 border border-gray-500 text-[0.65rem] px-2 rounded-full"
  실측: bg=white | color=rgb(107,114,128) | border=1px solid rgb(107,114,128)
        border-radius=9999px | font-size=10.4px | height=22px | padding=0 8px

날짜 구분자 ("오늘"):
  실측: color=rgb(110,111,114) — text-secondary | font-size=16px

리스트 아이템:
  class="flex group relative items-center justify-between rounded-lg hover:bg-background-secondary cursor-pointer"
  실측: bg=rgba(0,0,0,0) | color=rgb(15,15,15) | font-size=14px
        border-radius=8px | hover: bg=rgb(245,245,245)

접기 버튼:
  class="w-8 h-8 p-1 bg-background-primary border border-gray-300 rounded-md rotate-180"
  실측: bg=white | border=1px solid #d1d5db | border-radius=6px | 32x32px
```

### C-5. SearchBar

```
사업장 드롭다운:
  class="inline-flex items-center gap-1 px-4 py-2 rounded-[6.25rem] border border-[#EAEAEA] text-gray-700 cursor-pointer hover:text-gray-900"
  실측: border=1px solid #eaeaea | border-radius=100px | color=rgb(55,65,81)
        height=38px | padding=8px 16px | font-size=16px

검색 컨테이너:
  class="flex w-full group flex-col justify-start h-full items-start rounded-xl border border-background-secondary transition-colors focus-within:border-aicfo focus-within:bg-background-primary group/search bg-background-secondary"
  실측: bg=rgb(245,245,245) | border=1px solid rgb(245,245,245)
        border-radius=12px | height=58px
        focus: border-color=#4f63d2 | bg=#ffffff

form:
  class="flex w-full searchform group flex-1 px-4 items-center"
  실측: height=56px

input:
  class="w-full group rounded-lg bg-transparent h-searchbar-height pl-2 text-base outline-none md:pl-4"
  실측: bg=transparent | border=none | font-size=16px
        padding-left=16px | height=56px | color=rgb(15,15,15)
        placeholder: color=#cdcdcd
```

### C-6. Tab Group (홈 상단 탭)

```
활성 탭 (일반질의):
  class="inline-flex items-center gap-2 px-4 py-2 border border-b-0 rounded-t-xl relative top-[1px] text-sm font-bold transition-all bg-white border-[#e8edf7] text-gray-800"
  실측: bg=rgb(255,255,255) | color=rgb(31,41,55) | border-radius=12px 12px 0 0
        font-size=14px | font-weight=700 | padding=8px 16px

비활성 탭:
  class="...bg-[#f5f7fa] border-transparent text-gray-400 hover:text-gray-600"
  실측: bg=rgb(245,247,250) | color=rgb(156,163,175)
```

### C-7. Category Chips

```
활성 칩 (자금현황):
  class="flex items-center gap-1.5 px-3.5 py-2 rounded-[10px] border whitespace-nowrap transition-all bg-[#4f63d2] border-[#4f63d2] text-white"
  실측: bg=rgb(79,99,210) | color=white | border=1px solid #4f63d2
        border-radius=10px | padding=8px 14px | font-size=16px

비활성 칩:
  class="...bg-[#f5f7fa] border-[#e8edf7] hover:bg-[#4f63d2] hover:text-white"
  실측: bg=rgb(245,247,250) | color=rgb(15,15,15) | border=1px solid #e8edf7
```

### C-8. Suggestion List Items

```
class="flex items-center gap-2 w-full px-3 py-2 rounded-lg hover:bg-gray-100 text-sm text-left whitespace-nowrap overflow-hidden"
실측: bg=transparent | color=rgb(15,15,15) | border-radius=8px
      font-size=14px | padding=8px 12px | hover: bg=rgb(243,244,246)
```

### C-9. 인기질의 Rank Badge

```
1위: class="w-7 h-7 rounded-lg flex items-center justify-center text-sm font-bold border bg-[#fef3c7] text-[#92400e]"
     실측: bg=rgb(254,243,199) | color=rgb(146,64,14) | 28x28px | border-radius=8px

2위: class="...bg-[#e0e7ff] text-[#3730a3]"
     실측: bg=rgb(224,231,255) | color=rgb(55,48,163)

3위: class="...bg-[#fce7f3] text-[#831843]"
     실측: bg=rgb(252,231,243) | color=rgb(131,24,67)

공통: font-size=14px | font-weight=700
```

### C-10. Chat 하단 탭 버튼

```
활성 (일반질의):
  class="inline-flex items-center gap-2 px-4 py-2 rounded-full text-sm font-bold transition-all border bg-white text-gray-800 border-gray-400"
  실측: bg=white | color=rgb(31,41,55) | border=1px solid #9ca3af
        border-radius=9999px | font-size=14px | font-weight=700

비활성 (FAQ):
  class="...bg-[#f5f7fa] border-[#e8edf7] text-gray-400 hover:text-gray-600"
```

### C-11. Chat Message

```
사용자 쿼리 텍스트:
  class="flex items-center gap-x-2 group"
  실측: color=rgb(79,99,210) | font-size=16px

타임스탬프 (p):
  class="text-sm font-normal text-nowrap text-tertiary"
  실측: color=rgb(160,160,160) | font-size=14px | font-weight=400

AI 응답 헤더:
  class="flex w-full items-center justify-between"
  실측: color=rgb(15,15,15) | font-size=16px
```

### C-12. Report Card

```
카드 외부:
  class="overflow-hidden rounded-sm border border-gray-200 bg-white shadow-2xl"
  실측: bg=white | border=1px solid #e5e7eb | border-radius=2px

상단 색상 바:
  class="h-2 w-full"
  실측: bg=rgb(37,99,235) | height=8px | width=100%

카드 본문:
  class="p-10"
  실측: padding=40px

헤더 구분선:
  class="flex justify-between items-start border-b-2 pb-5 mb-8"
  실측: border-bottom=2px solid #e5e7eb | pb=20px | mb=32px

보고서 제목 (H1):
  class="text-3xl font-extrabold tracking-tight text-gray-900"
  실측: font-size=30px | font-weight=800 | color=rgb(17,24,39)

날짜 (p):
  class="text-gray-500 mt-2 text-sm"
  실측: font-size=14px | color=rgb(107,114,128)

섹션 구분 블록:
  class="mb-8 rounded-r-md border-l-4 p-4"
  실측: border-left=4px solid rgb(37,99,235) | padding=16px
        bg=rgba(37,99,235,0.03) | border-radius=0 6px 6px 0
```

### C-13. Summary Card (요약 카드)

```
class="overflow-hidden rounded-2xl border border-slate-200 bg-gradient-to-br from-slate-50/50 to-white px-4 py-3.5 shadow-[0_2px_8px_rgba(0,0,0,0.05)] transition-all duration-200 hover:-translate-y-0.5 hover:shadow-[0_4px_16px_rgba(0,0,0,0.08)]"
실측: border=1px solid #e2e8f0 | border-radius=16px | padding=14px 16px
      box-shadow=0 2px 8px rgba(0,0,0,0.05)
      hover: translateY(-2px) | shadow 강화
```

### C-14. Data Table (보고서 내부 테이블)

```
헤더 행 (tr):
  class="border-b-2 border-slate-200 bg-gradient-to-r from-slate-50 to-slate-100/60"
  실측: border-bottom=2px solid #e2e8f0 | height=45px

데이터 행 홀수 (tr):
  class="border-b border-slate-100 transition-colors duration-150 hover:bg-blue-50/40 bg-white"
  실측: bg=white | border-bottom=1px solid #f1f5f9

데이터 행 짝수 (tr):
  class="...bg-slate-50/30"
  실측: bg=rgba(248,250,252,0.3)

셀 (td):
  class="whitespace-nowrap border-r border-slate-100 px-4 py-3 last:border-r-0 text-center text-slate-800"
  실측: color=rgb(30,41,59) | font-size=14px | padding=12px 16px
        border-right=1px solid #f1f5f9
```

### C-15. Analysis Cards (분석 인사이트 카드)

```
중요 (emerald):
  class="flex items-start gap-3 rounded-2xl border-l-[3px] px-4 py-3.5 shadow-[0_1px_4px_rgba(0,0,0,0.04)] border-l-emerald-400 bg-gradient-to-r from-emerald-50/80 to-transparent"
  실측: border-left=3px solid rgb(52,211,153) | border-radius=16px | padding=14px 16px

보조 (slate):
  class="...border-l-slate-300 bg-gradient-to-r from-slate-50/80 to-transparent"
  실측: border-left=3px solid rgb(203,213,225)
```

### C-16. Detail Section (상세조회 카드)

```
카드:
  class="mt-4 rounded-xl border border-slate-200 bg-white overflow-hidden"
  실측: border=1px solid #e2e8f0 | border-radius=12px

헤더:
  class="px-4 py-3 flex items-center gap-2"
  실측: padding=12px 16px

제목 H4:
  class="text-base font-bold text-slate-700"
  실측: font-size=16px | font-weight=700 | color=rgb(51,65,85)

총건수 span:
  class="text-sm text-slate-400"
  실측: font-size=14px | color=rgb(148,163,184)

날짜 Input:
  class="rounded-lg border border-slate-200 bg-slate-50 px-3 py-2 text-xs outline-none focus:border-blue-400 disabled:opacity-40"
  실측: bg=rgb(248,250,252) | border=1px solid #e2e8f0
        border-radius=8px | font-size=12px | height=36px

조회 버튼 (활성):
  class="...rounded-lg bg-blue-600 px-4 py-2 text-xs font-semibold text-white...hover:bg-blue-700 disabled:bg-slate-300"
  실측(disabled): bg=rgb(203,213,225)

Select (페이지당 건수):
  class="rounded border border-slate-200 bg-white px-2 py-1 text-xs outline-none focus:border-blue-400"
  실측: bg=white | border=1px solid #e2e8f0 | border-radius=4px
        font-size=12px | color=rgb(100,116,139) | height=26px
```

### C-17. Export Buttons

```
Excel 다운로드:
  class="ml-auto flex items-center gap-1 rounded-md bg-emerald-600 px-2.5 py-1 text-[11px] font-semibold text-white transition-colors hover:bg-emerald-700"
  실측: bg=rgb(5,150,105) | border-radius=6px | font-size=11px
        font-weight=600 | height=28px | padding=4px 10px

PDF 내보내기:
  class="flex items-center gap-1.5 px-3 py-2 text-xs font-medium rounded-lg border border-gray-200 bg-white text-gray-700 hover:bg-gray-50 transition-colors"
  실측: bg=white | color=rgb(55,65,81) | border=1px solid #e5e7eb
        border-radius=8px | font-size=12px | font-weight=500 | height=34px
```

### C-18. Settings 페이지

```
브레드크럼:
  class="bg-[#e7e7e7] px-9 py-2"
  실측: bg=rgb(231,231,231) | padding=8px 36px | font-size=16px

서브메뉴 아이템 (활성):
  class="flex items-center justify-between p-2 rounded-lg cursor-pointer transition-colors text-blue-700 font-medium"
  실측: color=rgb(29,78,216) | font-weight=500 | padding=8px | border-radius=8px
  [참고] 왼쪽 빨간 닷(•) 인디케이터 별도 존재

서브메뉴 텍스트 (활성):
  class="text-sm font-semibold"
  실측: font-size=14px | font-weight=600

Settings 테이블 헤더 (TH):
  class="ant-table-cell ant-table-column-has-sorters"
  실측: bg=rgb(78,103,131) | color=white | font-size=11.9px | font-weight=700
        padding=0 10px

정보 라벨 셀 (TH):
  class="ant-table-cell bg-gray-50 font-bold"
  실측: bg=rgb(249,250,251) | color=rgb(71,85,105) | font-weight=700
```

---

## D. 전체 버튼 변형 실측 표

| # | 용도 | bg | color | border | radius | font-size | 크기 |
|---|---|---|---|---|---|---|---|
| 1 | 카테고리 활성 | #4f63d2 | white | #4f63d2 | 10px | 16px | px14 py8 |
| 2 | 카테고리 비활성 | #f5f7fa | #0f0f0f | #e8edf7 | 10px | 16px | px14 py8 |
| 3 | Primary Blue (변경) | #3b82f6 | white | transparent | 12px | 14px | px15 |
| 4 | Secondary (비번변경) | #667efe | white | gray-200 | 8px | 12px | px16 py6 |
| 5 | Outline Pill (전체삭제) | white | #6b7280 | #6b7280 | 9999px | 10.4px | px8 h22 |
| 6 | Ghost Circle (헤더 아이콘) | white | #0f0f0f | #d1d5db | 9999px | 16px | 40x40 |
| 7 | 페이지네이션 활성 | #3b82f6 | white | #3b82f6 | 4px | 14px | 32x32 |
| 8 | 페이지네이션 비활성 | transparent | #d1d5db | #e5e7eb | 4px | 14px | 32x32 |
| 9 | 페이지네이션 이전/다음 | white | — | #d1d5db | 6px | — | 32x32 |
| 10 | Excel | #059669 | white | none | 6px | 11px | h28 |
| 11 | PDF 내보내기 | white | #374151 | #e5e7eb | 8px | 12px | h34 |
| 12 | 기간조회 (활성) | #2563eb | white | none | 8px | 12px | px16 py8 |
| 13 | 기간조회 (disabled) | #d1d5db | white | none | 8px | 12px | — |

---

## E. Typography 실측 요약

| 용도 | 태그 | font-size | font-weight | color |
|---|---|---|---|---|
| 보고서 제목 | H1 | 30px | 800 | #111827 |
| 섹션 헤더 | H4 | 16px | 700 | #334155 |
| 기본 본문 | div/p | 16px | 400 | #0f0f0f |
| 탭 버튼 | button | 14px | 700 | #1f2937 |
| 추천질문/리스트 | button | 14px | 400 | #0f0f0f |
| 날짜 (보고서) | p | 14px | 400 | #6b7280 |
| 타임스탬프 | p | 14px | 400 | #a0a0a0 |
| 총건수 서브 | span | 14px | 400 | #94a3b8 |
| 사이드바 레이블 | div | 12px | 400 | #4f4f4f |
| 테이블 셀 | td | 14px | 400 | #1e293b |
| 설정 테이블 헤더 | th | 11.9px | 700 | white |
| Export 버튼 | button | 11~12px | 500~600 | — |
| 날짜 Input | input | 12px | 400 | #0f0f0f |
| Select | select | 12px | 400 | #64748b |
| 소형 뱃지 | div | 10.4px | 400 | #6b7280 |

---

## F. Layout 측정값 요약

| 요소 | 측정값 |
|---|---|
| Header 높이 | 64px (fixed) |
| Header z-index | 9900 |
| 아이콘 사이드바 너비 | 72px |
| 최근질문 패널 너비 | 328px |
| 검색창 높이 (컨테이너) | 58px |
| 검색창 높이 (input) | 56px |
| 사업장 드롭다운 높이 | 38px |
| 보고서 카드 내부 패딩 | 40px |
| 보고서 상단 컬러 바 높이 | 8px |
| 요약 카드 패딩 | 14px 16px |
| 상세조회 카드 헤더 패딩 | 12px 16px |
