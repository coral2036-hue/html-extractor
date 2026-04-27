# AI 하나로 — 통합 디자인 시스템 + React 개발 문서

> 디자인 가이드와 React 컴포넌트 코드가 하나로 통합된 실무 완성형 문서  
> 작성 기준: 2026-04-03

---

# PART 1. Design System

---

## 1. Color Palette

### 1.1 Brand Colors

| 이름 | Hex | CSS Token | 사용처 |
|---|---|---|---|
| Brand Purple | `#4f63d2` | `--aicfo-purple` | 활성 버튼, 사용자 쿼리 텍스트, 검색창 포커스 |
| Brand Purple Accent | `#5267dd` | `--aicfo-purple-accent` | 버튼 hover |
| Brand Blue | `#667efe` | — | Secondary 액션 버튼 |
| Report Blue | `#2563eb` | `blue-600` | 보고서 상단 바, 섹션 accent |
| Hover Blue | `#005df9` | `--text-hover` | 텍스트 hover |

### 1.2 Background Colors

| 이름 | Hex | CSS Token | 사용처 |
|---|---|---|---|
| Primary | `#ffffff` | `--background-primary` | 메인 배경, 카드, 활성 탭 |
| Secondary | `#f5f5f5` | `--background-secondary` | 검색창, 사이드바, 비활성 요소 |
| Tertiary | `#edeef0` | `--background-tertiary` | 3단계 배경 |
| Sidebar | `#f5f5f5` | `--background-sidebar` | 좌측 아이콘 사이드바 |
| Gray | `#f9fafb` | `--background-gray` | 테이블 라벨 셀 |
| Breadcrumb | `#e7e7e7` | — | 브레드크럼 배경 |
| Tab Inactive | `#f5f7fa` | — | 비활성 탭/칩 배경 |
| Chip Border | `#e8edf7` | — | 카테고리 칩 비활성 보더 |

### 1.3 Text Colors

| 이름 | Hex | CSS Token | 사용처 |
|---|---|---|---|
| Primary | `#0f0f0f` | `--text-primary` | 기본 본문, 버튼 |
| Secondary | `#6e6f72` | `--text-secondary` | 보조 설명 |
| Tertiary | `#a0a0a0` | `--text-tertiary` | 타임스탬프, 힌트 |
| Disabled | `#4f4f4f` | `--text-disabled` | 사이드바 아이콘 |
| Placeholder | `#cdcdcd` | `--placeholder` | 입력창 플레이스홀더 |

### 1.4 Semantic / Status Colors

| 이름 | Hex | 사용처 |
|---|---|---|
| Border Default | `#d9d9d9` | 사이드바 우측 구분선 |
| Border Subtle | `#e8edf7` | 칩, 탭 보더 |
| Border Slate | `#e2e8f0` | 테이블, 카드 보더 |
| Table Header BG | `#4e6783` | 설정 테이블 헤더 배경 |
| Success Green | `#059669` | Excel 버튼 |
| Rank 1 BG / Text | `#fef3c7` / `#92400e` | 인기질의 1위 뱃지 |
| Rank 2 BG / Text | `#e0e7ff` / `#3730a3` | 인기질의 2위 뱃지 |
| Rank 3 BG / Text | `#fce7f3` / `#831843` | 인기질의 3위 뱃지 |

---

## 2. Typography

**기본 폰트:** Pretendard Variable
```css
font-family: "Pretendard Variable", -apple-system, BlinkMacSystemFont,
  system-ui, Roboto, "Helvetica Neue", "Segoe UI", "Apple SD Gothic Neo",
  "Malgun Gothic", sans-serif;
font-size: 16px; /* root */
```

| 레벨 | Tag | Size | Weight | Color | 용도 |
|---|---|---|---|---|---|
| H1 | h1 | 30px | 800 | `#111827` | 보고서 타이틀 |
| H4 | h4 | 16px | 700 | `#334155` | 섹션 헤더, 카드 제목 |
| Body-lg | div/p | 16px | 400 | `#0f0f0f` | 기본 본문, 버튼 |
| Tab | button | 14px | 700 | `#1f2937` | 탭 버튼 레이블 |
| Body-sm | p/button | 14px | 400 | `#0f0f0f` | 추천질문, 리스트 |
| Timestamp | p | 14px | 400 | `#a0a0a0` | 채팅 시각 표시 |
| Caption | — | 12px | 400~600 | `#64748b` | 날짜 Input, Export 버튼 |
| Sidebar | div | 12px | 400 | `#4f4f4f` | 사이드바 레이블 |
| Micro | div | 10.4px | 400 | `#6b7280` | 소형 뱃지 |

---

## 3. Spacing & Layout Tokens

| Token | Value | 용도 |
|---|---|---|
| `--topbar-height` | 64px | 상단 바 높이 |
| `--sidebar-1-width` | 72px | 아이콘 사이드바 너비 |
| `--sidebar-width` | 328px | 최근질문 패널 너비 |
| `--searchbar-height` | 56px | 검색창 input 높이 |
| `--right-modal-width` | 480px | 우측 모달 너비 |

---

## 4. Border Radius Scale

| 크기 | 값 | 컴포넌트 |
|---|---|---|
| xs | 4px | 페이지네이션 버튼, Select |
| sm | 6px | 이전/다음 버튼, Excel 버튼 |
| base | 8px | 서브메뉴 아이템, Input, 조회 버튼 |
| md | 10px | 카테고리 칩 |
| lg | 12px | 검색창 컨테이너, Primary 버튼 |
| xl | 16px | 요약 카드, 분석 카드 |
| tab | 12px 12px 0 0 | 상단 탭 (rounded-t-xl) |
| report | 2px | 보고서 카드 (rounded-sm) |
| full | 9999px | Pill 버튼 (전체삭제, 하단탭) |

---

## 5. Shadow

| 이름 | 값 | 용도 |
|---|---|---|
| Report | `shadow-2xl` (Tailwind) | 보고서 카드 |
| Card Default | `0 2px 8px rgba(0,0,0,0.05)` | 요약 카드 기본 |
| Card Hover | `0 4px 16px rgba(0,0,0,0.08)` | 요약 카드 hover |
| Analysis | `0 1px 4px rgba(0,0,0,0.04)` | 분석 인사이트 카드 |

---

## 6. Button Variants

| Variant | bg | color | border | radius | 크기 | 용도 |
|---|---|---|---|---|---|---|
| category-active | `#4f63d2` | white | `#4f63d2` | 10px | px14 py8 | 활성 카테고리 칩 |
| category-default | `#f5f7fa` | `#0f0f0f` | `#e8edf7` | 10px | px14 py8 | 비활성 카테고리 칩 |
| primary-blue | `#3b82f6` | white | transparent | 12px | px15 | 변경 버튼 |
| secondary | `#667efe` | white | gray-200 | 8px | px16 py6 | 비밀번호 변경 |
| outline-pill | white | `#6b7280` | `#6b7280` | 9999px | px8 h22 | 전체 삭제 |
| ghost-circle | white | `#0f0f0f` | `#d1d5db` | 9999px | 40×40 | 헤더 아이콘 버튼 |
| pagination-active | `#3b82f6` | white | `#3b82f6` | 4px | 32×32 | 현재 페이지 |
| pagination-inactive | transparent | `#d1d5db` | `#e5e7eb` | 4px | 32×32 | 비활성 페이지 |
| pagination-nav | white | — | `#d1d5db` | 6px | 32×32 | 이전/다음 화살표 |
| excel | `#059669` | white | none | 6px | h28 | Excel 내보내기 |
| pdf | white | `#374151` | `#e5e7eb` | 8px | h34 | PDF 내보내기 |
| period-active | `#2563eb` | white | none | 8px | px16 py8 | 기간 내역 조회 |
| period-disabled | `#d1d5db` | white | none | 8px | — | 조회 버튼 disabled |

