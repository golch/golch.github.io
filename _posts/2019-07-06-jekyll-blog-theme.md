---

layout: posts
comments: true
title: Jekyll 블로그에 테마 적용 (minimal mistakes)
date:   2019-07-06 12:54:20 +0900
categories: blog
---

## jekyll theme
기본테마에서 소스코드 부분 css 바꾸고 폰트를 바꿔서 사용하고 있었는데 왠지 마음에 안들어서 이번 기회에 테마를 변경하기로 했다.
테마는 여러 사람들이 추천해준 것들을 이것저것 눌러보다가 minimal-mistakes 이 녀석으로 정했다.  

[https://junhobaik.github.io/jekyll-apply-theme/](https://junhobaik.github.io/jekyll-apply-theme/)  
이 분의 적용기를 보고 따라했다.

[https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/](https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/)  
역시 문서작업이 잘되어있다.

막상 적용을 하고보니 폰트 사이즈나 세세한 스타일을 약간 조정하고 싶은 생각이 들지만, 일단 그냥 사용을 해 보자.
기본테마보다는 훨씬 좋은것 같다.

작업을 하다가 발견한게 있는데 jekyll 블로그의 템플릿 문법은 `{% raw %}{{}}{% endraw %}` 을 사용하도록 되어있다. 그래서 저 기호를 내 본문에 표현하려고 해도 안보이는 문제점이 있다.
검색을 해보니 raw 태그 라는것을 쓰면 된다고 한다.  
그 문법도 여기에 쓰기가 어려운데, 앞에는 raw 뒤에는 endraw 로 감싸는 방법이다.  


덧.  
ASUS 의 25만원짜리 노트북을 새로 샀다.  
셀러론에 4G램, 32G 디스크 11.6인치 무게 980g  
가볍고 배터리 오래가고 액정화면 쓸만하고 키보드도 그럭저럭 괜찮다.  
windows 10 이 깔려서 왔는데 도저히 느려서 못쓰겠더라.  
바로 밀어버리고 xubuntu 1804를 설치.  
부팅 빠르고 동작 빠릿하다.  
산김에 자꾸 가지고 놀려고 블로그 테마변경 작업을 시작했다.ㅋㅋ  
지킬서버 띄우는 정도는 문제없이 돌아간다.  
확실히 안드로이드 패드 보다는 활용도가 높다. 안드로이드는 스마트폰의 확대버전 정도라서, 책보거나 동영상 볼때는 좋은데 다른 일을 못하는게 불만.  
얘를 자주자주 가지고 다녀야겠다.  