# BranchQ_Design.md
> AI가 피그마와 기존 명세를 읽고도 **레이아웃/간격/컴포넌트/상태를 임의 해석하지 않도록 하는 디자인 기준 문서**
> BranchQ UI 구현 시 모든 화면은 이 문서를 우선 참조한다.

---

# 1. Design Philosophy

BranchQ는 금융 업무 시스템이므로 다음 원칙을 유지한다.

- 정보 우선
- 장식 최소화
- 숫자 정렬 일관성
- 클릭 흐름 예측 가능
- 상태 변화 명확

즉:

```txt
예쁘게보다 정확하게
강조보다 구조 먼저
```

---

# 2. Foundation Tokens

## Color Tokens

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
  --gray-400:#9CA3AF;
  --gray-500:#6B7280;
  --gray-700:#374151;
  --gray-900:#111827;
}
```

디자인 토큰은 primitive → semantic → component 순으로 적용한다. semantic token layer를 두면 컴포넌트 변경 시 전체 영향이 줄어든다. citeturn0search11turn0search15

---

## Typography

```txt
한글: Noto Sans KR
영문/숫자: Inter
```

| 용도 | 크기 | Weight |
|------|------|--------|
| 대제목 | 20px | Bold |
| 섹션 제목 | 16px | Bold |
| 본문 | 14px | Regular |
| 보조텍스트 | 12px | Medium |
| 숫자 KPI | 28px | Bold |

---

## Radius

```txt
4px = input / badge
6px = card
8px = modal
20px = pill button
```

---

## Spacing Scale

```txt
4 / 8 / 12 / 16 / 24 / 32
```

임의 숫자 금지.

---

# 3. Layout Rules

## App Layout

```tsx
Sidebar 70px fixed
Header 56px fixed
Content auto scroll
InputBar fixed bottom
```

---

## Sidebar

- width 70px
- active background = accent-bg
- icon 18px center
- label 10px bold

## Sidebar Interaction

- 한 번에 subpanel 하나만 open
- 외부 클릭 close
- active 유지

---

## Main Content Width

```txt
기본 max-width 1280px
Chat 영역 720px
보고서 영역 960px
```

---

# 4. Component Spec

## Number Stat Card

```txt
width 173px
height 90px
radius 6px
border gray200
```

## 내부 구조

```txt
값
라벨
diff
```

## 값

```txt
28px Inter Bold
```

## diff

```txt
▲ green
▼ red
```

---

## Data Table

금액 컬럼은 항상 우측 정렬.

```txt
amount = right align
score = center align
```

## score color

```txt
90+ red
80+ orange
else blue
```

---

## ShareRow

우측 정렬.

```txt
gap 10
height 36
radius 20
```

버튼 순서 고정:

```txt
보고서저장
PDF
Excel
공유하기
신고하기
```

---

## Modal

overlay blur 적용.

```txt
rgba(0,0,0,.5)
blur 4px
```

modal 내부 shadow 고정.

---

# 5. Interaction Rules

모든 interaction은 visual spec보다 먼저 정의한다.

GitHub Docs 스타일처럼 "구조 먼저, 표현 나중" 원칙을 따른다. citeturn0search2turn0search17

---

## Click Rule

```txt
hover
active
focus
disabled
```

4상태 모두 정의.

---

## Modal Rule

```txt
ESC close
outside click close
focus return
```

---

## Dropdown Rule

```txt
선택 즉시 close
현재 선택값 표시
```

---

# 6. AI Response Layout Rule

AI 메시지는 아래 순서 고정.

```txt
AIText
Blocks
ShareRow
```

---

## Block Spacing

```txt
block gap = 16px
section gap = 24px
```

---

## Block Width

```txt
full width = parent 기준
내부 card auto-fit 허용
```

---

# 7. Chart Rule

bar 높이는 실제 값 비율 사용.

```txt
height = value/max * maxHeight
```

값 표시:

```txt
30px 이상 내부 흰색
30px 이하 외부 상단
```

Material Design token 방식처럼 값 표현보다 semantic consistency 우선. citeturn0search12turn0search11

---

# 8. Design QA

## 반드시 체크

```txt
겹침 없음
overflow 없음
텍스트 clipping 없음
버튼 중심 정렬
```

---

## DOM QA

```txt
DOM 존재 확인
optional chaining
DOMContentLoaded 이후 bind
```

---

## 개발 QA

```bash
node --check
```

---

# 9. AI 사용 시 금지 규칙

## 금지

```txt
임의 margin 추가 금지
임의 radius 변경 금지
색상 새로 만들기 금지
```

---

## 허용

```txt
semantic token 참조
기존 block 재사용
state 우선 정의
```

---

# 10. 전달 순서

AI에게는 항상 아래 순서로 준다.

```txt
1. Master Prompt
2. Design.md
3. React Prompt
4. Figma
```

작은 정확한 spec이 큰 설명보다 효과적이다. 최근 design-system 문서화 방식도 component별 spec file 분리를 권장한다. citeturn0search7turn0search6
