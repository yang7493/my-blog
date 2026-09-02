---
layout: post
title: "HTML 총정리"
date: 2026-09-01
categories: [HTML, 정리]
tags: [html, tag, frontend, markup]
mermaid: true
---

## HTML 개요

HTML(HyperText Markup Language)은 웹 페이지를 작성하는 데 사용되는 표준 마크업 언어다.

- 웹 페이지의 **구조와 컨텐츠**를 정의하며, 웹 브라우저는 HTML 문서를 해석하여 사용자에게 시각적으로 표시한다.
- HTML은 **요소(element)**라고 불리는 **태그(tag)**를 사용하여 텍스트, 이미지, 링크, 리스트, 테이블 등 다양한 종류의 컨텐츠를 구성할 수 있다.

### HTML 작성 시 주의사항

| 번호 | 주의사항 |
|---|---|
| 1 | 태그는 대소문자를 구분하지 않지만, 소문자를 사용하는 것을 권장한다 |
| 2 | 시작 태그로 시작했으면 반드시 종료 태그로 닫아야 한다 |
| 3 | 파일 확장자는 반드시 `.html`로 설정해야 한다 |
| 4 | 문자의 공백은 한 개만 인식되며, 공백을 추가로 넣으려면 특수기호(`&nbsp;` 등)를 이용해야 한다 |

