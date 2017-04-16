#[Polymer Project]
#Custom Element Concept

개인 스터디 목적으로 번역합니다.(~~발번역....~~)
원문 : https://www.polymer-project.org/2.0/docs/devguide/custom-elements

Custom Elements 는 web을 위한 컴포넌트 모델을 제공한다. Custom Elements 가 제공하는 사양은 다음과 같다.
- 클래스와 Custom Element 이름을 연결하는 방법(메커니즘)
- Custom Element 상태(instance) 변경시, lifecycle callback 모음
- instance 에서 특정 attribute 가 변경될 때마다 호출된 callback

이를 더하면, 당신은 상태 변경에 반응하고 public API로 된 요소(element)를 만들 수 있다.

이 문서는 Polymer와 연관된 Custom Elements 의 개요를 제공한다. 더 자세한 Custom Elements의 개요를 원한다면 다음 링크를 참고하길 바란다.
https://developers.google.com/web/fundamentals/getting-started/primers/customelements

Custom Element를 정의하기 위해서 당신은 ES6 클래스와 연결된 Custom Element 이름을 생성해야 한다.
~~~javascript
// Create a class that extends HTMLElement (directly or indirectly)
class MyElement extends HTMLElement { … };

// Associate the new class with an element name
window.customElements.define('my-element', MyElement);
~~~

당신은 Custom Element를 마치 표준 element 처럼 이용할 수 있다.
```html
<my-element></my-element>
```
혹은 :
~~~javascript
var myEl = document.createElement('my-element');
~~~
혹은 :
~~~javascript
var myEl = new MyElement();
~~~

이 element class는 그것의 행동과 public API를 알려준다. class는 반드시 HTMLElement나 그 서브클래스를 확장(extend)해야한다(예를들어, 다른 custom element).

>**Custom Element 이름.** 
>사양에 따르면, custom element의 이름은 **반드시 소문자(ASCII문자)로 시작해야 하며, 대시(-)문자를 포함해야 한다.** 이미 존재하는 이름과 같아서 사용할 수 없는 element 이름도 있다. 자세한 내용은 아래 링크를 참고하길 바란다.
>https://html.spec.whatwg.org/multipage/scripting.html#custom-elements-core-concepts

Polymer는 기본 custom element 사양 위에 기능 모음을 제공한다. 이 기능들을 당신의 element에 추가하려면, Polymer의 기본 element class 를 확장(extend)해라.
Polymer.Element :
```javascript
<link rel="import" href="/bower_components/polymer/polymer_element.html">

<script>
  class MyPolymerElement extends Polymer.Element {
    static get is() { return 'my-polymer-element'; }
  }

  window.customElements.define(MyPolymerElement.is, MyPolymerElement);
</script>
```

Polymer는 또한 element name 이름을 return하는 **is** 라는 getter를 제공하기 위한 class를 요구한다.

Polymer는 기본 custom element에 다음 기능모음을 더한다.
- 일반적인 일을 처리할 수 있는 instance methods
- properties와 attributes를 자동처리 : 예를들어 동일한 attributes 기반으로 property를 셋팅하는 것
- 이미 작성된 `<template>` 기반으로 element instance를 위한 shadow DOM tree를 생성한다.
- 데이터 바인딩을 지원하고, property 변화를 관찰하고 계산하는 데이터 시스템 제공

##Custom Element lifecycle
Custom element 스펙은 특정 lifecycle 변화에 반응하는 당신의 코드를 실행하게 해주는 "custom element reactions"라고 불리는 callback 모음을 제공한다.

| Reaction                 | Called                                                                                   |
|--------------------------|------------------------------------------------------------------------------------------|
| constructor              | element가 업그레이드 될 때 호출(element가 생성되거나, 이전에 생성된 element가 정의될 때) |
| connectedCallback        | element가 문서에 추가될 때 호출                                                          |
| disconnectedCallback     | element가 문서에서 삭제될 때 호출                                                        |
| attributeChangedCallback | element의 attribute가 변경되거나 추가되거나 삭제되거나 수정될 때 호출                    |

각각의 reaction을 위해서 실행코드의 첫 줄은 반드시 superclass 생성자(constructor)나 reaction을 호출해야 한다. 생성자를 위해서는 간단히 `super()`를 호출하면 된다.

```javascript
constructor() {
  super();
  // …
}
```

다른 reaction을 위해서는 superclass method를 호출한다. 이것 때문에 Polymer는 element의 lifecycle에 끼어들 수 있다.

```javascript
connectedCallback() {
  super.connectedCallback();
  // …
}
```

element의 생성자는 몇가지 특별한 제약사항을 가진다 :
- 생성자 안의 첫 구문은 반드시 파라미터 없이 `super()` 메서드를 호출해야 한다.
- 생성자는 return문을 포함할 수 없다(단, `return` 혹은 `return this` 같은 단순 early return 은 가능).
- 생성자는 element의 attribute 혹은 자식을 시험할 수 없다. 또한 attribute나 자식을 추가하는 것도 안된다.

이런 제약들의 완전한 목록을 보려면 WHATWG HTML Specification의 다음 링크를 확인하라. [Requirements for custom element constructors](https://html.spec.whatwg.org/multipage/scripting.html#custom-element-conformance)

가능하면 생성자에서 처리하는 대신에 `connectedCallback` 혹은 그 뒤로 연기하라.

## One-time initialization


