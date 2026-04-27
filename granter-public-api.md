# granter API

그랜터 Public Docs API는 자산 연동 현황, 거래·증빙 데이터, 분류 기준, 잔액 흐름을 외부 시스템과 AI가 함께 이해할 수 있도록 구조화해 제공하는 읽기 전용 연동 문서입니다. 발급된 API key와 Basic 인증만으로 해당 워크스페이스 범위의 데이터를 안전하게 조회하고, 자체 서비스, 분석 파이프라인, AI 자동화 워크플로우에 바로 연결할 수 있습니다.

## 문서 이해를 위한 빠른 가이드

### 1) 이 문서가 다루는 범위

- `assets`: 카드, 계좌, 홈택스, 스토어처럼 거래가 들어오는 자산·연동 대상을 조회합니다.
- `tickets`: 카드 승인, 계좌 입출금, 세금계산서, 현금영수증, 결재 등 실제 업무에서 발생한 거래·증빙 이벤트를 조회합니다.
- `workflows`: 전자결재 문서, 결재선, 승인 상태처럼 결재 프로세스 자체를 조회합니다.
- `salary-histories`: 직원별 급여 내역, 지급 상태, 공제/수당 상세처럼 급여 도메인 데이터를 조회합니다.
- `inventory-products`, `inventories`: 품목 마스터와 현재 재고 수량을 분리해서 조회합니다.
- `balances`: 계좌별 잔액 흐름을 시계열로 조회해 자금 추이와 현금 상태를 파악할 수 있습니다.
- `tags`, `tag-details`, `categories`, `people`, `teams`, `contacts`: 원시 거래를 사람이 이해할 수 있는 회계·운영 언어로 바꾸는 기준 데이터를 조회합니다.

### 2) 추천 연동 순서

1. 발급된 live key 또는 test key를 준비하고 Basic 인증을 연결합니다.
2. `people`, `teams`, `contacts`, `categories`, `tags`, `tag-details`를 먼저 동기화해 분류 기준과 담당자 기준을 맞춥니다.
3. 전자결재 문서와 결재선 상태가 필요하면 `workflows`를 함께 수집합니다.
4. 급여 분석이나 인건비 자동화가 필요하면 `salary-histories`를 별도 도메인으로 함께 수집합니다.
5. 재고를 운영한다면 `inventory-products`와 `inventories`를 함께 수집해 품목 마스터와 수량 현황을 분리 적재합니다.
6. `assets`로 어떤 자산에서 데이터가 들어오는지 파악합니다.
7. `tickets`를 수집해 실제 거래·증빙 원천 데이터를 적재합니다.
8. 자금 흐름까지 보려면 `balances`를 별도 주기로 함께 수집합니다.

### 3) 설계 원칙과 실무 규칙

- 모든 조회는 API key가 속한 현재 워크스페이스 범위 안에서만 수행됩니다.
- Public Docs API는 API key 기준 분당 60회까지 호출할 수 있습니다.
- 응답은 원본 구조를 최대한 보존하므로, nullable/optional 필드는 자산 종류와 데이터 출처에 따라 비어 있을 수 있습니다.
- AI와 사람이 모두 이해하기 쉬운 데이터를 만들려면 `tickets`만 저장하지 말고 `workflows`, `salary-histories`, `inventory-products`, `inventories`, `categories`, `tags`, `people`, `teams`, `contacts` 같은 기준 정보도 함께 적재하는 편이 좋습니다.
- 날짜 입력은 `YYYY-MM-DD`, 응답 시각은 주로 ISO-8601 문자열 형식을 사용합니다.
- 금액은 단순 부호보다 `transactionType`, 티켓 타입, endpoint 의미를 함께 보고 해석하는 것이 안전합니다.

## API 활용 팁

### 1) AI에 문서를 전달할 때

- 우측 상단의 `.md 다운로드`로 문서를 내려받아 AI 프롬프트나 지식 파일에 그대로 넣는 방식을 권장합니다.
- Markdown 파일은 endpoint, 요청 필드, 응답 예시가 구조화되어 있어 PDF나 스크린샷보다 AI가 더 안정적으로 읽고 참조할 수 있습니다.
- AI에게 요청할 때는 이 파일 전체를 기준 문서로 주고, 필요한 endpoint와 예시 body를 함께 만들도록 지시하면 가장 안정적입니다.

### 2) Rate limit 대응

- Public Docs API는 API key 기준 분당 60회까지 호출할 수 있습니다.
- 여러 endpoint를 한 번에 수집할 때는 동시 요청 수를 낮추고, `429 Too Many Requests`가 오면 잠시 대기한 뒤 재시도하세요.
- 초기 백필이나 대량 적재는 1분 단위 배치로 나누어 호출하는 편이 안전합니다.

### 3) 조회 기간이 31일을 넘는 경우

- 기간 기반 조회는 요청 1회당 최대 31일까지 사용할 수 있습니다.
- 더 긴 기간이 필요하면 `startDate`, `endDate`를 31일 이하 구간으로 나눠 순차 조회한 뒤 결과를 합치세요.
- 합칠 때는 응답의 `id`를 기준으로 중복을 제거하고, 적재 테이블에서는 보통 `workspaceId + id` 조합을 키로 둡니다.

```json
[
  { "startDate": "2026-01-01", "endDate": "2026-01-31" },
  { "startDate": "2026-02-01", "endDate": "2026-02-28" },
  { "startDate": "2026-03-01", "endDate": "2026-03-31" }
]
```

## 그랜터 데이터 구조와 활용 관점

### 1) Workspace

Workspace는 하나의 회사 또는 운영 단위를 뜻하는 가장 큰 데이터 경계입니다. API key는 특정 `workspaceId`와 연결되며, 같은 회사 안의 자산, 거래, 태그, 계정과목, 구성원 정보가 모두 이 범위 안에서 조회됩니다. 외부 시스템 입장에서는 "어느 회사의 재무 데이터를 읽고 있는가"를 결정하는 기준이라고 보면 됩니다.

### 2) Asset (자산)

Asset은 계좌, 카드, 홈택스, 마켓 스토어처럼 거래가 수집되는 연결 대상입니다. 단순 목록이 아니라, 어떤 채널에서 어떤 데이터가 들어오는지 보여주는 출처 정보에 가깝습니다. 자산 유형(`assetType`), 활성 상태(`isActive`), 기관명, 계좌번호/카드번호 같은 식별 정보를 보면 이후 Ticket과 Balance를 어떤 맥락에서 해석해야 하는지 알 수 있습니다.

```json
{
  "id": 101,
  "assetType": "CARD",
  "workspaceId": 123,
  "organizationName": "신한카드",
  "isActive": true
}
```

### 3) Ticket (거래 티켓)

Ticket은 실제 업무에서 발생한 거래·증빙 단위 데이터를 의미합니다. 카드 승인 1건, 계좌 입출금 1건, 세금계산서 1건, 현금영수증 1건처럼 "한 번의 재무 이벤트"를 표현한다고 보면 됩니다. 금액(`amount`), 방향(`transactionType`: IN/OUT), 발생일(`transactAt`), 내용(`content`), 분류 상태(`isIncluded`) 등을 가지며, 장부 정리, AI 분류, 분석 리포트, 업무 자동화의 공통 원천 데이터가 됩니다.

```json
{
  "id": 98765,
  "ticketType": "EXPENSE_TICKET",
  "workspaceId": 123,
  "amount": 12000,
  "transactionType": "OUT",
  "transactAt": "2026-02-23T10:15:00.000Z",
  "content": "점심 식대",
  "isIncluded": true
}
```

### 4) Workflow (전자결재 문서)

`workflows`는 기안 문서, 결재 상태, 결재선, 검토자 정보를 담는 전자결재 데이터입니다. `tickets`가 정산·증빙 중심의 재무 이벤트라면, `workflows`는 누가 어떤 안건을 올렸고 현재 몇 차 결재까지 진행됐는지를 보여주는 운영 문서에 가깝습니다. 비용 승인 플로우, 결재 병목 분석, 결재 대기 모니터링, 내부 승인 이력 적재에 적합합니다.

### 5) Salary History (급여 내역)

`salary-histories`는 급여 지급일, 총급여, 실지급액, 보험료, 세액, 수당·공제 상세를 담는 독립 도메인 데이터입니다. 급여는 티켓 도메인과 별도로 관리되므로, 인건비 분석, 급여 지급 상태 추적, 급여명세 자동화, 노무/세무 협업용 적재가 필요할 때는 `tickets` 대신 `salary-histories`를 직접 읽는 편이 맞습니다.

### 6) Inventory Product / Inventory (품목 / 재고)

`inventory-products`는 품목명, 단가, 통화, 추가 속성 같은 품목 마스터를 조회하는 API이고, `inventories`는 해당 품목의 현재 수량, 메시지 수, 최근 수정 시각 같은 재고 현황을 조회하는 API입니다. 하나는 "무엇을 관리하는가", 다른 하나는 "현재 얼마나 남아 있는가"에 초점이 있으므로 같이 적재하는 편이 자연스럽습니다.

### 7) Reference Data (분류/담당자 기준 정보)

`categories`, `tags`, `tag-details`, `people`, `teams`, `contacts`는 원시 거래를 사람이 읽을 수 있는 회계·운영 언어로 바꾸는 기준 데이터입니다. 예를 들어 "이 거래가 어떤 비용인지", "어떤 프로젝트/조직/담당자/팀/거래처와 연결되는지"를 설명할 때 사용합니다. 티켓 응답만으로도 일부 정보를 볼 수 있지만, AI 분석이나 외부 리포트까지 고려한다면 이 기준 데이터를 별도로 동기화해 두는 편이 훨씬 안정적입니다.

### 8) Balance (잔액 시계열)

`balances`는 특정 기간의 계좌 잔액 변화를 보여주는 시계열 데이터입니다. `tickets`가 "무슨 거래가 있었는가"를 설명한다면, `balances`는 "그 결과로 계좌에 얼마가 남아 있는가"를 보여줍니다. 자금 추이, 일별 잔액 리포트, 유동성 모니터링, 현금 흐름 대시보드에 적합한 데이터입니다.

### 9) 관계와 조회 관점

- Workspace 1개 아래에 여러 Asset이 존재합니다.
- Workspace 1개 아래에 여러 Ticket이 존재합니다.
- Workspace 1개 아래에 여러 Workflow(전자결재 문서)가 존재합니다.
- Workspace 1개 아래에 여러 Salary History(급여 내역)가 존재합니다.
- Workspace 1개 아래에 여러 Inventory Product(품목)와 Inventory(재고 엔트리)가 존재합니다.
- Ticket은 특정 Asset에서 수집된 거래나 증빙 이벤트를 표현합니다.
- `workflows` API는 결재 문서, 결재선, 승인 진행 상태를 읽을 때 사용합니다.
- `salary-histories` API는 직원별 급여 내역, 지급 상태, 공제/수당 구성을 읽을 때 사용합니다.
- `inventory-products`는 품목 마스터를, `inventories`는 현재 수량과 재고 상태를 읽을 때 사용합니다.
- `assets` API는 데이터 출처와 연동 상태를 이해할 때, `tickets` API는 실제 거래 원천 데이터를 읽을 때 사용합니다.
- `balances` API는 자금 상태와 잔액 흐름을 따로 추적할 때 사용합니다.
- `tags`(1차 태그), `tag-details`(2차 태그), `categories`, `people`, `teams`, `contacts` API는 Ticket을 해석하고 분류하는 기준 데이터로 사용합니다.

### 10) 통합 시 자주 보는 포인트

- 동일한 회사 데이터를 반복 적재할 때는 `workspaceId + 내부 id` 조합으로 기본 키를 설계하는 것이 일반적입니다.
- 티켓 집계 시에는 `transactionType`, `ticketType`, `isIncluded`를 함께 봐야 사람이 보는 리포트와 AI 분석 결과가 모두 안정적입니다.
- 전자결재 분석이 목적이라면 `workflows.status`, `currentStep`, `steps`를 함께 저장해 결재 대기와 승인 흐름을 추적하는 편이 좋습니다.
- 급여 분석이 목적이라면 `salaryPaymentStatus`, `employmentType`, `recognizedAmount`, `netAmount`를 함께 저장해 지급 현황과 인건비 구조를 분리해서 보는 편이 좋습니다.
- 재고 분석이 목적이라면 `inventory-products`와 `inventories`를 분리 저장하고, `inventoryProductId`로 조인하는 편이 운영 화면과 분석 리포트 양쪽에 유리합니다.
- 자산별 활성 여부와 참조 데이터의 숨김 여부(`isHidden`)는 운영 화면과 외부 리포트에서 의미가 다를 수 있으므로 원본 값을 함께 보관하는 편이 좋습니다.


---

## POST /api/public-docs/tickets

카드, 계좌, 세금계산서, 현금영수증, 결재 등 실제 거래·증빙 데이터를 조회합니다.

### Request Fields

- `workspaceId`는 요청값을 받지 않고 API key에서 자동으로 결정됩니다.
- 기간 기반 조회는 요청 1회당 최대 31일까지 사용할 수 있으며, 더 긴 범위는 31일 이하 구간으로 나눠 조회한 뒤 합쳐서 사용하세요.

- `ticketType` (required, enum): 조회 대상 티켓 타입. 아래 TicketType 상세 목록을 참고하세요.
  - TicketType 상세 (기본 접힘)
    - `EXPENSE_TICKET`: 카드 - 카드 승인/사용 내역 티켓
    - `BANK_TRANSACTION_TICKET`: 계좌 - 계좌 입출금 거래 티켓
    - `TAX_INVOICE_TICKET`: 세금계산서 - 세금계산서 증빙 티켓
    - `CASH_RECEIPT_TICKET`: 현금영수증 - 현금영수증 증빙 티켓
    - `WORKFLOW`: 결재 - 워크플로우 결재 티켓
    - `MERCHANT_CARD_TRANSACTION_TICKET`: 포스기 - 가맹점 카드거래 티켓
    - `ECOMMERCE_SETTLEMENT_DETAIL_TICKET`: 이커머스정산 상세 - 이커머스 정산 상세 티켓
    - `ECOMMERCE_SETTLEMENT`: 이커머스정산 - 이커머스 정산 집계 티켓
    - `PG_SETTLEMENT`: PG정산 - PG 정산 집계 티켓
    - `SALARY_HISTORY`: 급여 - 급여 내역 티켓
    - `MANUAL_TRANSACTION_TICKET`: 수기관리 - 수기 입력 거래 티켓
    - `Total`: 전체 - 통합/전체 조회용 타입
- `startDate` (optional, YYYY-MM-DD): 조회 시작일
- `endDate` (optional, YYYY-MM-DD): 조회 종료일
- `contactId` (optional, number): 거래처 ID
- `contactQueryOption` (optional, "NONE" | "EXACT_MATCH"): 거래처 검색 옵션
- `expenseCategoryId` (optional, number): 계정과목 ID
- `expenseCategoryQueryOption` (optional, "NONE" | "EXACT_MATCH"): 계정과목 검색 옵션
- `tagDetailId` (optional, number): 2차 태그 ID
- `tagDetailQueryOption` (optional, "NONE" | "EXACT_MATCH"): 2차 태그 검색 옵션
- `tagId` (optional, number): 1차 태그 ID
- `assetId` (optional, number): 자산 ID

### Request Example

```json
{
  "ticketType": "EXPENSE_TICKET",
  "startDate": "2026-03-01",
  "endDate": "2026-03-07"
}
```

### Response Example

