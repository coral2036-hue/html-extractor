# BranchQ Master Prompt (실전 개발용)

# 0. 프로젝트 목표

BranchQ는 **금융 업무용 AI 질의응답 + 보고서 시스템**이다.

핵심 목표:

- 금융 데이터 정확성 유지
- 블록 기반 AI 응답 렌더링
- 보고서/이상거래/상담/예측 공통 UI 일관성 유지
- 모든 버튼 실제 동작
- 데모가 아니라 실제 제품 흐름처럼 동작

---

# 1. 기술 스택

```ts
React 18
TypeScript
Chart.js 4
XLSX.js
CSS Variables
Noto Sans KR + Inter
```

---

# 2. 디자인 토큰

```css
:root {
  --primary:#1E293B;
  --accent:#2563EB;
  --success:#16A34A;
  --warning:#CA8A04;
  --error:#DC2626;

  --gray-50:#F9FAFB;
  --gray-100:#F3F4F6;
  --gray-200:#E5E7EB;
  --gray-500:#6B7280;
  --gray-700:#374151;

  --radius:4px;
  --radius-lg:6px;
  --radius-xl:8px;
}
```

---

# 3. 기본 레이아웃

```tsx
<AppLayout>
  <Sidebar width={70} />
  <MainArea>
    <Header height={56} />
    <Content />
    <InputBar />
  </MainArea>
</AppLayout>
```

## Sidebar

- 💬 새 채팅
- 🕐 최근 질문
- 📋 맞춤보고서 (Beta)
- ⚙️ 사용설정
- 🚪 로그아웃

## Sidebar 규칙

```ts
active = accent bg
subpanel = exclusive open
outside click close
```

---

# 4. React State 정의

```ts
interface AppState {
  selectedCategory: Category;
  selectedModel: Model;
  subPanelOpen: 'recent' | 'report' | 'settings' | null;
  modal: ModalType | null;
  messages: Message[];
  loading: boolean;
}
```

## Message 구조

```ts
type Message =
 | UserMessage
 | AIMessage;
```

```ts
interface UserMessage {
  role: 'user';
  text: string;
}
```

```ts
interface AIMessage {
  role: 'ai';
  text?: string;
  blocks: Block[];
  btnType: 'report' | 'report-save' | 'anomaly' | 'consult';
}
```

---

# 5. Block JSON Contract

## NumberStat

```ts
interface NumberStatBlock {
  type:'number-stat';
  items:{
    label:string;
    value:string;
    diff?:string;
    color?:string;
  }[];
}
```

## DataTable

```ts
interface DataTableBlock {
  type:'data-table';
  columns:string[];
  rows:any[];
}
```

## BarChart

```ts
interface BarChartBlock {
  type:'bar-chart';
  labels:string[];
  values:number[];
}
```

## Alert

```ts
interface AlertBlock {
  type:'alert-box';
  level:'warning'|'error'|'info'|'success';
  title:string;
  message:string;
}
```

## 전체 Block Union

```ts
type Block =
 | NumberStatBlock
 | DataTableBlock
 | BarChartBlock
 | AlertBlock
 | CalloutBlock
 | StepsBlock
 | QueryDetailBlock;
```

---

# 6. BlockRenderer 규칙

```tsx
<BlockRenderer block={block} />
```

```ts
switch(block.type)
```

default 금지, exhaustive 처리.

---

# 7. Chat 렌더링 규칙

## User Bubble

```ts
right align
bg accent
radius 20
```

## AI Bubble

```ts
left align
white
border gray200
radius 8
```

## 내부 순서

```tsx
AIText
Blocks
ShareRow
```

---

# 8. ShareRow 핸들러 계약

```ts
onPdfDownload(messageId)
onExcelDownload(messageId)
openShareModal(messageId)
reportIssue(messageId)
```

## 버튼 순서

```ts
보고서저장
PDF
Excel
공유하기
신고하기
```

---

# 9. Modal 계약

```ts
type ModalType =
 | 'model-select'
 | 'api-setting'
 | 'share'
 | 'kakao-preview'
 | 'email';
```

## 공통 규칙

```ts
ESC close
outside click close
focus return
```

## DOM 안전 처리

```ts
DOM 존재 확인 후 bind
optional chaining
DOMContentLoaded 이후 mount
```

---

# 10. Chart 규칙

```ts
height = value/max * maxHeight
```

```ts
bar > 30px 내부 흰색
bar <= 30px 상단 외부
```

---

# 11. MVP 우선순위

## Phase 1

```ts
S0
S1
M1
ShareRow
```

## Phase 2

```ts
S4 이상거래
S6 보고서
```

## Phase 3

```ts
Builder
Email/Kakao
API Setting
```

---

# 12. QA Checklist

## 체크 1

```bash
node --check
```

## 체크 2

- DOM 존재 확인

## 체크 3

- 이벤트 바인딩 null 여부 확인

## 체크 4

핵심 클릭 플로우:

```ts
새채팅
모델선택
공유하기
보고서저장
모달닫기
```

## 체크 5

- 콘솔 오류 0개

---

# 13. AI 코딩 지시 앞 문장

```txt
기존 구조를 임의 변경하지 말고, state / block contract / layout contract를 우선 지킨 뒤 구현하세요.
```
