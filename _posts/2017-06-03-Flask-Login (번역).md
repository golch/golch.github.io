---

layout: posts
comments: true
title: Flask-Login (번역)
date:   2017-06-03 12:10:20 +0900
categories: flask

---

# Flask-Login (번역)

원문 : [https://flask-login.readthedocs.io/en/latest/](https://flask-login.readthedocs.io/en/latest/)

> *마크다운 문법으로 작성했기 때문에, 내부링크가 동작하지 않습니다. 또한 원문 하단의 API 문서는 추후에 다시 번역해서 업로드 하도록 하겠습니다.*



플라스크 로그인은 플라스크를 위한 유저 세션 관리를 제공한다. 이것은 장기간에 걸쳐 로그인, 로그아웃, 그리고 당신의 유저의 세션을 기억하는 등의 평범한 업무를 처리한다.

이것은 :

- 세션 안의 활성 유저의 ID를 저장한다. 그리고 로그인과 로그아웃을 쉽게 해준다.
- 로그인 한 유저(혹은 로그아웃 한 유저)로 제한하여 볼 수 있도록 해준다.
- 일반적으로 까다로운 "나를 기억하는" 기능성을 처리하도록 해준다.
- 쿠키도둑으로부터 당신의 유저세션을 보호하는 것을 도와준다.
- Flask-Principal 이나 다른 권한관리 확장기능과 통합을 가능하게 해준다.



그러나 다음은 하지 않는다 :

- 특정 데이터베이스나 다른 저장방법을 제공하지 않는다. 어떻게 유저를 저장할지는 온전히 당신의 책임이다.
- username과 password를 사용하거나, OpenIDs, 혹은 다른 권한부여 방법을 사용하는 것을 제한하지 않는다.
- "logged in or not" 너머의 권한관리는 처리하지 않는다.
- 유저 등록이나 계정 복구는 처리하지 않는다.



## 설치하기

pip를 통한 설치 :

```shell
$ pip install flask-login
```





## 어플리케이션 설정

플라스크-로그인을 사용하는데 가장 중요한 부분은 [LoginManager](#flask_login.LoginManager.user_loader) class이다. 당신은 어플리케이션을 위해서 코드 어딘가에 아래처럼 하나를 생성해야 한다.

```python
login_manager = LoginManager()
```



로그인 매니저는 당신의 어플리케이션과 플라스크-로그인이 함께 동작하기 위한 코드를 포함한다. 이것은 유저를 어떻게 불러올 것인지, 유저가 로그인을 원할 때 어디로 보낼 것인지 등의 코드이다.

일단 실제 어플리케이션 오브젝트가 생성되고 나면, 당신은 로그인을 위해 이것을 설정할 수 있다 :

```python
login_manager.init_app(app)
```



## 어떻게 동작하는가

당신은 [user_loader](#flask_login.LoginManager.user_loader) 콜백함수를 제공하는 것이 필요할 것이다. 이 콜백함수는 세션에 저장된 유저 ID로부터 유저 객체를 다시 로드하는데 쓰인다. 이것은 유저의 unicode ID를 가져야 하며, 이에 상응하는 유저 객체를 리턴한다. 예제를 보자 :

```python
@login_manager.user_loader
def load_user(user_id):
    return User.get(user_id)
```



만일 ID가 검증되지 않으면 [None](https://docs.python.org/3/library/constants.html#None) (exception이 발생하지 않는다!)을 리턴한다(이런 경우에는, 세션에서 ID를 수동으로 제거하고 프로세스는 계속될 것이다.).



## 당신의 유저 클래스

당신이 유저들을 나타내기위해 사용하는 클래스는 아래의 프로퍼티와 메서드를 구현(implement)한다 :



**is_authenticated**

만일 유저가 권한을 획득했다면(그들이 유효한 자격증명을 제공했다면) 이 프로퍼티는 [True](https://docs.python.org/3/library/constants.html#True)를 리턴해야 한다. (유저가 [login_required](#flask_login.login_required)의 기준을 채웠을 때에만 권한을 획득한다.)



**is_active**

이 유저가 활성 유저이고, 권한을 획득했다면 이 프로퍼티는 [True](https://docs.python.org/3/library/constants.html#True)를 리턴해야 한다. 또한 이 유저들은 계정을 활성화했고, 정지되지 않았으며 당신의 어플리케이션이 그 계정을 거부하지 않았어야 한다. 비활성화 된 계정들은 (억지로 그렇게 하지 않는 이상) 로그인이 안 될 것이다.



**is_anonymous**

만일 익명의 사용자(anonymous user)라면 이 프로퍼티는 [True](https://docs.python.org/3/library/constants.html#True)를 리턴해야 한다. (실제 유저라면 대신 [False](https://docs.python.org/3/library/constants.html#False)를 리턴한다.)



**get_id()**

이 메서드는 유니크하게 이 유저를 구분하는 **unicode**를 리턴해야 한다. 또한 이 **unicode**는 [user_loader](#flask_login.LoginManager.user_loader) 콜백함수로부터 유저를 불러오는데 사용될 수도 있다. 만일 ID가 내부적으로는 [int](https://docs.python.org/3/library/functions.html#int) 이거나 다른 타입이라도 당신은 **unicode**로 변환해야만 한다. 따라서 이것이 **unicode**라는 것을 명심하라.



유저 클래스 구현(implement)하는 것을 쉽게 하기 위해서, 당신은 [UserMixin](#flask_login.UserMixin) 으로부터 상속받을 수 있다. UserMixin은 모든 프로퍼티와 메서드를 위해서 디폴트 구현을 제공한다.(물론, 반드시 요구되는 것은 아니다.)



## 로그인 예제

일단 유저가 권한획득을 한다면, 당신은 [login_user]() 함수를 통해서 그들을 로그인 시킨다.

예제 :

```python
@app.route('/login', methods=['GET', 'POST'])
def login():
    # Here we use a class of some kind to represent and validate our
    # client-side form data. For example, WTForms is a library that will
    # handle this for us, and we use a custom LoginForm to validate.
    form = LoginForm()
    if form.validate_on_submit():
        # Login and validate the user.
        # user should be an instance of your `User` class
        login_user(user)

        flask.flash('Logged in successfully.')

        next = flask.request.args.get('next')
        # is_safe_url should check if the url is safe for redirects.
        # See http://flask.pocoo.org/snippets/62/ for an example.
        if not is_safe_url(next):
            return flask.abort(400)

        return flask.redirect(next or flask.url_for('index'))
    return flask.render_template('login.html', form=form)
```

*경고 :* 당신은 **반드시** [next]() 파라미터의 유효성을 검사해야 한다. 만일 이것을 하지 않으면, 당신의 어플리케이션은 오픈 리다이렉트(open redirects)에 취약해질 것이다. is_safe_url의 구현 예시를 보려면 이 [Flask Snippet](http://flask.pocoo.org/snippets/62/)을 참고하라.

> 참고(역주) - **오픈 리다이렉트** : 명확한 URL을 사용하지 않고, 사용자로부터 받은 파라미터 정보를 그대로 리다이렉트 하는 경우 발생되는 취약점. 국문으로 번역하여 "공개된 재전송" 이라고 부르는 문서도 있으나 여기서는 영문 그대로 독음하여 번역하였다. 참고 : [티스토리 블로그](http://horae.tistory.com/entry/Open-Redirect-Cheat-sheet) 

이렇게나 간단하다. 당신은 모든 템플릿에서 사용 가능한 [current_user](#flask_login.current_user) 프록시를 통해 로그인한 유저 정보에 접근할 수 있다 :

```jinja2
{% if current_user.is_authenticated %}
  Hi {{ current_user.name }}!
{% endif %}
```



로그인 할 당신의 유저정보를 요청하는 화면은 [login_required](#flask_login.login_required) 를 통해서 장식될 수 있다 :

```python
@app.route("/settings")
@login_required
def settings():
    pass
```



유저가 로그아웃 할 준비가 되었다면 :

```python
@app.route("/logout")
@login_required
def logout():
    logout_user()
    return redirect(somewhere)
```

그들은 로그아웃 될 것이다. 그리고 그들의 세션의 어떤 쿠키도 정리될 것이다.



## 로그인 프로세스 커스터마이징

기본으로 유저가 로그인 되지 않은 상태에서 [login_required](#flask_login.login_required) 화면에 접근하려고 할 때, Flask-Login은 메시지를 보여주고 로그인 화면으로 리다이렉트 할 것이다. (만일 로그인 화면이 세팅되어있지 않다면, 401에러를 내고 종료될 것이다.)

로그인 화면의 이름은 [LoginManager.login_view](#flask_login.LoginManager.login_view)로 셋팅될 수 있다. 예제를 보면 :

```python
login_manager.login_view = "users.login"
```



기본으로 보여주는 메시지는 "Please log in to access this page." 이다. 이 메시지를 편집하려면 [LoginManager.login_message](#flask_login.LoginManager.login_message)를 셋팅하면 된다 :

```python
login_manager.login_message = u"Bonvolu ensaluti por uzi tiun paĝon."
```



이 메시지 카테고리를 편집하려면 **LoginManager.login_message_category**를 셋팅한다 :

```python
login_manager.login_message_category = "info"
```



로그인 화면으로 리다이렉트 될 때, 쿼리스트링에 **next** 변수를 가지고 있을 것이다. 이 변수는 유저가 접근하려고 시도했던 페이지를 가리킨다. 또는 **USE_SESSION_FOR_NEXT**가 [True](https://docs.python.org/3/library/constants.html#True)라면, 그 페이지는 세션 내부에 **next** 키로 저장된다.

만일 이 프로세스를 더 커스터마이징 하고싶다면, [LoginManager.unauthorized_handler](#flask_login.LoginManager.unauthorized_handler)로 함수를 장식하라 :

```python
@login_manager.unauthorized_handler
def unauthorized():
    # do stuff
    return a_response
```



## Authorization header를 사용한 로그인

> 주의! : 이 메서드는 더이상 사용되지 않을 예정이다. 대신 아래의 **request_loader** 를 사용하라.

때때로 당신은 Authorization header를 활용한 기본 권한 로그인을 지원하기를 원할 것이다. header를 통한 로그인을 지원하려면 당신은 header_loader 콜백함수 제공이 필요할 것이다. 이 콜백은 user id 대신에 header 값을 받는다는 것을 제외하면, 당신의 user_loader 콜백과 동일게 동작해야 한다. 예제를 보자 :

```python
@login_manager.header_loader
def load_user_from_header(header_val):
    header_val = header_val.replace('Basic ', '', 1)
    try:
        header_val = base64.b64decode(header_val)
    except TypeError:
        pass
    return User.query.filter_by(api_key=header_val).first()
```

기본으로 Authorization header 값은 당신의 header_loader 콜백에 전달된다. 이 값은 AUTH_HEADER_NAME 설정을 사용함으로서 변경할 수 있다.



## Request Loader를 활용한 커스텀 로그인

때때로 당신은 쿠키(header 값이나 질의어로 전달된 api key)를 사용하지 않고 유저를 로그인 시키기를 원할 것이다. 이런 경우에는 **request_loader** 콜백을 사용해야 한다. 이 콜백은 user_id 대신에 Flask request를 받는다는 것을 제외하면, 당신의 [user_loader](#flask_login.LoginManager.user_loader) 콜백과 동일하게 동작해야 한다.

예를 들어, URL 인자와 **Authorization** header를 이용한 Basic Auth를 둘다 지원하는 것을 보자 :

```python
@login_manager.request_loader
def load_user_from_request(request):

    # first, try to login using the api_key url arg
    api_key = request.args.get('api_key')
    if api_key:
        user = User.query.filter_by(api_key=api_key).first()
        if user:
            return user

    # next, try to login using Basic Auth
    api_key = request.headers.get('Authorization')
    if api_key:
        api_key = api_key.replace('Basic ', '', 1)
        try:
            api_key = base64.b64decode(api_key)
        except TypeError:
            pass
        user = User.query.filter_by(api_key=api_key).first()
        if user:
            return user

    # finally, return None if both methods did not login the user
    return None
```



## 익명 유저

기본으로, 실제로 로그인 하지 않은 유저는 **current_user**가 **AnonymousUserMixin** 객체로 셋팅된다. 이것은 아래의 프로퍼티와 메서드를 가진다 :

- **is_active**와 **is_authenticated**는 [False](https://docs.python.org/3/library/constants.html#False)
- **is_anonymouse**는 [True](https://docs.python.org/3/library/constants.html#True)
- **get_id()** 는 [None](https://docs.python.org/3/library/constants.html#None)을 리턴



만일 당신이 익명 유저에 대한 커스텀 요구사항을 가지고 있다면(예를 들어, 그들에게 허가 필드가 필요할 수도 있다.), 당신은 **LoginManager**를 가지는 익명 유저를 생성해주는 callable(클래스 혹은 생성함수)을 제공할 수 있다 :

```python
login_manager.anonymous_user = MyAnonymousUser
```



## Remember Me

기본적으로, 유저가 브라우저를 닫으면 Flask 세션은 삭제되고 유저는 로그아웃 처리된다. "Remember Me"는 유저가 브라우저를 닫을 때, 의도치않게 로그아웃 되는 것을 방지한다. 이것이 유저가 로그아웃 한 후에도 유저의 username 혹은 password를 기억한다는 것을 의미하지는 **않는다**.

"Remember Me" 함수는 구현하기 까다로울 수도 있다. 그러나 Flask-Login은 이것을 거의 투명하게(단지 remember=True를 **login_user**에 전달하는 것 만으로) 만들어준다. 쿠키는 유저의 컴퓨터에 저장될 것이다. 그리고나서 세션에 유저정보가 없다면, Flask-Login은 쿠키에 있는 user ID를 자동으로 복원한다. 쿠키는 위조방지이기 때문에, 유저가 조작하려고 하면(본인 ID 대신에 다른 유저의 ID를 넣으려고 하는 등) 없던 것처럼 거부될 것이다.

그 정도 레벨의 기능은 자동으로 처리된다. 하지만 당신이 쿠키의 보안을 증가시키고 싶다면 추가적인 하부구조를 제공할 수 있다.



## 대체 토큰(Alternative Tokens)

user ID를 토큰을 기억하기 위한 값으로 사용하는 것은 당신이 사용자의 ID를 그들의 로그인 세션에서 유효하지 않은 값으로 변경해야 함을 의미한다. 이것을 개선하기 위한 한 방법은 사용자의 ID 대신 대체 세션 토큰을 사용하는 것이다. 예제를 보자 :

```python
@login_manager.user_loader
def load_user(session_token):
    return User.query.filter_by(session_token=session_token).first()
```



그리고 나면 당신의 User 클래스의 **get_id** 메서드는 사용자 ID 대신 세션 토큰을 리턴할 것이다 :

```python
def get_id(self):
    return unicode(self.session_token)
```



이 방법은 유저가 비밀번호를 변경했을 때, 당신이 자유롭게 유저의 세션토큰을 무작위로 생성된 값으로 변경하도록 해준다. 이것은 기존의 권한이 있는 세션이 유효한 세션을 위해서 중단될 것임을 확신할 수 있도록 해준다. 마치 두번째 유저 ID인 것처럼, 세션토큰은 반드시 유저마다 유니크한 값을 가져야 한다는 것을 기억하라.



## Fresh Logins

유저가 로그인할 때, 그들의 세션은 "새로운(fresh)" 마크가 붙는다. 이것은 실질적으로 그 세션에 권한을 획득했음을 나타낸다. 그들의 세션이 없어지고 "remember me" 쿠키를 통해서 다시 로그인할 때, 이 세션은 "새롭지 않은(non-fresh)" 마크가 붙는다.

**login_required** 는 새로운지 아닌지를 구별하지 않는다. 이것은 대부분의 페이지에서는 괜찮다. 그러나 누군가의 개인정보를 변경하는 것과 같은 민감한 행동은 새로운 로그인을 요구해야 한다. (누군가의 비밀번호를 변경하는 행동은 항상 비밀번호 재입력을 요구해야 한다.)

유저가 로그인했다는 것을 확인하는 것 이외에, **fresh_login_required** 은 그들의 로그인이 새롭다는 것을 확인시켜줄 것이다. 그렇지 않다면, 그들의 인증을 다시 받을 수 있는 페이지로 보낼 것이다. **LoginManager.refresh_view**, **needs_refresh_message**, needs_refresh_message_category를 셋팅함으로써 당신은 **login_required**를 커스터마이징 했던 방식과 동일하게 그 행동을 커스터마이징 할 수 있다 :

```python
login_manager.refresh_view = "accounts.reauthenticate"
login_manager.needs_refresh_message = (
    u"To protect your account, please reauthenticate to access this page."
)
login_manager.needs_refresh_message_category = "info"
```



혹은 콜백함수를 제공함으로써 refresh를 처리한다 :

```python
@login_manager.needs_refresh_handler
def refresh():
    # do stuff
    return a_response
```

세션에 다시 "새로움" 마크를 붙이려면 **confirm_login** 함수를 콜한다.



## 쿠키 셋팅

어플리케이션 셋팅에서 쿠키의 상세정보를 커스터마이징 할 수 있다.

| 변수명                      | 설명 및 기본값                                 |
| ------------------------ | ---------------------------------------- |
| REMEMBER_COOKIE_NAME     | "remember me" 정보를 저장하기 위한 쿠키의 이름. **기본값 :** remember_token |
| REMEMBER_COOKIE_DURATION | 쿠키가 만료되기까지 남은 시간 datatime.timedelta 객체에 담긴다. **기본값 :** 365일(1 non-leap Gregorian year) |
| REMEMBER_COOKIE_DOMAIN   | "Remember Me" 쿠키가 도메인을 넘어간다면, 도메인 값을 여기에 셋팅한다(.example.com 과 같이 입력하면 example.com의 모든 서브도메인에서 쿠키사용을 허가한다.). **기본값 : None** |
| REMEMBER_COOKIE_PATH     | "Remember Me" 쿠키의 특정 경로. **기본값 : /**     |
| REMEMBER_COOKIE_SECURE   | 보안채널(HTTPS등)의 "Remember Me" 쿠키의 범위를 정해준다. **기본값 : None** |
| REMEMBER_COOKIE_HTTPONLY | "Remember Me" 쿠키에 클라이언트의 스크립트 접근을 방지한다. **기본값 : False** |



## 세션 보호

쿠키도둑으로부터 "Remember Me" 토큰을 보호하는 것을 도와주는 기능들이 동작하고 있더라도, 세션쿠키는 여전히 취약하다. Flask-Login은 당신의 유저세션이 도난당하는 것을 방지하는 세션 보호를 포함하고 있다.

당신은 **LoginManager**과 어플리케이션의 설정에서 세션보호를 설정할 수 있다. 사용 가능하다면 **basic** 혹은 **strong** 모드 둘 다 작동하게 할 수있다. **LoginManager**에서 셋팅할 경우, **session_protection** 항목을 "basic"이나 "strong"으로 설정한다 :

```python
login_manager.session_protection = "strong"
```



셋팅을 하지 않으려면 :

```python
login_manager.session_protection = None
```



기본적으로는 "basic" 모드가 활성화 되어있다. 어플리케이션 설정에서 **SESSION_PROTECTION** 을 None, "basic", "strong"으로 셋팅함으로써 위의 설정을 무효화 할 수 있다.

세션 보호가 활성화 될 때, 각각의 요청은 유저의 컴퓨터에 식별자(기본적으로, IP주소와 유저 에이전트의 안전한 해시값)를 생성한다. 만일 세션이 관련된 식별자가 없다면, 생성된 것이 저장될 것이다. 만일 식별자가 있다면, 그리고 생성된 것과 일치한다면 요청은 올바르게 처리된다.

만일 **basic** 모드에서 식별자가 일치하지 않거나, 세션이 영구적일 때, 세션은 간단하게 "새롭지 않은(non-fresh)" 마크가 붙을 것이다. 그리고 어떤 "새로운(fresh)" 로그인 요청이라도 유저가 다시 권한을 획득하도록 강제할 것이다(물론, 당신은 적절한 곳에서 이미 fresh login을 사용하고 있어야 한다.).

만일 **strong** 모드에서 영구적이지 않은 세션의 식별자가 일치하지 않으면, 전체 세션(remember token이 존재한다면 그것도 함께)이 삭제된다. 



## Disabling Session Cookie for APIs

API를 인증할 때, 당신은 Flask 세션 쿠키의 설정을 비활성화 하고 싶을 것이다. 그렇게 하려면, 당신이 요청에서 셋팅한 플래그를 이용해서 세션저장을 건너뛰도록 해주는 커스텀 세션 인터페이스를 사용하라. 예제를 보자 :

```python
from flask import g
from flask.sessions import SecureCookieSessionInterface
from flask_login import user_loaded_from_header

class CustomSessionInterface(SecureCookieSessionInterface):
    """Prevent creating session from API requests."""
    def save_session(self, *args, **kwargs):
        if g.get('login_via_header'):
            return
        return super(CustomSessionInterface, self).save_session(*args,
                                                                **kwargs)

app.session_interface = CustomSessionInterface()

@user_loaded_from_header.connect
def user_loaded_from_header(self, user=None):
    g.login_via_header = True
```

이것은 사용자가 당신의 **header_loader**를 사용해서 권한을 획득할 때면 언제든지 Flask 세션쿠키의 셋팅을 막아준다.



## Localization

기본값으로, 유저가 로그인을 요청할 때, **LoginManager**는 메시지를 보여주는데 flash를 사용한다. 이 메시지들은 영문이다. 만일 당신이 지역화(localization)를 원한다면, **LoginManager**의 **localize_callback** 항목을 이 메시지들이 flash로 보내지기 전에 불러지는 함수(gettext와 같은)로 셋팅하라. 이 함수는 메시지와 함께 실행될 것이다. 그리고 리턴값이 대신 flash로 전달될 것이다.