```json
[
  {
    "id": 49176,
    "ticketType": "EXPENSE_TICKET",
    "workspaceId": 1,
    "description": "",
    "content": "GLOBAL SOFTWARE INC.",
    "amount": 14493,
    "transactionType": "OUT",
    "transactAt": "2026-02-28T17:04:34",
    "status": "CONFIRMED",
    "anomalyStatus": "NORMAL",
    "expenseCategory": {
      "id": 1008,
      "name": "숙박비",
      "subInfo": "판매비와 일반관리비",
      "description": "출장업무 등 숙박에 대한 비용",
      "code": "812",
      "type": "COMMON",
      "isHidden": false,
      "costType": "FIXED",
      "isFavorite": false,
      "taxType": "EXEMPT"
    },
    "users": [
      {
        "id": 73,
        "name": "담당자 A",
        "email": "user-a@example.com",
        "isDeleted": false
      }
    ],
    "owners": [
      {
        "id": 73,
        "name": "소유자 A",
        "email": "owner-a@example.com",
        "isDeleted": false
      }
    ],
    "isIncluded": true,
    "tag": "",
    "tagId": null,
    "contact": "",
    "contactId": null,
    "tagDetail": "",
    "tagDetailId": null,
    "messages": [
      {
        "id": 16099,
        "ticketId": 49176,
        "user": {
          "id": 4,
          "name": "운영자 A",
          "email": "operator@example.com",
          "isDeleted": false
        },
        "userId": 4,
        "userName": "운영자 A",
        "content": "계정과목을 없음에서 숙박비로 변경하였습니다.",
        "type": "COMMON_CHANGE",
        "createdAt": "2026-03-01T21:52:10.826539",
        "deletedAt": null,
        "isDeleted": false
      }
    ],
    "messageCount": 1,
    "attachments": [],
    "attachmentCount": 0,
    "createdAt": "2026-02-28T19:50:38.773108",
    "parentId": null,
    "isSplitted": false,
    "assetId": 53,
    "cardUsage": {
      "workspaceId": 1,
      "card": {
        "id": 53,
        "workspaceId": 1,
        "name": "KB국민 SME기업카드",
        "nickname": "운영 법인카드",
        "number": "558526******0880",
        "organization": "KB",
        "organizationName": "국민카드",
        "businessType": "CD",
        "businessTypeName": "CD",
        "owners": [
          {
            "id": 73,
            "name": "소유자 A",
            "email": "owner-a@example.com",
            "isDeleted": false
          }
        ],
        "isHidden": false,
        "clientType": "CORPORATE",
        "isActive": true,
        "isDormant": false,
        "isPossibleDormant": false,
        "userAutoAssignEnabled": true,
        "isFree": false,
        "hasPassword": false,
        "expirationYear": "2030",
        "expirationMonth": "09",
        "limitAmount": 1000000,
        "usedAmount": 0,
        "remainLimit": 0,
        "previousCardNumber": null
      },
      "organizationName": "국민카드",
      "paymentDate": "2026-02-28T17:04:34",
      "storeName": "GLOBAL SOFTWARE INC.",
      "storeType": "없음",
      "storeAddress": "",
      "storeCorporateNumber": "",
      "amount": 14245,
      "canceledAmount": 0,
      "vat": 0,
      "installmentMonth": 0,
      "paymentType": "FULL",
      "paymentStatus": "NORMAL",
      "approvalNumber": "323461",
      "discountedAmount": 0,
      "originalAmount": "10.00",
      "currency": "USD",
      "isStoreInfoUpdated": true,
      "isPurchased": true
    },
    "purchaseUsage": {
      "workspaceId": 1,
      "card": {
        "id": 53,
        "workspaceId": 1,
        "name": "KB국민 SME기업카드",
        "nickname": "운영 법인카드",
        "number": "558526******0880",
        "organization": "KB",
        "organizationName": "국민카드",
        "businessType": "CD",
        "businessTypeName": "CD",
        "owners": [
          {
            "id": 73,
            "name": "소유자 A",
            "email": "owner-a@example.com",
            "isDeleted": false
          }
        ],
        "isHidden": false,
        "clientType": "CORPORATE",
        "isActive": true,
        "isDormant": false,
        "isPossibleDormant": false,
        "userAutoAssignEnabled": true,
        "isFree": false,
        "hasPassword": false,
        "expirationYear": "2030",
        "expirationMonth": "09",
        "limitAmount": 1000000,
        "usedAmount": 0,
        "remainLimit": 0,
        "previousCardNumber": null
      },
      "organizationName": "국민카드",
      "paymentDate": "2026-02-28T00:00:00",
      "storeName": "GLOBAL SOFTWARE INC.",
      "storeType": "",
      "storeAddress": "",
      "storeCorporateNumber": "",
      "amount": 14493,
      "canceledAmount": 0,
      "vat": 0,
      "installmentMonth": 0,
      "paymentType": "FULL",
      "paymentStatus": "NORMAL",
      "approvalNumber": "GRANTER-0",
      "discountedAmount": 0,
      "originalAmount": "10.000",
      "currency": "USD",
      "purchaseDate": "2026-03-03",
      "exchangeRate": "1449.30",
      "foreignReceiptAmount": "10.00",
      "feeAmount": 187
    },
    "bankTransaction": null,
    "taxInvoice": null,
    "paymentStatus": null,
    "purchaseStatus": "PURCHASED",
    "merchantCardTransaction": null,
    "merchantPurchaseTransaction": null,
    "cashReceipt": null,
    "manualTransaction": null,
    "hasPurchaseCanceledUsages": false,
    "ecommerceSettlementDetailTransaction": null,
    "taxType": "EXEMPT"
  }
]
```

### Response Fields (Full Expanded)