---

## 7. Component State 규칙

| 상태 | 처리 방식 |
|---|---|
| hover | bg 변경 또는 `translateY(-2px)` |
| focus | `border-color: #4f63d2`, `background: white` |
| active (selected) | `background: #4f63d2`, `color: white` |
| disabled | `background: #d1d5db`, `cursor: not-allowed`, `opacity: 0.4` |
| loading | 스켈레톤 3줄 바 + `animate-pulse` |

---

## 8. 화면 Layout 구조
```
┌─────────────────────────────────────────────────────────┐
│  Header (fixed, h=64px, z=9900, bg=white)               │
│  [큐브 로고 + "AI 하나로"]        [문의하기] [알림벨]     │
├──────┬──────────────────────────────────────────────────┤
│ Side │  Recent Panel (width: 0 ↔ 328px 토글)            │
│  bar │  - 헤더: "최근 질문" + 전체삭제 버튼              │
│ 72px │  - 날짜 구분자 (오늘, 어제 ...)                   │
│      │  - 리스트 아이템 (font-size: 14px)                │
│[새채] │                                                   │
│[최질] │  Main Content Area                               │
│[설정] │  ┌────────────────────────────────────────────┐  │
│[로그] │  │ [홈] 타이틀 + SearchBar                     │  │
│      │  │      TabGroup (일반질의/인기질의/FAQ)         │  │
│      │  │      CategoryChips (자금현황…세금계산서)      │  │
│      │  │      SuggestionList                        │  │
│      │  │                                            │  │
│      │  │ [채팅] UserQuery + ReportCard              │  │
│      │  │        SummaryCards + DataTable            │  │
│      │  │        AnalysisCards + DetailSection       │  │
│      │  │                                            │  │
│      │  │ [설정] SettingsSidebar + 콘텐츠 영역        │  │
│      │  └────────────────────────────────────────────┘  │
│      │  Bottom Bar: BottomTabs + ChatInput               │
└──────┴──────────────────────────────────────────────────┘
```

---

# PART 2. React 개발 문서

---

## 1. 프로젝트 설정

### 패키지 설치
```bash
npm create vite@latest ai-hanaro -- --template react-ts
cd ai-hanaro
npm install
npm install -D tailwindcss postcss autoprefixer
npm install antd
npx tailwindcss init -p
```

### tailwind.config.js
```js
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: ['./index.html', './src/**/*.{js,ts,jsx,tsx}'],
  theme: {
    extend: {
      colors: {
        aicfo: {
          DEFAULT: '#4f63d2',
          accent: '#5267dd',
        },
        'brand-blue': '#667efe',
        'report-blue': '#2563eb',
        'bg-primary': '#ffffff',
        'bg-secondary': '#f5f5f5',
        'bg-tertiary': '#edeef0',
        'bg-sidebar': '#f5f5f5',
        'text-main': '#0f0f0f',
        'text-sub': '#6e6f72',
        'text-hint': '#a0a0a0',
        'text-side': '#4f4f4f',
        'border-default': '#d9d9d9',
        'border-subtle': '#e8edf7',
        'chip-inactive': '#f5f7fa',
      },
      height: {
        topbar: 'var(--topbar-height)',
        searchbar: 'var(--searchbar-height)',
      },
      width: {
        'sidebar-icon': 'var(--sidebar-1-width)',
        'sidebar-panel': 'var(--sidebar-width)',
      },
      fontFamily: {
        sans: [
          '"Pretendard Variable"',
          '-apple-system',
          'BlinkMacSystemFont',
          'system-ui',
          'Roboto',
          '"Helvetica Neue"',
          '"Segoe UI"',
          '"Apple SD Gothic Neo"',
          '"Malgun Gothic"',
          'sans-serif',
        ],
      },
    },
  },
  plugins: [],
};
```

### src/styles/globals.css
```css
@tailwind base;
@tailwind components;
@tailwind utilities;

:root {
  --topbar-height: 4rem;
  --sidebar-1-width: 4.5rem;
  --sidebar-width: 20.5rem;
  --searchbar-height: 3.5rem;
  --right-modal-width: 30rem;

  --aicfo-purple: #4f63d2;
  --aicfo-purple-accent: #5267dd;
  --placeholder: #cdcdcd;
  --border: #d9d9d9;
  --background-primary: #ffffff;
  --background-secondary: #f5f5f5;
  --background-tertiary: #edeef0;
  --background-sidebar: #f5f5f5;
  --background-gray: #f9fafb;
  --button-disabled: #eaeaea;
  --text-primary: #0f0f0f;
  --text-secondary: #6e6f72;
  --text-tertiary: #a0a0a0;
  --text-disabled: #4f4f4f;
  --text-hover: #005df9;
  --font-family: "Pretendard Variable", -apple-system, BlinkMacSystemFont,
    system-ui, Roboto, "Helvetica Neue", "Segoe UI", "Apple SD Gothic Neo",
    "Malgun Gothic", sans-serif;

  font-size: 16px;
  height: 100%;
}

body {
  font-family: var(--font-family);
  color: var(--text-primary);
  background-color: var(--background-primary);
}
```

---

## 2. 컴포넌트 디렉토리 구조
```
src/
├── components/
│   ├── layout/
│   │   ├── AppLayout.tsx          # 전체 레이아웃 래퍼
│   │   ├── TopBar.tsx             # 상단 헤더
│   │   ├── Sidebar.tsx            # 좌측 아이콘 사이드바
│   │   └── RecentPanel.tsx        # 최근 질문 슬라이드 패널
│   ├── home/
│   │   ├── HomePage.tsx           # 홈 메인 페이지
│   │   ├── SearchBar.tsx          # 검색창 (드롭다운 + 인풋)
│   │   ├── TabGroup.tsx           # 일반질의/인기질의/FAQ 탭
│   │   ├── CategoryChips.tsx      # 자금현황 등 카테고리 칩
│   │   └── SuggestionList.tsx     # 추천 질문 목록
│   ├── popular/
│   │   ├── PopularList.tsx        # 인기질의 TOP30 목록
│   │   └── RankBadge.tsx          # 순위 뱃지 (1/2/3위)
│   ├── chat/
│   │   ├── ChatPage.tsx           # 채팅 페이지
│   │   ├── UserQuery.tsx          # 사용자 질문 표시
│   │   ├── ReportCard.tsx         # AI 응답 보고서 카드
│   │   ├── SummaryCard.tsx        # 잔고/건수 요약 카드
│   │   ├── DataTable.tsx          # 보고서 내 데이터 테이블
│   │   ├── AnalysisCard.tsx       # 분석 인사이트 카드
│   │   ├── DetailSection.tsx      # 상세조회 (날짜 필터 + 테이블)
│   │   └── ChatInput.tsx          # 하단 입력창 + 탭
│   ├── settings/
│   │   ├── SettingsPage.tsx       # 사용설정 페이지
│   │   ├── SettingsSidebar.tsx    # 설정 서브메뉴
│   │   ├── UserTable.tsx          # 사용자 등록관리 테이블
│   │   ├── MyInfo.tsx             # 내정보 페이지
│   │   └── CompanyInfo.tsx        # 회사정보 페이지
│   └── common/
│       ├── Button.tsx             # 공통 버튼 컴포넌트
│       ├── RankBadge.tsx          # 순위 뱃지
│       ├── Pagination.tsx         # 페이지네이션
│       └── Breadcrumb.tsx         # 브레드크럼
├── styles/
│   └── globals.css
└── App.tsx
```

