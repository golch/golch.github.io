---

layout: posts
comments: true
title: Django Study(1)
date:   2019-01-01 13:31:20 +0900
categories: python, djnago
---

## 다짐

새해를 맞아 앞으로 어떻게 공부할지에 대한 고민을 잠시 해봤다. 항상 시작했다가 조용히 그만두는게 안좋은 버릇이라...
앞으로 작은 내용이라도 공부했던 것들을 정리해서 블로그에 꾸준히 올려보려고 한다. 나중에라도 다시 볼 수 있도록 하는 의미도 있고, 다른 분들께 도움이 된다면 더욱 좋겠다.

## Django Study

일단 공부할 첫 주제로는 django 를 선택했다. 회사에서 필요한 것과 약간 겹치기도 하고, 코드와 설명을 함께 작성하기 적당할 것 같아서이다.
앞으로 주제도 조금씩 확장해볼 계획이다.

## django login

django 에서의 로그인 처리에 대해서 공부를 할 것이다. 간단할 것 같지만 막상 필요해서 구현하려고 하면 고민할게 많은... 그래서 이번 기회에 정리를 조금 해두면 좋을 것 같다.
기본적인 로그인 / 로그아웃 기능과 실제로 로그인이 필요한 페이지를 정하고, 로그인이 안된 상태일때 어떤 페이지를 보여줘야 할지 등을 다룰 것이고, 여유가 된다면 회원가입 기능까지 구현을 할 예정이다.

## Using Tools

- 개발용 IDE 로는 pycharm 을 사용한다.
- python 은 3.6 버전을 사용하며, 환경은 virtualenv 를 사용한다.
- Django 는 2.1.4 버전을 사용한다.
- database 는 sqlite3 을 사용한다.

## 구현한 내용

우선 django 를 활용해서 mysite 라는 app 을 생성한다. 참고로 위에 나열한 환경설정 및 django 처음 실행에 관련한 내용은 이 포스팅에서는 제외한다.
로그인 등 user 권한관리에 관련된 내용을 처리할 user_auth 라는 app 을 생성한다.
주의할 점은 settings.py 의 INSTALLED_APPS 에 추가를 해야한다.

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    # added by golch from here
    'user_auth',
]
```

그리고 새로 만든 user_auth 로 url 이 연결될 수 있도록 mysite/urls.py 에 패턴을 추가한다.

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('auth/', include('user_auth.urls')),
]
```

이제 user_auth 에도 urls.py 를 만들어야 한다. 아래와 같이 만든다.

```python
from .views import login_page, login_view

urlpatterns = [
    path('login/', login_page, name='login'),
    path('do_login/', login_view, name='do_login'),
]
```

이름이 직관적이지 않은 것 같지만 (이름 짓는게 제일 어렵다.) login 은 페이지를 보여줄 것이고, do_login 은 실제 로그인 처리를 할 것이다.

그래서 user_auth 의 views.py 는 아래와 같이 만든다.

```python
from django.shortcuts import render
from django.contrib.auth import authenticate, login, logout


def login_view(request):
    username = request.POST['username']
    password = request.POST['password']
    user = authenticate(request, username=username, password=password)
    if user is not None:
        login(request, user)
        # Redirect to a success page.
        return render(request, 'login_success.html')
    else:
        # Return an 'invalid login' error message.
        return render(request, 'login_failed.html')


def login_page(request):
    return render(request, 'login.html')

```

아직 로그인 후 할일과 로그인 실패일때 할일을 정하지 못했으므로 그냥 각각의 html 을 만들어서 리다이렉트 하도록 하였다.
django 의 좋은 점은 이정도의 코드만 작성하면 기본적인 로그인 기능이 동작한다는 것이다.
게다가 admin 페이지까지 공짜로 활용할 수 있다.

로그인 관련한 좀더 상세한 내용은 다음 포스팅에서 이어서 다루도록 하겠다.


### 참고자료 링크

[https://docs.djangoproject.com/en/2.1/topics/auth/default/](https://docs.djangoproject.com/en/2.1/topics/auth/default/)
[https://docs.djangoproject.com/en/1.7/topics/templates/](https://docs.djangoproject.com/en/1.7/topics/templates/)

소스코드 저장소 : 
[https://github.com/golch/django_study](https://github.com/golch/django_study)