| Path | Type | Required | Description |
| --- | --- | --- | --- |
| [] | Ticket | required | BE `/tickets` 원본 응답 배열의 각 원소 |
| [].id | number | required | 티켓 ID |
| [].workspaceId | number | required | 워크스페이스 ID |
| [].content | string | required | 거래/증빙 내용 |
| [].amount | number | required | 거래 금액 |
| [].transactionType | "IN" \| "OUT" \| "ALL" | required | 입출금 방향 |
| [].transactAt | ISO-8601 string | required | 거래 일시 |
| [].modifiedTransactAt | ISO-8601 string | optional | 수정 거래 일시 |
| [].description | string | required | 설명 |
| [].expenseCategory | ExpenseCategory | optional | 계정과목 |
| [].expenseCategory.id | number | required | 계정과목 ID |
| [].expenseCategory.name | string | required | 계정과목명 |
| [].expenseCategory.subInfo | string | required | 부가 정보 |
| [].expenseCategory.description | string | required | 설명 |
| [].expenseCategory.code | number \| string | required | 코드 |
| [].expenseCategory.type | "COMMON" \| "CUSTOM" | required | 카테고리 타입 |
| [].expenseCategory.isHidden | boolean | required | 숨김 여부 |
| [].expenseCategory.costType | "NONE" \| "VARIABLE" \| "FIXED" | required | 비용 유형 |
| [].expenseCategory.isFavorite | boolean | required | 즐겨찾기 여부 |
| [].expenseCategory.workspaceId | number | optional | 워크스페이스 ID |
| [].expenseCategory.taxType | "DETERMINE_NEEDED" \| "TAXABLE" \| "EXEMPT" \| "ZERO_RATED" \| null | optional | 기본 과세 유형 |
| [].anomalyStatus | "NORMAL" \| "ABUSE_SUSPECTED" \| "ABUSE_CONFIRMED" \| "ABUSE_REJECTED" | required | 이상치 상태 |
| [].status | "NONE" \| "CONFIRMED" \| "USER_CONFIRMED" | required | 티켓 상태 |
| [].tag | string | required | 1차 태그명 |
| [].tagId | number \| null | required | 1차 태그 ID |
| [].contact | string | required | 거래처명 |
| [].contactId | number \| undefined | required | 거래처 ID |
| [].tagDetail | string | required | 2차 태그명 |
| [].tagDetailId | number \| null | required | 2차 태그 ID |
| [].isIncluded | boolean | required | 포함 여부 |
| [].users | UserResponse[] | required | 연결 유저 목록 |
| [].users.id | number | required | 유저 ID |
| [].users.name | string | required | 유저 이름 |
| [].users.email | string | required | 유저 이메일 |
| [].users.isDeleted | boolean | required | 삭제 여부 |
| [].owners | UserResponse[] | required | 소유자 목록 |
| [].owners.id | number | required | 유저 ID |
| [].owners.name | string | required | 유저 이름 |
| [].owners.email | string | required | 유저 이메일 |
| [].owners.isDeleted | boolean | required | 삭제 여부 |
| [].messages | Message[] | required | 메시지 목록 |
| [].messages.id | number | required | 메시지 ID |
| [].messages.ticketId | number | required | 티켓 ID |
| [].messages.user | UserResponse | required | 작성자 |
| [].messages.user.id | number | required | 유저 ID |
| [].messages.user.name | string | required | 유저 이름 |
| [].messages.user.email | string | required | 유저 이메일 |
| [].messages.user.isDeleted | boolean | required | 삭제 여부 |
| [].messages.userId | number | required | 작성자 ID |
| [].messages.userName | string | required | 작성자 이름 |
| [].messages.content | string | required | 메시지 내용 |
| [].messages.type | MessageType | required | 메시지 타입 |
| [].messages.createdAt | string | required | 생성 시각 |
| [].messages.deletedAt | string \| null | optional | 삭제 시각 |
| [].messageCount | number | required | 메시지 수 |
| [].attachments | Attachment[] | required | 첨부파일 목록 |
| [].attachments.id | number | required | 첨부파일 ID |
| [].attachments.ticketId | number | required | 티켓 ID |
| [].attachments.name | string | required | 파일명 |
| [].attachments.uploadedUrl | string | required | 업로드 URL |
| [].attachments.contentType | string | required | MIME 타입 |
| [].attachmentCount | number | required | 첨부파일 수 |
| [].ticketType | TicketType | required | 조회 대상 티켓 타입 |
| [].cardUsage | CardUsage | optional | 카드 사용 상세 |
| [].cardUsage.workspaceId | number | required | 워크스페이스 ID |
| [].cardUsage.card | TicketAsset | required | 카드 자산 정보 |
| [].cardUsage.card.id | number | required | 자산 ID |
| [].cardUsage.card.workspaceId | number | required | 워크스페이스 ID |
| [].cardUsage.card.name | string | required | 자산명 |
| [].cardUsage.card.nickname | string | required | 자산 별칭 |
| [].cardUsage.card.organization | string | required | 기관 코드 |
| [].cardUsage.card.organizationName | string | required | 기관명 |
| [].cardUsage.card.isActive | boolean | required | 활성 여부 |
| [].cardUsage.card.isPossibleDormant | boolean | required | 휴면 가능 여부 |
| [].cardUsage.card.isDormant | boolean | required | 휴면 여부 |
| [].cardUsage.card.number | string | required | 자산 번호 |
| [].cardUsage.card.owners | UserResponse[] | required | 소유자 목록 |
| [].cardUsage.card.owners.id | number | required | 유저 ID |
| [].cardUsage.card.owners.name | string | required | 유저 이름 |
| [].cardUsage.card.owners.email | string | required | 유저 이메일 |
| [].cardUsage.card.owners.isDeleted | boolean | required | 삭제 여부 |
| [].cardUsage.card.clientType | "PERSONAL" \| "CORPORATE" \| "ALL" | required | 고객 유형 |
| [].cardUsage.card.businessType | string | required | 업종 코드 |
| [].cardUsage.card.businessTypeName | string | required | 업종명 |
| [].cardUsage.card.userAutoAssignEnabled | boolean | optional | 유저 자동 할당 여부 |
| [].cardUsage.card.expirationYear | string | optional | 카드 만료 연도 |
| [].cardUsage.card.expirationMonth | string | optional | 카드 만료 월 |
| [].cardUsage.card.limitAmount | number | optional | 카드 한도 금액 |
| [].cardUsage.card.usedAmount | number | optional | 카드 사용 금액 |
| [].cardUsage.card.remainLimit | number | optional | 카드 잔여 한도 |
| [].cardUsage.card.isAutoApproval | boolean | optional | 자동 승인 여부 |
| [].cardUsage.card.previousCardNumber | string | optional | 이전 카드번호 |
| [].cardUsage.card.accountBalance | number | optional | 계좌 잔액 |
| [].cardUsage.card.originalBalance | number | optional | 원본 잔액 |
| [].cardUsage.card.currencyCode | string | optional | 통화 코드 |
| [].cardUsage.card.startDate | string | optional | 조회 시작일 |
| [].cardUsage.card.endDate | string \| null | optional | 조회 종료일 |
| [].cardUsage.card.accountType | string | optional | 계좌 유형 |
| [].cardUsage.card.isTransactionVisible | boolean | optional | 거래내역 보유 여부 |
| [].cardUsage.card.totalAmount | number | optional | 증권 총액 |
| [].cardUsage.card.depositAmount | number | optional | 증권 예수금 |
| [].cardUsage.card.foreignDepositAmount | object[] | optional | 외화예수금 상세 목록 |
| [].cardUsage.card.foreignDeposits | number | optional | 외화예수금 합계 |
| [].cardUsage.card.totalValuationAmount | number | optional | 총 평가금액 |
| [].cardUsage.card.accountNumber | string | optional | 계좌 번호 |
| [].cardUsage.card.products | Product[] | optional | 증권 보유 상품 목록 |
| [].cardUsage.card.registrationNumber | string | optional | 사업자등록번호 |
| [].cardUsage.card.establishNumber | string | optional | 개업일자 코드 |
| [].cardUsage.card.companyName | string | optional | 회사명 |
| [].cardUsage.card.ceoName | string | optional | 대표자명 |
| [].cardUsage.card.address | string | optional | 주소 |
| [].cardUsage.card.contactName | string | optional | 담당자명 |
| [].cardUsage.card.email | string | optional | 담당자 이메일 |
| [].cardUsage.card.tel | string | optional | 연락처 |
| [].cardUsage.card.businessTypes | string | optional | 업태 |
| [].cardUsage.card.businessItems | string | optional | 종목 |
| [].cardUsage.card.category | string | optional | 홈택스 카테고리 |
| [].cardUsage.card.createdAt | string | optional | 생성 시각 |
| [].cardUsage.card.updatedAt | string | optional | 수정 시각 |
| [].cardUsage.card.readCertificate | object | optional | 조회 인증서 정보 |
| [].cardUsage.card.issueCertificate | object | optional | 발행 인증서 정보 |
| [].cardUsage.paymentDate | string | required | 결제일 |
| [].cardUsage.storeName | string | required | 가맹점명 |
| [].cardUsage.storeAddress | string | required | 가맹점 주소 |
| [].cardUsage.amount | number | required | 사용 금액 |
| [].cardUsage.canceledAmount | number | required | 취소 금액 |
| [].cardUsage.discountedAmount | number | required | 할인 금액 |
| [].cardUsage.vat | number | required | 부가세 |
| [].cardUsage.installmentMonth | number | required | 할부 개월 |
| [].cardUsage.paymentType | string | required | 결제 방식 |
| [].cardUsage.paymentStatus | "NORMAL" \| "CANCELED" \| "REJECTED" \| "PURCHASE_CANCELED" | required | 결제 상태 |
| [].cardUsage.approvalNumber | number | required | 승인번호 |
| [].cardUsage.storeCorporateNumber | number | required | 가맹점 사업자번호 |
| [].cardUsage.originalAmount | number | required | 원본 금액 |
| [].cardUsage.currency | string | required | 통화 코드 |
| [].cardUsage.exchangeRate | number | optional | 환율 |
| [].cardUsage.purchaseDate | string | optional | 매입일 |
| [].cardUsage.feeAmount | number | optional | 수수료 |
| [].cardUsage.isPurchased | boolean | optional | 매입 여부 |
| [].purchaseUsage | CardUsage | optional | 매입 카드 사용 상세 |
| [].purchaseUsage.workspaceId | number | required | 워크스페이스 ID |
| [].purchaseUsage.card | TicketAsset | required | 카드 자산 정보 |
| [].purchaseUsage.card.id | number | required | 자산 ID |
| [].purchaseUsage.card.workspaceId | number | required | 워크스페이스 ID |
| [].purchaseUsage.card.name | string | required | 자산명 |
| [].purchaseUsage.card.nickname | string | required | 자산 별칭 |
| [].purchaseUsage.card.organization | string | required | 기관 코드 |
| [].purchaseUsage.card.organizationName | string | required | 기관명 |
| [].purchaseUsage.card.isActive | boolean | required | 활성 여부 |
| [].purchaseUsage.card.isPossibleDormant | boolean | required | 휴면 가능 여부 |
| [].purchaseUsage.card.isDormant | boolean | required | 휴면 여부 |
| [].purchaseUsage.card.number | string | required | 자산 번호 |
| [].purchaseUsage.card.owners | UserResponse[] | required | 소유자 목록 |
| [].purchaseUsage.card.owners.id | number | required | 유저 ID |
| [].purchaseUsage.card.owners.name | string | required | 유저 이름 |
| [].purchaseUsage.card.owners.email | string | required | 유저 이메일 |
| [].purchaseUsage.card.owners.isDeleted | boolean | required | 삭제 여부 |
| [].purchaseUsage.card.clientType | "PERSONAL" \| "CORPORATE" \| "ALL" | required | 고객 유형 |
| [].purchaseUsage.card.businessType | string | required | 업종 코드 |
| [].purchaseUsage.card.businessTypeName | string | required | 업종명 |
| [].purchaseUsage.card.userAutoAssignEnabled | boolean | optional | 유저 자동 할당 여부 |
| [].purchaseUsage.card.expirationYear | string | optional | 카드 만료 연도 |
| [].purchaseUsage.card.expirationMonth | string | optional | 카드 만료 월 |
| [].purchaseUsage.card.limitAmount | number | optional | 카드 한도 금액 |
| [].purchaseUsage.card.usedAmount | number | optional | 카드 사용 금액 |
| [].purchaseUsage.card.remainLimit | number | optional | 카드 잔여 한도 |
| [].purchaseUsage.card.isAutoApproval | boolean | optional | 자동 승인 여부 |
| [].purchaseUsage.card.previousCardNumber | string | optional | 이전 카드번호 |
| [].purchaseUsage.card.accountBalance | number | optional | 계좌 잔액 |
| [].purchaseUsage.card.originalBalance | number | optional | 원본 잔액 |
| [].purchaseUsage.card.currencyCode | string | optional | 통화 코드 |
| [].purchaseUsage.card.startDate | string | optional | 조회 시작일 |
| [].purchaseUsage.card.endDate | string \| null | optional | 조회 종료일 |
| [].purchaseUsage.card.accountType | string | optional | 계좌 유형 |
| [].purchaseUsage.card.isTransactionVisible | boolean | optional | 거래내역 보유 여부 |
| [].purchaseUsage.card.totalAmount | number | optional | 증권 총액 |
| [].purchaseUsage.card.depositAmount | number | optional | 증권 예수금 |
| [].purchaseUsage.card.foreignDepositAmount | object[] | optional | 외화예수금 상세 목록 |
| [].purchaseUsage.card.foreignDeposits | number | optional | 외화예수금 합계 |
| [].purchaseUsage.card.totalValuationAmount | number | optional | 총 평가금액 |
| [].purchaseUsage.card.accountNumber | string | optional | 계좌 번호 |
| [].purchaseUsage.card.products | Product[] | optional | 증권 보유 상품 목록 |
| [].purchaseUsage.card.registrationNumber | string | optional | 사업자등록번호 |
| [].purchaseUsage.card.establishNumber | string | optional | 개업일자 코드 |
| [].purchaseUsage.card.companyName | string | optional | 회사명 |
| [].purchaseUsage.card.ceoName | string | optional | 대표자명 |
| [].purchaseUsage.card.address | string | optional | 주소 |
| [].purchaseUsage.card.contactName | string | optional | 담당자명 |
| [].purchaseUsage.card.email | string | optional | 담당자 이메일 |
| [].purchaseUsage.card.tel | string | optional | 연락처 |
| [].purchaseUsage.card.businessTypes | string | optional | 업태 |
| [].purchaseUsage.card.businessItems | string | optional | 종목 |
| [].purchaseUsage.card.category | string | optional | 홈택스 카테고리 |
| [].purchaseUsage.card.createdAt | string | optional | 생성 시각 |
| [].purchaseUsage.card.updatedAt | string | optional | 수정 시각 |
| [].purchaseUsage.card.readCertificate | object | optional | 조회 인증서 정보 |
| [].purchaseUsage.card.issueCertificate | object | optional | 발행 인증서 정보 |
| [].purchaseUsage.paymentDate | string | required | 결제일 |
| [].purchaseUsage.storeName | string | required | 가맹점명 |
| [].purchaseUsage.storeAddress | string | required | 가맹점 주소 |
| [].purchaseUsage.amount | number | required | 사용 금액 |
| [].purchaseUsage.canceledAmount | number | required | 취소 금액 |
| [].purchaseUsage.discountedAmount | number | required | 할인 금액 |
| [].purchaseUsage.vat | number | required | 부가세 |
| [].purchaseUsage.installmentMonth | number | required | 할부 개월 |
| [].purchaseUsage.paymentType | string | required | 결제 방식 |
| [].purchaseUsage.paymentStatus | "NORMAL" \| "CANCELED" \| "REJECTED" \| "PURCHASE_CANCELED" | required | 결제 상태 |
| [].purchaseUsage.approvalNumber | number | required | 승인번호 |
| [].purchaseUsage.storeCorporateNumber | number | required | 가맹점 사업자번호 |
| [].purchaseUsage.originalAmount | number | required | 원본 금액 |
| [].purchaseUsage.currency | string | required | 통화 코드 |
| [].purchaseUsage.exchangeRate | number | optional | 환율 |
| [].purchaseUsage.purchaseDate | string | optional | 매입일 |
| [].purchaseUsage.feeAmount | number | optional | 수수료 |
| [].purchaseUsage.isPurchased | boolean | optional | 매입 여부 |
| [].bankTransaction | BankTransaction | optional | 계좌 거래 상세 |
| [].bankTransaction.bankTransactionId | number | required | 은행거래 ID |
| [].bankTransaction.workspaceId | number | required | 워크스페이스 ID |
| [].bankTransaction.transactionType | "IN" \| "OUT" \| "ALL" | required | 거래 방향 |
| [].bankTransaction.description | string | required | 설명 |
| [].bankTransaction.afterTransactionBalance | number | required | 거래 후 잔액 |
| [].bankTransaction.originalAfterTransactionBalance | number | optional | 원본 거래 후 잔액 |
| [].bankTransaction.amount | number | required | 거래 금액 |
| [].bankTransaction.originalAmount | number | optional | 원본 거래 금액 |
| [].bankTransaction.transactAt | string | required | 거래 일시 |
| [].bankTransaction.bankAccount | TicketAsset | required | 계좌 자산 정보 |
| [].bankTransaction.bankAccount.id | number | required | 자산 ID |
| [].bankTransaction.bankAccount.workspaceId | number | required | 워크스페이스 ID |
| [].bankTransaction.bankAccount.name | string | required | 자산명 |
| [].bankTransaction.bankAccount.nickname | string | required | 자산 별칭 |
| [].bankTransaction.bankAccount.organization | string | required | 기관 코드 |
| [].bankTransaction.bankAccount.organizationName | string | required | 기관명 |
| [].bankTransaction.bankAccount.isActive | boolean | required | 활성 여부 |
| [].bankTransaction.bankAccount.isPossibleDormant | boolean | required | 휴면 가능 여부 |
| [].bankTransaction.bankAccount.isDormant | boolean | required | 휴면 여부 |
| [].bankTransaction.bankAccount.number | string | required | 자산 번호 |
| [].bankTransaction.bankAccount.owners | UserResponse[] | required | 소유자 목록 |
| [].bankTransaction.bankAccount.owners.id | number | required | 유저 ID |
| [].bankTransaction.bankAccount.owners.name | string | required | 유저 이름 |
| [].bankTransaction.bankAccount.owners.email | string | required | 유저 이메일 |
| [].bankTransaction.bankAccount.owners.isDeleted | boolean | required | 삭제 여부 |
| [].bankTransaction.bankAccount.clientType | "PERSONAL" \| "CORPORATE" \| "ALL" | required | 고객 유형 |
| [].bankTransaction.bankAccount.businessType | string | required | 업종 코드 |
| [].bankTransaction.bankAccount.businessTypeName | string | required | 업종명 |
| [].bankTransaction.bankAccount.userAutoAssignEnabled | boolean | optional | 유저 자동 할당 여부 |
| [].bankTransaction.bankAccount.expirationYear | string | optional | 카드 만료 연도 |
| [].bankTransaction.bankAccount.expirationMonth | string | optional | 카드 만료 월 |
| [].bankTransaction.bankAccount.limitAmount | number | optional | 카드 한도 금액 |
| [].bankTransaction.bankAccount.usedAmount | number | optional | 카드 사용 금액 |
| [].bankTransaction.bankAccount.remainLimit | number | optional | 카드 잔여 한도 |
| [].bankTransaction.bankAccount.isAutoApproval | boolean | optional | 자동 승인 여부 |
| [].bankTransaction.bankAccount.previousCardNumber | string | optional | 이전 카드번호 |
| [].bankTransaction.bankAccount.accountBalance | number | optional | 계좌 잔액 |
| [].bankTransaction.bankAccount.originalBalance | number | optional | 원본 잔액 |
| [].bankTransaction.bankAccount.currencyCode | string | optional | 통화 코드 |
| [].bankTransaction.bankAccount.startDate | string | optional | 조회 시작일 |
| [].bankTransaction.bankAccount.endDate | string \| null | optional | 조회 종료일 |
| [].bankTransaction.bankAccount.accountType | string | optional | 계좌 유형 |
| [].bankTransaction.bankAccount.isTransactionVisible | boolean | optional | 거래내역 보유 여부 |
| [].bankTransaction.bankAccount.totalAmount | number | optional | 증권 총액 |
| [].bankTransaction.bankAccount.depositAmount | number | optional | 증권 예수금 |
| [].bankTransaction.bankAccount.foreignDepositAmount | object[] | optional | 외화예수금 상세 목록 |
| [].bankTransaction.bankAccount.foreignDeposits | number | optional | 외화예수금 합계 |
| [].bankTransaction.bankAccount.totalValuationAmount | number | optional | 총 평가금액 |
| [].bankTransaction.bankAccount.accountNumber | string | optional | 계좌 번호 |
| [].bankTransaction.bankAccount.products | Product[] | optional | 증권 보유 상품 목록 |
| [].bankTransaction.bankAccount.registrationNumber | string | optional | 사업자등록번호 |
| [].bankTransaction.bankAccount.establishNumber | string | optional | 개업일자 코드 |
| [].bankTransaction.bankAccount.companyName | string | optional | 회사명 |
| [].bankTransaction.bankAccount.ceoName | string | optional | 대표자명 |
| [].bankTransaction.bankAccount.address | string | optional | 주소 |
| [].bankTransaction.bankAccount.contactName | string | optional | 담당자명 |
| [].bankTransaction.bankAccount.email | string | optional | 담당자 이메일 |
| [].bankTransaction.bankAccount.tel | string | optional | 연락처 |
| [].bankTransaction.bankAccount.businessTypes | string | optional | 업태 |
| [].bankTransaction.bankAccount.businessItems | string | optional | 종목 |
| [].bankTransaction.bankAccount.category | string | optional | 홈택스 카테고리 |
| [].bankTransaction.bankAccount.createdAt | string | optional | 생성 시각 |
| [].bankTransaction.bankAccount.updatedAt | string | optional | 수정 시각 |
| [].bankTransaction.bankAccount.readCertificate | object | optional | 조회 인증서 정보 |
| [].bankTransaction.bankAccount.issueCertificate | object | optional | 발행 인증서 정보 |
| [].bankTransaction.counterparty | string | optional | 거래 상대방 |
| [].bankTransaction.descriptionType | string | optional | 설명 분류 |
| [].bankTransaction.content | string | optional | 내용 |
| [].bankTransaction.branch | string | optional | 지점 |
| [].taxInvoice | TaxInvoice | optional | 세금계산서 상세 |
| [].taxInvoice.id | number | required | 세금계산서 ID |
| [].taxInvoice.supplier | TaxInvoiceUser | required | 공급자 |
| [].taxInvoice.supplier.registrationNumber | string | required | 사업자등록번호 |
| [].taxInvoice.supplier.companyName | string | required | 회사명 |
| [].taxInvoice.supplier.ceoName | string | optional | 대표자명 |
| [].taxInvoice.supplier.name | string | required | 담당자명 |
| [].taxInvoice.supplier.businessTypes | string | required | 업태 |
| [].taxInvoice.supplier.businessItems | string | required | 종목 |
| [].taxInvoice.supplier.businessPlace | string | required | 사업장 주소 |
| [].taxInvoice.supplier.email | string | required | 이메일 |
| [].taxInvoice.supplier.email2 | string | optional | 보조 이메일 |
| [].taxInvoice.supplier.id | number | required | 유저 ID |
| [].taxInvoice.supplier.establishNumber | string | optional | 개업일자 코드 |
| [].taxInvoice.supplier.userType | "SUPPLIER" \| "CONTRACTOR" \| "TRUSTEE" | optional | 당사자 유형 |
| [].taxInvoice.supplier.taxInvoice | TaxInvoice | optional | 관련 세금계산서 |
| [].taxInvoice.supplier.phone | string | optional | 휴대전화 |
| [].taxInvoice.supplier.tel | string | optional | 전화번호 |
| [].taxInvoice.contractor | TaxInvoiceUser | required | 공급받는자 |
| [].taxInvoice.contractor.registrationNumber | string | required | 사업자등록번호 |
| [].taxInvoice.contractor.companyName | string | required | 회사명 |
| [].taxInvoice.contractor.ceoName | string | optional | 대표자명 |
| [].taxInvoice.contractor.name | string | required | 담당자명 |
| [].taxInvoice.contractor.businessTypes | string | required | 업태 |
| [].taxInvoice.contractor.businessItems | string | required | 종목 |
| [].taxInvoice.contractor.businessPlace | string | required | 사업장 주소 |
| [].taxInvoice.contractor.email | string | required | 이메일 |
| [].taxInvoice.contractor.email2 | string | optional | 보조 이메일 |
| [].taxInvoice.contractor.id | number | required | 유저 ID |
| [].taxInvoice.contractor.establishNumber | string | optional | 개업일자 코드 |
| [].taxInvoice.contractor.userType | "SUPPLIER" \| "CONTRACTOR" \| "TRUSTEE" | optional | 당사자 유형 |
| [].taxInvoice.contractor.taxInvoice | TaxInvoice | optional | 관련 세금계산서 |
| [].taxInvoice.contractor.phone | string | optional | 휴대전화 |
| [].taxInvoice.contractor.tel | string | optional | 전화번호 |
| [].taxInvoice.issuer | object | required | 발행자 |
| [].taxInvoice.issuer.email | string | required | 이메일 |
| [].taxInvoice.issuer.id | number | required | 유저 ID |
| [].taxInvoice.issuer.isDeleted | boolean | required | 삭제 여부 |
| [].taxInvoice.issuer.name | string | required | 이름 |
| [].taxInvoice.trustee | TaxInvoiceUser | required | 수탁자 |
| [].taxInvoice.trustee.registrationNumber | string | required | 사업자등록번호 |
| [].taxInvoice.trustee.companyName | string | required | 회사명 |
| [].taxInvoice.trustee.ceoName | string | optional | 대표자명 |
| [].taxInvoice.trustee.name | string | required | 담당자명 |
| [].taxInvoice.trustee.businessTypes | string | required | 업태 |
| [].taxInvoice.trustee.businessItems | string | required | 종목 |
| [].taxInvoice.trustee.businessPlace | string | required | 사업장 주소 |
| [].taxInvoice.trustee.email | string | required | 이메일 |
| [].taxInvoice.trustee.email2 | string | optional | 보조 이메일 |
| [].taxInvoice.trustee.id | number | required | 유저 ID |
| [].taxInvoice.trustee.establishNumber | string | optional | 개업일자 코드 |
| [].taxInvoice.trustee.userType | "SUPPLIER" \| "CONTRACTOR" \| "TRUSTEE" | optional | 당사자 유형 |
| [].taxInvoice.trustee.taxInvoice | TaxInvoice | optional | 관련 세금계산서 |
| [].taxInvoice.trustee.phone | string | optional | 휴대전화 |
| [].taxInvoice.trustee.tel | string | optional | 전화번호 |
| [].taxInvoice.approvalNumber | string | required | 승인번호 |
| [].taxInvoice.contentType | "SUMMARY" \| "DETAIL" | required | 내용 타입 |
| [].taxInvoice.transactionType | "SELL" \| "BUY" \| "TRUSTEE" | required | 거래 타입 |
| [].taxInvoice.issueName | string | required | 발행명 |
| [].taxInvoice.reportingDate | string | required | 작성일자 |
| [].taxInvoice.sendDate | string | optional | 전송일자 |
| [].taxInvoice.issueDate | string | optional | 발행일자 |
| [].taxInvoice.supplyValue | number | required | 공급가액 |
| [].taxInvoice.taxAmount | number | required | 세액 |
| [].taxInvoice.totalAmount | number | required | 합계금액 |
| [].taxInvoice.purposeType | "RECEIPT" \| "CHARGE" | required | 영수/청구 구분 |
| [].taxInvoice.representationItems | string | required | 대표 품목 |
| [].taxInvoice.note | string | required | 비고 |
| [].taxInvoice.status | "REPORTED" \| "SENT" \| "ISSUED" | required | 상태 |
| [].taxInvoice.taxInvoiceType | string | required | 세금계산서 종류 |
| [].taxInvoice.tradeItems | TradeItem[] | required | 품목 목록 |
| [].taxInvoice.tradeItems.purchaseExpiryDate | string | required | 공급일자 |
| [].taxInvoice.tradeItems.taxItemName | string | required | 품목명 |
| [].taxInvoice.tradeItems.standards | string | required | 규격 |
| [].taxInvoice.tradeItems.quantity | number | required | 수량 |
| [].taxInvoice.tradeItems.unitPrice | number | required | 단가 |
| [].taxInvoice.tradeItems.supplyValue | number | required | 공급가액 |
| [].taxInvoice.tradeItems.taxAmount | number | required | 세액 |
| [].taxInvoice.tradeItems.note | string | required | 비고 |
| [].taxInvoice.tradeItems.isIncludedVat | boolean | optional | 부가세 포함 여부 |
| [].taxInvoice.modificationReason | string | optional | 수정사유 |
| [].taxInvoice.originalApprovalNumber | number | optional | 원본 승인번호 |
| [].taxInvoice.issueOption | IssueOption | optional | 발행 옵션 |
| [].taxInvoice.issueOption.issueType | string | required | 발행 타입 |
| [].taxInvoice.issueOption.taxType | "TAX" \| "ZERO_TAX_RATE" \| "TAX_EXEMPTION" | required | 과세 유형 |
| [].taxInvoice.issueOption.chargeDirection | string | required | 청구 방향 |
| [].taxInvoice.issueOption.kwon | number | required | 권 |
| [].taxInvoice.issueOption.ho | number | required | 호 |
| [].taxInvoice.issueOption.contractorType | "BUSINESS" \| "INDIVIDUAL" \| "FOREIGNER" | required | 공급받는자 유형 |
| [].taxInvoice.issueOption.forceIssue | boolean | required | 강제 발행 여부 |
| [].taxInvoice.issueOption.writeSpecification | boolean | required | 명세 작성 여부 |
| [].taxInvoice.issueOption.closeDownStatus | string | required | 휴폐업 상태 |
| [].taxInvoice.issueOption.closeDownDate | string | required | 휴폐업 일자 |
| [].paymentStatus | "PAID" \| "UNPAID" | optional | 결제 상태 |
| [].createdAt | ISO-8601 string | required | 생성 시각 |
| [].parentId | number | optional | 부모 티켓 ID |
| [].isSplitted | boolean | required | 분할 티켓 여부 |
| [].purchaseStatus | "APPROVED" \| "PURCHASED" \| "PURCHASE_ONLY" | optional | 매입 상태 |
| [].isSoftwareExpenditure | boolean | optional | 소프트웨어 지출 여부 |
| [].merchantCardTransaction | MerchantCardTransaction | optional | 가맹점 카드 거래 상세 |
| [].merchantCardTransaction.asset | TicketAsset | required | 자산 정보 |
| [].merchantCardTransaction.asset.id | number | required | 자산 ID |
| [].merchantCardTransaction.asset.workspaceId | number | required | 워크스페이스 ID |
| [].merchantCardTransaction.asset.name | string | required | 자산명 |
| [].merchantCardTransaction.asset.nickname | string | required | 자산 별칭 |
| [].merchantCardTransaction.asset.organization | string | required | 기관 코드 |
| [].merchantCardTransaction.asset.organizationName | string | required | 기관명 |
| [].merchantCardTransaction.asset.isActive | boolean | required | 활성 여부 |
| [].merchantCardTransaction.asset.isPossibleDormant | boolean | required | 휴면 가능 여부 |
| [].merchantCardTransaction.asset.isDormant | boolean | required | 휴면 여부 |
| [].merchantCardTransaction.asset.number | string | required | 자산 번호 |
| [].merchantCardTransaction.asset.owners | UserResponse[] | required | 소유자 목록 |
| [].merchantCardTransaction.asset.owners.id | number | required | 유저 ID |
| [].merchantCardTransaction.asset.owners.name | string | required | 유저 이름 |
| [].merchantCardTransaction.asset.owners.email | string | required | 유저 이메일 |
| [].merchantCardTransaction.asset.owners.isDeleted | boolean | required | 삭제 여부 |
| [].merchantCardTransaction.asset.clientType | "PERSONAL" \| "CORPORATE" \| "ALL" | required | 고객 유형 |
| [].merchantCardTransaction.asset.businessType | string | required | 업종 코드 |
| [].merchantCardTransaction.asset.businessTypeName | string | required | 업종명 |
| [].merchantCardTransaction.asset.userAutoAssignEnabled | boolean | optional | 유저 자동 할당 여부 |
| [].merchantCardTransaction.asset.expirationYear | string | optional | 카드 만료 연도 |
| [].merchantCardTransaction.asset.expirationMonth | string | optional | 카드 만료 월 |
| [].merchantCardTransaction.asset.limitAmount | number | optional | 카드 한도 금액 |
| [].merchantCardTransaction.asset.usedAmount | number | optional | 카드 사용 금액 |
| [].merchantCardTransaction.asset.remainLimit | number | optional | 카드 잔여 한도 |
| [].merchantCardTransaction.asset.isAutoApproval | boolean | optional | 자동 승인 여부 |
| [].merchantCardTransaction.asset.previousCardNumber | string | optional | 이전 카드번호 |
| [].merchantCardTransaction.asset.accountBalance | number | optional | 계좌 잔액 |
| [].merchantCardTransaction.asset.originalBalance | number | optional | 원본 잔액 |
| [].merchantCardTransaction.asset.currencyCode | string | optional | 통화 코드 |
| [].merchantCardTransaction.asset.startDate | string | optional | 조회 시작일 |
| [].merchantCardTransaction.asset.endDate | string \| null | optional | 조회 종료일 |
| [].merchantCardTransaction.asset.accountType | string | optional | 계좌 유형 |
| [].merchantCardTransaction.asset.isTransactionVisible | boolean | optional | 거래내역 보유 여부 |
| [].merchantCardTransaction.asset.totalAmount | number | optional | 증권 총액 |
| [].merchantCardTransaction.asset.depositAmount | number | optional | 증권 예수금 |
| [].merchantCardTransaction.asset.foreignDepositAmount | object[] | optional | 외화예수금 상세 목록 |
| [].merchantCardTransaction.asset.foreignDeposits | number | optional | 외화예수금 합계 |
| [].merchantCardTransaction.asset.totalValuationAmount | number | optional | 총 평가금액 |
| [].merchantCardTransaction.asset.accountNumber | string | optional | 계좌 번호 |
| [].merchantCardTransaction.asset.products | Product[] | optional | 증권 보유 상품 목록 |
| [].merchantCardTransaction.asset.registrationNumber | string | optional | 사업자등록번호 |
| [].merchantCardTransaction.asset.establishNumber | string | optional | 개업일자 코드 |
| [].merchantCardTransaction.asset.companyName | string | optional | 회사명 |
| [].merchantCardTransaction.asset.ceoName | string | optional | 대표자명 |
| [].merchantCardTransaction.asset.address | string | optional | 주소 |
| [].merchantCardTransaction.asset.contactName | string | optional | 담당자명 |
| [].merchantCardTransaction.asset.email | string | optional | 담당자 이메일 |
| [].merchantCardTransaction.asset.tel | string | optional | 연락처 |
| [].merchantCardTransaction.asset.businessTypes | string | optional | 업태 |
| [].merchantCardTransaction.asset.businessItems | string | optional | 종목 |
| [].merchantCardTransaction.asset.category | string | optional | 홈택스 카테고리 |
| [].merchantCardTransaction.asset.createdAt | string | optional | 생성 시각 |
| [].merchantCardTransaction.asset.updatedAt | string | optional | 수정 시각 |
| [].merchantCardTransaction.asset.readCertificate | object | optional | 조회 인증서 정보 |
| [].merchantCardTransaction.asset.issueCertificate | object | optional | 발행 인증서 정보 |
| [].merchantCardTransaction.cardCompany | string | required | 카드사 |
| [].merchantCardTransaction.cardName | string | required | 카드명 |
| [].merchantCardTransaction.cardType | CardType | required | 카드 타입 |
| [].merchantCardTransaction.cardNumber | string | required | 카드번호 |
| [].merchantCardTransaction.approvalNumber | string | required | 승인번호 |
| [].merchantCardTransaction.installmentMonth | number | required | 할부개월 |
| [].merchantCardTransaction.transactionType | string | required | 거래구분 |
| [].merchantCardTransaction.transactAt | string | required | 거래일시 |
| [].merchantCardTransaction.amount | number | required | 금액 |
| [].merchantPurchaseTransaction | MerchantPurchaseTransaction | optional | 가맹점 매입 거래 상세 |
| [].merchantPurchaseTransaction.asset | TicketAsset | required | 자산 정보 |
| [].merchantPurchaseTransaction.asset.id | number | required | 자산 ID |
| [].merchantPurchaseTransaction.asset.workspaceId | number | required | 워크스페이스 ID |
| [].merchantPurchaseTransaction.asset.name | string | required | 자산명 |
| [].merchantPurchaseTransaction.asset.nickname | string | required | 자산 별칭 |
| [].merchantPurchaseTransaction.asset.organization | string | required | 기관 코드 |
| [].merchantPurchaseTransaction.asset.organizationName | string | required | 기관명 |
| [].merchantPurchaseTransaction.asset.isActive | boolean | required | 활성 여부 |
| [].merchantPurchaseTransaction.asset.isPossibleDormant | boolean | required | 휴면 가능 여부 |
| [].merchantPurchaseTransaction.asset.isDormant | boolean | required | 휴면 여부 |
| [].merchantPurchaseTransaction.asset.number | string | required | 자산 번호 |
| [].merchantPurchaseTransaction.asset.owners | UserResponse[] | required | 소유자 목록 |
| [].merchantPurchaseTransaction.asset.owners.id | number | required | 유저 ID |
| [].merchantPurchaseTransaction.asset.owners.name | string | required | 유저 이름 |
| [].merchantPurchaseTransaction.asset.owners.email | string | required | 유저 이메일 |
| [].merchantPurchaseTransaction.asset.owners.isDeleted | boolean | required | 삭제 여부 |
| [].merchantPurchaseTransaction.asset.clientType | "PERSONAL" \| "CORPORATE" \| "ALL" | required | 고객 유형 |
| [].merchantPurchaseTransaction.asset.businessType | string | required | 업종 코드 |
| [].merchantPurchaseTransaction.asset.businessTypeName | string | required | 업종명 |
| [].merchantPurchaseTransaction.asset.userAutoAssignEnabled | boolean | optional | 유저 자동 할당 여부 |
| [].merchantPurchaseTransaction.asset.expirationYear | string | optional | 카드 만료 연도 |
| [].merchantPurchaseTransaction.asset.expirationMonth | string | optional | 카드 만료 월 |
| [].merchantPurchaseTransaction.asset.limitAmount | number | optional | 카드 한도 금액 |
| [].merchantPurchaseTransaction.asset.usedAmount | number | optional | 카드 사용 금액 |
| [].merchantPurchaseTransaction.asset.remainLimit | number | optional | 카드 잔여 한도 |
| [].merchantPurchaseTransaction.asset.isAutoApproval | boolean | optional | 자동 승인 여부 |
| [].merchantPurchaseTransaction.asset.previousCardNumber | string | optional | 이전 카드번호 |
| [].merchantPurchaseTransaction.asset.accountBalance | number | optional | 계좌 잔액 |
| [].merchantPurchaseTransaction.asset.originalBalance | number | optional | 원본 잔액 |
| [].merchantPurchaseTransaction.asset.currencyCode | string | optional | 통화 코드 |
| [].merchantPurchaseTransaction.asset.startDate | string | optional | 조회 시작일 |
| [].merchantPurchaseTransaction.asset.endDate | string \| null | optional | 조회 종료일 |
| [].merchantPurchaseTransaction.asset.accountType | string | optional | 계좌 유형 |
| [].merchantPurchaseTransaction.asset.isTransactionVisible | boolean | optional | 거래내역 보유 여부 |
| [].merchantPurchaseTransaction.asset.totalAmount | number | optional | 증권 총액 |
| [].merchantPurchaseTransaction.asset.depositAmount | number | optional | 증권 예수금 |
| [].merchantPurchaseTransaction.asset.foreignDepositAmount | object[] | optional | 외화예수금 상세 목록 |
| [].merchantPurchaseTransaction.asset.foreignDeposits | number | optional | 외화예수금 합계 |
| [].merchantPurchaseTransaction.asset.totalValuationAmount | number | optional | 총 평가금액 |
| [].merchantPurchaseTransaction.asset.accountNumber | string | optional | 계좌 번호 |
| [].merchantPurchaseTransaction.asset.products | Product[] | optional | 증권 보유 상품 목록 |
| [].merchantPurchaseTransaction.asset.registrationNumber | string | optional | 사업자등록번호 |
| [].merchantPurchaseTransaction.asset.establishNumber | string | optional | 개업일자 코드 |
| [].merchantPurchaseTransaction.asset.companyName | string | optional | 회사명 |
| [].merchantPurchaseTransaction.asset.ceoName | string | optional | 대표자명 |
| [].merchantPurchaseTransaction.asset.address | string | optional | 주소 |
| [].merchantPurchaseTransaction.asset.contactName | string | optional | 담당자명 |
| [].merchantPurchaseTransaction.asset.email | string | optional | 담당자 이메일 |
| [].merchantPurchaseTransaction.asset.tel | string | optional | 연락처 |
| [].merchantPurchaseTransaction.asset.businessTypes | string | optional | 업태 |
| [].merchantPurchaseTransaction.asset.businessItems | string | optional | 종목 |
| [].merchantPurchaseTransaction.asset.category | string | optional | 홈택스 카테고리 |
| [].merchantPurchaseTransaction.asset.createdAt | string | optional | 생성 시각 |
| [].merchantPurchaseTransaction.asset.updatedAt | string | optional | 수정 시각 |
| [].merchantPurchaseTransaction.asset.readCertificate | object | optional | 조회 인증서 정보 |
| [].merchantPurchaseTransaction.asset.issueCertificate | object | optional | 발행 인증서 정보 |
| [].merchantPurchaseTransaction.cardCompany | string | required | 카드사 |
| [].merchantPurchaseTransaction.cardName | string | required | 카드명 |
| [].merchantPurchaseTransaction.cardType | string | required | 카드 타입 |
| [].merchantPurchaseTransaction.cardNumber | string | required | 카드번호 |
| [].merchantPurchaseTransaction.approvalNumber | string | required | 승인번호 |
| [].merchantPurchaseTransaction.transactAt | string | required | 거래일시 |
| [].merchantPurchaseTransaction.purchaseDate | string | required | 매입일 |
| [].merchantPurchaseTransaction.purchaseAmount | number | required | 매입 금액 |
| [].merchantPurchaseTransaction.totalFee | number | required | 총 수수료 |
| [].merchantPurchaseTransaction.merchantServiceFee | number | required | 가맹점 수수료 |
| [].merchantPurchaseTransaction.pointFee | number | required | 포인트 수수료 |
| [].merchantPurchaseTransaction.etcFee | number | required | 기타 수수료 |
| [].merchantPurchaseTransaction.paidAmount | number | required | 지급 금액 |
| [].merchantPurchaseTransaction.expectedPaymentDate | string | required | 지급 예정일 |
| [].cashReceipt | CashReceipt | optional | 현금영수증 상세 |
| [].cashReceipt.id | number | required | 현금영수증 ID |
| [].cashReceipt.workspaceId | number | required | 워크스페이스 ID |
| [].cashReceipt.receiptType | "SALES" \| "PURCHASE" | required | 영수증 구분 |
| [].cashReceipt.transactAt | string | required | 거래 일시 |
| [].cashReceipt.transactDate | string | required | 국세청 승인일자 |
| [].cashReceipt.transactionType | "APPROVAL" \| "CANCEL" | required | 거래구분 |
| [].cashReceipt.taxationType | "TAXABLE" \| "NON_TAXABLE" | optional | 과세형태 |
| [].cashReceipt.deductionStatus | string | optional | 공제여부 |
| [].cashReceipt.usage | "TAX_DEDUCTION" \| "EXPENSE_PROOF" | required | 용도구분 |
| [].cashReceipt.issueType | string | optional | 발행구분 |
| [].cashReceipt.issueStatus | string | required | 발행 상태 |
| [].cashReceipt.approvalNumber | string | required | 승인번호 |
| [].cashReceipt.totalAmount | number | required | 총금액 |
| [].cashReceipt.supplyValue | number | required | 공급가액 |
| [].cashReceipt.vat | number | required | 부가세 |
| [].cashReceipt.serviceFee | number | required | 봉사료 |
| [].cashReceipt.content | string | required | 거래 내용 |
| [].cashReceipt.ntsResultCode | string | optional | 국세청 결과 코드 |
| [].cashReceipt.issuer | CashReceiptCounterParty | required | 발행자 |
| [].cashReceipt.issuer.registrationNumber | string | optional | 사업자번호 |
| [].cashReceipt.issuer.companyName | string | optional | 상호 |
| [].cashReceipt.issuer.userId | number | optional | 유저 ID |
| [].cashReceipt.issuer.identityNumber | string | optional | 식별번호 |
| [].cashReceipt.issuer.userName | string | optional | 사용자명 |
| [].cashReceipt.issuer.emails | string[] | optional | 이메일 목록 |
| [].cashReceipt.buyer | CashReceiptCounterParty | required | 구매자 |
| [].cashReceipt.buyer.registrationNumber | string | optional | 사업자번호 |
| [].cashReceipt.buyer.companyName | string | optional | 상호 |
| [].cashReceipt.buyer.userId | number | optional | 유저 ID |
| [].cashReceipt.buyer.identityNumber | string | optional | 식별번호 |
| [].cashReceipt.buyer.userName | string | optional | 사용자명 |
| [].cashReceipt.buyer.emails | string[] | optional | 이메일 목록 |
| [].cashReceipt.cancelOption | object | optional | 취소 정보 |
| [].cashReceipt.cancelOption.cancelReason | string | required | 취소 사유 |
| [].cashReceipt.cancelOption.originalApprovalNumber | string | required | 원본 승인번호 |
| [].cashReceipt.cancelOption.originalTransactDate | string | required | 원본 승인일자 |
| [].cashReceipt.cancelOption.originalCashReceiptId | number | required | 원본 현금영수증 ID |
| [].manualTransaction | ManualTransaction | optional | 수기거래 상세 |
| [].manualTransaction.id | number | required | 수기거래 ID |
| [].manualTransaction.currency | CurrencyCode | required | 통화 코드 |
| [].manualTransaction.manualTransactionType | "ETC" \| "CASH" \| "INVOICE" | required | 수기거래 타입 |
| [].manualTransaction.tradeItems | ManualTransactionTradeItem[] | required | 거래 품목 |
| [].manualTransaction.tradeItems.itemName | string | required | 품목명 |
| [].manualTransaction.tradeItems.quantity | number | required | 수량 |
| [].manualTransaction.tradeItems.taxAmount | number | required | 세액 |
| [].manualTransaction.tradeItems.unitPrice | number | required | 단가 |
| [].hasPurchaseCanceledUsages | boolean | optional | 매입 취소 사용내역 보유 여부 |
| [].ecommerceSettlementDetailTransaction | EcommerceSettlementDetailTransaction | optional | 이커머스 정산 상세 |
| [].ecommerceSettlementDetailTransaction.asset | object | required | 이커머스 스토어 자산 |
| [].ecommerceSettlementDetailTransaction.asset.id | number | optional | 자산 ID |
| [].ecommerceSettlementDetailTransaction.asset.organization | string | optional | 조직 (COUPANG, NAVER 등) |
| [].ecommerceSettlementDetailTransaction.asset.nickname | string | optional | 별칭 |
| [].ecommerceSettlementDetailTransaction.ecommerceSettlementId | number | required | 정산 ID |
| [].ecommerceSettlementDetailTransaction.settlementDate | string | required | 정산일 |
| [].ecommerceSettlementDetailTransaction.settlementRatio | number | required | 정산 비율 |
| [].ecommerceSettlementDetailTransaction.name | string | required | 항목명 |
| [].ecommerceSettlementDetailTransaction.amount | number | required | 금액 |
| [].ecommerceSettlementDetailTransaction.type | string | required | 정산 상세 타입 |
| [].ecommerceSettlementDetailTransaction.transactionType | "IN" \| "OUT" | required | 입출금 방향 |
| [].isRecommended | boolean | optional | 추천 티켓 여부 |
| [].taxType | "DETERMINE_NEEDED" \| "TAXABLE" \| "EXEMPT" \| "ZERO_RATED" \| null | optional | 과세 유형 |

