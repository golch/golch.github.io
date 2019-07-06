---
layout: posts
comments: true
title:  "Django MySQL 연동하기"
date:   2017-01-20 14:39:20 +0900
categories: django
---

Django Tutorial 을 진행하면 기본 database 가 SQLite 로 되어있는 것을 볼 수 있다. 보통 웹사이트를 만들 때에 많이 사용하는 MySQL로 database 를 바꾸는 방법을 알아보자.  
[원문참고](https://docs.djangoproject.com/en/1.10/intro/tutorial02/){:target="_blank"}


1. PyMySQL 라이브러리 사용  
django 를 사용할때 MySQLdb 이걸 이용해서 DB연결을 하는 것이 보통이나 이 라이브러리가 python 버전에 영향을 받는 것 같다.(python3.x 지원을 안하는듯..)

따라서 MySQL 연동을 위해서는 다른 라이브러리를 사용해야 한다.

{% highlight shell %}
$ pip install PyMySQL
{% endhighlight %}

위 명령어로 설치하자. [PyMySQL 참고](https://github.com/PyMySQL/PyMySQL){:target="_blank"}

** 참고 : MySQL의 설치는 딱히 설명하지 않고 넘어간다.

2. settings.py  
django 로 돌아와서 settings.py 파일의 DATABASES 부분을 아래와 같이 설정한다.
{% highlight python %}
import pymysql
pymysql.install_as_MySQLdb()


... 중략


#MySQL
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'inform_place',
        'USER': 'django-test',
        'PASSWORD': '1111',
        'HOST': 'localhost',
        'PORT': '3306',
    }
}
{% endhighlight %}

이렇게 코딩한 후 다시 실행해봐야 한다. 단, migration 하는 것을 잊지말자.

{% highlight shell %}
$ python manage.py migrate
{% endhighlight %}

에러 없이 잘 동작한다면 서버를 올려보자.

{% highlight shell %}
$ python manage.py runserver
{% endhighlight %}

markdown 으로 포스팅 첨 해보는데 생각보다 편하다.  
우분투나 맥이나 아무데서나 쉽게할 수 있는듯..  

[jekyll-docs]: http://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