---

## 3. 핵심 컴포넌트 구현 코드

### 3.1 AppLayout
```tsx
// src/components/layout/AppLayout.tsx
import { useState } from 'react';
import TopBar from './TopBar';
import Sidebar from './Sidebar';
import RecentPanel from './RecentPanel';

interface AppLayoutProps {
  children: React.ReactNode;
}

export default function AppLayout({ children }: AppLayoutProps) {
  const [recentOpen, setRecentOpen] = useState(false);

  return (
    <div className="flex h-screen text-[var(--text-primary)]">
      <header className="fixed top-0 left-0 right-0 h-[var(--topbar-height)] shrink-0 md:flex z-[9900] bg-white border-b border-[var(--border)]">
        <TopBar />
      </header>

      <aside className="shrink-0 z-50 flex justify-between h-screen absolute md:relative whitespace-nowrap">
        {/* 아이콘 사이드바 */}
        <div className="flex z-20 relative h-full border-r border-[var(--border)] bg-[var(--background-sidebar)]">
          <Sidebar onRecentClick={() => setRecentOpen((v) => !v)} />
        </div>
        {/* 최근 질문 슬라이드 패널 */}
        <div
          className={`relative transition-all duration-300 overflow-hidden ${
            recentOpen ? 'w-[var(--sidebar-width)]' : 'w-0'
          }`}
        >
          <RecentPanel />
        </div>
      </aside>

      <main className="w-full h-full pt-[var(--topbar-height)] overflow-auto">
        {children}
      </main>
    </div>
  );
}
```

### 3.2 TopBar
```tsx
// src/components/layout/TopBar.tsx
import { HeadphonesIcon, BellIcon } from 'lucide-react';

export default function TopBar() {
  return (
    <nav className="mx-auto flex h-full w-full items-center justify-between px-4">
      {/* 로고 */}
      <div className="flex items-center gap-2">
        <img src="/icons/cube.png" alt="logo" className="w-8 h-8" />
        <span className="text-lg font-bold text-[var(--text-primary)]">AI 하나로</span>
        <span className="text-xs text-blue-500 font-bold">+</span>
      </div>
      {/* 우측 버튼 */}
      <div className="flex items-center gap-2">
        <button className="bg-white p-2 w-10 h-10 border border-gray-300 rounded-full hover:bg-gray-50 transition-colors">
          <HeadphonesIcon className="w-5 h-5 text-gray-600" />
        </button>
        <button className="relative outline-none">
          <BellIcon className="w-5 h-5 text-[var(--text-primary)]" />
        </button>
      </div>
    </nav>
  );
}
```

### 3.3 Sidebar
```tsx
// src/components/layout/Sidebar.tsx
import { PlusIcon, ClockIcon, SettingsIcon, LogOutIcon } from 'lucide-react';

interface SidebarProps {
  onRecentClick: () => void;
}

const MENU_ITEMS = [
  { icon: PlusIcon, label: '새 채팅', onClick: () => {} },
  { icon: ClockIcon, label: '최근 질문', onClick: 'recent' },
  { icon: SettingsIcon, label: '사용 설정', onClick: () => {} },
];

export default function Sidebar({ onRecentClick }: SidebarProps) {
  return (
    <div className="flex flex-col items-center text-[var(--text-disabled)] w-[var(--sidebar-1-width)] justify-between h-full py-4">
      {/* 로고 */}
      <div className="flex justify-center z-40 items-center p-5 pb-7 shrink-0">
        <img src="/icons/logo.png" alt="logo" className="w-8 h-8" />
      </div>

      {/* 메뉴 아이템 */}
      <div className="flex flex-col gap-y-4 px-4 flex-1">
        {MENU_ITEMS.map(({ icon: Icon, label, onClick }) => (
          <div
            key={label}
            onClick={onClick === 'recent' ? onRecentClick : onClick}
            className="group relative flex items-center justify-center flex-col text-left cursor-pointer text-xs gap-1 hover:text-[var(--aicfo-purple)] transition-colors"
          >
            <Icon className="w-5 h-5" />
            <span>{label}</span>
          </div>
        ))}
      </div>

      {/* 로그아웃 */}
      <div className="flex flex-col items-center cursor-pointer text-xs gap-1 pb-4 hover:text-red-500 transition-colors">
        <LogOutIcon className="w-5 h-5" />
        <span>로그아웃</span>
      </div>
    </div>
  );
}
```

### 3.4 RecentPanel
```tsx
// src/components/layout/RecentPanel.tsx
interface RecentItem {
  id: string;
  text: string;
  date: string;
}

interface RecentPanelProps {
  items?: RecentItem[];
  onClose?: () => void;
  onClearAll?: () => void;
}

export default function RecentPanel({ items = [], onClose, onClearAll }: RecentPanelProps) {
  return (
    <div className="w-[var(--sidebar-width)] h-full bg-[var(--background-sidebar)] border-r border-[var(--border)] flex flex-col">
      {/* 헤더 */}
      <div className="flex items-center justify-between px-4 py-3">
        <span className="font-semibold text-sm text-[var(--text-primary)]">최근 질문</span>
        <button
          onClick={onClearAll}
          className="bg-white text-gray-500 border border-gray-500 text-[0.65rem] px-2 rounded-full hover:bg-gray-50"
        >
          전체 삭제
        </button>
      </div>

      {/* 날짜 구분 */}
      <div className="px-4 py-1 text-xs text-[var(--text-secondary)]">오늘</div>

      {/* 리스트 */}
      <div className="flex-1 overflow-y-auto px-2">
        {items.map((item) => (
          <div
            key={item.id}
            className="flex group relative items-center justify-between rounded-lg hover:bg-[var(--background-secondary)] cursor-pointer px-3 py-2"
          >
            <span className="text-sm text-[var(--text-primary)] truncate">{item.text}</span>
          </div>
        ))}
      </div>

      {/* 접기 버튼 */}
      <button
        onClick={onClose}
        className="absolute right-0 top-1/2 -translate-y-1/2 translate-x-full w-8 h-8 p-1 bg-white border border-gray-300 rounded-md rotate-180 hover:bg-gray-50"
      >
        ›
      </button>
    </div>
  );
}
```

