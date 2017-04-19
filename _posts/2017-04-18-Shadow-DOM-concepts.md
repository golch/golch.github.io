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

*shadow root* 는 shadow tree의 최상단이다. Tree가 `<my-header>`에 붙은 element를 *shadow host*라고 부른다. 이 host는 `shadowRoot`라고 불리는 property를 갖는데, 이것은 shadow root를 참조한다. Shadow root는 그 host element로 식별되는 `host` property를 가진다.

Shadow tree는 그 element의 `children`과는 분리되어 있다. 당신은 이 shadow tree를 외부 element는 알 필요가 없는, 컴포넌트 **실행(implementation)**의 일부분으로 생각할 수도 있다. Element의 자식(children)은 그것의 공개 인터페이스(public interface)의 일부분이다.

당신은 부득이하게 `attachShadow`를 호출함으로서 shadow tree를 element에 추가할 수 있다.

```html
var div = document.createElement('div');
var shadowRoot = div.attachShadow({mode: 'open'});
shadowRoot.innerHTML = '<h1>Hello Shadow DOM</h1>';
```

Polymer는 `DOM template`을 이용해서 shadow tree를 추가하는데 선언적인 메커니즘을 제공한다. 당신이 element를 위해서 DOM template을 제공할 때, Polymer는 각각의 element instance 에 shadow root를 붙이고 template 콘텐츠를 shadow tree로 복사한다.

```html
<dom-module id="my-header">
  <template>
    <style>...</style>
    <header>
      <h1>I'm a header</h1>
      <button>Menu</button>
    </header>
  </template>
</dom-module>
```

Template이 `<style>` element를 포함한다는 것을 알아두라. Shadow tree에 위치한 CSS는 해당 shadow tree안에서만 유효하다. 그리고 다른 DOM에는 영향을 미치지 않는다.

# Shadow DOM 의 구성 (Shadow DOM and composition)

만일 element가 shadow DOM을 가지고 있다면, 기본적으로 **shadow tree는 그 element의 자식(children)대신에 그려진다.** 자식이 그려지도록 하기 위해서, `<slot>` element를 shadow tree에 추가한다. `<slot>`은 child node 가 어디에 그려질 것인지를 보여주는 placeholder라고 생각하라. 아래 '<my-header>' 의 shadow tree를 생각해보자:

```html
<header>
  <h1><slot></slot></h1>
  <button>Menu</button>
</header>
```
사용자는 이렇게 자식을 추가할 수 있다:
```html
<my-header>Shadow DOM</my-header>
```
header는 마치 `<slot>` element가 자식으로 대체된 것처럼 그려진다:
```html
<my-header>
  <header>
    <h1>Shadow DOM</h1>
    <button>Menu</button>
  </header>
</my-header>
```

Element의 진짜 자손 tree는 shadow DOM tree와는 대조적으로, 때때로 그것의 light DOM을 호출한다.

Light DOM과 shadow DOM을 그리기 위해서(for rendering) 하나의 tree로 만드는 과정을 *flattening* tree 라고 부른다. `<slot>` element가 그려지지 않는 동안, flattened tree에 포함되고, 예를들어 event bubbling의 일부가 될 수 있다.

