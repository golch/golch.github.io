---
layout: post
comments: true
title: Shadow DOM concepts (번역)
date:   2017-04-18 12:10:20 +0900
categories: polymer
---

원문보기 : https://www.polymer-project.org/2.0/docs/devguide/shadow-dom

Shadow DOM은 컴포넌트를 만들도록 도와주는 새로운 DOM 기능이다. 당신은 shadow DOM을 element 내부의 **범위가 지정된 하위트리** 라고 생각해도 된다.
> **웹 기초에 관해 더 읽어보기.** 이 문서는 polymer와 연관된 shadow DOM의 개요를 보여준다. Shadow DOM의 더 심화된 개요가 궁금하다면, 웹 기초에 관한 다음 링크를 읽어보라. [Shadow DOM v1: self-contained web components](https://developers.google.com/web/fundamentals/getting-started/primers/shadowdom?hl=en)

페이지 제목과 메뉴 버튼을 포함하고 있는 header 컴포넌트를 생각해보자 : 이 element의 DOM tree는 아마 아래와 같이 생겼을 것이다.
```html
<my-header>
  <header>
    <h1>
    <button>
```

Shadow DOM은 범위가 지정된 하위트리에서 당신이 자식(children)을 위치시킬 수 있도록 해준다. 그래서 문서레벨의 CSS는 임의로 버튼의 스타일을 변경할 수 없다. 이 하위트리는 shadow tree라고 불린다.
```html
<my-header>
  #shadow-root
    <header>
      <h1>
      <button>
```