### 3.5 SearchBar
```tsx
// src/components/home/SearchBar.tsx
import { useState } from 'react';
import { SearchIcon, ChevronDownIcon } from 'lucide-react';

interface SearchBarProps {
  placeholder?: string;
  onSearch: (query: string) => void;
  businesses?: string[];
}

export default function SearchBar({
  placeholder = '어떤 업무를 도와드릴까요?',
  onSearch,
  businesses = ['전체 사업장'],
}: SearchBarProps) {
  const [query, setQuery] = useState('');
  const [selectedBiz, setSelectedBiz] = useState(businesses[0]);

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    if (query.trim()) onSearch(query.trim());
  };

  return (
    <div className="flex items-center gap-3 w-full">
      {/* 사업장 드롭다운 */}
      <div className="inline-flex items-center gap-1 px-4 py-2 rounded-[100px] border border-[#EAEAEA] text-gray-700 cursor-pointer hover:text-gray-900 shrink-0 text-sm">
        <span>{selectedBiz}</span>
        <ChevronDownIcon className="w-4 h-4" />
      </div>

      {/* 검색 컨테이너 */}
      <div className="flex w-full group flex-col justify-start h-full items-start rounded-xl border border-[var(--background-secondary)] transition-colors focus-within:border-[var(--aicfo-purple)] focus-within:bg-white bg-[var(--background-secondary)]">
        <form
          onSubmit={handleSubmit}
          className="flex w-full flex-1 px-4 items-center"
        >
          <input
            type="text"
            value={query}
            onChange={(e) => setQuery(e.target.value)}
            placeholder={placeholder}
            className="w-full bg-transparent h-[var(--searchbar-height)] pl-2 text-base outline-none placeholder:text-[var(--placeholder)]"
          />
          <button type="submit" className="shrink-0 text-[var(--aicfo-purple)]">
            <SearchIcon className="w-5 h-5" />
          </button>
        </form>
      </div>
    </div>
  );
}
```

### 3.6 TabGroup
```tsx
// src/components/home/TabGroup.tsx
export type TabId = 'general' | 'popular' | 'faq';

interface Tab {
  id: TabId;
  label: string;
  iconSrc: string;
}

const TABS: Tab[] = [
  { id: 'general', label: '일반질의', iconSrc: '/icons/general.png' },
  { id: 'popular', label: '인기질의', iconSrc: '/icons/popular.png' },
  { id: 'faq', label: 'FAQ', iconSrc: '/icons/faq.png' },
];

interface TabGroupProps {
  activeTab: TabId;
  onChange: (tab: TabId) => void;
}

export default function TabGroup({ activeTab, onChange }: TabGroupProps) {
  return (
    <div className="flex items-end border-b border-[#e8edf7]">
      {TABS.map((tab) => {
        const isActive = tab.id === activeTab;
        return (
          <button
            key={tab.id}
            onClick={() => onChange(tab.id)}
            className={[
              'inline-flex items-center gap-2 px-4 py-2 border border-b-0 rounded-t-xl relative top-[1px] text-sm font-bold transition-all',
              isActive
                ? 'bg-white border-[#e8edf7] text-gray-800'
                : 'bg-[#f5f7fa] border-transparent text-gray-400 hover:text-gray-600',
            ].join(' ')}
          >
            <img src={tab.iconSrc} alt={tab.label} className="w-4 h-4" />
            {tab.label}
          </button>
        );
      })}
    </div>
  );
}
```

### 3.7 CategoryChips
```tsx
// src/components/home/CategoryChips.tsx
export interface Category {
  id: string;
  label: string;
  iconSrc: string;
}

export const CATEGORIES: Category[] = [
  { id: 'cash', label: '자금현황', iconSrc: '/icons/cash.png' },
  { id: 'deposit', label: '수시입출', iconSrc: '/icons/deposit.png' },
  { id: 'savings', label: '예적금', iconSrc: '/icons/savings.png' },
  { id: 'loan', label: '대출', iconSrc: '/icons/loan.png' },
  { id: 'forex', label: '외화', iconSrc: '/icons/forex.png' },
  { id: 'securities', label: '증권', iconSrc: '/icons/securities.png' },
  { id: 'trust', label: '신탁', iconSrc: '/icons/trust.png' },
  { id: 'card', label: '법인카드', iconSrc: '/icons/card.png' },
  { id: 'exchange', label: '환율', iconSrc: '/icons/exchange.png' },
  { id: 'tax', label: '세금계산서', iconSrc: '/icons/tax.png' },
];

interface CategoryChipsProps {
  active: string;
  onChange: (id: string) => void;
}

export default function CategoryChips({ active, onChange }: CategoryChipsProps) {
  return (
    <div className="flex items-center gap-2 flex-wrap">
      {CATEGORIES.map((cat) => {
        const isActive = cat.id === active;
        return (
          <button
            key={cat.id}
            onClick={() => onChange(cat.id)}
            className={[
              'flex items-center gap-1.5 px-3.5 py-2 rounded-[10px] border whitespace-nowrap transition-all text-base',
              isActive
                ? 'bg-[#4f63d2] border-[#4f63d2] text-white'
                : 'bg-[#f5f7fa] border-[#e8edf7] text-[var(--text-primary)] hover:bg-[#4f63d2] hover:text-white hover:border-[#4f63d2]',
            ].join(' ')}
          >
            <img src={cat.iconSrc} alt={cat.label} className="w-5 h-5" />
            {cat.label}
          </button>
        );
      })}
    </div>
  );
}
```

### 3.8 SuggestionList
```tsx
// src/components/home/SuggestionList.tsx
interface SuggestionListProps {
  items: string[];
  onSelect: (text: string) => void;
}

export default function SuggestionList({ items, onSelect }: SuggestionListProps) {
  return (
    <div className="rounded-xl border border-[var(--background-secondary)] bg-white p-4">
      <p className="text-sm text-[var(--text-secondary)] mb-3 font-medium">추천 질문</p>
      <div className="flex flex-col gap-1">
        {items.map((item, i) => (
          <button
            key={i}
            onClick={() => onSelect(item)}
            className="flex items-center gap-2 w-full px-3 py-2 rounded-lg hover:bg-gray-100 text-sm text-left whitespace-nowrap overflow-hidden transition-colors"
          >
            <img src="/icons/question.png" alt="질문 아이콘" className="w-4 h-4 shrink-0" />
            <span className="truncate text-[var(--text-primary)]">{item}</span>
          </button>
        ))}
      </div>
    </div>
  );
}
```

