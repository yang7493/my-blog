---
layout: post
title: "CSS 선택자 총정리"
date: 2026-09-02
categories: [CSS, 정리]
tags: [css, selector, frontend]
mermaid: true
---

## 선택자(Selector)란?

선택자는 특정한 HTML 요소를 선택할 때 사용하는 기능이다. 요소를 선택한 뒤 원하는 스타일(색상, 배경, 크기 등)을 적용할 수 있다. [

## 1. 기본 선택자

| 선택자 | 문법 | 의미 |
|---|---|---|
| 전체 선택자 | `*` | 문서 안의 모든 요소를 선택 |
| 태그 선택자 | `태그명` | 지정한 태그를 전부 선택 (예: `li`) |
| ID 선택자 | `#id값` | 특정 `id`를 가진 요소 하나를 선택 |
| 클래스 선택자 | `.class값` | 특정 `class`를 가진 요소들을 선택 |

```css
* { color: red; }             /* 전체 선택자 */
li { color: blueviolet; }     /* 태그 선택자 */
#id3 { color: aqua; }         /* ID 선택자 */
.class { color: aquamarine; } /* 클래스 선택자 */
```

## 2. 속성 선택자

기본 선택자 뒤에 `[]`를 붙여서 특정 속성이나 속성값을 가진 요소만 골라 선택할 수 있다.

| 문법 | 의미 |
|---|---|
| `[속성명=속성값]` | 속성값이 정확히 일치하는 요소 선택 |
| `[속성명~=속성값]` | 속성값이 띄어쓰기로 구분된 값들 중 하나로 포함된 요소 선택 |
| `[속성명\|=속성값]` | 속성값이 정확히 일치하거나, 그 값 뒤에 `-`가 곧바로 붙는 요소 선택 |
| `[속성명^=속성값]` | 속성값이 특정 문자열로 **시작**하는 요소 선택 |
| `[속성명$=속성값]` | 속성값이 특정 문자열로 **끝나는** 요소 선택 |
| `[속성명*=속성값]` | 속성값에 특정 문자열이 **포함된** 요소 선택 |

```css
div[name=name2] { background: orange; }     /* 정확히 일치 */
div[name~=name1] { background: yellowgreen; } /* 띄어쓰기 단위로 포함 */
div[class|=class] { background: violet; }    /* class 또는 class-... */
div[name^=name] { background: skyblue; }     /* name으로 시작 */
div[class$=class] { background: palevioletred; } /* class로 끝남 */
div[class*=div] { background: olivedrab; }   /* div를 포함 */
```

## 3. 자식 선택자 vs 후손 선택자 (헷갈렸던 부분)

오늘 가장 헷갈렸던 개념이라 조금 더 깊게 정리했다. 

```html
<div id="test1">
    <h4>자손 1번 입니다.</h4>
    <h4>자손 2번 입니다.</h4>
    <div>
        <ul id="testul">리스트
            <li>ul 태그의 자식이면서, div#test1 태그의 후손</li>
            <li>ul 태그의 자식이면서, div#test1 태그의 후손</li>
        </ul>
    </div>
</div>
```

<div class="mermaid">
flowchart TD
    T1["div#test1"] --> H1["h4 (1)"]
    T1 --> H2["h4 (2)"]
    T1 --> D2["div (익명)"]
    D2 --> UL["ul#testul"]
    UL --> LI1["li (1)"]
    UL --> LI2["li (2)"]
</div>

| 구분 | 기호 | 의미 | 예시 |
|---|---|---|---|
| **자식 선택자** | `>` | 바로 한 단계 아래에 있는 요소만 선택 | `#test1 > h4` |
| **후손 선택자** | (공백) | 몇 단계로 중첩되어 있든 하위 요소 전부 선택 | `#test1 h4` |

```css
/* 자식 선택자: div#test1의 '바로 아래(1단계)'에 있는 h4만 선택 */
#test1 > h4 { background: papayawhip; }

/* 자식 선택자를 연쇄로 사용 */
#test1 > ul > li { background: chocolate; }

/* 후손 선택자: div#test1 안의 ul을 몇 단계로 중첩되어 있든 전부 선택 */
#test1 ul { background: chocolate; }
```

여기서 헷갈렸던 포인트는 `#test1 > ul > li`였다. 위 HTML 구조를 보면 `ul#testul`은 `div#test1`의 **바로 아래(자식)가 아니라, 그 사이에 이름 없는 `div`가 하나 더 있는 손자(후손)** 요소다. 그래서 `#test1 > ul > li`는 아무것도 선택하지 못한다 — `>`는 "부모-자식(1촌)" 관계만 인정하기 때문이다. 반면 `#test1 ul`(공백)은 중간에 `div`가 몇 개 껴 있든 상관없이 `div#test1` 안에 있는 `ul`을 전부 찾아낸다.

> **정리**: `>`는 "내 바로 아래 자식만", 공백은 "내 안에 있으면 몇 단계든 전부"라고 구분

## 4. 동위(형제) 선택자

부모-자식 관계가 아니라, **같은 부모를 가진 형제 요소**를 선택하는 방법

| 선택자 | 기호 | 의미 |
|---|---|---|
| 인접 형제 선택자 | `A + B` | A 바로 다음에 오는 형제 B 하나만 선택 |
| 일반 형제 선택자 | `A ~ B` | A 뒤에 오는 형제 B를 전부 선택 |

```css
#div-test1 { background: red; }

/* div-test1 바로 다음에 오는 형제 div 하나만 선택 */
#div-test1 + div { background: gold; }

/* div-test1 뒤에 오는 형제 div를 전부 선택 */
#div-test1 ~ div { background: khaki; }
```

## 5. 구조 가상 클래스 선택자

형제 관계 안에서 **순서(위치)**로 요소를 선택하는 방법

| 선택자 | 의미 |
|---|---|
| `:first-child` | 부모의 첫 번째 자식일 때 선택 |
| `:last-child` | 부모의 마지막 자식일 때 선택 |
| `:nth-child(n)` | 부모의 n번째 자식일 때 선택 |
| `:nth-child(2n)` / `:nth-child(2n-1)` | 짝수 번째 / 홀수 번째 자식 선택 |
| `:nth-last-child(n)` | 뒤에서부터 n번째 자식일 때 선택 |
| `:not(선택자)` | 해당 조건에 **해당하지 않는** 요소 선택 (부정 선택자) |

```css
#test :first-child { background: rebeccapurple; }
#test :nth-child(2) { background: blueviolet; }
#test :nth-child(2n-1) { background: rgb(43, 226, 83); } /* 홀수 번째 */

/* 짝수 번째(2n)가 아닌 것만 선택 = 홀수 번째만 선택 */
#test2 p:not(:nth-child(2n)) { background: orange; }
```

## 6. 반응 선택자 / 상태 선택자

사용자의 행동이나 입력 양식의 상태에 따라 달라지는 선택자

| 선택자 | 의미 |
|---|---|
| `:hover` | 마우스를 올렸을 때 |
| `:active` | 클릭한 순간 |
| `:focus` | 입력창 등에 포커스가 갔을 때 |
| `:checked` | 체크박스/라디오가 체크되었을 때 |
| `:enabled` / `:disabled` | 입력 가능 / 입력 불가능(비활성화) 상태일 때 |

```css
#hover-test:hover { background: darkgreen; cursor: pointer; }
#active-test:active { background: red; }
#userId:focus, #userPwd:focus { background: hotpink; }
input[type=checkbox]:checked { width: 100px; height: 100px; }
option:disabled { background: red; }
```

## 7. 문자 선택자 (가상 요소)

요소 전체가 아니라, 요소 **안의 특정 부분**(문자 단위)을 선택할 때 쓴다.

| 선택자 | 의미 |
|---|---|
| `::first-letter` | 텍스트의 첫 글자만 선택 |
| `::first-line` | 텍스트의 첫 줄만 선택 |
| `::selection` | 사용자가 마우스로 드래그해서 선택한 영역 |

```css
#test3 p::first-letter { font: 50px bold; }
#test3 p::selection { color: red; }
```

`:hover`, `:focus`처럼 콜론 1개(`:`)로 쓰는 건 **가상 클래스**(요소의 상태), `::first-letter`처럼 콜론 2개(`::`)로 쓰는 건 **가상 요소**(요소 안의 특정 부분)

## 더 학습하면 좋은 개념

- **CSS 우선순위(Specificity)** — 오늘 배운 선택자들(태그, 클래스, ID, 속성)은 우선순위 점수가 서로 다르다. 여러 선택자가 같은 요소에 동시에 적용될 때 어떤 스타일이 이기는지 이해하려면 반드시 필요한 개념이다.
- **가상 클래스 vs 가상 요소(Pseudo-class vs Pseudo-element)** — `:hover`(콜론 1개)와 `::first-letter`(콜론 2개)의 차이를 문법으로만이 아니라 "상태를 선택하는가, 요소의 일부를 선택하는가"라는 개념으로 이해하면 좋다.
- **CSS 상속(Inheritance)** — 부모 요소의 스타일이 자식에게 자동으로 전달되는 속성들이 있다. 자식/후손 선택자와 헷갈리지 않으려면 "선택자로 지정하는 것"과 "상속으로 물려받는 것"을 구분해서 이해하면 좋다.
- **외부 스타일시트(External Stylesheet)** — 오늘 `<link rel="stylesheet">`로 `.css` 파일을 분리하는 방식을 실습했다. 왜 인라인/내부 스타일보다 외부 스타일시트가 유지보수에 유리한지 더 학습하면 좋다.
- **미디어 쿼리(Media Query)** — 화면 크기에 따라 다른 CSS를 적용하는 방법. 선택자로 요소를 고르는 것에서 한 걸음 더 나아가 반응형 디자인의 기초가 된다.

## 참고 자료

- [MDN - CSS selectors](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_selectors)
- [MDN - Child combinator (>)](https://developer.mozilla.org/en-US/docs/Web/CSS/Child_combinator)
- [MDN - Descendant combinator (공백)](https://developer.mozilla.org/en-US/docs/Web/CSS/Descendant_combinator)
- [MDN - Pseudo-classes and pseudo-elements](https://developer.mozilla.org/en-US/docs/Web/CSS/Pseudo-classes)
- [실습 코드 저장소 - yang7493/CSS-](https://github.com/yang7493/CSS-)

<script src="https://cdn.jsdelivr.net/npm/mermaid@10/dist/mermaid.min.js"></script>
<script>mermaid.initialize({ startOnLoad: true });</script>
