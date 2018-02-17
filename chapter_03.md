# Chapter 03 단위 테스트를 이용한 간단한 홈페이지 테스트

이전 장에서는 "To-Do"라고 타이틀을 바뀌어야 한다는 메시지와 함께 기능 테스트가 실패한 상테에서 마무리를 지었다. 이제 우리의 애플리케이션 작업을 시작해 보자.

### 경고: 이제부터 진짜다

잘못된 방향으로 갈 수도 있다. 하지만 괜찮다. 이것이 진정 배우는 길이기 때문이다.

애매한 설명 때문에 의도한 결과와 다른 결과를 얻을 수 있따. 이때는 한걸음 물어나서 성취하고자 하는 것이 무엇인지 생각해 보자. 그리고 어떤 파일을 편집하고 있는지, 사용자가 무엇을 하기 원하는지, 무엇을 테스트 하는 것인지 생각해 보자. 잘못된 파일이나 함수를 편집했을 수도 있고, 다른 테스트를 실행했을 수도 있다. 코드 복붙보다는 읽는 것을 멈추고 생각하는 것에서 TDD를 진정으로 배울 수 있을 것이다.

## 3.1 첫 Django 애플리케이션과 첫 단위 테스트

Django는 코드를 앱(app)형태로 구조화하도록 도와준다. 이론상 하나의 프로젝트는 여러 앱을 가질 수 있으며, 다른 사람이 만든 외부 앱도 사용할 수 있다. 또한 다른 프로젝트에서 만든 자신의 앱을 재사용할 수도 있다. 하지만 여기서는 이런 방식들을 사용하지 않는다. 어쨌든 앱은 코드를 구조화하기 좋은 수단이다.

작업 목록 앱을 시작해 보도록 하자.

```
$ python manage.py startapp lists
```

이를 실행하면 superlists/superlists와 같은 위치에 superlists/lists라는 폴더가 생성된다. 이 폴더는 다음과 같이 모델(model), 뷰(view) 등의 템플릿을 가지고 있으며, 우리의 관심사인 test 템플릿도 포함한다.

```sh
.
├── db.sqlite3
├── functional_tests.py
├── geckodriver.log
├── lists
│   ├── admin.py
│   ├── apps.py
│   ├── __init__.py
│   ├── migrations
│   │   └── __init__.py
│   ├── models.py
│   ├── tests.py
│   └── views.py
├── manage.py
└── superlists
    ├── __init__.py
    ├── __pycache__
    │   ├── __init__.cpython-36.pyc
    │   ├── settings.cpython-36.pyc
    │   ├── urls.cpython-36.pyc
    │   └── wsgi.cpython-36.pyc
    ├── settings.py
    ├── urls.py
    └── wsgi.py

```

## 3.2 단위 테스트는 무엇이고, 기능 테스트와 어떤 차이가 있을가?

다양한 용어를 다루다 보니, 단위 테스트(unit test)와 기능 테스트(Function test)가 어떻게 다를 지 궁금할 것이다. 기본적인 차이는, **기능 테스트**는 사용자 관점에서 애플리케이션 외부를 테스트하는 것이고, **단위 테스트**는 프로그래머 관점에서 그 내부를 테스트한다는 것이다.

이 책에서 다루는 TDD 접근법은 양쪽 테스트를 모두 적용한다. 이후 작업 순서는 다음과 같다.

1. **기능 테스트**를 작성해서, 사용자 관점의 새로운 기능성을 정의하는 것부터 시작한다.