### 3.9 RankBadge
```tsx
// src/components/common/RankBadge.tsx
const RANK_STYLES: Record<number, string> = {
  1: 'bg-[#fef3c7] text-[#92400e]',
  2: 'bg-[#e0e7ff] text-[#3730a3]',
  3: 'bg-[#fce7f3] text-[#831843]',
};

interface RankBadgeProps {
  rank: number;
}

export default function RankBadge({ rank }: RankBadgeProps) {
  const style = RANK_STYLES[rank] ?? 'bg-gray-100 text-gray-600';
  return (
    <div className={`w-7 h-7 rounded-lg flex items-center justify-center text-sm font-bold border ${style}`}>
      {rank}
    </div>
  );
}
```
### 3.10 Button (공통 버튼)
```tsx
// src/components/common/Button.tsx
import { ButtonHTMLAttributes } from 'react';

type ButtonVariant =
  | 'category-active'
  | 'category-default'
  | 'primary-blue'
  | 'secondary'
  | 'outline-pill'
  | 'ghost-circle'
  | 'excel'
  | 'pdf';

const VARIANTS: Record<ButtonVariant, string> = {
  'category-active':
    'bg-[#4f63d2] border border-[#4f63d2] text-white rounded-[10px] px-3.5 py-2 text-base hover:bg-[#5267dd] transition-all',
  'category-default':
    'bg-[#f5f7fa] border border-[#e8edf7] text-[#0f0f0f] rounded-[10px] px-3.5 py-2 text-base hover:bg-[#4f63d2] hover:text-white hover:border-[#4f63d2] transition-all',
  'primary-blue':
    'bg-blue-500 text-white rounded-xl px-4 py-1.5 text-sm hover:bg-blue-600 transition-colors',
  'secondary':
    'bg-[#667efe] text-white font-bold text-xs rounded-lg px-4 py-1.5 hover:bg-[#5a6fe0] transition-colors',
  'outline-pill':
    'bg-white text-gray-500 border border-gray-500 text-[0.65rem] px-2 rounded-full hover:bg-gray-50 transition-colors',
  'ghost-circle':
    'bg-white p-2 w-10 h-10 border border-gray-300 rounded-full hover:bg-gray-50 transition-colors flex items-center justify-center',
  'excel':
    'flex items-center gap-1 rounded-md bg-emerald-600 px-2.5 py-1 text-[11px] font-semibold text-white hover:bg-emerald-700 transition-colors',
  'pdf':
    'flex items-center gap-1.5 px-3 py-2 text-xs font-medium rounded-lg border border-gray-200 bg-white text-gray-700 hover:bg-gray-50 transition-colors',
};

interface ButtonProps extends ButtonHTMLAttributes<HTMLButtonElement> {
  variant?: ButtonVariant;
}

export default function Button({
  variant = 'primary-blue',
  className = '',
  children,
  ...props
}: ButtonProps) {
  return (
    <button
      className={`${VARIANTS[variant]} ${className} disabled:opacity-40 disabled:cursor-not-allowed`}
      {...props}
    >
      {children}
    </button>
  );
}
```

### 3.11 Pagination
```tsx
// src/components/common/Pagination.tsx
interface PaginationProps {
  current: number;
  total: number;
  pageSize?: number;
  onChange: (page: number) => void;
}

export default function Pagination({
  current,
  total,
  pageSize = 20,
  onChange,
}: PaginationProps) {
  const totalPages = Math.ceil(total / pageSize);

  const navBtnClass =
    'flex items-center justify-center border rounded text-sm w-8 h-8 border-gray-300 bg-white hover:bg-gray-50 disabled:text-gray-300 disabled:border-gray-200 disabled:cursor-not-allowed transition-colors';

  return (
    <div className="flex items-center gap-1">
      {/* 맨 앞 */}
      <button
        className={navBtnClass}
        onClick={() => onChange(1)}
        disabled={current === 1}
      >
        «
      </button>
      {/* 이전 */}
      <button
        className={navBtnClass}
        onClick={() => onChange(current - 1)}
        disabled={current === 1}
      >
        ‹
      </button>

      {/* 페이지 번호 */}
      {Array.from({ length: totalPages }, (_, i) => i + 1).map((page) => (
        <button
          key={page}
          onClick={() => onChange(page)}
          className={`flex items-center justify-center border rounded text-sm w-8 h-8 transition-colors ${
            page === current
              ? 'bg-blue-500 text-white border-blue-500'
              : 'bg-transparent text-gray-700 border-gray-200 hover:bg-gray-50'
          }`}
        >
          {page}
        </button>
      ))}

      {/* 다음 */}
      <button
        className={navBtnClass}
        onClick={() => onChange(current + 1)}
        disabled={current === totalPages}
      >
        ›
      </button>
      {/* 맨 끝 */}
      <button
        className={navBtnClass}
        onClick={() => onChange(totalPages)}
        disabled={current === totalPages}
      >
        »
      </button>
    </div>
  );
}
```

### 3.12 Breadcrumb
```tsx
// src/components/common/Breadcrumb.tsx
interface BreadcrumbProps {
  items: { label: string; active?: boolean }[];
}

export default function Breadcrumb({ items }: BreadcrumbProps) {
  return (
    <div className="bg-[#e7e7e7] px-9 py-2">
      <div className="flex gap-4 items-center text-sm text-[var(--text-primary)]">
        {items.map((item, i) => (
          <span key={i} className="flex items-center gap-4">
            {i > 0 && <span className="text-gray-400">/</span>}
            {item.active ? (
              <span className="font-bold bg-white px-3 py-0.5 rounded-md shadow-sm">
                {item.label}
              </span>
            ) : (
              <span className="text-gray-500">{item.label}</span>
            )}
          </span>
        ))}
      </div>
    </div>
  );
}
```

### 3.13 UserQuery (채팅 사용자 질문)
```tsx
// src/components/chat/UserQuery.tsx
interface UserQueryProps {
  text: string;
  timestamp: string;
}

export default function UserQuery({ text, timestamp }: UserQueryProps) {
  return (
    <div className="flex items-center justify-between w-full mb-4">
      <span className="flex items-center gap-x-2 text-[var(--aicfo-purple)] text-base font-normal">
        "{text}"
      </span>
      <p className="text-sm font-normal text-[var(--text-tertiary)] whitespace-nowrap ml-4">
        {timestamp}
      </p>
    </div>
  );
}
```

### 3.14 ReportCard (보고서 카드)
```tsx
// src/components/chat/ReportCard.tsx
interface ReportCardProps {
  title: string;
  date: string;
  children: React.ReactNode;
  onExportPdf?: () => void;
  onExportExcel?: () => void;
}

export default function ReportCard({
  title,
  date,
  children,
  onExportPdf,
  onExportExcel,
}: ReportCardProps) {
  return (
    <div className="overflow-hidden rounded-sm border border-gray-200 bg-white shadow-2xl my-4">
      {/* 상단 파란 바 */}
      <div className="h-2 w-full bg-blue-600" />

      {/* 본문 */}
      <div className="p-10">
        {/* 헤더 */}
        <div className="flex justify-between items-start border-b-2 border-gray-200 pb-5 mb-8">
          <div>
            <h1 className="text-3xl font-extrabold tracking-tight text-gray-900">
              {title}
            </h1>
            <p className="text-gray-500 mt-2 text-sm">{date}</p>
          </div>
          {/* 내보내기 버튼 */}
          <div className="flex items-center gap-2">
            {onExportPdf && (
              <button
                onClick={onExportPdf}
                className="flex items-center gap-1.5 px-3 py-2 text-xs font-medium rounded-lg border border-gray-200 bg-white text-gray-700 hover:bg-gray-50 transition-colors"
              >
                PDF 내보내기
              </button>
            )}
            {onExportExcel && (
              <button
                onClick={onExportExcel}
                className="flex items-center gap-1 rounded-md bg-emerald-600 px-2.5 py-1 text-[11px] font-semibold text-white hover:bg-emerald-700 transition-colors"
              >
                Excel 다운로드
              </button>
            )}
          </div>
        </div>

        {/* 콘텐츠 */}
        {children}
      </div>
    </div>
  );
}
```