### cURL Example

```bash
curl -X POST https://app.granter.biz/api/public-docs/tickets \
  -H "Content-Type: application/json" \
  -H "Authorization: Basic <BASE64(API_KEY:)>" \
  -d '{
    "ticketType": "EXPENSE_TICKET",
    "startDate": "2026-03-01",
    "endDate": "2026-03-07"
  }'
```

---

## POST /api/public-docs/assets

거래가 수집되는 자산과 연동 대상을 조회합니다.

### Request Fields

- `workspaceId`는 요청값을 받지 않고 API key에서 자동으로 결정됩니다.

- `assetType` (required, enum): CARD, BANK_ACCOUNT, HOME_TAX_ACCOUNT, CUSTOM, MERCHANT_GROUP, SECURITIES_ACCOUNT, ECOMMERCE, MANUAL

### Request Example

```json
{
  "assetType": "CARD"
}
```

### Response Example

```json
[
  {
    "id": 54,
    "assetType": "CARD",
    "number": "426586******9827",
    "workspaceId": 1,
    "name": "샘플 법인카드",
    "nickname": "운영 카드",
    "organization": "KB",
    "organizationName": "국민카드",
    "businessType": "CD",
    "businessTypeName": "CD",
    "isHidden": false,
    "isActive": true,
    "isDormant": false,
    "isPossibleDormant": false,
    "owners": [
      {
        "id": 67,
        "name": "소유자 A",
        "email": "owner-a@example.com",
        "isDeleted": false
      }
    ],
    "clientType": "CORPORATE",
    "userAutoAssignEnabled": true,
    "isFree": false,
    "hasPassword": false,
    "createdAt": "2024-12-25T13:01:51.863136",
    "modifiedAt": "2026-03-08T12:05:58.877862",
    "card": {
      "expirationYear": "2030",
      "expirationMonth": "09",
      "limitAmount": 1000000,
      "usedAmount": 0,
      "remainLimit": 0,
      "previousCardNumber": null
    },
    "bankAccount": null,
    "homeTaxAccount": null,
    "securitiesAccount": null,
    "customAsset": null,
    "ecommerceAccount": null
  }
]
```