오늘은 `body` 안에 들어가는 태그들을 8개 카테고리로 나누어 배웠고, [실습 코드는 GitHub 저장소](https://github.com/yang7493/01_HTML)에 파일별로 정리해두었다. 아래는 그 실습 코드를 기준으로 다시 정리한 내용이다.

## 1. 글자 관련 태그

| 태그 | 용도 |
|---|---|
| `<h1>` ~ `<h6>` | 제목 (숫자가 작을수록 중요도 높음) |
| `<br>` | 줄바꿈 |
| `<hr>` | 수평선으로 영역을 구분 |
| `<p>` | 문단 |
| `<pre>` | 작성한 서식(공백, 줄바꿈)을 그대로 화면에 표현 |
| `<strong>` / `<b>` | 글자를 굵게 표시 |
| `<em>` / `<i>` | 글자를 기울여서 표시 |
| `<mark>` | 형광펜 효과 |
| `<u>` | 밑줄 |
| `<small>` | 글자를 작게 표시 |
| `<sub>` / `<sup>` | 아래첨자 / 윗첨자 |
| `<s>` | 취소선 |

실습하면서 가장 헷갈렸던 부분은 `p`와 `pre`의 차이였다.

```html
<p>문단 영역을 나누는 태그는 p와 pre 가 있다. 
    p태그는 문단을 나누는 태그 이지만, 
    한 칸의 공백만 입력 해주고, 줄바꿈은 적용하지 않게 된다. 
</p>
<pre>
    pre 태그는 여러 칸 띄어쓰기              혹은 
    줄 바꿈 등을 포함해서 화면에 표현한다. 
</pre>
```

`p`는 코드에 아무리 공백과 줄바꿈을 넣어도 화면에는 한 칸으로 합쳐지지만, `pre`는 작성한 그대로(여러 칸 공백, 줄바꿈)를 화면에 표현한다.

## 2. 목록 관련 태그

| 태그 | 용도 |
|---|---|
| `<ul>` | 순서 없는 목록 (불릿) |
| `<ol>` | 순서 있는 목록 (번호) |
| `<li>` | 목록의 항목 |

`<ol>`은 `type` 속성으로 번호 매기는 방식을 바꿀 수 있다는 걸 실습으로 확인했다.

| `type` 값 | 결과 |
|---|---|
| `type="a"` | 소문자 알파벳 (a, b, c...) |
| `type="A"` | 대문자 알파벳 (A, B, C...) |
| `type="I"` | 로마 숫자 (I, II, III...) |

```html
<ol type="a">
    <li>HTML</li>
    <li>CSS</li>
    <li>JS</li>
</ol>
```

## 3. 표 관련 태그

| 태그 | 용도 |
|---|---|
| `<table>` | 표 전체를 감싸는 태그 (기본적인 표 생성) |
| `<tr>` | 표의 행(row) |
| `<th>` | 표의 제목 셀 |
| `<td>` | 표에 들어갈 데이터 셀 |
| `<caption>` | 표 제목 |

실습에서 가장 유용했던 건 `colspan`(열 합치기)과 `rowspan`(행 합치기) 속성으로 이력서 같은 표를 만든 부분이었다.

```html
<table border="2">
    <caption>회원 이력서</caption>
    <tr>
        <td colspan="2" rowspan="2" width="130px" height="150px">사진</td>
        <td width="80px">이름</td>
        <td width="200px"></td>
    </tr>
    <tr>
        <td>연락처</td>
        <td></td>
    </tr>
    <tr>
        <td>주소</td>
        <td colspan="3"></td>
    </tr>
</table>
```

`colspan="2"`는 옆으로 2칸, `rowspan="2"`는 아래로 2칸을 하나의 셀로 합쳐준다. 표 레이아웃을 짤 때 셀 개수 계산이 헷갈릴 수 있어서 직접 그려보며 확인했다.

## 4. 영역 관련 태그

| 태그 | 용도 |
|---|---|
| `<div>` | **블록(block)** 요소. 뒤에 오는 태그가 자동으로 다음 줄로 내려간다 |
| `<span>` | **인라인(inline)** 요소. 줄바꿈 없이 옆으로 붙는다 |

```html
<div style="border: 1px solid black; background: red">첫 번째 영역</div>
<div style="border: 1px solid black; background: yellow">두 번째 영역</div>

<span style="border: 1px solid black; background: red">첫 번째 영역</span>
<span style="border: 1px solid black; background: yellow">두 번째 영역</span>
```

`div`는 하나의 영역을 만들어서 내부 요소에 공통 속성을 적용할 때 많이 쓰이고, 각 `div`는 줄이 바뀌며 세로로 쌓인다. 반면 `span`은 줄바꿈 없이 가로로 이어 붙는다는 걸 직접 렌더링해보고 확인했다.

`header`, `nav`, `main`, `section`, `article`, `aside`, `footer`처럼 **의미가 있는(semantic)** 영역 태그들도 있는데, `div`/`span`은 의미 없이 순수하게 레이아웃/스타일링 용도로만 쓰인다는 차이가 중요했다.

## 5. 이미지 관련 태그

| 속성 | 용도 |
|---|---|
| `src` | 불러올 이미지 파일의 경로 |
| `alt` | 이미지를 읽을 수 없을 때 대체 표시할 텍스트 |
| `width` / `height` | 이미지 크기 지정 |

`width`, `height` 값을 픽셀(`px`)로 고정하면 화면 크기가 바뀌어도 이미지 크기가 그대로 유지되고, `%`(퍼센트)로 지정하면 화면 크기에 비례해서 이미지 크기가 변한다는 차이를 실습으로 비교했다.

```html
<!-- 고정 크기: 화면이 커져도 그대로 유지 -->
<img src="sample/sample/image/flower1.PNG" width="200px" height="100px">

<!-- 가변 크기: 화면 크기에 비례해서 변함 -->
<img src="sample/sample/image/flower1.PNG" width="20%" height="100px">
```

## 6. 미디어 관련 태그

| 태그 | 용도 |
|---|---|
| `<audio>` | 오디오(소리) 삽입 |
| `<video>` | 동영상 삽입 |

두 태그 모두 `controls` 속성을 넣어야 재생/정지 컨트롤 UI가 화면에 나타난다. `audio`에는 `loop` 속성을 추가해서 반복 재생도 실습했다.

```html
<audio src="sample/sample/audio/major.mp3" controls loop>뮤직 큐</audio>
<video src="sample/sample/video/video1.mp4" controls></video>
```

## 7. 하이퍼링크 관련 태그

`<a>`(anchor) 태그는 웹 문서가 다른 문서와 구분되는 가장 핵심적인 기능이다. 텍스트뿐 아니라 버튼, 이미지를 클릭해서도 이동시킬 수 있다.

| 속성 | 용도 |
|---|---|
| `href` | 이동할 주소 (다른 파일, 외부 사이트, `#id` 등) |
| `target="_blank"` | 새 탭에서 열기 |
| `target="_self"` | 현재 탭에서 열기 (기본값) |

실습에서 특히 흥미로웠던 건 **같은 페이지 안에서 이동하는 앵커**였다.

```html
<h1 id="title">하이퍼링크 관련 태그</h1>
...
<ul>
    <li><a href="#index1">목차 1</a></li>
    <li><a href="#index2">목차 2</a></li>
</ul>
...
<h4 id="index1">목차 1번 영역</h4>
...
<a href="#title"><h3>메인으로...</h3></a>
```

원리는 이렇다: 이동하고 싶은 위치의 태그에 `id`값을 지정해두고, `<a href="#id값">`으로 링크를 걸면 클릭했을 때 해당 `id`를 가진 태그 위치로 화면이 이동한다. 목차(TOC), "맨 위로 가기" 버튼을 만들 때 쓰는 방식이라는 걸 이해했다.

또한 `<a>` 태그 안에 `<img>`를 넣으면 이미지를 클릭했을 때도 링크로 이동시킬 수 있었다.

```html
<a href="https://www.naver.com">
    <img src="sample/sample/image/river1.PNG">
</a>
```

## 8. 폼 관련 태그

`form` 태그는 사용자가 값을 입력할 수 있는 양식을 제공하고, 내부의 `input` 태그로 입력받은 데이터를 서버로 전달하는 역할을 한다.

| `form` 속성 | 용도 |
|---|---|
| `action` | 입력값을 전송받을 서버 주소 |
| `method="get"` | 전송하는 데이터가 URL에 노출됨 |
| `method="post"` | 전송하는 데이터가 URL에 노출되지 않음 |

`input` 태그는 `type` 속성에 따라 완전히 다른 입력 방식으로 동작한다는 게 오늘 가장 크게 배운 부분이다.

| `type` 값 | 용도 |
|---|---|
| `text` | 한 줄짜리 텍스트 입력 |
| `password` | 입력값이 가려지는 비밀번호 입력 |
| `search` | 검색어 입력 |
| `email` | 이메일 형식 입력 |
| `number` | 숫자 입력 (`min`, `max`, `step` 속성으로 범위·간격 지정) |
| `range` | 슬라이더 형태의 숫자 입력 |
| `date` / `month` / `week` / `time` / `datetime-local` | 날짜·시간 입력 |
| `radio` | 여러 선택지 중 하나만 선택 (`name`이 같아야 그룹으로 묶임) |
| `checkbox` | 여러 선택지 중 복수 선택 가능 |
| `color` | 색상 선택기 |
| `file` | 파일 선택 (`multiple`로 여러 개 선택 가능) |
| `hidden` | 화면에 보이지 않지만 함께 전송되는 값 |

라디오 버튼은 `name` 속성 값을 동일하게 맞춰야 "하나만 선택되는 그룹"으로 묶인다는 점이 인상 깊었다.

```html
<label>성별 : </label>
<input id="male" type="radio" name="gender" value="남성">
<label for="male">남성</label>
<input id="female" type="radio" name="gender" value="여성">
<label for="female">여성</label>
```

`select`/`option`은 드롭다운 목록을 만들고, `multiple` 속성을 추가하면 여러 개를 동시에 선택할 수 있는 목록으로 바뀐다.

```html
<select name="nation2" size="2" multiple>
    <option value="ko">한국</option>
    <option value="ch">중국</option>
    <option value="jp" selected>일본</option>
</select>
```

`button` 태그는 `type`을 지정하지 않으면 기본값이 `submit`이라는 점도 실습으로 확인했다. `textarea`는 `input type="text"`와 달리 여러 줄을 입력할 수 있고 크기 조절도 가능하다.

<div class="mermaid">
flowchart LR
    A[form] --> B[label]
    A --> C[input / select / textarea]
    A --> D[button]
    B -->|for 속성으로 연결| C
    A -->|action + method| E[Server]
</div>

## 더 학습하면 좋은 개념

- **시맨틱 HTML(Semantic HTML)** — `div`/`span`처럼 의미 없는 태그와 `header`/`nav`/`article`처럼 의미 있는 태그의 차이를 배웠다. 왜 시맨틱 태그를 쓰는 게 SEO와 접근성에 좋은지 더 깊게 이해하면 좋다.
- **웹 접근성(Accessibility, a11y)** — `alt`, `label`의 `for` 속성처럼 오늘 배운 것들이 스크린 리더 사용자를 위한 접근성과 직결된다. `aria-*` 속성까지 확장해서 학습하면 좋다.
- **HTML5 폼 유효성 검사(Form Validation)** — 오늘 `required`, `min`/`max`/`step` 같은 속성을 써봤는데, `pattern` 정규식 검증 등 브라우저가 자체적으로 값을 검증해주는 기능을 더 익히면 좋다.
- **GET과 POST의 차이** — 오늘 `form`의 `method` 속성으로 맛만 봤는데, HTTP 메서드 관점에서 GET/POST가 서버와 어떻게 통신하는지 이해하면 폼 동작 원리가 더 명확해진다.
- **CSS와의 관계(Box Model, display 속성)** — `div`(block)와 `span`(inline)의 차이가 CSS의 box model, display 속성과 바로 연결된다. 다음 학습 주제로 자연스럽게 이어진다.

## 참고 자료

- [MDN - HTML elements reference](https://developer.mozilla.org/en-US/docs/Web/HTML/Element)
- [MDN - HTML 소개](https://developer.mozilla.org/ko/docs/Learn/Getting_started_with_the_web/HTML_basics)
- [MDN - 시맨틱 HTML](https://developer.mozilla.org/ko/docs/Glossary/Semantics)
- [MDN - HTML Forms 가이드](https://developer.mozilla.org/en-US/docs/Learn/Forms)
- [실습 코드 저장소 - yang7493/01_HTML](https://github.com/yang7493/01_HTML)

<script src="https://cdn.jsdelivr.net/npm/mermaid@10/dist/mermaid.min.js"></script>
<script>mermaid.initialize({ startOnLoad: true });</script>