### 3.15 SummaryCard (잔고/건수 요약 카드)
```tsx
// src/components/chat/SummaryCard.tsx
interface SummaryCardProps {
  label: string;
  value: string;
}

export default function SummaryCard({ label, value }: SummaryCardProps) {
  return (
    <div className="overflow-hidden rounded-2xl border border-slate-200 bg-gradient-to-br from-slate-50/50 to-white px-4 py-3.5 shadow-[0_2px_8px_rgba(0,0,0,0.05)] transition-all duration-200 hover:-translate-y-0.5 hover:shadow-[0_4px_16px_rgba(0,0,0,0.08)]">
      <p className="text-xs text-slate-500 mb-1">{label}</p>
      <p className="text-xl font-bold text-[var(--text-primary)]">{value}</p>
    </div>
  );
}
```

### 3.16 DataTable (보고서 내 데이터 테이블)
```tsx
// src/components/chat/DataTable.tsx
interface Column {
  key: string;
  header: string;
  align?: 'left' | 'center' | 'right';
}

interface DataTableProps {
  columns: Column[];
  rows: Record<string, React.ReactNode>[];
  onExcelDownload?: () => void;
}

export default function DataTable({ columns, rows, onExcelDownload }: DataTableProps) {
  return (
    <div className="overflow-hidden rounded-xl border border-slate-200">
      {/* 테이블 헤더 액션 */}
      {onExcelDownload && (
        <div className="flex justify-end px-4 py-2 bg-slate-50 border-b border-slate-200">
          <button
            onClick={onExcelDownload}
            className="ml-auto flex items-center gap-1 rounded-md bg-emerald-600 px-2.5 py-1 text-[11px] font-semibold text-white hover:bg-emerald-700 transition-colors"
          >
            Excel 다운로드
          </button>
        </div>
      )}

      <div className="overflow-x-auto">
        <table className="w-full">
          {/* 컬럼 헤더 */}
          <thead>
            <tr className="border-b-2 border-slate-200 bg-gradient-to-r from-slate-50 to-slate-100/60">
              {columns.map((col) => (
                <th
                  key={col.key}
                  className={`px-4 py-3 text-xs font-semibold text-slate-600 whitespace-nowrap text-${col.align ?? 'center'}`}
                >
                  {col.header}
                </th>
              ))}
            </tr>
          </thead>

          {/* 데이터 행 */}
          <tbody>
            {rows.map((row, i) => (
              <tr
                key={i}
                className={`border-b border-slate-100 transition-colors duration-150 hover:bg-blue-50/40 ${
                  i % 2 === 0 ? 'bg-white' : 'bg-slate-50/30'
                }`}
              >
                {columns.map((col) => (
                  <td
                    key={col.key}
                    className={`whitespace-nowrap border-r border-slate-100 px-4 py-3 last:border-r-0 text-sm text-slate-800 text-${col.align ?? 'center'}`}
                  >
                    {row[col.key]}
                  </td>
                ))}
              </tr>
            ))}
          </tbody>
        </table>
      </div>
    </div>
  );
}
```

### 3.17 AnalysisCard (분석 인사이트 카드)
```tsx
// src/components/chat/AnalysisCard.tsx
type AnalysisVariant = 'important' | 'secondary';

interface AnalysisCardProps {
  text: string;
  variant?: AnalysisVariant;
  icon?: React.ReactNode;
}

const VARIANT_STYLES: Record<AnalysisVariant, string> = {
  important:
    'border-l-emerald-400 bg-gradient-to-r from-emerald-50/80 to-transparent',
  secondary:
    'border-l-slate-300 bg-gradient-to-r from-slate-50/80 to-transparent',
};

export default function AnalysisCard({
  text,
  variant = 'important',
  icon,
}: AnalysisCardProps) {
  return (
    <div
      className={`flex items-start gap-3 rounded-2xl border-l-[3px] px-4 py-3.5 shadow-[0_1px_4px_rgba(0,0,0,0.04)] ${VARIANT_STYLES[variant]}`}
    >
      {icon && <span className="shrink-0 mt-0.5">{icon}</span>}
      <p className="text-sm text-[var(--text-primary)] leading-relaxed">{text}</p>
    </div>
  );
}
```

### 3.18 DetailSection (상세조회 카드)
```tsx
// src/components/chat/DetailSection.tsx
import { useState } from 'react';
import DataTable from './DataTable';

interface DetailSectionProps {
  totalCount: number;
  columns: { key: string; header: string; align?: 'left' | 'center' | 'right' }[];
  rows: Record<string, React.ReactNode>[];
  onSearch?: (startDate: string, endDate: string) => void;
}

export default function DetailSection({
  totalCount,
  columns,
  rows,
  onSearch,
}: DetailSectionProps) {
  const [startDate, setStartDate] = useState('');
  const [endDate, setEndDate] = useState('');
  const [pageSize, setPageSize] = useState(20);

  return (
    <div className="mt-4 rounded-xl border border-slate-200 bg-white overflow-hidden">
      {/* 헤더 */}
      <div className="px-4 py-3 flex items-center gap-2 border-b border-slate-100">
        <svg className="w-4 h-4 text-blue-500" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2" />
        </svg>
        <h4 className="text-base font-bold text-slate-700">상세조회</h4>
        <span className="text-sm text-slate-400">(조회 총건수: {totalCount}건)</span>
      </div>

      {/* 날짜 필터 */}
      <div className="px-4 py-3 flex items-center gap-3 flex-wrap border-b border-slate-100 bg-slate-50/50">
        <div className="flex items-center gap-2 text-xs text-slate-600">
          <span>시작일</span>
          <input
            type="date"
            value={startDate}
            onChange={(e) => setStartDate(e.target.value)}
            className="rounded-lg border border-slate-200 bg-slate-50 px-3 py-2 text-xs outline-none focus:border-blue-400"
          />
        </div>
        <div className="flex items-center gap-2 text-xs text-slate-600">
          <span>종료일</span>
          <input
            type="date"
            value={endDate}
            onChange={(e) => setEndDate(e.target.value)}
            className="rounded-lg border border-slate-200 bg-slate-50 px-3 py-2 text-xs outline-none focus:border-blue-400"
          />
        </div>
        <button
          onClick={() => onSearch?.(startDate, endDate)}
          disabled={!startDate || !endDate}
          className="flex items-center gap-1.5 rounded-lg bg-blue-600 px-4 py-2 text-xs font-semibold text-white hover:bg-blue-700 disabled:bg-slate-300 transition-colors"
        >
          기간 내역 조회
        </button>
      </div>

      {/* 테이블 상단 컨트롤 */}
      <div className="px-4 py-2 flex items-center justify-between border-b border-slate-100">
        <span className="text-xs text-slate-500">조회데이터 전체 {totalCount}건</span>
        <select
          value={pageSize}
          onChange={(e) => setPageSize(Number(e.target.value))}
          className="rounded border border-slate-200 bg-white px-2 py-1 text-xs outline-none focus:border-blue-400 text-slate-600"
        >
          {[10, 20, 50, 100].map((n) => (
            <option key={n} value={n}>{n}</option>
          ))}
        </select>
      </div>

      {/* 데이터 테이블 */}
      <DataTable columns={columns} rows={rows} />
    </div>
  );
}
```