### Response Fields (Full Expanded)

| Path | Type | Required | Description |
| --- | --- | --- | --- |
| [] | Asset | required | BE `/assets` 원본 응답 배열의 각 원소 |
| [].id | number | required | 자산 ID |
| [].assetType | AssetType | required | 자산 타입 |
| [].number | string | required | 자산 번호 |
| [].workspaceId | number | required | 워크스페이스 ID |
| [].name | string | required | 자산명 |
| [].nickname | string | required | 별칭 |
| [].organization | OrganizationType | required | 금융기관 코드 |
| [].organizationName | string | required | 금융기관명 |
| [].businessType | BusinessType | required | 업종 |
| [].businessTypeName | BusinessTypeName | required | 업종명 |
| [].isHidden | boolean | required | 숨김 여부 |
| [].isActive | boolean | required | 활성 여부 |
| [].isDormant | boolean | required | 휴면 여부 |
| [].isPossibleDormant | boolean | required | 휴면 가능 여부 |
| [].isFree | boolean | required | 무료 여부 |
| [].owners | UserResponse[] | required | 소유자 목록 |
| [].owners.id | number | required | 유저 ID |
| [].owners.name | string | required | 유저 이름 |
| [].owners.email | string | required | 유저 이메일 |
| [].owners.isDeleted | boolean | required | 삭제 여부 |
| [].clientType | ClientType | required | 고객 유형 |
| [].userAutoAssignEnabled | boolean | required | 자동 할당 여부 |
| [].card | AssetCardInfo | optional | 카드 자산 정보 |
| [].card.expirationYear | string | required | 만료 연도 |
| [].card.expirationMonth | string | required | 만료 월 |
| [].card.limitAmount | number | required | 한도 금액 |
| [].card.usedAmount | number | required | 사용 금액 |
| [].card.remainLimit | number | required | 잔여 한도 |
| [].card.previousCardNumber | string | optional | 이전 카드 번호 |
| [].bankAccount | AssetBankAccountInfo | optional | 계좌 자산 정보 |
| [].bankAccount.accountBalance | number | required | 계좌 잔액 |
| [].bankAccount.originalBalance | number | required | 원본 계좌 잔액 |
| [].bankAccount.currencyCode | string | required | 통화 코드 |
| [].bankAccount.startDate | string | required | 조회 시작일 |
| [].bankAccount.endDate | string | required | 조회 종료일 |
| [].bankAccount.accountNumber | string | required | 계좌 번호 |
| [].bankAccount.accountName | string | required | 계좌명 |
| [].bankAccount.nickName | string | required | 별칭 |
| [].bankAccount.accountType | AccountType | required | 계좌 유형 |
| [].bankAccount.isTransactionVisible | boolean | required | 거래내역 보유 여부 |
| [].homeTaxAccount | AssetHomeTaxAccountInfo | optional | 홈택스 자산 정보 |
| [].homeTaxAccount.category | "CASH_RECEIPT" \| "TAX_INVOICE" | required | 홈택스 자산 카테고리 |
| [].homeTaxAccount.registrationNumber | string | required | 사업자등록번호 |
| [].homeTaxAccount.establishNumber | string | optional | 개업일자 코드 |
| [].homeTaxAccount.companyName | string | required | 회사명 |
| [].homeTaxAccount.ceoName | string | required | 대표자명 |
| [].homeTaxAccount.address | string | required | 주소 |
| [].homeTaxAccount.contactName | string | required | 담당자명 |
| [].homeTaxAccount.tel | string | required | 연락처 |
| [].homeTaxAccount.businessTypes | string | required | 업태 |
| [].homeTaxAccount.businessItems | string | required | 종목 |
| [].homeTaxAccount.readCertificate | object | optional | 조회 인증서 |
| [].homeTaxAccount.readCertificate.id | number | required | 인증서 ID |
| [].homeTaxAccount.readCertificate.isExpired | boolean | required | 만료 여부 |
| [].homeTaxAccount.readCertificate.expiredAt | string | optional | 만료 시각 |
| [].homeTaxAccount.readCertificate.connectedAt | string | optional | 연결 시각 |
| [].homeTaxAccount.issueCertificate | object | optional | 발행 인증서 |
| [].homeTaxAccount.issueCertificate.registDateTime | string | required | 등록 시각 |
| [].homeTaxAccount.issueCertificate.expireDateTime | string | required | 만료 시각 |
| [].homeTaxAccount.issueCertificate.issuerDistinguishName | string | required | 발급자 구분명 |
| [].homeTaxAccount.issueCertificate.issuerName | string | required | 발급자명 |
| [].homeTaxAccount.issueCertificate.subjectDistinguishName | string | required | 주체 구분명 |
| [].homeTaxAccount.issueCertificate.oid | string | required | OID |
| [].homeTaxAccount.issueCertificate.registContactName | string | required | 등록자명 |
| [].homeTaxAccount.issueCertificate.registContactId | string | required | 등록자 ID |
| [].securitiesAccount | AssetSecuritiesAccountInfo | optional | 증권 자산 정보 |
| [].securitiesAccount.totalAmount | number | required | 총액(원화) |
| [].securitiesAccount.depositAmount | number | required | 예수금(원화) |
| [].securitiesAccount.foreignDepositAmount | number | required | 외화예수금(원화) |
| [].securitiesAccount.foreignDeposits | ForeignDeposit[] | required | 외화예수금 보유 목록 |
| [].securitiesAccount.foreignDeposits.id | number | required | 외화예수금 ID |
| [].securitiesAccount.foreignDeposits.currency | CurrencyCode | required | 통화 코드 |
| [].securitiesAccount.foreignDeposits.amount | number | required | 금액 |
| [].securitiesAccount.foreignDeposits.createdAt | string | required | 생성 시각 |
| [].securitiesAccount.totalValuationAmount | number | required | 보유 주식 평가액 합계(원화) |
| [].securitiesAccount.accountNumber | string | required | 계좌번호 |
| [].securitiesAccount.products | Product[] | required | 보유 투자상품 |
| [].securitiesAccount.products.id | number | required | 상품 ID |
| [].securitiesAccount.products.productType | string | required | 상품 유형 코드 |
| [].securitiesAccount.products.productTypeName | string | required | 상품 유형명 |
| [].securitiesAccount.products.name | string | required | 상품명 |
| [].securitiesAccount.products.code | string | optional | 상품 코드 |
| [].securitiesAccount.products.quantity | number | optional | 수량 |
| [].securitiesAccount.products.currency | CurrencyCode | required | 통화 코드 |
| [].securitiesAccount.products.krwPresentAmount | number | optional | 현재 금액(원화) |
| [].securitiesAccount.products.presentAmount | number | optional | 현재 금액 |
| [].securitiesAccount.products.krwValuationAmount | number | optional | 평가 금액(원화) |
| [].securitiesAccount.products.valuationAmount | number | optional | 평가 금액 |
| [].securitiesAccount.products.krwValuationPL | number | optional | 평가 손익(원화) |
| [].securitiesAccount.products.valuationPL | number | optional | 평가 손익 |
| [].securitiesAccount.products.earningsRate | number | optional | 수익률 |
| [].securitiesAccount.products.createdAt | string | required | 생성 시각 |
| [].customAsset | AssetCustomInfo | optional | 커스텀 자산 정보 |
| [].customAsset.assetType | "CASH" \| "LIABILITY" \| "ASSET" | required | 커스텀 자산 유형 |
| [].customAsset.assetNumber | string | required | 자산 번호 |
| [].customAsset.amount | number | required | 금액 |
| [].ecommerceAccount | AssetEcommerceAccountInfo | optional | 이커머스 스토어 계정 정보 |
| [].ecommerceAccount.username | string | required | 스토어 사용자명 |
| [].ecommerceAccount.isOtpVerified | boolean | required | OTP 인증 여부 |
| [].createdAt | string | optional | 생성 시각 |
| [].modifiedAt | string | optional | 수정 시각 |
| [].hasPassword | boolean | optional | 비밀번호 보유 여부 |

### cURL Example

```bash
curl -X POST https://app.granter.biz/api/public-docs/assets \
  -H "Content-Type: application/json" \
  -H "Authorization: Basic <BASE64(API_KEY:)>" \
  -d '{
    "assetType": "CARD"
  }'
```

---

## POST /api/public-docs/balances

계좌별 잔액 흐름과 현금 추이를 조회합니다.

### Request Fields

- `workspaceId`는 요청값을 받지 않고 API key에서 자동으로 결정됩니다.
- 기간 기반 조회는 요청 1회당 최대 31일까지 사용할 수 있으며, 더 긴 범위는 31일 이하 구간으로 나눠 조회한 뒤 합쳐서 사용하세요.

- `startDate` (optional, YYYY-MM-DD): 조회 시작일 (전달 시 endDate와 함께 전달 필수)
- `endDate` (optional, YYYY-MM-DD): 조회 종료일 (전달 시 startDate와 함께 전달 필수)

### Request Example

```json
{
  "startDate": "2026-03-01",
  "endDate": "2026-03-07"
}
```

### Response Example

```json
[
  {
    "bankAccount": {
      "id": 3,
      "assetType": "BANK_ACCOUNT",
      "number": "140014******561",
      "workspaceId": 1,
      "name": "기업자유예금",
      "nickname": "메인 통장",
      "organization": "SHINHAN_BANK",
      "organizationName": "신한은행",
      "businessType": "BK",
      "businessTypeName": "BK",
      "isHidden": false,
      "isActive": true,
      "isDormant": false,
      "isPossibleDormant": false,
      "owners": [
        {
          "id": 9,
          "name": "소유자 B",
          "email": "owner-b@example.com",
          "isDeleted": false
        }
      ],
      "clientType": "CORPORATE",
      "userAutoAssignEnabled": true,
      "isFree": false,
      "hasPassword": false,
      "createdAt": "2024-07-18T11:23:29.369334",
      "modifiedAt": "2026-03-08T12:06:11.683152",
      "card": null,
      "bankAccount": {
        "accountBalance": -89911970,
        "originalBalance": "-89911970",
        "currencyCode": "KRW",
        "startDate": "20240119",
        "endDate": "",
        "accountNumber": "140014******561",
        "accountName": "기업자유예금",
        "nickName": "메인 통장",
        "accountType": "DEPOSIT",
        "isTransactionVisible": true
      },
      "homeTaxAccount": null,
      "securitiesAccount": null,
      "customAsset": null,
      "ecommerceAccount": null
    },
    "balances": [
      {
        "accountBalance": -89911970,
        "baseDate": "2026-03-01",
        "originalBalance": -89911970
      },
      {
        "accountBalance": -89911970,
        "baseDate": "2026-03-02",
        "originalBalance": -89911970
      }
    ]
  }
]
```

### Response Fields (Full Expanded)

| Path | Type | Required | Description |
| --- | --- | --- | --- |
| [] | AssetBalance | required | BE `/bank-accounts/daily-balances` 응답을 계좌 기준으로 가공한 배열의 각 원소 |
| [].bankAccount | TicketAsset | required | 계좌 자산 정보 |
| [].bankAccount.id | number | required | 자산 ID |
| [].bankAccount.workspaceId | number | required | 워크스페이스 ID |
| [].bankAccount.name | string | required | 자산명 |
| [].bankAccount.nickname | string | required | 자산 별칭 |
| [].bankAccount.organization | string | required | 기관 코드 |
| [].bankAccount.organizationName | string | required | 기관명 |
| [].bankAccount.isActive | boolean | required | 활성 여부 |
| [].bankAccount.isPossibleDormant | boolean | required | 휴면 가능 여부 |
| [].bankAccount.isDormant | boolean | required | 휴면 여부 |
| [].bankAccount.number | string | required | 자산 번호 |
| [].bankAccount.owners | UserResponse[] | required | 소유자 목록 |
| [].bankAccount.owners.id | number | required | 유저 ID |
| [].bankAccount.owners.name | string | required | 유저 이름 |
| [].bankAccount.owners.email | string | required | 유저 이메일 |
| [].bankAccount.owners.isDeleted | boolean | required | 삭제 여부 |
| [].bankAccount.clientType | "PERSONAL" \| "CORPORATE" \| "ALL" | required | 고객 유형 |
| [].bankAccount.businessType | string | required | 업종 코드 |
| [].bankAccount.businessTypeName | string | required | 업종명 |
| [].bankAccount.userAutoAssignEnabled | boolean | optional | 유저 자동 할당 여부 |
| [].bankAccount.expirationYear | string | optional | 카드 만료 연도 |
| [].bankAccount.expirationMonth | string | optional | 카드 만료 월 |
| [].bankAccount.limitAmount | number | optional | 카드 한도 금액 |
| [].bankAccount.usedAmount | number | optional | 카드 사용 금액 |
| [].bankAccount.remainLimit | number | optional | 카드 잔여 한도 |
| [].bankAccount.isAutoApproval | boolean | optional | 자동 승인 여부 |
| [].bankAccount.previousCardNumber | string | optional | 이전 카드번호 |
| [].bankAccount.accountBalance | number | optional | 계좌 잔액 |
| [].bankAccount.originalBalance | number | optional | 원본 잔액 |
| [].bankAccount.currencyCode | string | optional | 통화 코드 |
| [].bankAccount.startDate | string | optional | 조회 시작일 |
| [].bankAccount.endDate | string \| null | optional | 조회 종료일 |
| [].bankAccount.accountType | string | optional | 계좌 유형 |
| [].bankAccount.isTransactionVisible | boolean | optional | 거래내역 보유 여부 |
| [].bankAccount.totalAmount | number | optional | 증권 총액 |
| [].bankAccount.depositAmount | number | optional | 증권 예수금 |
| [].bankAccount.foreignDepositAmount | object[] | optional | 외화예수금 상세 목록 |
| [].bankAccount.foreignDeposits | number | optional | 외화예수금 합계 |
| [].bankAccount.totalValuationAmount | number | optional | 총 평가금액 |
| [].bankAccount.accountNumber | string | optional | 계좌 번호 |
| [].bankAccount.products | Product[] | optional | 증권 보유 상품 목록 |
| [].bankAccount.registrationNumber | string | optional | 사업자등록번호 |
| [].bankAccount.establishNumber | string | optional | 개업일자 코드 |
| [].bankAccount.companyName | string | optional | 회사명 |
| [].bankAccount.ceoName | string | optional | 대표자명 |
| [].bankAccount.address | string | optional | 주소 |
| [].bankAccount.contactName | string | optional | 담당자명 |
| [].bankAccount.email | string | optional | 담당자 이메일 |
| [].bankAccount.tel | string | optional | 연락처 |
| [].bankAccount.businessTypes | string | optional | 업태 |
| [].bankAccount.businessItems | string | optional | 종목 |
| [].bankAccount.category | string | optional | 홈택스 카테고리 |
| [].bankAccount.createdAt | string | optional | 생성 시각 |
| [].bankAccount.updatedAt | string | optional | 수정 시각 |
| [].bankAccount.readCertificate | object | optional | 조회 인증서 정보 |
| [].bankAccount.issueCertificate | object | optional | 발행 인증서 정보 |
| [].balances | BalanceByDate[] | required | 기간별 잔액 목록 |
| [].balances.baseDate | YYYY-MM-DD | required | 잔액 기준일 |
| [].balances.accountBalance | number | required | 계좌 잔액 (원화) |
| [].balances.originalBalance | number | required | 원본 통화 기준 잔액 |

### cURL Example

```bash
curl -X POST https://app.granter.biz/api/public-docs/balances \
  -H "Content-Type: application/json" \
  -H "Authorization: Basic <BASE64(API_KEY:)>" \
  -d '{
    "startDate": "2026-03-01",
    "endDate": "2026-03-07"
  }'
```

---

## POST /api/public-docs/exchange-rates

기준일의 통화별 환율 정보를 조회합니다.

### Request Fields

- `workspaceId`는 요청값을 받지 않고 API key에서 자동으로 결정됩니다.
- `baseDate`는 선택값이며, 미입력 시 백엔드 기본 기준일을 사용합니다.

- `baseDate` (optional, YYYY-MM-DD): 환율 기준일

### Request Example

```json
{
  "baseDate": "2026-03-31"
}
```

### Response Example

```json
[
  {
    "currencyCode": "USD",
    "presentName": "미국 달러",
    "baseForeignAmount": 1,
    "krwAmount": 1472.4,
    "baseDate": "2026-03-31"
  },
  {
    "currencyCode": "JPY",
    "presentName": "일본 엔",
    "baseForeignAmount": 100,
    "krwAmount": 985.31,
    "baseDate": "2026-03-31"
  }
]
```

### Response Fields (Full Expanded)

