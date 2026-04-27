# BranchQ Master Prompt v2

## 프로젝트 목표
BranchQ는 금융 업무용 AI 질의응답, 보고서, 이상거래 탐지, 상담 지원 시스템이다.

## 절대 규칙
- state 먼저 정의
- block contract 먼저 정의
- layout contract 유지
- 기존 구조 임의 변경 금지
- visual보다 interaction 우선

## AppState
```ts
interface AppState {
  selectedCategory: Category;
  selectedModel: Model;
  subPanelOpen: 'recent' | 'report' | 'settings' | null;
  modal: ModalType | null;
  messages: Message[];
  loading: boolean;
  toast: ToastState | null;
}
```

## Message Contract
```ts
type Message = UserMessage | AIMessage;
```

## AIMessage
```ts
interface AIMessage {
  role:'ai';
  text?:string;
  blocks: Block[];
  btnType:'report'|'report-save'|'anomaly'|'consult';
}
```

## Block 14종 유지
- report-header
- number-stat
- summary-cards
- data-table
- bar-chart
- line-chart
- alert-box
- callout
- pattern-analysis
- steps
- key-value
- query-detail
- source-box
- related-questions

## Modal Contract
- model-select
- api-setting
- share
- kakao-preview
- email

## QA
- DOM 존재 확인
- optional chaining
- node --check
- 클릭 플로우 5개
- console error 0