>참고(번역자 추가) : **Event Bubbling이란?**
>계층적 구조의 DOM에서 특정 엘리먼트에 이벤트가 발생할 경우, 연쇄적인 반응이 일어나는데 이를 이벤트 전파라고 한다. 이벤트 대상 엘리먼트를 시작으로 최하위 엘리먼트까지 이벤트가 전파되는 것을 캡쳐링이라고 하고, 반대로 최상위 엘리먼트에서 대상 엘리먼트까지 이벤트가 전파되는 것을 버블링이라 한다.(출처 : http://blog.jui.io/?p=33)

당신은 **slots**라는 것을 이용해서 자식이 flattened tree의 어디로 가야하는지를 조작할 수 있다.
```html
<h2><slot name="title"></slot></h2>
<div><slot></slot></div>
```

이름 붙여진 slot은 같은 이름의 slot attribute가 있는 최상위 자식에서만 허용된다.
```html
<span slot="title">A heading</span>
```

`name` attribute가 없는 slot은 `slot` attribute가 없는 모든 자식에서 기본 slot처럼 쓰인다. 만일 자식의 `slot` attribute가 shadow tree의 어떤 slot name과도 일치하지 않는다면, 그 element는 전혀 그려지지 않는다.

예를들어, 아래 shadow tree의 `example-card` element를 보자 :

```html
<h2><slot name="title"></slot></h2>
<div><slot></slot></div>
```

만일 example card가 이렇게 사용된다면 :
```html
<example-card>
  <span slot="title">Card Title</span>
  <div>
    Some text for the body of the card.
  </div>
  <span slot="footer">This footer doesn't show up.</span>
</example-card>
```

첫번째 span은 `title` slot에 할당되었다. `slot` attribute를 가지지 않은 div는 default slot에 할당되었다. slot name이 있지만 shadow tree에는 없는 마지막 span은 flattened tree에 보이지 않고, 그려지지 않는다.

오직 최상위 자식만이 slot과 일치할 수 있다는 것을 알아두자. 아래 예제를 보라 :
```html
<example-card>
  <div>
   <span slot="title">Am I a title?</span>
  </div>
  <div>
    Some body text.
  </div>
</example-card>
```

`<example-card>`는 두 개의 최상위 자식(`<div>`)을 가진다. 둘다 default slot에 할당된다. span 의 `slot` attribute는 아무런 영향을 미치지 못하는데, span이 최상위 자식이 아니기 때문이다.

## Fallback content

slot은 *fallback content* 를 포함할 수 있다. Fallback content는 slot에 아무것도 할당되지 않았을 때 보여지는 것을 말한다. 예를들어 :

```html
<fancy-note>
  #shadow-root
    <slot id="icon">
      <img src="note.png">
    </slot>
    <slot></slot>
</fancy-note>
```

사용자는 element를 위해서 자신의 아이콘을 이렇게 공급할 수 있다 :

```html
<!-- shows note with warning icon -->

<fancy-note>

  <img slot="icon" src="warning.png">

  Do not operate heavy equipment while coding.

</fancy-note>
```

만일 사용자가 아이콘을 빠뜨렸다면, fallback content가 기본 아이콘을 제공한다 :

```html
<!-- shows note with default icon -->

<fancy-note>

  Please code responsibly.

</fancy-note>
```



## Multi-level distribution

slot element가 slot에 할당되기도 한다. 예를들어 2 계층의 shadow tree를 생각해보자.

```html
<parent-element>
  #shadow-root
    <child-element>
      <!-- parent-element renders its light DOM children inside
           child-element -->
      <slot id="parent-slot">

<child-element>
  #shadow-root
    <div>
      <!-- child-element renders its light DOM children inside this div -->
      <slot id="child-slot">
```

주어진 마크업은 아래와 같다 :

```html
<parent-element>
  <span>I'm light DOM</span>
</parent-element>
```

flattened tree는 아래와 같이 생겼을 것이다 :

```html
<parent-element>
  <child-element>
    <div>
      <slot id="child-slot">
        <slot id="parent-slot>
          <span>I'm in light DOM</span>
```

처음에는 이렇게 배치되는 것이 약간 혼란스러울 수 있다. 각각의 계층에서 light DOM children은 host의 shadow DOM의 slot에 할당된다. span("I'm light DOM") 은 `<parent-element>` 의 shadow DOM 내부의 `#parent-slot`에 할당된다. 그리고 `#parent-slot`은 `<child-element>`의 shadow DOM 내부의 `#child-slot`에 할당된다.

slot element가 그려지지 않으면 rendered tree는 훨씬 간단해진다 :

```html
<parent-element>
  <child-element>
    <div>
      <span>I'm in light DOM</span>
```

특정 언어에서, 하나의 slot이라도 그 assigned nodes 혹은 fallback content에 의해서 대체되었다면, slot의 *distributed nodes*는 곧 assigned nodes이다. 따라서 예제에서, `#child-slot`은 하나의 distributed node(span)를 갖는다. 당신은 distributed nodes를 *rendered tree에서 slot의 자리를 차지하는 node의 리스트*로 생각할 수 있다.



## Slot APIs

Shadow DOM은 distribution을 체크하기 위한 몇 가지 새로운 API를 제공한다: 

- `HTMLElement.assignedSlot` 프로퍼티는 element를 위한 assigned slot을 주거나, element가 slot에 할당되지 않으면 `null`을 준다 .
- `HTMLSlotElement.assignedNodes` 메서드는 주어진 slot과 연관된 node의 목록을 리턴한다. `{flatten: true}` 옵션과 함께 호출되면, slot의 *distributed nodes*를 리턴한다.
- HTMLSlotElement.slotchange 이벤트는 slot의 distributed nodes가 변경될 때 죽는다.

더 자세한 내용을 보려면 웹 기초의 [Working with slots in JS](https://developers.google.com/web/fundamentals/primers/shadowdom/?hl=en#workwithslots) 링크를 보라.



## 추가되거나 제거된 자식 관찰 (Observe added and removed children)

`Polymer.FlattenedNodesObserver` 클래스는 element의 *flattened tree*를 추적하기 위한 유틸리티를 제공한다. 어떤 `<slot>` element가 distributed node에 의해서 대체되었다면, 이는 child nodes의 목록이다. `FlattenedNodesObserver`는 `lib/utils/flattened-nodes-observer.html`로부터 로드될 수 있는 추가적인 유틸리티이다.

```html
<link rel="import" href="/bower_components/polymer/lib/utils/flattened-nodes-observer.html">
```

`Polymer.FlattenedNodesObserver.getFlattenedNodes(node)` 는 특정 노드의 flattened node 목록을 리턴한다.

언제 flattened node list가 변경되었는지를 추적하려면 `Polymer.FlattenedNodesObserver` 클래스를 사용하라.

```javascript
this._observer = new Polymer.FlattenedNodesObserver(this.$.slot, (info) => {
  this._processNewNodes(info.addedNodes);
  this._processRemovedNodes(info.removedNodes);
});
```

당신은 노드가 추가되거나 제거될 때 호출되는 콜백인 `FlattenedNodesObserver` 을 보았다. 이 콜백은 `addedNodes`와 `removedNodes`와 함께 하나의 오브젝트 인자를 갖는다.

이 메서드는 관찰하는 것을 중지할 수 있는 핸들을 리턴한다.

```javascript
this._observer.disconnect();
```

`FlattenedNodesObserver`에 관한 몇 가지 알아둘 점 :

- 콜백 인자(argument)는 element 뿐 아니라 추가되거나 제거된 노드까지 나열한다. 만일 element만 보고 싶다면 노드 목록을 필터할 수 있다 :

  ```javascript
  info.addedNodes.filter(function(node) {
    return (node.nodeType === Node.ELEMENT_NODE)
  });
  ```

- 옵저버(observer) 핸들은 또한 unit test를 위해 사용될 수 있는 `flush` 메서드를 제공한다.



# Event retargeting

캡슐화 되어있는 shadow tree를 지키기 위해서, 몇몇 이벤트는 shadow DOM 범위 안에서 중지된다.

다른 버블링 이벤트는 tree를 타고 올라오면서 재타겟팅 된다. 재타겟팅은 이벤트의 타겟을 조정해서 마치 리스닝 element처럼 그것이 같은 범위에서 element를 대표하도록 한다.

예를들어 주어진 tree가 다음과 같다면 :

```html
<example-card>
  #shadow-root
    <div>
      <fancy-button>
        #shadow-root
          <img>
```

만약 유저가 이미지 element를 클릭하면, 클릭이벤트는 tree를 타고 위로 올라간다(bubble up) :

- 이미지 element의 리스너 자신은 `<img>`를 타겟으로 받는다.
- `<fancy-button>`의 리스너는 `<fancy-button>`을 타겟으로 받는다. 왜냐하면 원래 타겟은 그 shadow root 내부에 있기 때문이다.
- `<example-card>`의 shadow DOM 내의 `<div>` 리스너 역시 `<fancy-button>` 을 타겟으로 받는다. 왜냐하면 그들은 같은 shadow DOM tree에 있기 때문이다.
- `<example-card>`의 리스너는 자기자신을 타겟으로 받는다.



이벤트는 `composedPath` 메서드를 제공한다. 이 메서드는 이벤트가 지나갈 nodes 배열을 리턴한다. 이 경우에, 배열은 다음을 포함한다 :

- `<img>` element 자신
- `<fancy-button>`의 shadow root
- `<div>` element
- `<example-card>`의 shadow root
- `<example-card>` 의 조상(예를들어, `<body>`, `<html>`, `document`, `window`)



기본적으로, 커스텀 이벤트는 shadow DOM 바운더리를 통해서 전파되지 **않는다**. 이를 허용하고 재타겟팅 하기 위해서는 반드시 `composed` 플래그를 `true`로 셋팅해야 한다 :

```javascript
var event = new CustomEvent('my-event', {bubbles: true, composed: true});
```

shadow tree 이벤트의 더 많은 정보를 원한다면 shadow DOM의 웹 기초 아티클인 [The Shadow DOM event model](https://developers.google.com/web/fundamentals/getting-started/primers/shadowdom#events) 를 참고하자.



# Shadow DOM styling

Shadow tree 내부의 스타일은 해당 shadow tree 범위로 한정된다. 그리고 밖에있는 element에 영향을 미치지 않는다. Shadow tree 외부의 스타일 역시 안쪽의 식별자와는 매칭되지 않는다. 그러나 `color`와 같은 상속가능한 스타일 프로퍼티는 여전히 상속되어 내려갈 수 있다.

```html
<style>

  body { color: white; }

  .test { background-color: red; }

</style>

<styled-element>
  #shadow-root
    <style>
      div { background-color: blue; }
    </style>
    <div class="test">Test</div>
```

이 예제에서 비록 `div` 식별자가 `.test` 식별자보다 메인 문서에서 덜 확정적이라고 하더라도, `<div>`는 파란 배경색을 갖는다. 왜냐하면 메인 문서의 shadow DOM에는 `<div>` 가 전혀 들어있지 않기 때문이다. 반대로 body 내의 글자색(white)은 아래 레벨인 `<styled-element>`와 그 shadow root로 상속된다.

shadow tree내부의 스타일이 외부와 일치하는 한가지 사례가 있다. `:host` 혹은 `:host()` 가짜클래스(pseudoclass)를 이용하면 스타일을 host element로 정의할 수 있다.

```html
#shadow-root
  <style>
    /* custom elements default to display: inline */
    :host {
      display: block;
    }
    /* set a special background when the host element
       has the .warning class */
    :host(.warning) {
      background-color: red;
    }
  </style>
```

당신은 또한 `::slotted()`를 사용해서 slot에 할당된 light DOM children에도 스타일을 줄 수 있다. 예를들어, `::slotted(img)` 는 shadow tree에서 slot에 할당된 어떤 이미지 태그를 선택한다.

```html
  #shadow-root
    <style>
      :slotted(img) {
        border-radius: 100%;
      }
    </style>
```

더 많은 정보를 위해서, shadow DOM의 웹 기초 아티클인 [Styling](https://developers.google.com/web/fundamentals/getting-started/primers/shadowdom#styling)을 참고하라.



# Theming(테마 정하기) and custom properties

당신은 shadow tree **외부의** CSS rule을 사용하는 것으로는 shadow tree의 어떤것에도 직접적으로 스타일을 줄 수 없다. 예외는 tree를 따라 상속되는 (color나 font 같은) CSS 프로퍼티이다. Shadow tree는 host로부터 CSS 프로퍼티를 상속한다.

유저가 element를 커스텀하게 하려면, 당신은 CSS 커스텀 프로퍼티 및 믹스인을 사용해서 특정 스타일링 프로퍼티를 노출시켜야한다. 커스텀 프로퍼티는 스타일링 API를 element에 추가하는 방법을 제공한다.

**Polyfill 제약사항**. 커스텀 프로퍼티나 믹스인의 polyfill 처리된 버전을 사용할 때, 당신이 알아야 할 수많은 제약사항이 있다. 자세한 것은 [the Shady CSS README file](https://github.com/webcomponents/shadycss/blob/master/README.md#limitations)을 참고하자.

> 참고 : Polyfill(번역자 추가)
>
> 폴리필이란? 폴리필(polyfill)은 개발자가 특정 기능이 지원되지 않는 브라우저를 위해 사용할 수 있는 코드 조각이나 플러그인을 말한다. 폴리필은 HTML5 및 CSS3와 오래된 브라우저 사이의 간격을 메꾸는 역할을 담당한다.
>
> 출처: [http://webdir.tistory.com/328](http://webdir.tistory.com/328) [WEBDIR]
>



당신은 커스텀 프로퍼티를 CSS rule에 대체될 수 있는 변수라고 생각할 수도 있다 :

```css
:host {
  background-color: var(--my-theme-color);
}
```

이것은 host의 background-color를 `--my-theme-color` 커스텀 프로퍼티 값으로 셋팅한다. 누구든 당신의 element를 사용하는 사람은 프로퍼티를 더 높은 레벨에서 셋팅할 수 있다 :

```css
html {
  --my-theme-color: red;
}
```

커스텀 프로퍼티는 tree 아래로 상속된다. 그래서 document 레벨로 셋팅된 값은 shadow tree 내부에서도 접근이 가능하다.

이것은 프로퍼티가 셋팅되지 않을 때를 대비해서 기본값을 포함할 수 있다 :

```css
:host {
  background-color: var(--my-theme-color, blue);
}
```

기본값은 심지어 다른 `var()` 함수가 될수도 있다 :

```css
background-color: var(--my-theme-color, var(--another-theme-color, blue));
```



## Custom property mixins

커스텀 프로퍼티 *믹스인*은 커스텀 프로퍼티 스펙의 상단에 만들어지는 기능이다. 기본적으로, 믹스인은 오브젝트 값을 갖는 커스텀 프로퍼티이다 :

```css
html {
  --my-custom-mixin: {
    color: white;
    background-color: blue;
  }
}
```

컴포넌트는 `@Apply` rule을 이용해서 모든 rule 집합을 import하거나 *mix in* 할 수 있다 :

```css
:host {
  @apply --my-custom-mixin;
}
```

`@apply` rule은 `--my-custom-mixin` inline 콘텐츠를 추가함으로써 그것이 사용된 곳에서 동일한 효과를 갖는다.



# Shadow DOM polyfills

Shadow DOM이 모든 플랫폼에서 사용 가능한 것이 아니기 때문에, Polymer는 만약 이것이 설치되어 있다면 shady DOM과 shady CSS polyfill의 이점을 택한다. 이 polyfill은 `webcomponents-lite.js` polyfill bundle에 포함된다.

이 polyfill은 좋은 퍼포먼스를 유지하면서 native shadow DOM의 합리적인 에뮬레이션을 제공한다. 그러나 완전히 polyfill될 수 없는 shadow DOM의 기능들이 있다. 만일 당신이 native shadow DOM을 포함하지 않는 브라우저를 지원한다면, 당신은 이 제한사항을 알고 있어야 한다. 또한 shady DOM으로 만들어진 어플리케이션을 디버깅할 때, shady DOM polyfill의 자세한 부분을 이해하는데도 도움이 된다.

## Polyfill은 어떻게 동작하는가

polyfill은 shadow DOM을 에뮬레이트하는데 몇 가지 기술들의 조합을 이용한다 :

- Shady DOM. Shadow tree의 논리적인 구분과 그 자손 tree를 내부적으로 동일하게 하라. 그래서 정확하게 자식이 light DOM에 추가되거나 shadow DOM이 그려지도록 하라. native shadow DOM API가 에뮬레이트 되기 위해서 영향받는 element의 DOM API를 패치하라.
- Shady CSS. shadow DOM 자식에 클래스를 추가하고 스타일 rule을 재작성함으로써 정확한 범위에 적용될 수 있도록 스타일 캡슐화를 제공하라.

아래 섹션은 각각의 polyfill에 관해서 더 깊이있게 논의한다.



**Shady DOM polyfill**

native shadow DOM이 없는 브라우저는 오직 document와 그 자식 tree밖에 그리지 못한다.

Shadow DOM의 flattened tree 그리는 것을 에뮬레이트 하기 위해서, shady DOM polyfill은 분리된 논리 tree와, 가상의 `children`과 `shadowRoot` 프로퍼티를 유지해야 한다. 각각의 host element의 실제 `children` (브라우저에 보이는 자손 tree)은 shadow와 light DOM children의 pre-flattened tree이다. 당신이 개발자도구를 통해서 보는 tree는 logical tree가 아니고 rendered tree처럼 보인다.

polyfill 아래에서, slot element는 tree의 브라우저 시점에는 나타나지 않는다. 따라서 native shadow DOM과는 다르게, slot은 이벤트 버블링의 일부가 되지 않는다.

polyfill은 shadow DOM에 의해서 영향을 받는 node의 기존의 DOM API를 패치한다. 여기서 node는 shadow tree안에 있거나, shadow tree에 연결되었거나(hose), shadow host의 light DOM children 을 말한다. 예를들어, shadow root와 node의  `appendChild` 를 호출할 때, polyfill은 light DOM children의 *가상의* tree의 자식을 더하고, *rendered* tree에서 자식이 어디 나타나야 하는지를 계산하고, 그리고 나서 정확한 장소의 실제 자손 tree에 더한다.

더 많은 정보를 위해서, [Shady DOM polyfill README](https://github.com/webcomponents/shadydom/blob/master/README.md) 을 보라.



**Shady CSS polyfill**

Shady CSS polyfill은 shadow DOM 스타일 캡슐화를 에뮬레이트 한다. 그리고 CSS 커스텀 프로퍼티 및 믹스인을 위한 에뮬레이션을 제공한다.

캡슐화를 에뮬레이트 하기 위해서, shady CSS polyfill은 shady DOM tree 안에서 클래스를 element에 더한다. 또한 안에서 element의 템플릿으로 정의된 스타일 rule을 재작성한다.

Shady CSS는 document-level 스타일시트에서 스타일 rule을 재작성하지 않는다. 이는 document-level 스타일이 shadow tree에 새어들어갈 수 있음을 의미한다. 그러나 그것은 element 외부에서 polyfilled style을 쓰기 위한 커스텀 element인 `<custom-style>`을 제공한다. 이것은 커스텀 CSS 프로퍼티와 다시쓰는 rule에 대한 지원을 포함한다. 그래서 shadow tree로 새어나가지 않는다.

```html
<custom-style>
  <style>
    /* Set CSS custom properties */
    html { --my-theme-color: blue; }
    /* Document-level rules in a custom-style don't leak into shady DOM trees */
    .warning { color: red; }
  </style>
</custom-style>
```

더 많은 정보를 위해서 [Shady CSS polyfill README](https://github.com/webcomponents/shadycss/blob/master/README.md)를 참고하라.



## Resources

더 읽을거리

- [Shadow DOM v1: self-contained web components](https://developers.google.com/web/fundamentals/primers/shadowdom/?hl=en) on Web Fundamentals.
- [Custom properties specification](https://www.w3.org/TR/css-variables-1/)).
- [Custom property mixins proposal](https://tabatkins.github.io/specs/css-apply-rule/).
- [Shady DOM polyfill README](https://github.com/webcomponents/shadydom/blob/master/README.md).
- [Shady CSS polyfill README](https://github.com/webcomponents/shadycss/blob/master/README.md).