### 3.19 ChatInput (하단 입력창)
```tsx
// src/components/chat/ChatInput.tsx
import { useState } from 'react';
import { SearchIcon, ChevronDownIcon } from 'lucide-react';

type BottomTabId = 'general' | 'faq';

interface ChatInputProps {
  onSend: (query: string) => void;
  businesses?: string[];
}

export default function ChatInput({ onSend, businesses = ['전체 사업장'] }: ChatInputProps) {
  const [query, setQuery] = useState('');
  const [activeTab, setActiveTab] = useState<BottomTabId>('general');
  const [selectedBiz] = useState(businesses[0]);

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    if (query.trim()) {
      onSend(query.trim());
      setQuery('');
    }
  };

  return (
    <div className="border-t border-[var(--border)] bg-white px-4 py-3">
      {/* 하단 탭 */}
      <div className="flex items-center gap-2 mb-2">
        {(['general', 'faq'] as BottomTabId[]).map((tab) => {
          const isActive = tab === activeTab;
          const label = tab === 'general' ? '일반질의' : 'FAQ';
          const iconSrc = tab === 'general' ? '/icons/general.png' : '/icons/faq.png';
          return (
            <button
              key={tab}
              onClick={() => setActiveTab(tab)}
              className={[
                'inline-flex items-center gap-2 px-4 py-2 rounded-full text-sm font-bold transition-all border',
                isActive
                  ? 'bg-white text-gray-800 border-gray-400'
                  : 'bg-[#f5f7fa] border-[#e8edf7] text-gray-400 hover:text-gray-600',
              ].join(' ')}
            >
              <img src={iconSrc} alt={label} className="w-4 h-4" />
              {label}
            </button>
          );
        })}
      </div>

      {/* 입력창 */}
      <div className="flex items-center gap-3">
        {/* 사업장 드롭다운 */}
        <div className="inline-flex items-center gap-1 px-4 py-2 rounded-[100px] border border-[#EAEAEA] text-gray-700 cursor-pointer hover:text-gray-900 shrink-0 text-sm">
          <span>{selectedBiz}</span>
          <ChevronDownIcon className="w-4 h-4" />
        </div>

        {/* 텍스트 인풋 */}
        <div className="flex w-full group flex-col justify-start items-start rounded-xl border border-[var(--background-secondary)] transition-colors focus-within:border-[var(--aicfo-purple)] focus-within:bg-white bg-[var(--background-secondary)]">
          <form onSubmit={handleSubmit} className="flex w-full flex-1 px-4 items-center">
            <input
              type="text"
              value={query}
              onChange={(e) => setQuery(e.target.value)}
              placeholder="어떤 업무를 도와드릴까요?"
              className="w-full bg-transparent h-[var(--searchbar-height)] pl-2 text-base outline-none placeholder:text-[var(--placeholder)]"
            />
            <button type="submit" className="shrink-0 text-[var(--aicfo-purple)]">
              <SearchIcon className="w-5 h-5" />
            </button>
          </form>
        </div>
      </div>
    </div>
  );
}
```

### 3.20 SettingsSidebar
```tsx
// src/components/settings/SettingsSidebar.tsx
type SettingsMenu = 'users' | 'myinfo' | 'company';

interface SettingsSidebarProps {
  active: SettingsMenu;
  onChange: (menu: SettingsMenu) => void;
  userName?: string;
  companyName?: string;
}

const MENUS: { id: SettingsMenu; label: string }[] = [
  { id: 'users', label: '사용자 등록관리' },
  { id: 'myinfo', label: '내정보' },
  { id: 'company', label: '회사정보' },
];

export default function SettingsSidebar({
  active,
  onChange,
  userName = '',
  companyName = '',
}: SettingsSidebarProps) {
  return (
    <div className="w-[260px] h-full bg-[var(--background-sidebar)] border-r border-[var(--border)] flex flex-col py-4 px-3">
      {/* 사용자 정보 */}
      <div className="px-3 pb-4 border-b border-[var(--border)] mb-3">
        <p className="font-bold text-[var(--text-primary)]">{userName}</p>
        <p className="text-sm text-[var(--text-secondary)]">{companyName}</p>
      </div>

      {/* 메뉴 그룹 */}
      <div className="w-full rounded-lg py-2 bg-[var(--background-sidebar)]">
        <div className="flex items-center justify-between px-4 py-1">
          <span className="text-sm font-semibold flex items-center gap-2">
            ⚙️ 사용설정
          </span>
        </div>

        {MENUS.map((menu) => {
          const isActive = menu.id === active;
          return (
            <div
              key={menu.id}
              onClick={() => onChange(menu.id)}
              className={[
                'flex items-center justify-between p-2 rounded-lg cursor-pointer transition-colors mx-2',
                isActive ? 'text-blue-700 font-medium' : 'text-[var(--text-primary)] hover:bg-[var(--background-secondary)]',
              ].join(' ')}
            >
              <div className="flex items-center gap-2">
                {isActive && (
                  <span className="w-1.5 h-1.5 rounded-full bg-red-500 shrink-0" />
                )}
                <span className={`text-sm ${isActive ? 'font-semibold' : ''}`}>
                  {menu.label}
                </span>
              </div>
            </div>
          );
        })}
      </div>
    </div>
  );
}
```

### 3.21 UserTable (사용자 목록 테이블 — Ant Design)
```tsx
// src/components/settings/UserTable.tsx
import { Table, Input, Button } from 'antd';
import type { ColumnsType } from 'antd/es/table';
import { SearchOutlined } from '@ant-design/icons';

interface UserRecord {
  key: string;
  type: string;
  name: string;
  id: string;
  branchId: string;
  phone: string;
  status: string;
}

const columns: ColumnsType<UserRecord> = [
  { title: '유형', dataIndex: 'type', key: 'type', align: 'center', sorter: true },
  { title: '사용자명', dataIndex: 'name', key: 'name', ellipsis: true, sorter: true },
  { title: '아이디', dataIndex: 'id', key: 'id', align: 'center' },
  { title: '브랜치사용자 아이디', dataIndex: 'branchId', key: 'branchId', align: 'center' },
  { title: '전화번호', dataIndex: 'phone', key: 'phone', align: 'center' },
  { title: '상태', dataIndex: 'status', key: 'status', align: 'center' },
];

interface UserTableProps {
  data: UserRecord[];
  total: number;
  pageSize?: number;
  onSearch?: (value: string) => void;
}

export default function UserTable({ data, total, pageSize = 20, onSearch }: UserTableProps) {
  return (
    <div className="flex flex-col gap-3">
      {/* 검색창 */}
      <div className="flex items-center gap-2">
        <Input
          placeholder="검색어를 입력하세요"
          suffix={<SearchOutlined className="text-gray-400" />}
          style={{ width: 280, borderRadius: 6 }}
          onPressEnter={(e) => onSearch?.((e.target as HTMLInputElement).value)}
        />
      </div>

      {/* 테이블 */}
      <Table
        columns={columns}
        dataSource={data}
        pagination={{ pageSize, total, showSizeChanger: false }}
        size="small"
        style={{ fontSize: 14 }}
        /* 헤더 배경은 globals.css에서 ant-table-thead th 오버라이드 */
      />
    </div>
  );
}
```