2. 기능 테스트가 실패하고 나면, 어떻게 코드를 작성해야 테스트를 통과할지(또는 적어도 현재 문제를 해결할수 있는 방법)을 생각해 보도록 한다. 이 시점에서 하나 또는 그 이상의 단위 테스트를 이용해서 어떻게 코드가 동작해야 하는지 정의한다.(기본적으로 모든 코드가 (적어도)하나 이상의 단위 테스트에 의해 테스트가 되야 한다.

3. 단위 테스트가 실패하고 나면 단위 테스트를 통과할 수 있을 정도의 최소한의 코드만 작성한다. 기능 테스트가 완전해질 때까지 2와 3을 반복해야 할 수도 있다.

4. 기능 테스트를 재실행해서 통과하는지 또는 제대로 동작하는지 확인한다. 이 과정에서 새로운 단위 테스트를 작성해야 할 수도 있다.

이 과정을 잘 보면, 기능 테스트는 상위 레벨의 개발을 주도하고, 단위 테스트는 하위 레벨을 주도한다는 것을 알 수 있다.

과정이 너무 많다고 느낄 수도 있다. 하지만 기능 테스트와 단위 테스트가 전혀 다른 목적을 가지고 있어서 서로 달느 결과를 초래할 수 있기 때문에 꼭 필요한 과정이라고 할 수 있다.

```
기능 테스트는 제대로 된 기능성을 갖춘 애플리케이션을 구축하도록 도우며, 그 기능이 망가지지 않도록 해 준다. 
반면, 단위 테스트는 깔끔하고 버그 없는 코드를 작성하도록 돕는다.
```

이론은 이 정도로 하고, 이제 실습을 통해 확인해 보도록 하자.

## 3.3 Django에서의 단위 테스트

홈페이지 뷰를 위한 단위 테스트를 어떻게 작성하는가 보자. lists/tests.py 파일을 열어 보면 다음과 같은 코드를 볼 수 있다.

```python
from django.test import TestCase

# Create your tests here
```

Django는 특수한 TestCase 버전을 사용하고 있다. 기본 unittest.TestCase의 확장 버전으로, 다음 장에서 다룰 몇 가지 Django 특화 기능들이 추가되어 있다.

TDD 주기는 실패 테스트를 작성한 후 테스트를 통과할 수 있는 코드를 작성하는 과정이라는 것을 보았다. 단위 테스트의 경우에는 그것이 어떠한 형태든 자동화된 테스트 실행자에 의해 실행된다는 것을 알아둘 필요가 있다. functional_tests.py는 우리가 직접 실해자만, Django를 이용해 만든 파일은 다소 마법같이 느껴진다. 고의적인 실패 테스트를 만들어서 확인해 보자.

```py
from django.test import TestCase

class SmokeTest(TestCase):
	
	def test_bad_maths(self):
		self.assertEqual(1 + 1, 3)
```
이 미스터리한 테스트 실행자를 호출해 보자. 이전과 같이 manage.py를 사용하면 가능하다.

```
$ python manage.py test
Creating test database for alias 'default'...
F
======================================================================
FAIL: test_bad_maths (lists.tests.SmokeTest)
 ---------------------------------------------------------------------
Traceback (most recent call last):
  File "...python-tdd-book/lists/tests.py", line 6, in test_bad_maths
    self.assertEqual(1 + 1, 3)
AssertionError: 2 != 3

 ---------------------------------------------------------------------
Ran 1 test in 0.001s

FAILED (failures=1)
System check identified no issues (0 silenced).
Destroying test database for alias 'default'...
```
제대로 동작하는것 같다. 커밋하기 좋은 시점이니 커밋을 한다. 

```
$ git status  # lists/가 untracked 파일이라고 표시된다. 
$ git add lists
$ git diff --staged  # 커밋하려는 추가 코드를 보여준다.
$ git commit -m "의도적인 실패 테스트와 함께 앱을 lists에 추가"
```

-m 옵션은 커밋 메시지를 커맨드라인상에서 바로 추가할 수 있게 해 준다. 중요한 규칙은 "커밋하기 직전에 내용을 반드시 확인"해야 한다는 것을 기억하자.

## 3.4 Django의 MVC, URL, 뷰 함수

Django는 대체로 **모델-뷰-컨트롤러**(Model-View-Controller, MVC)라는 고전적인 패턴을 따른다. "대체로"라는 표현에 주목하자. 왜냐하면 분명한 모델은 있지만, 뷰는 컨트롤러와 비슷한 역할을 하기 때문이다. 뷰는 보이는 부분을 담당하는 템플릿이라는 개념에 주안을 두고 있다.

Django의 주요 역할은 일반적인 웹 사용자가 특정 URL을 요청했을 때, 어떤 처리를 할 것인지 결정하는 것이다. Django의 처리 흐름은 다음과 같다.

1. 특정 URL에 대한 HTTP "요청"을 받는다.

2. Django는 특정 규칙을 이용해서 해당 요청에 어떤 뷰 함수를 실행할지 결정한다. (URL "해석"이라고 하는 처리다.)

3. 이 뷰 기능이 요청을 처리해서 HTTP "응답"으로 반환한다.

따라서 우리가 테스트해야 할 것은 두 가지이다.
- URL의 사이트 루트("/")를 해석해서 특정 뷰 기능에 매칭시킬 수 있는가?
- 이 뷰 기능이 특정 HTML을 반환하게 해서 기능 테스트를 통과 할 수 있는가?

첫 번째 것부터 시작해 보자. lists/tests.py 파일을 열어서 테스트 코드를 다음과 같이 수정한다.

```py
from django.urls import resolve
from django.test import TestCase
from lists.views import home_page  # 1

class HomePageTest(TestCase):

    def test_root_url_resolves_to_home_page_view(self):
        found = resolve('/')  # 2
        self.assertEqual(found.func, home_page)  # 3
```

어떤 처리를 하고 있는지 보자.

**2, 3** resolve 는 Django가 내부적으로 사용하는 함수로, URL을 해석해서 일치하는 뷰 함수를 찾는다. 여기서는 "/"(사이트 루트)가 호출될 때 resolve를 실행해서 home_page라는 함수를 호출한다.

**1** Home Page는 어떤 함수일까? 곧 작성하게 될 뷰 함수로 HTML을 반환한다. import 처리 부분에서 보이는 것처럼, 이 함수를 lists/views.py에 저장할 계획이다.

이 테스트를 실행하면 어떤 일이 벌어질까?

```sh
$ python manage.py test
ImportError: cannot import name 'home_page'
```

예상한 대로 재미없는 에러가 발생한다. 아직 존재하지 않는 무언가를 임포트하려 했기 때문이다. 하지막 예측된 실패가 발생했기 때문에 TDD관점에서는 좋은 소식이다. 실패한 기능 테스트와 실패한 단위 테스트를 모두 가지고 있으니, 테스트 고트님의 은혜가 우리 코드에 충만할 것이다.

## 3.5 마침내 실질적인 애플리케이션 코드를 작성한다.

TDD는 인내를 요하는 작업으로 점진적으로 진행되며 개선 속도도 느리다. 특히 지금 배우는 과정으로 막 걸음마를 떼었기 때문에, 한 번에 한 줄의 코드만 수정(또는 추가)해 갈 것이다. 실패 테스트를 해결하기 위한 최소한의 수정만 하도록 한다.

현재 우리가 가지고 있는 실패 테스트가 무엇인지 보자. lists.views에서 home_page를 임포트할 수 없는 상태다. 다음과 같이 이 문제를 해결해 보자.

*lists/views.py*

```py
from djnago.shortcuts import render

# Create your views here.
home_page = None

```

"장난치는 거지?" 무슨 뜻인지 안다.

동료들이 처음 TDD를 보여주었을 때 필자도 똑같은 반응을 보였다. 조금만 참기 바란다. 이것이 이후 실습의 시발점이자 모든 것이라는 것을 곧 보게 될 것이다. 조금 짜증날 수 있겠지만, 여기서는 일단 따라와주기 바란다.

테스트를 다음과 같이 실행해 보자.

```sh
$ python manage.py test
Creating test database for alias 'default'...

E

======================================================================

ERROR: test_root_url_resolves_to_home_page_view (lists.tests.HomePageTest) # 1

---------------------------------------------------------------------

Traceback (most recent call last):
	File "/.../superlists/lists/tests.py", line 8, in
test_root_url_resolves_to_home_page_view
	found = resolve('/') # 2
  File ".../django/urls/base.py", line 27, in resolve
	return get_resolver(urlconf).resolve(path)
  File ".../django/urls/resolvers.py", line 392, in resolve
	raise Resolver404({'tried': tried, 'path': new_path})
django.urls.exceptions.Resolver404: {'tried': [[<RegexURLResolver  # 3
<RegexURLPattern list> (admin:admin) ^admin/>]], 'path': ''} # 4   

---------------------------------------------------------------------

Ran 1 test in 0.002s

FAILED (errors=1)
System check identified no issues (0 silenced).
Destroying test database for alias 'default'...
```

#### 트레이스 백 읽기
**3, 4** 먼저 확인해야 할 것이 "에러"다. 이 에러 부분이 핵심인데, 어떤 때는 이 부분만 읽고 바로 문제를 파악할 수 있다. 하지만 이 예제의 경우는 에러만으로는 충분한 문제 파악이 어렵다.

**1** 다음으로 확인할 것은 "어떤 테스트가 실패하고 있는가?"다. 이 실패가 예측된 실패인지를 확인해야 한다. 이 예에선 예측된 실패이다.

**2** 마지막으로 실패를 발생시키는 "테스트 코드"를 찾는다. 트레이스백 상단부터 아래로 내려가면서 테스트 파일명을 찾는다. 그래서 어떤 테스트 함수의 몇 번째 코드 라인에서 실패가 발생하는지 확인한다. 이 예에선 resolve 함수를 호출해서 "/"부분을 해석하는 부분이다.

문제 있는 코드를 확인할 때 일반적으로 사용하는 4단계 과정이 있따. 이후로 계속 이 4단계 과정을 보며 Django코드를 확인하는 것을 보게 될 것이다.

이렇게 모든 정보를 취합해 트레이스백을 해석한 결과 "/"를 확인하려고 할 때 Django가 404에러를 발생시키고 있다는 것을 알았다. 즉 Django가 "/"에 해당하는 URL맵핑을 찾을 수 없다. 이 문제를 해결해보자.

### urls.py

Django는 urls.py파일을 이용해서 어떻게 URL을 뷰 함수에 맵핑할지 정의한다. superlists/superlists 폴더에 전체 사이트를 대상으로 하는 메인 urls.py가 있다. 파일 내용을 살펴보자.

*superlists/urls.py*

```py
"""superlists URL Configuration

The `urlpatterns` list routes URLs to views. For more information please see:

https://docs.djangoproject.com/en/1.11/topics/http/urls/

Examples:

Function views

1. Add an import: from my_app import views

2. Add a URL to urlpatterns: url(r'^$', views.home, name='home')

Class-based views

1. Add an import: from other_app.views import Home

2. Add a URL to urlpatterns: url(r'^$', Home.as_view(), name='home')

Including another URLconf

1. Import the include() function: from django.conf.urls import url, include

2. Add a URL to urlpatterns: url(r'^blog/', include('blog.urls'))

"""

from django.conf.urls import url

from django.contrib import admin

urlpatterns = [

url(r'^admin/', admin.site.urls),

]
```
이전과 마찬가지로 도움이 되는 주석과 사용 방법 등이 적혀있다.

url항목은 정규표현식으로 시작하며, 어떤 URL을 해석해서 어떤 함수로 요청을 보낼지를 정의한다. 대상 함수는 superlists.views.home처럼 마침표 조합의 형태다. 또는 include를 이용해서 다른 urls.py 파일을 지정할 수도 있다.

urlpatterns의 첫 번째 항목은 정규표현 ^$으로 빈 문자열을 가리킨다. 이것이 앞서 테스트했던 "/" 같이 사이트 루트 역할을 할 수 있을지 확인해보자. 

또한 admin URL 을 제거해 주자. 당장 Django 어드민 페이지가 필요하진 않다.

```py
from django.conf.urls import url
from lists import views

urlpatterns = [
	url(r'^$', views.home_page, name='home'),
	]
```

다시 테스트를 실행해보자.

**python manage.py test**

```sh

[...]
TypeError: view must be a callable or a list/tuple in the case of include().
```

진전이 있다! 더이상 404에러를 갖지는 않는다.

트레이스백이 다소 번잡하지만, 마지막 메시지가 무엇이 일어났는지 말해준다: 단위 테스트가 URL과 lists/views.py에 있는 home_page = None 사이의 링크를 만들었다. 하지만 home_page를 호출할 수 없다는 메시지가 뜨고 있따. home_page가 아직 함수가 아니기 때문이다. None을 실제 함수로 변경해 보도록 하자. 모든 코드 변경은 테스트에 의해 검증되어야 한다.

*lists/views.py*로 돌아가 보자.

```
from django.shortcuts import render
# Create your views here.
def home_page():
	pass

```

테스트를 실행해 보자.

```sh
$ python manage.py test
Creating test database for alias 'default'...
.
---------------------------------------------------------------------
Ran 1 test in 0.003s

OK
System check identified no issues (0 silenced).
Destroying test database for alias 'default'...
```

만세! 우리의 첫번째 단위 테스트가 성공했다. 이건 중대한 사건이니 커밋해 준다.

```
$ git diff # urls.py, tests.py, views.py의 변경 내역을 보여준다.
$ git commit -am "첫 단위 테스트와 url 맵핑 그리고 임시 뷰"
```

```
git commit -am은 빠른 처리를 위한 것으로 최소한의 커밋 내용만 보여준다.
따라서 사전에 git status나 git diff를 이용해서 변경 내역을 잘 파악해두는 것이 좋다.
```

## 3.6 뷰를 위한 단위 테스트

뷰를 위한 테스트를 작성할 때는 단순히 빈 함수를 작성하는 것이 아니라 HTML 형식의 실제 응답을 반환하는 함수를 작성해야 한다. lists/tests.py를 열어서 다음과 같이 새로운 테스트를 추가한다.

```py
from django.urls import resolve
from django.test import TestCase
from django.http import HttpRequest
from lists.views import home_page

class HomePageTest(TestCase):

	def test_root_url_resolves_to_home_page_view(self):
		found = resolve('/')
		self.assertEqual(found.func, home_page)

	def test_home_page_returns_correct_html(self):
		request = HttpRequest() # 1
		response = home_page(request) # 2
		html = response.content.decode('utf8') # 3
		self.assertTrue(html.startswith('<html>')) # 4
		self.assertIn('<title>To-Do lists</title>', html) # 5
		self.assertTrue(html.endswith('</html>')) # 4
```

**1** HttpRequest 객체를 생성해서 사용자가 어떤 요청을 브라우저에 보내는지 확인한다.

**2** 이것을 home_page 뷰에 전달해서 응답을 취득한다. 이 객체는 HttpResponse라는 클래스의 인스턴스다. 응답 내용(HTML 형태로 사용자에게 보내는 것)이 특정 속성을 가지고 있는지 확인한다.

**3** 다음으로, 응답의 .content를 추출한다. 이는 유저의 브라우저와 엮여있는 0과 1들로 구성된 바이트 들이다. .decode()를 호출해서 HTML의 문자열로 변환시켜 유저에게 전해진다. 

**4** 응답 내용이 ```<html>``` 으로 시작하고 ```</html>```로 끝나는지 확인한다.

**5** 반환 내용의 ```<title>``` 태그에 "To-Do lists" 라는 단어가 있는지 확인한다. 앞선 기능 테스트에서 확인한 것이기 때문에 단위 테스트도 확인해 주어야 한다.

다시 한번 강조하지만, 단위 테스트는 기능 테스트에 의해 파생되며 더 실제 코드에 가깝다. 따라서 우리는 프로그래머처럼 생각해야 한다.

단위 테스트를 실행해서 결과를 확인하자.

```sh
TypeError: home_page() takes 0 positional arguments but 1 was given
```

### 단위 테스트-코드 주기

여기서부터 **TDD 단위 테스트 주기**에 대해 생각해야 한다.

1. 터미널에서 단위 테스트를 실행해서 어떻게 실패하는지 확인한다.
2. 편집기상에서 현재 실패 테스트를 수정하기 위한 최소한의 코드를 변경한다.

그리고 이것을 반복한다.

코드 품질을 높이고 싶다면 코드 변경을 최소화해야 한다. 또한 이렇게 최소화한 코드는 하나하나 테스트에 의해 검증되야 한다. 매우 고된 작업이라고 생각될 수 있지만, 한번 익숙해지기 시작하면 속도는 빨라진다. 따라서 아무리 자신 있는 부분이라도 작은 단위로 나누어 코드를 변경하도록 한다.

얼마나 빨리 이 주기를 따라갈 수 있는지 보도록 하자.

- 최소한의 코드 변경

*lists/views.py *

```py
def home_page(request):
	pass
```

- 테스트

```sh
html = response.content.decode('utf8')
AttributeError: 'NoneType' object has no attribute 'content'
```

- 코드: 가정한 대로 django.http.httpResponse를 사용한다.

*lists/views.py*

```py
from django.http import HttpResponse

# Create your views here
def home_page(request):
	return HttpResponse()
```

- 다시 테스트

```sh
	self.assertTrue(html.startswith('<html>'))

AssertionError: False is not true
```

- 다시 코드

*list/views.py*

```py
def home_page(request):
	return HttpResponse('<html><title>To-Do lists</title>')
```

- 테스트: 거의 다 왔다.

```sh
	self.assertTrue(html.endswith('</html>'))
AssertionError: False is not true
```

- 드디어 마지막:

*lists/views.py*

```py
def home_page(request):
	return HttpResponse('<html><title>To-Do lists</title></html>')
```

- 확실한가?

```sh
$ python manage.py test
Creating test database for alias 'default'...
..
---------------------------------------------------------------------
Ran 2 tests in 0.001s

OK

System check identified no issues (0 silenced).
Destroying test database for alias 'default'...
```

자, 이제 기능 테스트를 실행해보자. 개발 서버를 가동하는 것을 잊지 말자. 경주가 끝나는 것처럼 느껴지는데, 정말 끝낼 수 있을까?

```sh
$ python functional_tests.py

F
======================================================================
FAIL: test_can_start_a_list_and_retrieve_it_later (__main__.NewVisitorTest)
---------------------------------------------------------------------

Traceback (most recent call last):
  File "functional_tests.py", line 19, in
test_can_start_a_list_and_retrieve_it_later
    self.fail('Finish the test!')
AssertionError: Finish the test!

---------------------------------------------------------------------

Ran 1 test in 1.609s

FAILED (failures=1)
```
실패라고? 아 그게 아니라고? 여기서 실패는 작업 완료 메시지를 출력하기 위해 심어둔 AssertionError 때문이다. 그렇다, 성공한 것이다. 드디어 웹 페이지를 가지게 되었다.

아직까지 이 모든 테스트가 어떻게 진행됐는지 몰라서 당황할 필요는 없다. 뒤에 하나씩 다루니 걱정은 접어두자.

일단 진정하고 작업한 것을 뒤돌아 보자.

```sh
git diff # tests.py와 views.py에 추가한 새로운 테스트 내역을 보여준다.
git commit -am "최소 HTML을 반환하는 기본 뷰"
```

꽤 많은 작업을 했다. git log --oneline을 실행해서 지금껏 우리가 작업한 것을 확인해 보자.

```sh
$ git log --oneline
3585b25 최소 HTML을 반환하는 기본 뷰
a86608d 첫 단위 테스트와 url 맵핑 그리고 임시 뷰
d470e9e 의도적인 실패 테스트와 함꼐 앱을 lists에 추가
[...]
```

지금까지 우리가 배운 것을 정리해 보면 다음과 같다.

- Django 앱 실행
- Django 단위 테스트 실행자
- 기능 테스트와 단위 테스트의 차이
- Django URL 해석 및 urls.py
- Django 뷰 함수 및 요청, 응답 객체
- 기본 HTML 반환

### 기억해두면 좋은 명령어 및 개념

> Django 개발 서버 실행
**python manage.py runserver**

>기능 테스트 실행
**python functional_tests.py**

> 단위 테스트 실행
**python manage.py test**

> 단위 테스트-코드 주기
1. 터미널에서 단위 테스트 실행
2. 편집기에서 최소 코드 수정
3. 반복