| Path | Type | Required | Description |
| --- | --- | --- | --- |
| [] | ExchangeRate | required | 통화별 환율 배열 |
| [].currencyCode | string | required | 통화 코드 |
| [].presentName | string | required | 통화 이름 |
| [].baseForeignAmount | number | required | 기준 외화 금액 |
| [].krwAmount | number | required | 원화 금액 |
| [].baseDate | YYYY-MM-DD | required | 기준 날짜 |

### cURL Example

```bash
curl -X POST https://app.granter.biz/api/public-docs/exchange-rates \
  -H "Content-Type: application/json" \
  -H "Authorization: Basic <BASE64(API_KEY:)>" \
  -d '{
    "baseDate": "2026-03-31"
  }'
```

---

## GET /api/public-docs/tags

거래를 큰 범주로 묶는 1차 태그 기준 데이터를 조회합니다.

### Request Fields

- `workspaceId`는 요청값을 받지 않고 API key에서 자동으로 결정됩니다.
- 요청 body는 사용하지 않습니다.

### Response Example

```json
[
  {
    "id": 82,
    "name": "프로젝트 A",
    "description": "2025.06.19 ~ 2026.01.05 프로젝트 기준 태그",
    "isHidden": false,
    "createdAt": "2025-09-27T19:48:29.285999",
    "modifiedAt": "2026-02-15T11:59:13.743678"
  }
]
```

### Response Fields (Full Expanded)

| Path | Type | Required | Description |
| --- | --- | --- | --- |
| [] | Tag | required | BE `/tags` 원본 응답 배열의 각 원소 |
| [].id | number | required | 1차/2차 태그 ID |
| [].name | string | required | 1차/2차 태그명 |
| [].description | string | required | 설명 |
| [].createdAt | string | required | 생성 시각 |
| [].modifiedAt | string | required | 수정 시각 |
| [].type | "Tag" \| "TagDetail" \| "Supplier" | required | 태그 분류 타입 (Tag=1차 태그, TagDetail=2차 태그) |
| [].workspaceId | number | optional | 워크스페이스 ID |
| [].tagId | number | optional | 상위 1차 태그 ID (2차 태그에 해당) |
| [].code | string \| number | optional | 코드 |
| [].outAmount | number | optional | 지출 집계 금액 |
| [].inAmount | number | optional | 입금 집계 금액 |
| [].isHidden | number \| boolean | optional | 숨김 여부 |

### cURL Example

```bash
curl -X GET https://app.granter.biz/api/public-docs/tags \
  -H "Authorization: Basic <BASE64(API_KEY:)>"
```

---

## GET /api/public-docs/tag-details

1차 태그 아래의 세부 분류 기준 데이터를 조회합니다.

### Request Fields

- `workspaceId`는 요청값을 받지 않고 API key에서 자동으로 결정됩니다.
- 요청 body는 사용하지 않습니다.

### Response Example

```json
[
  {
    "id": 5,
    "workspaceId": 1,
    "name": "마케팅 비용",
    "description": "",
    "tagId": 82,
    "isHidden": false,
    "createdAt": "2024-11-11T19:58:19.541909",
    "modifiedAt": "2026-01-30T14:38:32.668294"
  }
]
```

### Response Fields (Full Expanded)

| Path | Type | Required | Description |
| --- | --- | --- | --- |
| [] | Tag | required | BE `/tag-details` 원본 응답 배열의 각 원소 |
| [].id | number | required | 1차/2차 태그 ID |
| [].name | string | required | 1차/2차 태그명 |
| [].description | string | required | 설명 |
| [].createdAt | string | required | 생성 시각 |
| [].modifiedAt | string | required | 수정 시각 |
| [].type | "Tag" \| "TagDetail" \| "Supplier" | required | 태그 분류 타입 (Tag=1차 태그, TagDetail=2차 태그) |
| [].workspaceId | number | optional | 워크스페이스 ID |
| [].tagId | number | optional | 상위 1차 태그 ID (2차 태그에 해당) |
| [].code | string \| number | optional | 코드 |
| [].outAmount | number | optional | 지출 집계 금액 |
| [].inAmount | number | optional | 입금 집계 금액 |
| [].isHidden | number \| boolean | optional | 숨김 여부 |

### cURL Example

```bash
curl -X GET https://app.granter.biz/api/public-docs/tag-details \
  -H "Authorization: Basic <BASE64(API_KEY:)>"
```

---

## GET /api/public-docs/categories

회계 처리와 분석에 사용하는 계정과목 기준 데이터를 조회합니다.

### Request Fields

- `workspaceId`는 요청값을 받지 않고 API key에서 자동으로 결정됩니다.
- 요청 body는 사용하지 않습니다.

### Response Example

```json
[
  {
    "id": 285,
    "name": "가수금",
    "subInfo": "부채",
    "description": "계정과목이 명확치 않은 현금 입금액",
    "code": "257",
    "type": "COMMON",
    "isHidden": false,
    "costType": "VARIABLE",
    "isFavorite": false,
    "taxType": null
  }
]
```

### Response Fields (Full Expanded)

| Path | Type | Required | Description |
| --- | --- | --- | --- |
| [] | ExpenseCategory | required | BE `/expense-categories` 원본 응답 배열의 각 원소 |
| [].id | number | required | 계정과목 ID |
| [].name | string | required | 계정과목명 |
| [].subInfo | string | required | 부가 정보 |
| [].description | string | required | 설명 |
| [].code | number \| string | required | 코드 |
| [].type | "COMMON" \| "CUSTOM" | required | 카테고리 타입 |
| [].isHidden | boolean | required | 숨김 여부 |
| [].costType | "NONE" \| "VARIABLE" \| "FIXED" | required | 비용 유형 |
| [].isFavorite | boolean | required | 즐겨찾기 여부 |
| [].workspaceId | number | optional | 워크스페이스 ID |
| [].taxType | "DETERMINE_NEEDED" \| "TAXABLE" \| "EXEMPT" \| "ZERO_RATED" \| null | optional | 기본 과세 유형 |

### cURL Example

```bash
curl -X GET https://app.granter.biz/api/public-docs/categories \
  -H "Authorization: Basic <BASE64(API_KEY:)>"
```

---

## GET /api/public-docs/people

담당자, 결재자, 분류 주체로 활용되는 워크스페이스 구성원 데이터를 조회합니다.

### Request Fields

- `workspaceId`는 요청값을 받지 않고 API key에서 자동으로 결정됩니다.
- 요청 body는 사용하지 않습니다.

### Response Example

```json
[
  {
    "id": 4,
    "name": "구성원 A",
    "email": "member-a@example.com",
    "workspaceId": 1,
    "workspaceName": "샘플 워크스페이스",
    "teamId": 133,
    "teamName": "마케팅팀",
    "nickname": "담당자 A",
    "phoneNumber": "01012345678",
    "role": {
      "id": 1,
      "name": "소유자",
      "isOwner": true,
      "isDefault": false
    },
    "cards": [],
    "notificationConfig": {
      "userId": 4,
      "slack": false,
      "appPush": false,
      "email": false,
      "kakao": false
    }
  }
]
```

### Response Fields (Full Expanded)

| Path | Type | Required | Description |
| --- | --- | --- | --- |
| [] | User | required | BE `/workspaces` 응답의 `users` 배열 원본 |
| [].id | number | required | 유저 ID |
| [].name | string | required | 이름 |
| [].email | string | required | 이메일 |
| [].workspaceId | number | optional | 워크스페이스 ID |
| [].workspaceName | string | optional | 워크스페이스명 |
| [].teamId | number | optional | 팀 ID |
| [].teamName | string | optional | 팀명 |
| [].phoneNumber | string | optional | 전화번호 |
| [].role | WorkspaceRole | required | 워크스페이스 역할 |
| [].role.id | number | required | 역할 ID |
| [].role.name | string | required | 역할명 |
| [].role.isOwner | boolean | required | 오너 여부 |
| [].role.isDefault | boolean | required | 기본 역할 여부 |

### cURL Example

```bash
curl -X GET https://app.granter.biz/api/public-docs/people \
  -H "Authorization: Basic <BASE64(API_KEY:)>"
```

---

## GET /api/public-docs/contacts

거래처, 고객사, 공급사처럼 상대방 기준으로 관리하는 거래처 데이터를 조회합니다.

### Request Fields

- `workspaceId`는 요청값을 받지 않고 API key에서 자동으로 결정됩니다.
- 요청 body는 사용하지 않습니다.
- 현재 응답은 최신 거래처 목록 기준으로 최대 100건을 조회합니다.

### Response Example

```json
[
  {
    "id": 928,
    "workspaceId": 1,
    "registrationNumber": "1111111111",
    "companyName": "샘플 거래처",
    "ceoName": "대표자 A",
    "businessTypes": "",
    "businessItems": "",
    "businessPlace": "",
    "email": null,
    "email2": null,
    "phone": null,
    "tel": null,
    "bankName": null,
    "bankAccountOwnerName": null,
    "accountNumber": null,
    "lastIssuedAt": "2026-02-19T16:32:29.514778",
    "managers": [],
    "region": "DOMESTIC",
    "nation": "",
    "isHidden": false,
    "memo": "",
    "isFavorite": false
  }
]
```

### Response Fields (Full Expanded)

| Path | Type | Required | Description |
| --- | --- | --- | --- |
| [] | Contact | required | BE `/contacts` 원본 응답 배열의 각 원소 |
| [].id | number | required | 거래처 ID |
| [].workspaceId | number | required | 워크스페이스 ID |
| [].registrationNumber | string | required | 사업자등록번호 |
| [].companyName | string | required | 회사명 |
| [].ceoName | string | required | 대표자명 |
| [].businessTypes | string | required | 업태 |
| [].businessItems | string | required | 종목 |
| [].businessPlace | string | required | 사업장 주소 |
| [].email | string \| null | optional | 이메일 |
| [].email2 | string \| null | optional | 보조 이메일 |
| [].phone | string \| null | optional | 휴대전화 |
| [].tel | string \| null | optional | 전화번호 |
| [].lastIssuedAt | string | required | 최근 발행 시각 |
| [].region | "DOMESTIC" \| "FOREIGN" | optional | 거래처 지역 구분 |
| [].isHidden | boolean | optional | 숨김 여부 |
| [].managers | ContactManager[] | optional | 거래처 담당자 목록 |
| [].managers.id | number | required | 거래처 담당자 ID |
| [].managers.contactId | number | required | 거래처 ID |
| [].managers.name | string | required | 담당자 이름 |
| [].managers.phoneNumber | string \| null | optional | 휴대전화 |
| [].managers.telNumber | string \| null | optional | 전화번호 |
| [].managers.faxNumber | string \| null | optional | 팩스번호 |
| [].managers.email | string \| null | optional | 이메일 |
| [].managers.memo | string | required | 메모 |
| [].nation | string | optional | 국가 |
| [].accountNumber | string \| null | optional | 계좌번호 |
| [].bankName | string \| null | optional | 은행명 |
| [].bankAccountOwnerName | string \| null | optional | 예금주명 |
| [].memo | string | optional | 메모 |
| [].isFavorite | boolean | optional | 즐겨찾기 여부 |

### cURL Example

```bash
curl -X GET https://app.granter.biz/api/public-docs/contacts \
  -H "Authorization: Basic <BASE64(API_KEY:)>"
```

---

## GET /api/public-docs/teams

구성원을 조직 단위로 묶어 관리하는 팀 기준 데이터를 조회합니다.

### Request Fields

- `workspaceId`는 요청값을 받지 않고 API key에서 자동으로 결정됩니다.
- 요청 body는 사용하지 않습니다.

### Response Example

```json
[
  {
    "id": 6,
    "name": "인사팀"
  }
]
```

### Response Fields (Full Expanded)

| Path | Type | Required | Description |
| --- | --- | --- | --- |
| [] | Team | required | BE `/teams` 원본 응답 배열의 각 원소 |
| [].id | number | required | 팀 ID |
| [].name | string | required | 팀명 |

### cURL Example

```bash
curl -X GET https://app.granter.biz/api/public-docs/teams \
  -H "Authorization: Basic <BASE64(API_KEY:)>"
```

---

## GET /api/public-docs/workflows

기안 문서, 결재 상태, 결재선 정보를 포함한 전자결재 데이터를 조회합니다.

### Request Fields

- `workspaceId`는 요청값을 받지 않고 API key에서 자동으로 결정됩니다.
- 요청 body는 사용하지 않습니다.
- 직접 호출 시 `status`, `draftedByMe`, `limit`, `offset` query string을 사용할 수 있습니다.
- 테스트 패널은 기본 목록 조회를 수행합니다.

- `status` (optional, "REQUESTED" | "APPROVED" | "REJECTED" | "COMPLETED"): 결재 상태 필터
- `draftedByMe` (optional, boolean): 내가 올린 결재만 조회할지 여부
- `limit` (optional, number): 조회 건수 제한. 지정하지 않으면 최대 100건까지 조회합니다.
- `offset` (optional, number): 페이지 오프셋

### Response Example

```json
[
  {
    "id": 333,
    "workspaceId": 1,
    "user": {
      "id": 8,
      "name": "기안자 A",
      "email": "drafter@example.com",
      "isDeleted": false
    },
    "title": "3월 운영비 집행",
    "content": "<p>3월 운영비 집행 승인 요청입니다.</p>",
    "amount": 50000,
    "originalAmount": 50000,
    "expectedPaymentDate": "2026-02-11",
    "status": "COMPLETED",
    "currentStep": 1,
    "currency": "KRW",
    "createdAt": "2026-02-11T11:12:14.478782",
    "messages": [
      {
        "id": 878,
        "user": {
          "id": 8,
          "name": "기안자 A",
          "email": "drafter@example.com",
          "isDeleted": false
        },
        "content": "1차 결재가 승인되었습니다.",
        "type": "SYSTEM",
        "createdAt": "2026-02-11T11:12:25.120704",
        "deletedAt": null
      },
      {
        "id": 879,
        "user": {
          "id": 8,
          "name": "기안자 A",
          "email": "drafter@example.com",
          "isDeleted": false
        },
        "content": "결재 상태가 결재완료로 변경되었습니다.",
        "type": "SYSTEM",
        "createdAt": "2026-02-11T11:12:27.811770",
        "deletedAt": null
      }
    ],
    "attachments": [],
    "refundAccount": null,
    "steps": [
      {
        "id": 573,
        "approver": {
          "id": 8,
          "name": "결재자 A",
          "email": "approver@example.com",
          "isDeleted": false
        },
        "step": 1,
        "status": "APPROVED",
        "createdAt": "2026-02-11T11:12:14.483147",
        "modifiedAt": "2026-02-11T11:12:25.137155"
      }
    ],
    "reviewers": [],
    "tag": null,
    "tagId": null,
    "modifiedAt": "2026-02-11T11:12:27.813271"
  }
]
```

### Response Fields (Full Expanded)

| Path | Type | Required | Description |
| --- | --- | --- | --- |
| [] | Workflow | required | BE `/workflows` 원본 응답 배열의 각 원소 |
| [].id | number | required | 전자결재 ID |
| [].workspaceId | number | required | 워크스페이스 ID |
| [].title | string | required | 결재안 제목 |
| [].content | string | required | 결재 내용 |
| [].amount | number | required | 결재 금액 |
| [].originalAmount | number | required | 원본 결재 금액 |
| [].expectedPaymentDate | YYYY-MM-DD | required | 이체 요청일 |
| [].status | "REQUESTED" \| "APPROVED" \| "REJECTED" \| "COMPLETED" | required | 결재 상태 |
| [].currency | string | required | 통화 코드 |
| [].createdAt | ISO-8601 string | required | 결재 요청 시각 |
| [].modifiedAt | ISO-8601 string | required | 최종 수정 시각 |
| [].currentStep | number | required | 현재 결재 차수 |
| [].tag | string \| null | optional | 연결된 태그명 |
| [].tagId | number \| null | optional | 연결된 태그 ID |
| [].user | User | required | 기안자 정보 |
| [].user.id | number | required | 기안자 ID |
| [].user.name | string | required | 기안자 이름 |
| [].user.email | string | required | 기안자 이메일 |
| [].user.isDeleted | boolean | required | 삭제 여부 |
| [].refundAccount | RefundAccount | optional | 환급 계좌 정보 |
| [].refundAccount.accountHolder | string | required | 예금주명 |
| [].refundAccount.accountNumber | string | required | 계좌번호 |
| [].refundAccount.organizationName | string | required | 은행명 |
| [].steps | WorkflowStep[] | required | 결재 단계 목록 |
| [].steps.id | number | required | 결재 단계 ID |
| [].steps.step | number | required | 결재 차수 |
| [].steps.status | "REQUESTED" \| "APPROVED" \| "REJECTED" \| "COMPLETED" | required | 결재 단계 상태 |
| [].steps.createdAt | ISO-8601 string | required | 생성 시각 |
| [].steps.modifiedAt | ISO-8601 string | required | 수정 시각 |
| [].steps.approver | Approver | required | 결재자 정보 |
| [].steps.approver.id | number | required | 결재자 ID |
| [].steps.approver.name | string | required | 결재자 이름 |
| [].steps.approver.email | string | required | 결재자 이메일 |
| [].steps.approver.isDeleted | boolean | required | 삭제 여부 |
| [].reviewers | Reviewer[] | required | 검토자 목록 |
| [].reviewers.id | number | required | 검토자 연결 ID |
| [].reviewers.reviewer | UserResponse | required | 검토자 정보 |
| [].reviewers.reviewer.id | number | required | 유저 ID |
| [].reviewers.reviewer.name | string | required | 유저 이름 |
| [].reviewers.reviewer.email | string | required | 유저 이메일 |
| [].reviewers.reviewer.isDeleted | boolean | required | 삭제 여부 |
| [].messages | WorkflowMessage[] | optional | 결재 메시지 목록 |
| [].messages.id | number | required | 메시지 ID |
| [].messages.content | string | required | 메시지 내용 |
| [].messages.type | string | required | 메시지 타입 |
| [].messages.createdAt | ISO-8601 string | required | 생성 시각 |
| [].messages.deletedAt | ISO-8601 string \| null | optional | 삭제 시각 |
| [].messages.user | UserResponse | required | 작성자 정보 |
| [].messages.user.id | number | required | 유저 ID |
| [].messages.user.name | string | required | 유저 이름 |
| [].messages.user.email | string | required | 유저 이메일 |
| [].messages.user.isDeleted | boolean | required | 삭제 여부 |
| [].attachments | WorkflowAttachment[] | optional | 첨부파일 목록 |
| [].attachments.id | number | required | 첨부파일 ID |
| [].attachments.name | string | required | 파일명 |
| [].attachments.uploadedUrl | string | required | 업로드 URL |
| [].attachments.contentType | string | required | MIME 타입 |

