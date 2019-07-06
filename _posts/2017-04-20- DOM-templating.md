---
layout: posts
comments: true
title: DOM templating (번역)
date:   2017-04-20 12:10:20 +0900
categories: polymer
---

원문보기 : [https://www.polymer-project.org/2.0/docs/devguide/dom-template](https://www.polymer-project.org/2.0/docs/devguide/dom-template)

수많은 엘리먼트는 그들의 기능을 수행하기 위해서 DOM 엘리먼트의 subtree를 사용한다. DOM templating은 당신의 엘리먼트를 위한 DOM subtree를 만드는 쉬운 방법을 제공한다.

기본적으로, 엘리먼트에 DOM template를 추가하는 것은 Polymer가 엘리먼트를 위한 shadow root를 생성하고 template을 shadow tree에 복제하는 것을 초래한다.

DOM template은 또한 데이터 바인딩과 명시적인 이벤트 핸들러를 사용가능하도록 한다.

# DOM template 개요

엘리먼트의 DOM template을 명시하기 위해서 :

1. 엘리먼트의 name과 매칭되는 `id` 애트리뷰트가 포함된 `<dom-module>` 엘리먼트를 생성하라.
2. `<dom-module>` 내부에 `<template>` 엘리먼트를 생성하라.

Polymer는 이 template의 콘텐츠를 엘리먼트의 local DOM에 복제한다.

**Example :**

```html
<dom-module id="x-foo">

  <template>I am x-foo!</template>

  <script>
    class XFoo extends Polymer.Element {
      static get is() { return  'x-foo' }
    }
    customElements.define(XFoo.is, XFoo);
  </script>

</dom-module>
```

이 예제에서, 엘리먼트를 정의하는 DOM template 과 JavaScript는 동일한 파일 안에 있다. 당신은 엘리먼트가 정의되기 이전에 DOM template이 파싱 될 때에만, 이 부분을 다른 파일로 분리할 수 있다.

> **Note :** 엘리먼트는 테스팅을 위한 경우가 아니라면, 일반적으로 메인 문서(document) 바깥쪽에서 정의되어야 한다. 메인 문서 내에서 엘리먼트를 정의하는 것에 관한 경고들을 보려면, [main document definitions](https://www.polymer-project.org/2.0/docs/devguide/registering-elements#main-document-definitions)를 참고하라.



# 자동 노드 찾기 (Automatic node finding)

Polymer는 엘리먼트가 DOM template을 초기화할 때 노드 ID의 정적인 지도를 만든다. 이는 자주 사용하는 노드에 수동으로 쿼리할 필요 없이 편안하게 접근하기 위해서이다. `id`가 있는 엘리먼트 template에 명시된 어떤 노드라도 `this.$` hash에 `id` 에 의해서 저장된다.

> **Note :**  (`dom-repeat`과 `dom-if` 템플릿을 포함한) 데이터 바인딩을 사용해서 동적으로 생성된 노드는 `this.$` hash에 추가되지 않는다. 해시는 오직 *정적으로* 생성된 local DOM 노드만을 포함한다(결국, 엘리먼트의 가장 바깥쪽 template에서 정의된 노드를 말한다).
>
> **Example :**

```html
<dom-module id="x-custom">

  <template>
    Hello World from <span id="name"></span>!
  </template>

  <script>
    class MyElement extends Polymer.Element {
      static get is() { return  'x-custom' }
      ready() {
        super.ready();
        this.$.name.textContent = this.tagName;
      }
    }
  </script>

</dom-module>
```

당신의 엘리먼트의 shadow DOM에 동적으로 생성된 노드를 위치시키기 위해서, 표준(standard) DOM인 `querySelector` 메서드를 사용하라.

```javascript
this.shadowRoot.querySelector(selector)
```

