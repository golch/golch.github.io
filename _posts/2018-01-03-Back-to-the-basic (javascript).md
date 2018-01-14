---

layout: post
comments: true
title: Back to the basic (javascript)
date:   2018-01-03 12:10:20 +0900
categories: javascript
---

## 기본으로

최근 파이썬을 사용해서 이런저런 개발 업무를 하고 있었는데, 다른 개발자분과 이야기를 나누다가 문득 기본이 부족하다는 느낌을 받았다. 아무래도 컴퓨터 공학과 출신도 아니고, 그 동안 어떤 문제가 해결되면 그냥 거기서 끝나버리는 버릇이 생긴 것 같다. 다시 처음 프로그래밍을 공부하는 학생의 마음으로 기본적인 것들을 하나하나 정리해 나가서, 궁극적으로는 자바스크립트를 좀더 잘 사용하기 위해서 이 포스팅을 시작하게 되었다. 부디 꾸준히 만들어 나가길...  



## 자바스크립트의 자료형

이 내용은 https://developer.mozilla.org/ko/docs/Web/JavaScript/Data_structures 사이트를 참고하여 작성하였습니다.  



언어를 처음 배울 때는 자료구조를 먼저 공부하는 것이 당연하다. ~~내가 잘 몰랐을뿐...~~ 

자바스크립트는 크게 기본 자료형과 객체 자료형 두 가지 자료형을 가지고 있다.

- 기본 자료형
  - Number : 모든 숫자형 (integer, float, double, ... , NaN)
  - String
  - Null : null
  - Undefined : undefined (값이 정해지지 않음)
  - Boolean : true or false
  - Symbol (ECMAScript6 추가됨) : 추후 자세히 공부할 예정
- Object 자료형
  - 일반적으로 컴퓨터 과학에서, 객체는 식별자로 참조할 수 있는, 메모리에 있는 값이다.
  - 속성 : 자바스크립트의 객체는 속성을 담고있는 가방(collection) 으로 볼 수 있다. 속성은 객체를 포함해서 어떤 자료형도 될 수 있다. 속성을 식별하는 키 값은 String 혹은 Symbol 자료형 이다.
  - 일반 객체 및 함수
    - 자바스크립트 객체는 키와 값의 매핑이다.
    - 함수는 일반 객체에서 호출 가능한 특성을 추가한 객체이다.
  - 배열
    - 배열은 정수키를 가지는 일련의 값들을 표현하기 위한 객체이다.
    - 배열에는 length 속성이 있다.
    - indexOf, push 같은 함수를 사용할 수 있다.
    - 배열(array)는 리스트(list)나 집합(set)을 표현하는데 적합하다.
  - Dates
    - 시간을 사용하고 싶다면 [Date utility](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)를 사용하자. 최고의 선택이다.
  - WeakMaps, Maps, Sets
    - ECMAScript6 에서 표준이 될 것 같다.
    - 키가 문자열 뿐 아니라 객체가 될 수도 있다. Set은 오브젝트의 집합을 나타내는 반면에 WeakMaps와 Maps는 오브젝트에 값을 연관시킬 수 있다. Map과 WeakMap의 차이는 전자는 오브젝트 키를 열거할 수 있다는 것이다. 이것은 가비지 콜렉션에서 이점을 준다.
    - ECMAScript 5를 이용해서 Map과 Set을 구현할 수 있을 것이다. 그러나 오브젝트는 크기 비교가 안된다는 점 때문에(예를들어 어떤 오브젝트는 다른 오브젝트보다 '작다'라고 할 수 없다) look-up에 소요되는 시간이 선형 시간이지 않을 것이다. 네이티브 구현은(WeakMaps를 포함해서) look-up 시간이 거의 로그 시간에서 상수 시간이다.