**Ant Design 테이블 헤더 색상 오버라이드 (globals.css 추가):**
```css
/* Ant Design 테이블 헤더 커스텀 */
.ant-table-thead > tr > th {
  background-color: #4e6783 !important;
  color: #ffffff !important;
  font-weight: 700 !important;
  font-size: 11.9px !important;
}

.ant-table-thead > tr > th.ant-table-column-has-sorters:hover {
  background-color: #3d5269 !important;
}
```

---

## 4. 페이지 조합 예시

### 4.1 HomePage 조합
```tsx
// src/components/home/HomePage.tsx
import { useState } from 'react';
import SearchBar from './SearchBar';
import TabGroup, { TabId } from './TabGroup';
import CategoryChips from './CategoryChips';
import SuggestionList from './SuggestionList';
import PopularList from '../popular/PopularList';

const SUGGESTIONS: Record<string, string[]> = {
  cash: ['자금현황', '자금 변동 현황', '월간 자금 흐름', '가용 자금'],
  deposit: ['수시입출계좌 잔액은?', '이번 달 수시입출 거래내역', '농협은행 잔액은?'],
  savings: ['예적금 현황', '만기 예정 계좌 목록'],
  loan: ['대출 잔액 얼마 있어?', '기업대출상담시 제출서류 알려줘'],
  forex: ['오늘 환율 알려줘', '보유 중인 외화 총액'],
  securities: ['증권계좌 잔액은?', '2026년 증권계좌 거래내역 보고서 만들어줘'],
  trust: ['신탁 현황 알려줘'],
  card: ['오늘 카드 얼마나 썼어?', '최근 1개월 법인카드 승인 내역'],
  exchange: ['달러 환율 알려줘', '3월 환율 알려줘'],
  tax: ['공급가액 큰 순서로 세금계산서 보여줘', '최근 받은 세금계산서 내역'],
};

export default function HomePage({ onSearch }: { onSearch: (q: string) => void }) {
  const [activeTab, setActiveTab] = useState<TabId>('general');
  const [activeCategory, setActiveCategory] = useState('cash');

  return (
    <div className="flex flex-col items-center justify-start pt-16 px-8 gap-6 max-w-4xl mx-auto w-full">
      {/* 타이틀 */}
      <div className="flex items-center gap-2">
        <h2 className="text-xl font-bold text-[var(--text-primary)]">
          어떤 업무를 도와드릴까요?
        </h2>
        <span>🦙</span>
      </div>

      {/* 검색창 */}
      <SearchBar onSearch={onSearch} />

      {/* 탭 + 콘텐츠 */}
      <div className="w-full">
        <TabGroup activeTab={activeTab} onChange={setActiveTab} />

        <div className="border border-[#e8edf7] rounded-b-xl rounded-tr-xl p-4 bg-white">
          {activeTab === 'general' && (
            <>
              <CategoryChips active={activeCategory} onChange={setActiveCategory} />
              <div className="mt-4">
                <SuggestionList
                  items={SUGGESTIONS[activeCategory] ?? []}
                  onSelect={onSearch}
                />
              </div>
            </>
          )}
          {activeTab === 'popular' && <PopularList onSelect={onSearch} />}
          {activeTab === 'faq' && (
            <SuggestionList
              items={['계좌 등록 방법을 알려주세요.', '인증서 관리는 어떻게 하나요?', '거래내역 조회 방법은?', 'Agent 설치 방법은?']}
              onSelect={onSearch}
            />
          )}
        </div>
      </div>
    </div>
  );
}
```

### 4.2 SettingsPage 조합
```tsx
// src/components/settings/SettingsPage.tsx
import { useState } from 'react';
import SettingsSidebar from './SettingsSidebar';
import Breadcrumb from '../common/Breadcrumb';
import UserTable from './UserTable';
import MyInfo from './MyInfo';
import CompanyInfo from './CompanyInfo';

type SettingsMenu = 'users' | 'myinfo' | 'company';

const BREADCRUMB_LABELS: Record<SettingsMenu, string> = {
  users: '사용자 등록관리',
  myinfo: '내정보',
  company: '회사정보',
};

export default function SettingsPage() {
  const [activeMenu, setActiveMenu] = useState<SettingsMenu>('users');

  return (
    <div className="flex h-full">
      {/* 서브 사이드메뉴 */}
      <SettingsSidebar
        active={activeMenu}
        onChange={setActiveMenu}
        userName="정병옥님"
        companyName="씨앤에스"
      />

      {/* 콘텐츠 영역 */}
      <div className="flex-1 flex flex-col overflow-hidden">
        {/* 브레드크럼 */}
        <Breadcrumb
          items={[
            { label: '사용설정' },
            { label: BREADCRUMB_LABELS[activeMenu], active: true },
          ]}
        />

        {/* 페이지 본문 */}
        <div className="flex-1 overflow-auto p-6">
          {activeMenu === 'users' && <UserTable data={[]} total={0} />}
          {activeMenu === 'myinfo' && <MyInfo />}
          {activeMenu === 'company' && <CompanyInfo />}
        </div>
      </div>
    </div>
  );
}
```

---

## 5. 개발 시 주요 주의사항

1. **CSS 변수 우선 사용** — 하드코딩 대신 `var(--aicfo-purple)` 등 토큰 사용
2. **Tailwind + CSS Variables 혼용** — Tailwind arbitrary value `bg-[var(--aicfo-purple)]` 방식으로 연결
3. **Ant Design 테이블 헤더** — globals.css에서 `!important`로 색상 오버라이드 필요
4. **SearchBar focus 상태** — `focus-within:border-aicfo focus-within:bg-background-primary` Tailwind 클래스로 처리
5. **Pretendard 폰트** — CDN 또는 npm(`@fontsource/pretendard`) 방식으로 로드 후 CSS 변수에 등록
6. **카테고리 칩 hover** — `hover:bg-[#4f63d2] hover:text-white hover:border-[#4f63d2]` 세트로 함께 적용
7. **RecentPanel 토글** — `width: 0 ↔ var(--sidebar-width)` transition으로 슬라이드 구현
8. **보고서 카드** — `shadow-2xl` + `rounded-sm(2px)` 조합이 실제 디자인과 동일
9. **Summary Card hover** — `hover:-translate-y-0.5` + `hover:shadow-[0_4px_16px_rgba(0,0,0,0.08)]` 함께 사용
10. **페이지네이션** — 현재 페이지: `bg-blue-500 text-white border-blue-500` / 비활성: `transparent + border-gray-200`