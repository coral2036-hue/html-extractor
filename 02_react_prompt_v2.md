# BranchQ React Prompt v2

## Folder
/components
/layout
/chat
/blocks
/modals
/builder

## Core Components
- Sidebar
- Header
- InputBar
- ChatArea
- ShareRow
- BlockRenderer

## Rendering Order
AIText -> Blocks -> ShareRow

## Hooks
- useChatState
- useModalState
- useSubPanelState

## BlockRenderer
switch(block.type) exhaustive

## ShareRow Handler
- onPdfDownload
- onExcelDownload
- openShareModal
- reportIssue

## Modal Rule
- ESC close
- outside click close
- focus return