### cURL Example

```bash
curl -X GET "https://app.granter.biz/api/public-docs/workflows?status=REQUESTED&limit=100" \
  -H "Authorization: Basic <BASE64(API_KEY:)>"
```

---

## POST /api/public-docs/salary-histories

직원별 급여 내역, 지급 상태, 보험료, 수당·공제 상세를 조회합니다.

### Request Fields

- `workspaceId`는 요청값을 받지 않고 API key에서 자동으로 결정됩니다.
- 급여는 티켓과 분리된 독립 도메인입니다.
- 조회 시 `startDate`, `endDate`는 필수입니다.
- 기간 기반 조회는 요청 1회당 최대 31일까지 사용할 수 있으며, 더 긴 범위는 31일 이하 구간으로 나눠 조회한 뒤 합쳐서 사용하세요.
- `employmentType`, `salaryPaymentStatus`, `employeeId`, `keyword`는 선택 필터입니다.

- `startDate` (required, YYYY-MM-DD): 조회 시작일
- `endDate` (required, YYYY-MM-DD): 조회 종료일
- `employmentType` (optional, "REGULAR_EMPLOYEE" | "NON_REGULAR_EMPLOYEE" | "SPECIAL_EMPLOYEE" | "REPRESENTATIVE" | array): 고용 형태 필터. 단일 값 또는 배열을 전달할 수 있습니다.
- `salaryPaymentStatus` (optional, "PENDING" | "REVIEWED" | "PAID" | array): 급여 지급 상태 필터. 단일 값 또는 배열을 전달할 수 있습니다.
- `employeeId` (optional, number): 직원 ID 필터
- `keyword` (optional, string): 직원명 또는 설명 키워드 검색

### Request Example

```json
{
  "startDate": "2026-02-01",
  "endDate": "2026-02-28"
}
```

### Response Example

```json
[
  {
    "id": 303,
    "workspaceId": 1,
    "employee": {
      "id": 67,
      "name": "직원 A",
      "email": "employee-a@example.com",
      "isDeleted": false
    },
    "employeeName": "직원 A",
    "employeeEmail": null,
    "employmentType": "REGULAR_EMPLOYEE",
    "totalAmount": 5000000,
    "paymentDate": "2026-02-01",
    "mealAmount": 3000,
    "bonusAmount": 10000,
    "pensionInsuranceAmount": 261000,
    "healthInsuranceAmount": 198080,
    "employInsuranceAmount": 49580,
    "careInsuranceAmount": 26020,
    "bank": "국민은행",
    "bankAccount": "123-45-****",
    "recognizedAmount": 5003000,
    "netAmount": 4519877,
    "description": "",
    "employmentDiscount": "NONE",
    "incomeTaxAmount": 405590,
    "localIncomeTaxAmount": 40550,
    "salaryPaymentStatus": "PENDING",
    "isEmailNotified": false,
    "emailNotifiedAt": null,
    "messageCount": 0,
    "scheduledWorkHours": 28,
    "shortfallHours": 0,
    "unpaidLeaveHours": 0,
    "recognizedWorkHours": 28,
    "allowanceAmount": 500000,
    "appliedRatioType": "",
    "appliedRatio": 1,
    "taxExemptions": [{ "title": "비과세급", "amount": 3000 }],
    "incentives": [{ "title": "상여금", "amount": 10000 }],
    "allowances": [{ "title": "직책수당", "amount": 500000 }],
    "deductions": [{ "title": "기타 공제", "amount": 12303 }],
    "createdAt": "2026-02-11T14:02:48.545431",
    "modifiedAt": "2026-02-28T14:22:59.327028"
  }
]
```

### Response Fields (Full Expanded)

| Path | Type | Required | Description |
| --- | --- | --- | --- |
| [] | SalaryHistory | required | BE `/salary-histories` 원본 응답 배열의 각 원소 |
| [].id | number | required | 급여 이력 ID |
| [].workspaceId | number | required | 워크스페이스 ID |
| [].employee | UserResponse | optional | 직원 정보 |
| [].employee.id | number | required | 유저 ID |
| [].employee.name | string | required | 유저 이름 |
| [].employee.email | string | required | 유저 이메일 |
| [].employee.isDeleted | boolean | required | 삭제 여부 |
| [].employeeId | number | required | 직원 ID |
| [].employeeName | string | optional | 직원명 |
| [].employeeEmail | string \| null | optional | 직원 이메일 |
| [].employmentType | "REGULAR_EMPLOYEE" \| "NON_REGULAR_EMPLOYEE" \| "SPECIAL_EMPLOYEE" \| "REPRESENTATIVE" | required | 고용 형태 |
| [].totalAmount | number | required | 총급여 |
| [].recognizedAmount | number | required | 실질인정금액 |
| [].paymentDate | YYYY-MM-DD | required | 지급일 |
| [].mealAmount | number | required | 비과세 식대 |
| [].bonusAmount | number | required | 상여금 |
| [].allowanceAmount | number | required | 수당 합계 |
| [].pensionInsuranceAmount | number | required | 국민연금 |
| [].healthInsuranceAmount | number | required | 건강보험 |
| [].employInsuranceAmount | number | required | 고용보험 |
| [].careInsuranceAmount | number | required | 장기요양보험 |
| [].bank | string | optional | 은행명 |
| [].bankAccount | string | optional | 은행 계좌번호 |
| [].netAmount | number | required | 실지급액 |
| [].description | string | optional | 설명 |
| [].employmentDiscount | "NONE" \| "GENERAL" \| "YOUTH" | required | 고용 할인 유형 |
| [].incomeTaxAmount | number | optional | 소득세 |
| [].localIncomeTaxAmount | number | optional | 지방소득세 |
| [].salaryPaymentStatus | "PENDING" \| "REVIEWED" \| "PAID" | required | 급여 지급 상태 |
| [].isEmailNotified | boolean | required | 명세서 email 발송 여부 |
| [].emailNotifiedAt | ISO-8601 string \| null | optional | email 발송 시각 |
| [].messageCount | number | required | 메시지 개수 |
| [].scheduledWorkHours | number | required | 당월 근무 시간 |
| [].shortfallHours | number | required | 미달 근무 시간 |
| [].unpaidLeaveHours | number | optional | 무급휴가 시간 |
| [].recognizedWorkHours | number | required | 근무 인정 시간 |
| [].appliedRatioType | string | optional | 적용 비율 타입 |
| [].appliedRatio | number | optional | 적용 비율 |
| [].taxExemptions | TitleAmount[] | optional | 비과세급 상세 |
| [].taxExemptions.title | string | required | 항목명 |
| [].taxExemptions.amount | number | required | 금액 |
| [].incentives | TitleAmount[] | optional | 인센티브 상세 |
| [].incentives.title | string | required | 항목명 |
| [].incentives.amount | number | required | 금액 |
| [].allowances | TitleAmount[] | optional | 수당 상세 |
| [].allowances.title | string | required | 항목명 |
| [].allowances.amount | number | required | 금액 |
| [].deductions | TitleAmount[] | optional | 공제 상세 |
| [].deductions.title | string | required | 항목명 |
| [].deductions.amount | number | required | 금액 |
| [].createdAt | ISO-8601 string | required | 생성 시각 |
| [].modifiedAt | ISO-8601 string | required | 수정 시각 |

### cURL Example

```bash
curl -X POST https://app.granter.biz/api/public-docs/salary-histories \
  -H "Content-Type: application/json" \
  -H "Authorization: Basic <BASE64(API_KEY:)>" \
  -d '{
    "startDate": "2026-02-01",
    "endDate": "2026-02-28",
    "employmentType": "REGULAR_EMPLOYEE",
    "salaryPaymentStatus": "PENDING"
  }'
```

---

## GET /api/public-docs/inventory-products

품목명, 단가, 통화, 추가 속성으로 구성된 품목 마스터를 조회합니다.

### Request Fields

- `workspaceId`는 요청값을 받지 않고 API key에서 자동으로 결정됩니다.
- 요청 body는 사용하지 않습니다.
- 직접 호출 시 `limit`, `lastSeenId` query string을 사용할 수 있습니다.

- `limit` (optional, number): 조회 건수 제한
- `lastSeenId` (optional, number): 페이지네이션 기준 ID

### Response Example

```json
[
  {
    "id": 63,
    "name": "샘플 품목 A",
    "unitPrice": 123,
    "currency": "KRW",
    "additionalProperties": [
      { "id": 23, "key": "EXP date", "value": "" },
      { "id": 22, "key": "창고", "value": "" },
      { "id": 13, "key": "커스텀속성2", "value": "" }
    ]
  }
]
```

### Response Fields (Full Expanded)

| Path | Type | Required | Description |
| --- | --- | --- | --- |
| [] | InventoryProduct | required | BE `/inventory-products` 원본 응답 배열의 각 원소 |
| [].id | number | required | 품목 ID |
| [].name | string | required | 품목명 |
| [].unitPrice | number | required | 단가 |
| [].currency | CurrencyCode | required | 통화 코드 |
| [].additionalProperties | InventoryAdditionalProperty[] | optional | 추가 속성 목록 |
| [].additionalProperties.id | number | required | 추가 속성 ID |
| [].additionalProperties.key | string | required | 속성명 |
| [].additionalProperties.value | string | required | 속성값 |

### cURL Example

```bash
curl -X GET "https://app.granter.biz/api/public-docs/inventory-products?limit=100" \
  -H "Authorization: Basic <BASE64(API_KEY:)>"
```

---

## GET /api/public-docs/inventories

품목별 현재 재고 수량, 재고 메시지 수, 최근 수정 시각을 포함한 재고 현황을 조회합니다.

### Request Fields

- `workspaceId`는 요청값을 받지 않고 API key에서 자동으로 결정됩니다.
- 요청 body는 사용하지 않습니다.
- 직접 호출 시 `limit`, `lastSeenId` query string을 사용할 수 있습니다.

- `limit` (optional, number): 조회 건수 제한
- `lastSeenId` (optional, number): 페이지네이션 기준 ID

### Response Example

```json
[
  {
    "id": 3,
    "inventoryProductId": 2,
    "name": "샘플 재고 품목 A",
    "unitPrice": 880000,
    "quantity": 10,
    "currency": "KRW",
    "createdAt": "2025-12-05T12:29:29.850909",
    "modifiedAt": "2026-02-11T13:05:46.900783",
    "messageCount": 0,
    "additionalProperties": [
      { "id": 18, "key": "1211", "value": "123123" },
      { "id": 14, "key": "123", "value": "123" },
      { "id": 22, "key": "창고", "value": "" }
    ]
  }
]
```

### Response Fields (Full Expanded)

| Path | Type | Required | Description |
| --- | --- | --- | --- |
| [] | InventoryStock | required | BE `/inventories` 원본 응답 배열의 각 원소 |
| [].id | number | required | 재고 엔트리 ID |
| [].inventoryProductId | number | required | 품목 ID |
| [].name | string | required | 품목명 |
| [].unitPrice | number | required | 단가 |
| [].quantity | number | required | 현재 재고 수량 |
| [].currency | CurrencyCode | required | 통화 코드 |
| [].createdAt | ISO-8601 string | required | 생성 시각 |
| [].modifiedAt | ISO-8601 string | required | 수정 시각 |
| [].messageCount | number | required | 메시지 개수 |
| [].additionalProperties | InventoryAdditionalProperty[] | optional | 추가 속성 목록 |
| [].additionalProperties.id | number | required | 추가 속성 ID |
| [].additionalProperties.key | string | required | 속성명 |
| [].additionalProperties.value | string | required | 속성값 |

### cURL Example

```bash
curl -X GET "https://app.granter.biz/api/public-docs/inventories?limit=100" \
  -H "Authorization: Basic <BASE64(API_KEY:)>"
```

---

## POST /api/public-docs/attendances

구성원의 출퇴근 기록과 휴가 사용 내역을 기간별로 조회합니다.

### Request Fields

- `workspaceId`는 요청값을 받지 않고 API key에서 자동으로 결정됩니다.
- 조회 시 `startDate`, `endDate`는 필수입니다.
- 기간 기반 조회는 요청 1회당 최대 31일까지 사용할 수 있으며, 더 긴 범위는 31일 이하 구간으로 나눠 조회한 뒤 합쳐서 사용하세요.
- `userIds`로 특정 구성원만 필터링하거나, `recordType`으로 근무/휴가를 구분할 수 있습니다.

- `startDate` (required, YYYY-MM-DD): 조회 시작일
- `endDate` (required, YYYY-MM-DD): 조회 종료일
- `userIds` (optional, number[]): 조회 대상 유저 ID 목록
- `recordType` (optional, "WORK" | "LEAVE"): 기록 타입 필터. WORK: 근무만, LEAVE: 휴가만, 미입력 시 전체

### Request Example

```json
{
  "startDate": "2026-03-01",
  "endDate": "2026-03-15"
}
```

### Response Example

```json
[
  {
    "timeCard": {
      "workspaceId": 1,
      "userId": 67,
      "baseDate": "2026-03-10",
      "startedAt": "2026-03-10T09:00:00",
      "endedAt": "2026-03-10T18:00:00",
      "createdAt": "2026-03-10T09:00:12.345678",
      "modifiedAt": "2026-03-10T18:00:45.123456"
    },
      "workRecord": {
        "workRecordId": 101,
        "clockInLocation": {
          "ip": "192.168.1.10",
          "address": "서울특별시 강남구",
        "addressDetail": "테헤란로 123",
        "latitude": 37.5065,
        "longitude": 127.0536,
        "radius": 200.0
      },
      "clockOutLocation": {
        "ip": "192.168.1.10",
        "address": "서울특별시 강남구",
        "addressDetail": "테헤란로 123",
        "latitude": 37.5065,
          "longitude": 127.0536,
          "radius": 200.0
        },
        "isRemote": false,
        "breakStartedAt": "2026-03-10T12:00:00",
        "breakEndedAt": "2026-03-10T13:00:00",
        "additionalBreakTimes": [
          {
            "startedAt": "2026-03-10T18:00:00",
            "endedAt": "2026-03-10T18:30:00"
          }
        ]
      },
    "leaveRecord": null
  },
  {
    "timeCard": {
      "workspaceId": 1,
      "userId": 67,
      "baseDate": "2026-03-12",
      "startedAt": "2026-03-12T00:00:00",
      "endedAt": "2026-03-12T23:59:59",
      "createdAt": "2026-03-11T15:30:00.000000",
      "modifiedAt": "2026-03-12T10:00:00.000000"
    },
    "workRecord": null,
    "leaveRecord": {
      "leaveRecordId": 55,
      "approverUserId": 4,
      "reason": "개인 사유",
      "status": "APPROVED",
      "approvedAt": "2026-03-11T16:00:00",
      "rejectedAt": null,
      "rejectedReason": null,
      "createdAt": "2026-03-11T15:30:00.000000",
      "modifiedAt": "2026-03-11T16:00:00.000000"
    }
  }
]
```

### Response Fields (Full Expanded)

| Path | Type | Required | Description |
| --- | --- | --- | --- |
| [] | TimeCardRecord | required | 근무/휴가 기록 배열 |
| [].timeCard | TimeCard | required | 타임카드 정보 |
| [].timeCard.workspaceId | number | required | 워크스페이스 ID |
| [].timeCard.userId | number | required | 유저 ID |
| [].timeCard.baseDate | YYYY-MM-DD | required | 기준일 |
| [].timeCard.startedAt | ISO-8601 string | required | 시작 일시 |
| [].timeCard.endedAt | ISO-8601 string | optional | 종료 일시 |
| [].timeCard.createdAt | ISO-8601 string | required | 생성 일시 |
| [].timeCard.modifiedAt | ISO-8601 string | required | 수정 일시 |
| [].workRecord | WorkRecord \| null | optional | 근무 기록 (휴가인 경우 null) |
| [].workRecord.workRecordId | number | required | 근무 기록 ID |
| [].workRecord.clockInLocation | ClockLocation | required | 출근 위치 |
| [].workRecord.clockInLocation.ip | string | required | IP 주소 |
| [].workRecord.clockInLocation.address | string | required | 주소 |
| [].workRecord.clockInLocation.addressDetail | string | required | 상세 주소 |
| [].workRecord.clockInLocation.latitude | number | required | 위도 |
| [].workRecord.clockInLocation.longitude | number | required | 경도 |
| [].workRecord.clockInLocation.radius | number | required | 허용 반경 (m) |
| [].workRecord.clockOutLocation | ClockLocation \| null | optional | 퇴근 위치 |
| [].workRecord.clockOutLocation.ip | string | required | IP 주소 |
| [].workRecord.clockOutLocation.address | string | required | 주소 |
| [].workRecord.clockOutLocation.addressDetail | string | required | 상세 주소 |
| [].workRecord.clockOutLocation.latitude | number | required | 위도 |
| [].workRecord.clockOutLocation.longitude | number | required | 경도 |
| [].workRecord.clockOutLocation.radius | number | required | 허용 반경 (m) |
| [].workRecord.isRemote | boolean | required | 원격 근무 여부 |
| [].workRecord.breakStartedAt | ISO-8601 string \| null | optional | 기본 휴게 시작 일시 |
| [].workRecord.breakEndedAt | ISO-8601 string \| null | optional | 기본 휴게 종료 일시 |
| [].workRecord.additionalBreakTimes | WorkBreakTime[] | required | 추가 휴게시간 목록 |
| [].workRecord.additionalBreakTimes.startedAt | ISO-8601 string | required | 추가 휴게 시작 일시 |
| [].workRecord.additionalBreakTimes.endedAt | ISO-8601 string | required | 추가 휴게 종료 일시 |
| [].leaveRecord | LeaveRecord \| null | optional | 휴가 기록 (근무인 경우 null) |
| [].leaveRecord.leaveRecordId | number | required | 휴가 기록 ID |
| [].leaveRecord.approverUserId | number \| null | optional | 결재자 유저 ID |
| [].leaveRecord.reason | string | required | 휴가 사유 |
| [].leaveRecord.status | REQUESTED \| APPROVED \| REJECTED | required | 승인 상태 |
| [].leaveRecord.approvedAt | ISO-8601 string \| null | optional | 승인 일시 |
| [].leaveRecord.rejectedAt | ISO-8601 string \| null | optional | 반려 일시 |
| [].leaveRecord.rejectedReason | string \| null | optional | 반려 사유 |
| [].leaveRecord.createdAt | ISO-8601 string | required | 생성 일시 |
| [].leaveRecord.modifiedAt | ISO-8601 string | required | 수정 일시 |

