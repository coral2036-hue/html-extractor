# BranchQ React Prompt

## 폴더 구조
```txt
/components
/layout
/chat
/blocks
/modals
```

## 렌더링
```tsx
<BlockRenderer block={block} />
```

## Switch Rule
```ts
switch(block.type)
```

## ShareRow Handler
```ts
onPdfDownload(messageId)
onExcelDownload(messageId)
openShareModal(messageId)
reportIssue(messageId)
```
