---
layout: posts
comments: true
title: Style an element's shadow DOM (번역)
date:   2017-05-02 12:10:20 +0900
categories: polymer
---

원문보기 : [https://www.polymer-project.org/2.0/docs/devguide/style-shadow-dom](https://www.polymer-project.org/2.0/docs/devguide/style-shadow-dom)



# 당신의 엘리먼트에 스타일을 입히기

Polymer는 DOM templating과 shadow DOM API를 지원한다. 당신의 커스텀 엘리먼트를 위한 DOM template을 제공하고 싶을 때, Polymer는 당신의 엘리먼트를 위해 제공한 템플릿의 콘텐츠를 복사한다.

이것이 그 예제이다 :

**custom-element.html**

```html
<!-- Import polymer-element -->
<link rel="import" href="https://polygit.org/polymer+:2.0-preview/webcomponentsjs+:v1/shadydom+webcomponents+:master/shadycss+webcomponents+:master/custom-elements+webcomponents+:master/components/polymer/polymer-element.html">

<!-- Create a template for the custom element -->
<dom-module id='custom-element'>
  <template>
    <h1>Heading!</h1>
    <p>We are elements in custom-element's local DOM.</p>
  </template>

  <!-- Register the element -->
  <script>
    class CustomElement extends Polymer.Element {
      static get is() {
        return "custom-element";
      }
    }
    customElements.define(CustomElement.is, CustomElement);
  </script>
</dom-module>
```



**index.html**

```html
<!-- Load the polyfills -->
<script src="https://polygit.org/polymer+:2.0-preview/webcomponentsjs+:v1/shadydom+webcomponents+:master/shadycss+webcomponents+:master/custom-elements+webcomponents+:master/components/webcomponentsjs/webcomponents-loader.js"></script>

<!-- Load the custom element -->
<link rel="import" href="custom-element.html">
<!-- Drop the custom element on the page -->
<custom-element></custom-element>
```



템플릿 안의 HTML 엘리먼트는 당신의 커스텀 엘리먼트의 shadow DOM에서는 자식(children)이 된다. Shadow DOM은 shadow DOM의 내부에 있는 엘리먼트와 외부에 있는 엘리먼트가 매치되지 않는 캡슐화 메커니즘을 제공한다.

이와같이, shadow DOM 내부의 스타일링 법칙은 외부의 엘리먼트에 영향을 줄 정도로 "새 나갈" 수 없다.

Shadow DOM은 커스텀 엘리먼트의 스타일링 법칙의 캡슐화를 허락한다. 당신은 엘리먼트 범위 밖에 적용될 걱정없이 폰트나 글자 색, 클래스와 같은 엘리먼트의 스타일링 정보를 자유롭게 정의할 수 있다.

이것이 그 예제이다 :

**x-foo.html**

```html
<dom-module id='x-foo'>
  <template>
    <!-- Encapsulated, element-level stylesheet -->
    <style>
      p {
        color: green;
      }
      .myclass {
        color: red;
      }
    </style>
    <p>I'm a shadow DOM child element of x-foo.</p>
    <p class="myclass">So am I.</p>
  </template>
  ...
</dom-module>
```



**index.html**

```html
<link rel="import" href="x-foo.html">
<!-- Document-level stylesheet -->
<style>
  .myclass {
    color: blue;
  }
</style>
<x-foo></x-foo>
<!-- The following paragraph uses the document-level stylesheet. -->
<p class="myclass">I have nothing to do with x-foo. Because of encapsulation, x-foo's styles won't leak to me.</p>
```



Polymer에서 적용된 Shadow DOM의 더 자세한 설명을 보고 싶다면, 이 링크 [Shadow DOM concepts](https://developers.google.com/web/fundamentals/getting-started/primers/shadowdom) 를 보라.

Shadow DOM의 v1 API를 보고 싶다면, 이 링크 [Shadow DOM v1 : Self-Contained Web Components.](https://developers.google.com/web/fundamentals/getting-started/primers/shadowdom) 를 보라.



## 문서레벨(document-level) 스타일로부터 상속받아 사용하기

HTML 문서 내에서 사용할 때, 당신의 엘리먼트는 여전히 부모 엘리먼트에 적용된 모든 스타일링 정보를 상속받고 있을 것이다 :

**x-foo.html**

```html
<dom-module id='x-foo'>
  <template>
    <div>
      I inherit styles from x-foo's parent in the light DOM.
      I'm also sans-serif and blue.
    </div>
  </template>
</dom-module>
```



**index.html**

```html
<link rel="import" href="x-foo.html">
<!-- Document-level stylesheet -->
<style>
  p {
    font-family: sans-serif;
    color: blue;
  }
</style>

<!-- This paragraph uses document-level styles: -->
<p>I'm sans-serif and blue.</p>

<!-- And the text within x-foo inherits style from the paragraph element: -->
<p><x-foo></x-foo></p>
```



Shadow DOM 내부에서 선언된 스타일은 밖에서 선언된 스타일을 오버라이딩 한다 :

**x-foo.html**

```html
<dom-module id='x-foo'>
  <template>
    <!-- Encapsulated, element-level stylesheet -->
    <style>
      p {
        font-family: sans-serif;
        color: green;
      }
    </style>
    <p>I'm green.</p>
  </template>
</dom-module>
```



**index.html**

```html
<link rel="import" href="x-foo.html">
<!-- Document-level stylesheet -->
<style>
  p {
    font-family: sans-serif;
    color: blue;
  }
</style>
<p>I'm blue.</p>
<p><x-foo></x-foo></p>
```



## 호스트 엘리먼트에 스타일을 적용하기

Shadow DOM이 붙어 있는 엘리먼트는 호스트라고 알려져 있다. 호스트에 스타일을 입히기 위해서, `:host` 선택자를 사용한다.

호스트 엘리먼트의 상속가능한 프로퍼티는 자식에게 적용된 shadow tree를 따라 상속되어 내려간다.

**x-foo.html**

```html
<dom-module id='x-foo'>
  <template>
    <!-- Encapsulated, element-level stylesheet -->
    <style>
      :host {
        font-family: sans-serif;
        color: green;
        display: block;
        border: 1px solid;
      }
    </style>
    <p>I'm green.</p>
    <div>I'm green too.</div>
    <span>We're all green...</span>
  </template>
</dom-module>
```



**index.html**

```html
<link rel="import" href="x-foo.html">
<x-foo></x-foo>
```



당신은 외부로부터 가져온 스타일을 호스트 엘리먼트에 적용할 수도 있다. 예를들어, 타입 선택자를 사용한다.

```css
x-foo {
	background-color: blue;
}
```



**호스트 엘리먼트에 스타일을 적용하기 위해 CSS 선택자 사용하기**

당신은 언제, 어떻게 호스트에 스타일을 적용할지를 선택하기 위해서 CSS 선택자를 사용할 수 있다. 이 코드 샘플에는 :

- `:host` 선택자와 일치하는 모든 `<x-foo>` 엘리먼트
- `:host(.blue)` 선택자와 일치하는 `blue` 클래스의 `<x-foo>` 엘리먼트
- `:host(.red)` 선택자와 일치하는 `red` 클래스의 `<x-foo>` 엘리먼트
- `:host(.hover)` 선택자와 호버될 때 일치하는 `<x-foo>` 엘리먼트



**x-foo.html**

```html
<dom-module id="x-foo">
  <template>
    <style>
      :host { font-family: sans-serif; }
      :host(.blue) {color: blue;}
      :host(.red) {color: red;}
      :host(:hover) {color: green;}
    </style>
    <p>Hi, from x-foo!</p>
  </template>
</dom-module>
```



**index.html**

```html
<link rel="import" href="x-foo.html">

<x-foo class="blue"></x-foo>
<x-foo class="red"></x-foo>
```



`:host` 선택자 뒤에 오는 자손 선택자는 shadow tree의 엘리먼트와 매치된다. 이 예제에서, 호스트가 "warning" 클래스를 가진다면, CSS 선택자는 shadow tree에 있는 모든 `p` 엘리먼트에 적용된다 :



**x-foo.html**

```html
<dom-module id="x-foo">
  <template>
    <style>
      :host(.warning) p {
        color: red;
      }
    </style>
    <p>Make this text red if x-foo has class "warning", and black otherwise.</p>
  </template>
</dom-module>
```



**index.html**

```html
<link rel="import" href="x-foo.html">

<x-foo class="warning"></x-foo>
<x-foo></x-foo>
```



`:host` 선택자를 이용해서 스타일 적용하는 것은 shadow tree 내부의 법칙이 외부의 shadow tree 엘리먼트에 영향을 미칠 수 있는 두 가지 중 하나이다. 두 번째 인스턴스는 자식에게 분배된 스타일링 법칙을 적용하기 위해서  `::slotted()` 문법을 사용한다. 자세한 내용은 이 링크  [*Composition and slots* in Eric Bidelman's article on shadow DOM](https://developers.google.com/web/fundamentals/getting-started/primers/shadowdom#composition_slot) 를 보라.



## Style slotted content (distributed children)

당신은 런타임에도 다음의 문법을 사용함으로써 엘리먼트의 템플릿에 슬롯을 만들 수 있다 :

**x-foo.html**

```html
<dom-module id="x-foo">
  <template>
    <style>
      :host(.warning) p {
        color: red;
      }
    </style>
    <p>Make this text red if x-foo has class "warning", and black otherwise.</p>
  </template>
</dom-module>
<script>
  class XFoo extends Polymer.Element {
    static get is() {
      return "x-foo";
    }
  }
  customElements.define(XFoo.is, XFoo);
</script>
```



**index.html**

```html
<link rel="import" href="x-foo.html">

<x-foo class="warning"></x-foo>
<x-foo></x-foo>
```



slotted content에 스타일을 적용하려면, `::slotted()` 문법을 사용한다.

`::slotted(*)` 모든 slotted content를 선택한다 :

**x-foo.html**

```html
<dom-module id="x-foo">
  <template>
    <style>
      ::slotted(*) {
        font-family: sans-serif;
        color: green;
      }
    </style>
    <h1>
      <div><slot name='heading1'></slot></div>
    </h1>
    <p>
      <slot name='para'></slot>
    </p>
  </template>
  ...
</dom-module>
```



**index.html**

```html
<link rel="import" href="x-foo.html">
<x-foo>
  <div slot="heading1">Heading 1. I'm green.</div>
  <div slot="para">Paragraph text. I'm green too.</div>
</x-foo>
```



또한 엘리먼트 타입(태그 이름)에 의해서 선택할 수도 있다 :

**x-foo.html**

```html
<dom-module id="x-foo">
  <template>
    <style>
      ::slotted(h1) {
        font-family: sans-serif;
        color: green;
      }
      ::slotted(p) {
        font-family: sans-serif;
        color: blue;
      }
    </style>
    <slot name='heading1'></slot>
    <slot name='para'></slot>
  </template>
  ...
</dom-module>
```



**index.html**

```html
<link rel="import" href="x-foo.html">

<x-foo>
  <h1 slot="heading1">Heading 1. I'm green.</h1>
  <p slot="para">Paragraph text. I'm blue.</p>
</x-foo>
```



클래스에 의해서 선택하는 것도 가능하다 :

**x-foo.html**

```html
<dom-module id="x-foo">
  <template>
    <style>
      ::slotted(.green) {
        color: green;
      }
    </style>
    <p>
      <slot name='para1'></slot>
    </p>
    <p>
      <slot name='para2'></slot>
    </p>
  </template>
  ...
</dom-module>
```



**index.html**

```html
<link rel="import" href="x-foo.html">

<x-foo>
  <div slot="para1" class="green">I'm green!</div>
  <div slot="para1">I'm not green.</div>
  <div slot="para2" class="green">I'm green too.</div>
  <div slot="para2">I'm not green.</div>
</x-foo>
```



그리고 slot의 이름으로도 선택할 수 있다 :

**x-foo.html**

```html
<dom-module id="x-foo">
  <template>
    <style>
      slot[name='para1']::slotted(*) {
        color: green;
      }
    </style>
    <p>
      <slot name='para1'></slot>
    </p>
    <p>
      <slot name='para2'></slot>
    </p>
  </template>
  ...
</dom-module>
```



**index.html**

```html
<link rel="import" href="x-foo.html">

<x-foo>
  <div slot="para1">I'm green!</div>
  <div slot="para2">I'm not green.</div>
</x-foo>
```



# 엘리먼트 간 스타일 공유하기

## 스타일 모듈 사용하기

스타일을 공유하기 위해서 준비된 방법은 *스타일 모듈*과 함께하는 것이다. 당신은 스타일 모듈로 패키지를 만들고, 엘리먼트 간 이를 공유할 수 있다.

스타일 모듈을 만들기 위해서, 아래처럼 당신의 `<dom-module>` 과 `<template>` 엘리먼트의 스타일 블록을 묶어야 한다 :

```html
<dom-module id="my-style-module">
  <template>
    <style>
      <!-- Styles to share go here! -->
    </style>
  </template>
</dom-module>
```



스타일을 사용할 엘리먼트를 만들 때, 스타일 블록의 오프닝 태그에 스타일 모듈을 포함시킨다 :

```html
<dom-module id="new-element">
  <template>
    <style include="my-style-module">
      <!-- Any additional styles go here -->
    </style>
    <!-- The rest of your element template goes here -->
  </template>
</dom-module>
```



당신은 아마도 스타일 모듈을 별도의 html 파일로 패키지화 하기를 원할 것이다. 그 경우에는 스타일을 사용하는 엘리먼트에 그 파일을 임포트 할 필요가 있다.

아래가 그 예제이다 :

**my-colors.html**

```html
<!-- Define some custom properties in a style module. -->
<dom-module id='my-colors'>
  <template>
    <style>
      p.red {
        color: red;
      }
      p.green {
        color: green;
      }
      p.blue {
        color: blue;
      }
    </style>
  </template>
</dom-module>
```



**x-foo.html**

```html
<!-- Import the styles from the style module my-colors -->
<link rel="import" href="my-colors.html">
<dom-module id="x-foo">
  <template>
    <!-- Include the imported styles from my-colors -->
    <style include="my-colors"></style>
    <p class="red">I wanna be red</p>
    <p class="green">I wanna be green</p>
    <p class="blue">I wanna be blue</p>
  </template>
  ...
</dom-module>
```



**index.html**

```html
<link rel="import" href="x-foo.html">

<x-foo></x-foo>
```



## 외부의 스타일시트 사용하기 (사용되지 않을:deprecated)

> **앞으로 사라질 기능.** 이 실험적인 기능은 [style modules](https://www.polymer-project.org/2.0/docs/devguide/style-shadow-dom#style-modules) 에 우호적이다. 이것은 아직 지원되지만, 미래에는 지원이 없어질 예정이다.



Polymer는 외부의 엘리먼트의 local DOM에 적용될 스타일시트 로드를 지원하는 실험적인 기능을 포함한다. 이것은 스타일을 분리하고, 엘리먼트 간 공통 스타일을 공유하는 것을 좋아하거나, 스타일 pre-processing tool을 사용하는 개발자에게 일반적으로 편리하다. 이 문법은 전형적으로 스타일시트가 어떻게 로드되는지에 관해서 약간 다르다. 이것은 Inline 스타일에 적절하게 끼워지거나 주입된 스타일시트 텍스트를 로드하기 위한 기능 영향력으로서 HTML import(혹은 적절한 곳에 사용된 HTML import polyfill) 이다.

당신의 polymer 엘리먼트의 local DOM에 적용할 원격의 스타일시트를 포함하기 위해서는, 외부의 스타일시트를 참조하는 특별한 HTML import `<link>` 태그와 `type="css"` 를 당신의 `<dom-module>`에 넣어야 한다.

예를 들어 :

**style.css**

```css
.red { color: red; }
.green { color: green; }
.blue { color: blue; }
```



**x-foo.html**

```html
<!-- Include the styles, and use them in x-foo. -->
<dom-module id="x-foo">
  <link rel="import" type="css" href="style.css">
  <template>
    <p class="red">I wanna be red</p>
    <p class="green">I wanna be green</p>
    <p class="blue">I wanna be blue</p>
  </template>
  ...
</dom-module>
```



**index.html**

```html
<link rel="import" href="x-foo.html">

<x-foo></x-foo>
```



# 문서 레벨에서 `custom-style` 사용하기

현재의 Shadow DOM v1 스펙을 구현한 브라우저는 자동으로 스타일을 캡슐화하고, 엘리먼트에서의 범위를 정할 것이다.

몇몇 브라우저는 Shadow DOM v1 스펙을 구현하지 않았다. 이런 브라우저에서 당신의 앱과 엘리먼트가 정확히 보여지는지 확실히 하기 위해서는, 당신의 엘리먼트의 local DOM에서 스타일 정보가 새지 않는다는 것을 확실히 하기 위해서 `custom-style` 을 사용하는 것이 필요할 것이다.

`custom-style`은 브라우저가 이 스펙을 지원하지 않더라도 당신의 앱과 엘리먼트가 Shadow DOM v1 스펙에서 예상대로 동작한다는 것을 확실히 하기 위해서 polyfill의 집합을 가능하게 한다.

모든 브라우저에서 당신의 스타일이 Shadow DOM v1 스펙을 따라서 동작한다는 것을 확신하려면 *문서 레벨*의 스타일을 정의할 때 `custom-style`을 사용한다. `custom-style`은 `polymer.Element`를 포함하지 않는다. 그리고 반드시 각각 import 되어야 한다. `custom-style`은 레거시의 `polymer.html` 을 포함한다.

*Note : 당신은 메인 문서의 스타일을 정의하기 위해서는 오직 `custom-style`만을 사용해야 한다. 엘리먼트의 local DOM의 스타일을 정의하기 위해서는 그냥 `<style>` 블록을 사용한다.* 



## 예시

첫 번째 코드 샘플에서, 브라우저가 Shadow DOM v1 스펙을 지원하지 않으면 `p` 엘리먼트의 스타일은 B 문단으로 새어나간다. 두 번째 코드 샘플에서, 개발자가 `custom-style` 을 이용해서 스타일 블록을 감싸면, 새어나가지 않는다.



**x-foo.html**

```html
<dom-module id="x-foo">
  <template>
    <p>
      Paragraph B: I am in the shadow DOM of x-foo.
      If your browser implements the Shadow DOM v1 specs,
      I am black; otherwise, I'm red.
    </p>
  </template>
  ...
</dom-module>
```



**index.html**

```html
<link rel="import" href="x-foo.html">
<style>
  p {
    color: red;
  }
</style>
<p>Paragraph A: I am in the main document. I am red.</p>

<x-foo></x-foo>
```





**x-foo.html**

```html
<dom-module id="x-foo">
  <template>
    <p>Paragraph B: I am in the local DOM of x-foo. I am black on all browsers.</p>
  </template>
  ...
</dom-module>
```



**index.html**

```html

<!-- import custom-style -->
<link rel="import" href="/bower_components/polymer/lib/elements/custom-style.html">
<link rel="import" href="x-foo.html">

<custom-style>
  <style>
    p {
      color: red;
    }
  </style>
</custom-style>
<p>Paragraph A: I am in the main DOM. I am red.</p>
<x-foo></x-foo>
```



## 문법과 호환성

`custom-style`의 문법은 변경되었다. Polymer 2.x에서는, `<custom-style>` 은 엘리먼트를 감싼다. 당신은 Polymer 1.x와 다른 버전 간의 호환성이 보장되는 혼합 문법을 사용할 수 있다.



**Polymer 2.x**

```html
<custom-style>
  <style>
    p {
		...
    }
  </style>
</custom-style>
```



**Hibrid (1.x와 2.x 모두 사용 가능)**

```html
<custom-style>
  <style is="custom-style">
    p {
		...
    }
  </style>
</custom-style>
```



**Polymer 1.x**

```html
<style is="custom-style">
  p {
	  ...
  }
</style>
```