### cURL Example

```bash
curl -X POST https://app.granter.biz/api/public-docs/attendances \
  -H "Content-Type: application/json" \
  -H "Authorization: Basic <BASE64(API_KEY:)>" \
  -d '{
    "startDate": "2026-03-01",
    "endDate": "2026-03-15"
  }'
```

---

## POST /api/public-docs/leave-balances

구성원별 잔여 연차 현황을 조회합니다.

### Request Fields

- `workspaceId`는 요청값을 받지 않고 API key에서 자동으로 결정됩니다.
- `baseDate`는 선택값이며, 미입력 시 백엔드 기본 기준일로 계산합니다.

- `baseDate` (optional, YYYY-MM-DD): 잔여 연차를 계산할 기준일

### Request Example

```json
{
  "baseDate": "2026-03-31"
}
```

### Response Example

```json
[
  {
    "workspaceUserId": 18,
    "userId": 67,
    "isUnlimited": false,
    "theoreticalDays": 15,
    "adjustmentDays": -3,
    "remainingDays": 12
  }
]
```

### Response Fields (Full Expanded)

| Path | Type | Required | Description |
| --- | --- | --- | --- |
| [] | LeaveBalance | required | 구성원별 잔여 연차 배열 |
| [].workspaceUserId | number | required | 워크스페이스 유저 ID |
| [].userId | number | required | 유저 ID |
| [].isUnlimited | boolean | required | 무제한 연차 여부. true이면 나머지 일수 필드는 null일 수 있습니다. |
| [].theoreticalDays | number \| null | optional | 정책과 입사일 기준으로 계산한 이론적 발생 연차 일수 |
| [].adjustmentDays | number \| null | optional | 수동 조정, 사용, 소멸을 반영한 가감 일수 합계 |
| [].remainingDays | number \| null | optional | 현재 잔여 연차 일수 |

### cURL Example

```bash
curl -X POST https://app.granter.biz/api/public-docs/leave-balances \
  -H "Content-Type: application/json" \
  -H "Authorization: Basic <BASE64(API_KEY:)>" \
  -d '{
    "baseDate": "2026-03-31"
  }'
```

---

## POST /api/public-docs/leave-histories

구성원의 연차 변경 내역을 기간별로 조회합니다.

### Request Fields

- `workspaceId`는 요청값을 받지 않고 API key에서 자동으로 결정됩니다.
- 조회 시 `startDate`, `endDate`는 필수입니다.
- 기간 기반 조회는 요청 1회당 최대 31일까지 사용할 수 있으며, 더 긴 범위는 31일 이하 구간으로 나눠 조회한 뒤 합쳐서 사용하세요.
- 휴가 사용, 사용 취소, 소멸, 수동 조정 내역이 함께 반환됩니다.

- `startDate` (required, YYYY-MM-DD): 조회 시작일
- `endDate` (required, YYYY-MM-DD): 조회 종료일

### Request Example

```json
{
  "startDate": "2026-03-01",
  "endDate": "2026-03-31"
}
```

### Response Example

```json
[
  {
    "id": 91,
    "userId": 67,
    "requestUserId": 4,
    "days": 1,
    "type": "USAGE",
    "occurredAt": "2026-03-12",
    "expiresAt": null,
    "timeCardId": 310,
    "memo": "개인 사유",
    "createdAt": "2026-03-11T15:30:00.000000",
    "modifiedAt": "2026-03-11T16:00:00.000000"
  },
  {
    "id": 92,
    "userId": 67,
    "requestUserId": 4,
    "days": 2,
    "type": "ADJUSTMENT_ADD",
    "occurredAt": "2026-03-20",
    "expiresAt": "2027-03-19",
    "timeCardId": null,
    "memo": "관리자 수동 부여",
    "createdAt": "2026-03-20T09:00:00.000000",
    "modifiedAt": "2026-03-20T09:00:00.000000"
  }
]
```

### Response Fields (Full Expanded)

| Path | Type | Required | Description |
| --- | --- | --- | --- |
| [] | LeaveHistory | required | 연차 변경 이력 배열 |
| [].id | number | required | 연차 변경 이력 ID |
| [].userId | number | required | 대상 유저 ID |
| [].requestUserId | number | required | 변경을 요청한 유저 ID |
| [].days | number | required | 변경 일수 |
| [].type | USAGE \| USAGE_CANCEL \| EXPIRATION \| EXPIRATION_PROMOTED \| ADJUSTMENT_ADD \| ADJUSTMENT_SUBTRACT | required | 연차 변경 유형 |
| [].occurredAt | YYYY-MM-DD | required | 변경 발생일 |
| [].expiresAt | YYYY-MM-DD \| null | optional | 소멸 예정일 |
| [].timeCardId | number \| null | optional | 연관 타임카드 ID |
| [].memo | string \| null | optional | 메모 |
| [].createdAt | ISO-8601 string | required | 생성 일시 |
| [].modifiedAt | ISO-8601 string | required | 수정 일시 |

### cURL Example

```bash
curl -X POST https://app.granter.biz/api/public-docs/leave-histories \
  -H "Content-Type: application/json" \
  -H "Authorization: Basic <BASE64(API_KEY:)>" \
  -d '{
    "startDate": "2026-03-01",
    "endDate": "2026-03-31"
  }'
```

---

## POST /api/public-docs/holidays

기간 내 한국 공휴일 목록을 조회합니다.

### Request Fields

- `workspaceId`는 요청값을 받지 않고 API key에서 자동으로 결정됩니다.
- 조회 시 `startDate`, `endDate`는 필수입니다.
- 공공데이터포털 또는 대체 공휴일 소스에서 공휴일을 조회하며, 소스 장애 시 `enabled=false`로 응답합니다.

- `startDate` (required, YYYY-MM-DD): 조회 시작일
- `endDate` (required, YYYY-MM-DD): 조회 종료일

### Request Example

```json
{
  "startDate": "2026-05-01",
  "endDate": "2026-05-31"
}
```

### Response Example

```json
{
  "enabled": true,
  "dates": ["2026-05-05", "2026-05-25"],
  "holidays": [
    {
      "date": "2026-05-05",
      "name": "어린이날"
    },
    {
      "date": "2026-05-25",
      "name": "부처님오신날"
    }
  ]
}
```

### Response Fields (Full Expanded)

| Path | Type | Required | Description |
| --- | --- | --- | --- |
| enabled | boolean | required | 공휴일 조회 가능 여부 |
| reason | string \| null | optional | 조회 실패 사유 |
| dates | YYYY-MM-DD[] | required | 공휴일 날짜 목록 |
| holidays | Holiday[] | required | 공휴일 상세 목록 |
| holidays.date | YYYY-MM-DD | required | 공휴일 날짜 |
| holidays.name | string | required | 공휴일 이름 |

### cURL Example

```bash
curl -X POST https://app.granter.biz/api/public-docs/holidays \
  -H "Content-Type: application/json" \
  -H "Authorization: Basic <BASE64(API_KEY:)>" \
  -d '{
    "startDate": "2026-05-01",
    "endDate": "2026-05-31"
  }'
```

---

## Error Responses

### 401 Unauthorized

- Basic Authorization 헤더가 없거나, API key가 유효하지 않거나, API key에서 workspace 정보를 확인할 수 없는 경우

### 400 Bad Request

- 요청 body 형식/값이 잘못된 경우
- 티켓 API 유효성 규칙: `ticketType` 필수, `startDate/endDate`는 `YYYY-MM-DD` 형식, `startDate <= endDate`, ID 필드는 양의 정수
- 잔액 API 유효성 규칙: `startDate/endDate`는 함께 전달, `startDate/endDate`는 `YYYY-MM-DD` 형식, `startDate <= endDate`
- 급여 API 유효성 규칙: `startDate/endDate` 필수, `startDate/endDate`는 `YYYY-MM-DD` 형식, `startDate <= endDate`, `employeeId`는 양의 정수

### 429 Too Many Requests

- API key 기준 분당 60회 제한을 초과한 경우


---

## Aggregation Tips

### 1) 집계 설계의 기본 원칙

- 집계용 리포트를 만들 때는 `tickets`를 원천 이벤트, `balances`를 잔액 시계열로 나눠 보는 편이 가장 안전합니다. 거래 합계와 분류는 `tickets`, 시작/종료 잔액은 `balances` 기준으로 해석하세요.
- 기간 버킷은 보통 `daily`, `monthly`, `quarterly`, `total` 중 하나로 먼저 정하고, 그다음 `category`, `tag`, `user`, `contact`, `asset` 같은 집계 축을 선택해 표를 구성합니다.
- 실무에서는 보통 `기간 -> 자산/티켓 타입 -> isIncluded=true -> 중복 거래 제외 -> 태그/계정과목/거래처` 순서로 필터를 적용한 뒤 집계를 시작하면 결과가 안정적입니다.
- 금액은 `amount` 하나만 보지 말고 `transactionType`, `inAmount`, `outAmount`를 함께 해석하세요. 특히 지출표와 입출금표를 같은 규칙으로 합치려면 방향값을 분리해 두는 편이 좋습니다.

```json
{
  "period": "monthly",
  "filters": {
    "ticketTypes": ["EXPENSE_TICKET", "BANK_TRANSACTION_TICKET"],
    "startDate": "2026-02-01",
    "endDate": "2026-02-28",
    "isIncluded": true
  },
  "groupBy": "expenseCategoryId"
}
```

### 2) 예시: 월별 계정과목별 지출내역

- 월별 지출표는 보통 `tickets`와 `categories`를 같이 씁니다. 먼저 카드/계좌/수기 등 보고 싶은 티켓 타입을 모으고, `transactionType=OUT`, `isIncluded=true`인 건만 남깁니다.
- 그다음 `transactAt -> YYYY-MM`으로 월 버킷을 만들고, `expenseCategory.id` 기준으로 묶습니다. 표시명과 고정비/변동비 구분이 필요하면 `categories.id`로 조인해 `name`, `costType`을 붙입니다.
- 비용 리포트는 이렇게 만든 계정과목별 월 금액을 다시 합쳐 `총 소비`, `고정비`, `변동비`, `기타` 같은 상위 지표를 만듭니다. 즉, `월-계정과목` 1차 집계 테이블을 먼저 만들고 그 위에 상위 지표를 2차 계산하는 구조입니다.
- 손익계산서처럼 더 높은 단계의 리포트가 필요하면 `categories.subInfo`를 이용해 `매출`, `매출원가`, `판매관리비`, `영업외수익`, `영업외비용`으로 다시 묶어 `매출총이익`, `영업이익`, `세전이익`을 계산하면 됩니다.

```json
[
  {
    "period": "2026-02",
    "categoryId": 101,
    "categoryName": "식비",
    "costType": "VARIABLE",
    "outAmount": -158000,
    "displayAmount": 158000,
    "count": 9
  },
  {
    "period": "2026-02",
    "categoryId": 205,
    "categoryName": "급여",
    "costType": "FIXED",
    "outAmount": -4200000,
    "displayAmount": 4200000,
    "count": 4
  }
]
```

### 3) 예시: 자금일보 구성

- 자금일보는 `잔액 요약`과 `거래 상세`를 분리해서 만드는 편이 안정적입니다. 상단 잔액 표는 `balances`로 전일 잔액과 현재 잔액을 잡고, 같은 기간 `tickets`를 합산해 입금/출금 합계를 붙입니다.
- 화면 구성은 보통 상단에 자산별 `이전잔액 + 입출금 합계 + 현재잔액`을 두고, 하단에는 `계좌 입출금`, `카드 사용`, `세금계산서`, `현금영수증`, `수기관리` 같은 상세 테이블을 분리하는 방식이 읽기 쉽습니다.
- 체크카드 승인과 계좌 출금처럼 중복 가능성이 있는 건은 연결된 계좌 내역을 제외하는 규칙을 함께 두는 편이 좋습니다. 이렇게 해야 대표 거래만 남고 자금 흐름이 과대 집계되지 않습니다.
- 핵심은 `잔액은 balances`, `흐름과 분류는 tickets`로 역할을 나누는 것입니다. 티켓만으로 순유입을 계산할 수는 있지만, 그 값이 자금일보의 기초/기말 잔액과 항상 같지는 않습니다.

```json
{
  "date": "2026-03-08",
  "balances": [
    {
      "assetId": 11,
      "assetName": "국민은행",
      "openingBalance": 12500000,
      "inAmount": 3200000,
      "outAmount": -1850000,
      "closingBalance": 13850000
    }
  ],
  "sections": {
    "bankTransactions": [{ "ticketId": 1, "content": "거래처 입금", "amount": 3200000 }],
    "cardExpenses": [{ "ticketId": 2, "content": "점심 식대", "amount": 12000 }],
    "taxInvoices": [],
    "cashReceipts": [],
    "manualTransactions": []
  }
}
```

### 4) 같은 방식으로 확장할 수 있는 집계

- 태그별 분석은 `tagId`, `tagDetailId` 기준으로 같은 집계 패턴을 반복하면 됩니다. 계정과목 집계와 구조는 같고, 그룹 축만 바뀝니다.
- 거래처별 분석은 `contactId`, 담당자별 분석은 `user_ids`를 기준으로 묶습니다. 여러 사용자가 함께 사용한 건은 `amount / user_count`처럼 균등 배분 규칙을 미리 정해 두면 인원별 집계가 흔들리지 않습니다.
- 외부 대시보드는 `원천 티켓 보관 -> 1차 집계 테이블 생성 -> 리포트별 2차 계산` 흐름으로 설계하면 월별 지출표, 자금일보, 태그 리포트 같은 화면을 일관된 규칙으로 확장하기 쉽습니다.


---

## Traceability

- 단일 소스 파일: `lib/public-docs/spec.ts`
- 문서 콘텐츠 소스: `lib/public-docs/content.ts`
- API 문서 페이지(UI): `app/(outside)/api-docs/page.tsx`
- API 핸들러: `pages/api/public-docs/*.ts`
- 생성 문서: `public/docs/granter-public-api.md`
- 매니페스트(JSON): `public/docs/public-api-manifest.json`
- 동기화 스크립트: `scripts/sync-public-docs.mjs`

## Public API ↔ Upstream Mapping

| Public API | Public Method | Upstream API | Upstream Method | Handler |
| --- | --- | --- | --- | --- |
| /api/public-docs/tickets | POST | /tickets | GET | pages/api/public-docs/tickets.ts |
| /api/public-docs/assets | POST | /assets | GET | pages/api/public-docs/assets.ts |
| /api/public-docs/balances | POST | /bank-accounts/daily-balances | GET | pages/api/public-docs/balances.ts |
| /api/public-docs/exchange-rates | POST | /common/exchange-rates | GET | pages/api/public-docs/exchange-rates.ts |
| /api/public-docs/tags | GET | /tags | GET | pages/api/public-docs/tags.ts |
| /api/public-docs/tag-details | GET | /tag-details | GET | pages/api/public-docs/tag-details.ts |
| /api/public-docs/categories | GET | /expense-categories | GET | pages/api/public-docs/categories.ts |
| /api/public-docs/people | GET | /workspaces | GET | pages/api/public-docs/people.ts |
| /api/public-docs/contacts | GET | /contacts | GET | pages/api/public-docs/contacts.ts |
| /api/public-docs/teams | GET | /teams | GET | pages/api/public-docs/teams.ts |
| /api/public-docs/workflows | GET | /workflows | GET | pages/api/public-docs/workflows.ts |
| /api/public-docs/salary-histories | POST | /salary-histories | GET | pages/api/public-docs/salary-histories.ts |
| /api/public-docs/inventory-products | GET | /inventory-products | GET | pages/api/public-docs/inventory-products.ts |
| /api/public-docs/inventories | GET | /inventories | GET | pages/api/public-docs/inventories.ts |
| /api/public-docs/attendances | POST | /attendances | GET | pages/api/public-docs/attendances.ts |
| /api/public-docs/leave-balances | POST | /leaves/balance | GET | pages/api/public-docs/leave-balances.ts |
| /api/public-docs/leave-histories | POST | /leaves/histories | GET | pages/api/public-docs/leave-histories.ts |
| /api/public-docs/holidays | POST | /api/holidays/kr | GET | pages/api/public-docs/holidays.ts |
