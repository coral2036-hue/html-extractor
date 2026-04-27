# BranchQ Master Prompt

## 목적
BranchQ는 금융 업무용 AI 질의응답 + 보고서 시스템이다.

## 최상위 규칙
- state 먼저 정의
- block contract 먼저 정의
- layout contract 유지
- 구조 임의 변경 금지

## 핵심 State
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

## Block Contract
```ts
type Block =
 | NumberStatBlock
 | DataTableBlock
 | BarChartBlock
 | AlertBlock;
```

## QA
- DOM 존재 확인
- optional chaining
- node --check
- 핵심 클릭 5개 점검
