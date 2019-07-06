---
layout: posts
comments: true
title: Quick tour of Polymer(번역)
date:   2017-04-16 12:19:20 +0900
categories: polymer
---

이 문서는 개인 스터디 목적으로 아래 링크의 문서를 번역한 것입니다(~~발번역...~~). 코드 동작을 직접 보고싶으신 분들은 아래 링크에서 확인하시기 바랍니다.
[https://www.polymer-project.org/2.0/start/quick-tour](https://www.polymer-project.org/2.0/start/quick-tour)


확실하게, Polymer 는 웹 컴포넌트 만드는 것을 간단하게 해준다.

Custom element는 boilerplate 코드를 줄이고, 복잡하고 상호작용하는 element 제작을 훨씬 쉽게 만들어주는 Polymer의 특별한 기능들에 이점을 줄 수 있다.
- Element 등록
- Lifecycle 콜백
- Property 관찰
- Shadow DOM template
- 데이터 바인딩

아무것도 설치하지 않아도, 이 섹션에서 당신은 Polymer 라이브러리를 간단히 살펴볼 수 있다. 어떤 샘플 코드에서든지 Edit on Plunker 버튼을 클릭하면 코드를 수정하고 즉시 확인할 수 있다.
아래 예제 중 더 공부하고 싶은 것이 있다면 버튼을 클릭하라.

## Element 등록하기 (Register an element)
Element를 등록하기 위해서는, `Polymer.Element` 를 확장한 ES6 클래스를 생성해야한다. 그리고 `customElements.define` 메서드를 호출해야한다. 이 메서드는 새로운 element를 브라우저에 등록한다. Element 등록할 때는 element의 이름과 클래스 이름을 같게 만들고 property와 메서드를 추가한다. 커스텀 element의 이름은 **반드시 ASCII 문자로 시작해야 하며 대쉬(-)를 포함해야 한다.**

**custom-element.html**
```html
<link rel="import"  href="https://polygit.org/polymer+2.0.0-rc.2/components/polymer/polymer-element.html">

<script>
  // Define the class for a new element called custom-element
  class CustomElement extends Polymer.Element {
    static get is() { return "custom-element"; }
    constructor() {
        super();
        this.textContent = "I'm a custom-element.";
      }
  }
  // Register the new element with the browser
  customElements.define(CustomElement.is, CustomElement);
</script>
```

**index.html**
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <script src="https://polygit.org/webcomponentsjs+1.0.0-rc.5/components/webcomponentsjs/webcomponents-loader.js"></script>
    <link rel="import" href="custom-element.html">
  </head>
  <body>
    <custom-element></custom-element>
  </body>
</html>
```

이 예제는 `<custom-element>` 를 추가하기 위해서, 그것이 초기화 될 때 lifecycle 콜백을 사용한다. 커스텀 element의 초기화가 끝나면, `ready` lifecycle 콜백이 호출된다. 당신은 element 가 생성된 이후에, 단 한번 `ready` 콜백을 사용할 수 있다.

[Learn more : Element Registration](https://www.polymer-project.org/2.0/docs/devguide/registering-elements)

[Learn more : Lifecycle Callbacks](https://www.polymer-project.org/2.0/docs/devguide/registering-elements#lifecycle-callbacks)

## Shadow DOM 추가하기
수많은 element는 내부의 DOM nodes를 포함하고 있다. DOM nodes 는 element의 UI와 행동을 수행한다. 당신은 당신의 element에 shadow DOM tree를 생성하기 위해서 Polymer's DOM 템플릿을 사용할 수 있다.

**dom-element.html**
```html
<link rel="import"  href="https://polygit.org/polymer+2.0.0-rc.2/components/polymer/polymer-element.html">

<dom-module id="dom-element">

  <template>
    <p>I'm a DOM element. This is my shadow DOM!</p>
  </template>

  <script>
    class DomElement extends Polymer.Element {
      static get is() { return "dom-element"; }
    }
    customElements.define(DomElement.is, DomElement);
  </script>

</dom-module>
```

**index.html**
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <script src="https://polygit.org/webcomponentsjs+1.0.0-rc.5/components/webcomponentsjs/webcomponents-loader.js"></script>
    <link rel="import" href="dom-element.html">
  </head>
  <body>
    <dom-element></dom-element>
  </body>
</html>
```

[Learn more : DOM Templating](https://www.polymer-project.org/2.0/docs/devguide/dom-template)

## Shadow DOM 조립하기
Shadow DOM은 구성을 조작할 수 있도록 해준다. Element의 자식은 분산될 수 있는데, 마치 shadow DOM tree에 삽입된 것처럼 그려진다.
이 예제는 단순한 태그를 생성하는데, 이것은 이미지를 스타일 추가된 `<div>`로 감싼 태그이다.

**picture-frame.html**

```html
<link rel="import"  href="https://polygit.org/polymer+2.0.0-rc.2/components/polymer/polymer-element.html">

<dom-module id="picture-frame">

  <template>
    <!-- scoped CSS for this element -->
    <style>
      div {
        display: inline-block;
        background-color: #ccc;
        border-radius: 8px;
        padding: 4px;
      }
    </style>
    <div>
      <!-- any children are rendered here -->
      <slot></slot>
    </div>
  </template>

  <script>
    class PictureFrame extends Polymer.Element {
      static get is() { return "picture-frame"; }
    }
    customElements.define(PictureFrame.is, PictureFrame);
  </script>

</dom-module>
```

**index.html**

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <script src="https://polygit.org/webcomponentsjs+1.0.0-rc.5/components/webcomponentsjs/webcomponents-loader.js"></script>
    <link rel="import" href="picture-frame.html">
  </head>
  <body>
    <picture-frame>
      <img src="https://www.polymer-project.org/images/logos/p-logo-32.png">
    </picture-frame>
  </body>
</html>
```

> **Note :** `<dom-module>` 내부에서 정의된 CSS는 해당 element의 shadow DOM의 범위에 한한다. 따라서 여기서 정의된 div 규칙도 `<picture-frame>` 내부의 `<div>` 태그에서만 적용된다.

[Learn more : Composition & Distribution](https://www.polymer-project.org/2.0/docs/devguide/shadow-dom#shadow-dom-and-composition)

## 데이터바인딩 사용하기
당연하게도, 정적인 shadow DOM을 사용하는 것으로는 충분하지 않다. 당신은 보통 당신의 element가 shadow DOM을 동적으로 업데이트 하기를 원할것이다.

데이터바인딩은 당신의 element 변화를 빠르게 전달하고 boilerplate 코드를 줄이기에 아주 좋은 방법이다. 당신은 컴포넌트에 작성된 property들을 `{% raw %}{{}}{% endraw %}` (double-mustache) 문법을 통해서 바인드할 수 있다. `{% raw %}{{}}{% endraw %}`는 그 괄호 안의 값이 가리키는 property 값에 의해서 대체된다.

**name-tag.html**
```html
<!-- import polymer-element -->
<link rel="import"  href="https://polygit.org/polymer+2.0.0-rc.2/components/polymer/polymer-element.html">

<dom-module id="name-tag">
  <template>
    <!-- bind to the "owner" property -->
    This is <b>{{owner}}</b>'s name-tag element.
  </template>
  
  <script>
    class NameTag extends Polymer.Element {
      static get is() { return "name-tag"; }
      
      // set this element's owner property
      constructor() {
        super();
        this.owner = "Daniel";
      }
    }
    
    customElements.define(NameTag.is, NameTag);
  </script>
</dom-module>
```

**index.html**
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <script src="https://polygit.org/webcomponentsjs+1.0.0-rc.5/components/webcomponentsjs/webcomponents-loader.js"></script>
    <link rel="import" href="name-tag.html">
  </head>
  <body>
    <name-tag></name-tag>
  </body>
</html>
```

[Learn more : Data Binding](https://www.polymer-project.org/2.0/docs/devguide/data-binding)

## 프로퍼티의 선언(Declare a property)
Property는 element의 공개 API에서 중요한 부분이다. Polymer의 선언된 property는 수많은 공통패턴을 제공한다. 예를들면 기본값을 셋팅하고, 마크업으로부터 property를 설정하고, property 변화를 관찰하는 것 등을 제공한다.
아래 예제는 위 마지막 예제에서 `owner` 프로퍼티를 선언한다. 또한 `index.html` 마크업으로부터 owner property 설정하는 것을 보여준다.

**configurable-name-tag.html**
```html
<link rel="import"  href="https://polygit.org/polymer+2.0.0-rc.2/components/polymer/polymer-element.html">

<dom-module id="configurable-name-tag">

  <template>
    <!-- bind to the "owner" property -->
    This is <b>[[owner]]</b>'s name-tag element.
  </template>
  
  <script>
    class ConfigurableNameTag extends Polymer.Element {
      static get is() { return "configurable-name-tag"; }
      // configure owner property
      static get properties() {
        return {
          owner: {
            type: String,
            value: "Daniel",
          }
        };
      }
    }
    customElements.define(ConfigurableNameTag.is, ConfigurableNameTag);
  </script>

</dom-module>
```

**index.html**
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <script src="https://polygit.org/webcomponentsjs+1.0.0-rc.5/components/webcomponentsjs/webcomponents-loader.js"></script>
    <link rel="import" href="configurable-name-tag.html">
  </head>
  <body>
    <!-- configure a property from markup by setting
         the corresponding attribute                 -->
    <configurable-name-tag owner="Scott"></configurable-name-tag>
  </body>
</html>
```

[Learn more : Declared Properties](https://www.polymer-project.org/2.0/docs/devguide/properties)

## 프로퍼티 바인딩 하기(Bind to a property)
다음 콘텐츠에 덧붙이자면, 당신은 element의 property에 바인딩을 할 수 있다(`property-name="[[binding]]"`을 이용해서). Polymer property는 선택적으로 양방향 바인딩을 지원할 수 있는데, `property-name="{{binding}}"` 과 같이 표현한다.
이 예제는 양방향 바인딩을 사용한다 : 커스텀 element인 `iron-input`에 `owner` property를 바인딩 해서 사용자 타입에 따라서 업데이트 된다.

**editable-name-tag.html**
```html
<link rel="import"  href="https://polygit.org/polymer+2.0.0-rc.2/iron-input+polymerelements+:2.0-preview/shadycss+webcomponents+1.0.0-rc.2/components/polymer/polymer-element.html">
<!-- import the iron-input element -->
<link rel="import"  href="https://polygit.org/polymer+2.0.0-rc.2/iron-input+polymerelements+:2.0-preview/shadycss+webcomponents+1.0.0-rc.2/components/iron-input/iron-input.html">

<dom-module id="editable-name-tag">

  <template>
    <!-- bind to the "owner" property -->
    <p>This is <b>[[owner]]</b>'s name-tag element.</p>
    
    <!-- iron-input exposes a two-way bindable input value -->
    <iron-input bind-value="{{owner}}">
      <input is="iron-input" placeholder="Your name here...">
    </iron-input>
  </template>

  <script>
    class EditableNameTag extends Polymer.Element {
      static get is() { return "editable-name-tag"; }
      
      // configure the owner property
      static get properties() {
        return {
          owner: {
            type: String,
            value: 'Daniel'
          }
        };
      }
      
    }
    customElements.define(EditableNameTag.is, EditableNameTag);
  </script>

</dom-module>
```

**index.html**
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <script src="https://polygit.org/webcomponentsjs+1.0.0-rc.5/components/webcomponentsjs/webcomponents-loader.js"></script>
    <link rel="import" href="editable-name-tag.html">
  </head>
  <body>
    <editable-name-tag></editable-name-tag>
  </body>
</html>
```

>**Note :** `iron-input` 은 기본태그인 `<input>`을 감싸고, 양방향 바인딩과 값 확인(validation)을 제공한다.

## `<dom-repeat>` 사용해서 템플릿 반복하기
템플릿 반복자(`dom-repeat`)는 배열(array)로 바인드하는데 특화된 템플릿이다. 이것은 템플릿의 콘텐츠들을 배열의 항목으로 포함하는 하나의 인스턴스(instance)를 생성한다.

**employee-list.html**
```html
<!-- import polymer-element -->
<link rel="import"  href="https://polygit.org/polymer+2.0.0-rc.2/components/polymer/polymer-element.html">

<!-- import template repeater --> 
<link rel="import"  href="https://polygit.org/polymer+2.0.0-rc.2/components/polymer/lib/elements/dom-repeat.html">

<dom-module id="employee-list">
  <template>
    <div> Employee list: </div>
    <p></p>
    <template is="dom-repeat" items="{{employees}}">
        <div>First name: <span>{{item.first}}</span></div>
        <div>Last name: <span>{{item.last}}</span></div>
        <p></p>
    </template>
  </template>
  <script>
    class EmployeeList extends Polymer.Element {
      static get is() { return "employee-list"; }
      
      // set this element's employees property
      constructor() {
        super();
        this.employees = [
          {first: 'Bob', last: 'Li'},
          {first: 'Ayesha', last: 'Johnson'},
          {first: 'Fatma', last: 'Kumari'},
          {first: 'Tony', last: 'Morelli'}
        ]; 
      }
    }
  customElements.define(EmployeeList.is, EmployeeList);
  </script>

</dom-module>
```

**index.html**
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <script src="https://polygit.org/webcomponentsjs+1.0.0-rc.5/components/webcomponentsjs/webcomponents-loader.js"></script>
    <link rel="import" href="employee-list.html">
  </head>
  <body>
    <employee-list></employee-list>
  </body>
</html>
```

[Learn more : Template Repeater](https://www.polymer-project.org/2.0/docs/devguide/templates)
