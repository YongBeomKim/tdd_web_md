# 4장 왜 테스트를 하는 것인가?(그리고 리팩토링도)

지금까지 기본적인 TDD 동작을 살펴보았다. 이번 장에선 잠시 시간을 내서 왜 우리가 이런 테스트를 하고 있는지 생각해보도록 한다.

아마 여러분 중 몇몇은 심한 좌절감을 느꼈을 수도 있다. 이전에 단위 테스트를 다루어 본 적이 있거나, 단지 책에 있는 것을 허겁저겁 따라 하기도 벅찰 수 있다. 그래서 이런 질문을 던질 수 있다.

- 너무 테스트를 과하게 하는 것 아닌가?


- 어떤 것들은 중복 작업인게 분명하다. 기능 테스트와 단위 테스트에서 겹치는 부분들이 있다.


- 왜 django.core.urlresolver를 단위 테스트에 사용하는 거지? 이건 Django라는 외부 코드를 테스트하는 거 아닌가? 외부 코드 테스트는 의미가 없다고 생각해.


- 선언 코드 한 줄이나 상수 하나를 반환하는 함수로 단위 테스트를 하는 것은 너무 시시하다. 이건 시간 낭비다. 더 복잡한 처리를 위한 테스트를 만들어야 하는 것은 아닌가?


- 단위 테스트-코드 주기 동안 해야 하는 최소 코드 변경을 정말로 필요한가? 그냥 한번에 다 변경해서 마지막에만 테스트하는 것은 안 되는 건가? home_page = None은 정말 말도 안 돼.


- 실제 업무에서도 이렇게 코드를 작성하는건 아니겠지?

필자도 이런 질문들로 머리가 가득 찼던 시절이 있었다. 사실 의미 있는 질문들이다. 아직도 자신에게 이런 질문들을 항상 한다. '이게 정말 가치가 있는 것일까? 이거 좀 사이비 과학 같은데?'

## 4.1 프로그래밍은 우물에서 물을 파 올리는 것과 같다.

프로그래밍은 정말 어려운 작업이다. 종종 똑똑한 사람들이 성공하는 경우가 있다. TDD는 똑똑하지 못한 우리들을 도와주기 위해 존재한다. 켄트 백(TDD 창시자)은 우물가에 있는 물 뜨는 두레박을 비유로 들어 설명한다. 우물이 깊지 않으면 두레박도 꽉 차지 않아서 퍼 올리는 것이 쉽다. 가득 차 있다고 해도 처음 몇번은 쉽게 할 수 있다. 하지만 시간이 지나면서 곧 지치기 시작한다. 하지만 이때 도르래를 이용하면 직접 퍼 올리는 것보다 효율적이다. TDD는 이런 도르래와 같아서 여러분의 작업 효율을 올려주고 쉴 수 있는 시간도 준다. 또한 여러분이 뒤로 미끄러지는 것도 막아준다.

아마 대부분은 TDD가 좋은 방법이라는 것을 인정할 준비가 되어 있겠지만, 여전히 '이 정도까지 할 필요가 있나' 하는 의문을 품은 사람도 있을 것이다. 이렇게 작은것도 테스트해야 하고, 터무니없이 많은 작은 단계들을 거쳐야 하는 걸까?

TDD는 '훈련(discipline)'이다. 즉 자연스럽게 익힐 수 있는 것이 아니다. 대부분의 성과가 즉시 보여지는 것이 아니라 오랜 기간을 거쳐야 하기 때문에, 여러분 자신이 TDD에 적응할 수 있도록 노력해야 한다. 이것이 테스팅 고트님 그림을 통해 보여주고자 한 것이다.

### 시시한 함수에 대한 시시한 테스트의 이점
단기적 관점에선 간단한 함수나 상수를 위한 테스트 작성이 하찮게 여겨질 수도 있다. 물론 보다 수월한 규칙(일ㄹ부만 단위 테스트를 하는)을 따라서 TDD를 "적당하게" 할 수도 있다. 하지만 이 책을 통해 필자가 보여주고자 하는 것은 완벽하면서도 철저한 TDD다. 이것은 무술의 '형'(kata)와도 같다. (즉 정확한 동작을 배워서 기술 자체가 여러분의 뇌 근육이 되도록 하는 것이다. 문제는 애플리케이션이 복잡해지면서 발생하게 되는데, 이때는 정말 테스트가 필요해진다. 하지만 복잡성이라는 것은 여러분들이 모르는 사이에 조금씩 다가와서 복잡하다는 것 자체를 인지하지 못하고, 얼마 되지 않아 끓는 물 속에 개구리 되는 것이다.)

간단한 함수를 위한 간단한 테스트에 대해 언급하고 싶은 것이 두 가지 더 있다.

첫 번째는, 테스트가 정말 시시한 테스트라면 테스트 작성 자체에 시간이 많이 걸리지 않는다는 것이다. 그러니 불평하지 말고 작성하도록 하자.

두 번째는, 틀을 사용하는 것이 도움이 된다는 것이다. 쉬운 함수를 위한 테스트 틀이 있따면, 함수가 복잡해지더라도 심리적 부담을 줄일 수 있다(단기간에 if제어나 for 루프 처리가 늘어날 수 있다). 여러분이 인지하기도 전에 프로그램이 다형성 트리 파서(parser) 구조가 되더니 어느새 재귀 처리로까지 번질 수 있다. 하지만 초반부터 틀을 갖추어 테스트를 했기 때문에 새로운 테스트를 추가하는 것이 자연스럽고 테스트도 수월하다. 테스트를 해야 할 정도로 복잡하다고 판단한 후 테스트를 작성하기 시작한다면(복잡하다는 기준도 모호하지만), 틀이 없기 때문에 훨씬 더 많은 수고를 들여서 테스트를 만들고 수정해야 한다. 즉 개구리 수프가 되고 마는 것이다.

언제 테스트를 작성하기 시작해야 하는지 결정하려고 주관적인 규칙에 얽매이는 대신에, 지금 바로 필자가 제안하는 방식으로 훈련하기 바란다. 다른 훈련들과 마찬가지로 익숙해지기 전까지는 시간을 가지고 규칙에 익숙해져야 한다.

다시 본론으로 돌아가자.

## 4.2 셀레늄을 이용한 사용자 반응 테스트

마지막 장에서 어디까지 진행했는지 기억하는가? 앞 장의 테스트를 다시 실행해서 어디까지 진행했는지 확인하자.

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
"Problem loading page" (페이지 읽기 오류)나 "Unable to connect" (연결 불가) 에러가 발생하지 않았는가? 필자의 경우는 그렇다. manage.py runserver를 실행해서 개발 서버를 가동하지 않았기 때문이다. 서버를 가동하면 위와 같은 테스트 실패 메시지를 확인할 수 있다.

```
TDD가 훌륭한 이유 중 하나가 다음에 무엇을 해야 할지 잊어버릴 걱정이 없다는 것이다.
테스트를 다시 싱행하기만 하면 다음 작업이 무엇인지 가르쳐준다.
```

"Finish the test"(테스트를 끝내)라고 하고 있으니 이 명령을 따르도록 한다. functional_tests.py를 열어서 다음과 같이 수정한다.

*functional_tests.py*

```py
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
import time
import unittest

class NewVisitorTest(unittest.TestCase):

	def setUp(self):
		self.browser = webdriver.Firefox()

	def tearDown(self):
		self.browser.quit()

	def test_can_start_a_list_and_retrieve_it_later(self):
		# 에디스(Edith)는 멋진 작업 목록 온라인 앱이 나왔단느 소식을 듣고
		# 해당 웹 사이트를 확인하러 간다.
		self.browser.get('http://localhost:8000')

		# 웹 페이지 타이틀과 헤더가 'To-Do'를 표시하고 있다.
		self.assertIn('To-Do', self.browser.title)
		header_text = self.browser.find_element_by_tag_name('h1').text # 1
		self.assertIn('To-Do', header_text)

		# 그녀는 바로 작업을 추가하려고 한다.
		inputbox = self.browser.find_element_by_id('id_new_item') # 1
		self.assertEqual(
			inputbox.get_attribute('placeholder'),
			'작업 아이템 입력'
		)

		# "공작깃털 사기" 라고 텍스트 상자에 입력한다
		# (에디스의 취미는 날치 잡이용 그물을 만드는 것이다.)
		inputbox.send_keys('공작깃털 사기') # 2
		
		# 엔터키를 치면 페이지가 갱신되고 작업 목록에
		# "1: 공작깃털 사기" 아이템이 추가된다.
		inputbox.send_keys(Keys.ENTER) # 3
		time.sleep(1) # 4

		table = self.browser.find_element_by_id('id_list_table')
		rows = table.find_elements_by_tag_name('tr') # 1
		self.assertTrue(
			any(row.text == '1: 공작깃털 사기' for row in rows),
		)

		# 추가 아이템을 입력할 수 있는 여분의 텍스트 상자가 존재한다.
		# 다시 "공작깃털을 이용해서 그물 만들기" 라고 입력한다.(에디스는 매우 체계적인 사람이다)
		self.fail('Finish the test!')

		# 페이지는 다시 갱신되고, 두 개 아이템이 목록에 보인다.
		[...]
```
**1** 셀레니움이 제공하는 다양한 메소드들을 사용하고 있다. find_element_by_tag_name, find_element_by_id, find_elements_by_tag_name(element에 s가 붙어 있어서 복수의 요소를 반환한다.) 등이다.

**2** 셀레늄의 입력 요소를 타이핑하는 방법인 send_keys라는 것도 사용한다. 

**3** 키 클래스(임포트 하는 것을 잊지 말자)는 Enter나 Ctrl같은 특수 키 입력을 전송하는 역할을 한다.

**4** 엔터를 누르면, 페이지가 갱신된다. time.sleep은 우리가 새로운 페이지에서 무언가를 하기 전에 브라우저가 로딩이 확실히 끝나도록 한다. 이를 "명시적 대기"(매우 간단한 경우이다. 6장에서 이를 개선시키도록 하자)라 한다.

```
find_element_by ... 와 find_elements_by ... 함수의 차이점에 유의하자. 
전자는 하나의 요소만 반환하며 요소가 없는 경우 예외를 발새시킨다. 반면 후자는 리스트를 반환하여 이 리스트가 비어 있어도 괜찮다.
```
또한 any 함수를 보자. 이는 파이썬 내장 함수이다.

파이썬을 잘 모르는 사람들을 위해 간단히 설명하자면, 이 함수들은 "generator expression" 으로 "list comprehension"과 비슷하지만 더 진보된 기술이다. 필요하다면 구글을 통해 Guido가 직접 설명하는 자료를 찾아보자.

그러면 테스트를 진행해보자.

```sh
$ python functional_tests.py
[...]
selenium.common.exceptions.NoSuchElementException: Message: Unable to locate
element: h1
```

```<h1>```요소를 페이지에서 찾지 못했다는 메시지다. 그러면 어떻게 이 문제를 해결할 수 있을지 보자.

기능 테스트를 많이 수정했다면 커밋을 하는 것이 좋은 습관이다. 필자도 처음에는 이런 습관이 없어서 나중에 여러 변경 작업이 섞이고 나서야 후회했었다. 커밋 시에는 가능한 작은 단위로 하는 것이 좋다.

```sh
git diff # functional_tests.py 변경 내역 표시
git commit -am "기능 테스트가 작업 아이템을 추가할 수 있는지 확인"
```

## 4.3 "상수는 테스트하지 마라"는 규칙과 탈출구로 사용할 템플릿

lists/test.py에 있는 단위 테스트를 다시 보자. 현재는 특정 HTML 문자열을 확인하고 있지만, HTML을 테스트하기 위한 효율적인 방법이 아니다. 단위 테스트 시의 일반적인 규칙 중 하나는 "상수는 테스트하지 마라"다. HTML을 문자열로 테스트하는 것은 상수 테스트와 같다고 할 수 있다.

즉 다음과 같은 코드가 있다고 하자

```py
wibble = 3
```

이것을 테스트 할 때는 특별한 것이 없다.

```py
from myprogram import wibble
assert wibble == 3
```

단위 테스트는 로직이나 흐름 제어, 설정 등을 체크하기 윟나 것이다. 정확히 어떤 글자들이 HTML 문자열에 배열되 있는지 체크하는 어설션은 아무 의미가 없다. 이 경우는 템플릿을 사용하는 것이 훨씬 나은 접근법이다. HTML을 .html 확장자의 파일 형태로 보관할 수 있다면, 구문 검증을 더 효율적으로 할 수 있다. 이를 위한 많은 종류의 파이썬 템플릿 프레임워크가 존재하며, Django도 잘 동작하는 템플릿 프레임워크를 가지고 있다. 한번 사용해보자.

### 템플릿을 사용하기 위한 리팩터링

지금부터 할 작업은 뷰 함수가 이전과 같은 HTML을 반환하도록 하는 것이지만, 다른 프로세스를 적용하는 것이다. 이것을 리팩터링(Refactoring)이라고 한다. 리팩터링이란 "기능(결과물)은 바꾸지 않고" 코드 자체를 개선하는 작업을 일컫는다.

"기능은 바꾸지 않고" 라는 것이 핵심이다. 리팩터링을 통해 기능도 동시에 변경하거나 추가하려고 한다면 곧 문제에 부딪힐 것이다. 리팩터링은 그 자체가 하나의 기술 영역으로 매뉴얼까지 존재한다. 

첫 번째 규칙은 테스트 없이 리팩터링 할 수 없다는 것이다. 다행히도 우리는 TDD를 하고 있기 때문에, 잘 할 수 있을 것이다. 그러면 테스트를 통과하는지 잘 확인해보자. 이를 통해 리팩터링 작업이 가능한지를 결정할 수 있다.

```
$ python manage.py test
[...]
OK
```

훌륭하다. 테스트를 통과했다. 이제 HTML 문자열을 별도의 파일에 저장해보자. lists/templates라는 폴더를 만들고 여기에 템플릿 파일들을 저장하도록 한다. 이 폴더에 home.html이라는 새로운 파일을 만들어서 HTML 내용을 저장하도록 한다.

*lists/templates/home.html*
```
<html>
	<title>To-Do lists</title>
</html>
```
태그가 구분(강조)되는 것을 볼 수 있다. 훨씬 낫다! 그러면 뷰 함수를 다음과 같이 수정하자.

*lists/views.py*

```py
from django.shortcuts import render

def home_page(request):
	return render(request, 'home.html')
```

HttpResponse를 만드는 대신에 Django의 render 함수를 사용하고 있다. 이 함수는 첩선째 인수로 요청(request)을 지정하고, 두 번째 인수로 렌더링할 템플릿명을 지정한다. Django는 앱 폴더 내에 있는 templates라는 폴더를 자동으로 검색한다. 그리고 템플릿 콘텐츠를 기반으로 HttpResponse를 만들어준다.

```
템플릿은 Django의 아주 강력한 기능 중 하나이다. 무엇보다 파이썬 변수를 HTML 텍스트로 변경해 주는 기능은 큰 강점이다.
지금은 이 기능을 사용하지 않지만 뒤에서 배우게 된다. 
여기서 디스크에 있는 파일을 수동으로 읽지 않고, render와 render_to_string(뒤에 나옴)을 사용하는 것도 이것 때문이다.
```

동작하는지 확인해 보자.

```sh
$ python manage.py test
[...]
======================================================================
ERROR: test_home_page_returns_correct_html (lists.tests.HomePageTest) # 2
---------------------------------------------------------------------
Traceback (most recent call last):
  File "/.../superlists/lists/tests.py", line 17, in
test_home_page_returns_correct_html
    response = home_page(request) # 3
  File "/.../superlists/lists/views.py", line 5, in home_page
    return render(request, 'home.html') # 4
  File "/usr/local/lib/python3.6/dist-packages/django/shortcuts.py", line 48,
in render
    return HttpResponse(loader.render_to_string(*args, **kwargs),
  File "/usr/local/lib/python3.6/dist-packages/django/template/loader.py", line
170, in render_to_string
    t = get_template(template_name, dirs)
  File "/usr/local/lib/python3.6/dist-packages/django/template/loader.py", line
144, in get_template
    template, origin = find_template(template_name, dirs)
  File "/usr/local/lib/python3.6/dist-packages/django/template/loader.py", line
136, in find_template
    raise TemplateDoesNotExist(name)
django.template.base.TemplateDoesNotExist: home.html # 1

---------------------------------------------------------------------
Ran 2 tests in 0.004s
```

트레이스백을 분석할 기회가 다시 왔다.

**1** 먼저 에러를 확인하자: 테믚ㄹ릿을 반견할 수 없어서 에러가 발생하고 있다.

**2** 어떤 테스트가 실패하는지 다시 확인한다: HTML 뷰 테스트 부분이다.

**3** 테스트의 어느 코드에 문제가 있는지 확인한다: home_page 함수 호출에 문제가 있다.

**4** 마지막으로 애플리케이션의 어느 부분에서 에러가 발생하는지 확인한다: render호출 부분에서 에러가 발생한다.

왜 Django가 템플릿을 못 찾는 걸까? 템플릿 파일의 위치는 맞다.(lists/templates 폴더)

문제는 아직 이 앱을 Django에 등록하지 않았기 때문이다. 단순히 프로젝트 폴더에 있는 앱을 startapp 명령을 통해 실행하는 것만으로는 부족하다. Django에게 해당 앱을 사용한다는 신고를 해야 한다. 이를 위해선 settings.py 파일에 앱을 추가해야 한다. 파일을 열어서 INSTALLED_APPS 항목을 찾는다.

*superlists/settings.py*

```py
# Application definition
INSTALLED_APPS = [
	'django.contrib.admin',
	'django.contrib.auth',
	'django.contrib.contenttypes',
	'django.contrib.sessions',
	'django.contrib.messages',
	'django.contrib.staticfiles',
	'lists',
]
```

이미 많은 기본 앱이 설정돼 있는 것을 알 수 있다. 이 리스트 다음에 우리 앱을 추가하기만 하면 된다. 쉼표(,)를 끝에 추가하는 것을 지지 말자. 마지막에 추가할 때는 쉼표가 필요 없지만, 정작 있어야 할 때 추가하는 것을 잊어버려서 애를 먹을 수 있다. 파이썬은 다른 줄에 있는 문자열들을 합쳐버리기 때문에 주의가 필요하다.

수정했으면 다시 테스트를 실행해보자.

```sh
$ python manage.py test
    [...]
    self.assertTrue(html.endswith('</html>'))
AssertionError: False is not true
```

```
사용하는 텍스트 편집기가 마지막에 라인(\n)을 강제로 추가하지 않는다면, 이 에러가 발생하지 않는다. 
이 경우에는 다음의 설명을 무시하고 다음 단계로 건너뛰어도 좋다.
```

조금 진전이 있었다. 이제 템플릿 파일을 찾을 수 있는 듯하다. 하지만 여전히 마지막 세개 어설션이 실패하고 있다. 분명히 결과 마지막 부분이 이상하다. 디버그를 위해 print repr(response.content)를 이용해서 응답 내용을 출력해본 결과, 파일 마지막 부분에 라인(\n)이 추가돼 있는 것을 확인할 수 있다. 다음과 같이 수정하면 해결된다.

*lists/tests.py*

```py
self.assertTrue(html.strip().endswith('</html>'))
```

이건 작은 팁이지만, HTML 파일 마지막 부분에 있는 빈 공간 때문에 문제가 발생하는 것을 예방해 준다. 테스트를 다시 실행해보자.

```sh
$ python manage.py test
[...]
OK
```

코드 리팩터링 작업을 마쳤다. 테스트 결과도 만족스럽다. 다음은 상수를 테스트하지 않고 템플릿을 이용해서 렌더링하는 것을 테스트하도록 수정해 주어야 한다. 

### Django의 테스트 클라이언트

이를 테스트하는 한 가지 방법은 직접 테플릿을 테스트 안에서 렌더링 하고, view의 리턴값과 비교하는 것이다. Django의 render\_to\_string 함수를 통해 이러한 일을 할 수 있다.

*lists/tests.py*

```py
from django.template.loader import render_to_string
[...]
	def test_home_page_returns_correct_html(self):
		request = HttpRequest()
		response = home_page(request)
		html = response.content.decode('utf8')
		expected_html = render_to_string('home.html')
		self.assertEqual(html, expected_html)
```
하지만 이런 식으로 우리가 올바른 템플릿을 사용하는지 테스트 하는 것은 조금 불편하다. .decode()나 .strip()같은 것들도 다소 번잡하다. 대신, Django는 Django 테스트 클라이언트라는 툴을 제공하는데, 어떤 템플릿이 사용되는지 확인해주는 내장된 방법이다. 

*lists/test.py*
```py
def test_home_page_returns_correct_html(self):
	response = self.client.get('/') # 1

	html = response.content.decode('utf8') # 2
	self.assertTrue(html.startswith('<html>'))
	self.assertIn('<title>To-Do lists</title>', html)
	self.assertTrue(html.strip().endswith('</html>'))

	self.assertTemplateUsed(response, 'home.html') # 3
```
**1** 직접 HttpRequest 객체를 만들고 view함수를 호출하기 보다는, self.client.get을 호출하고, 우리가 원하는 url에 전해준다.

**2** 우리가 예상하는 대로 그대로 동작하도록, 예전 테스트는 그대로 둔다. 

**3** .assertTemplateUsed는 Django TestCase 클래스가 제공해 주는 테스트 메쏘드이다. 이는 어떤 템플릿이 응답을 렌더하기 위해 사용되었는지 확인할 수 있게 해 준다.(중요 - 이는 오직 테스트 클라이언트로부터 반환된 응답을 위해서만 동작할 것이다.)

테스트는 잘 동작할 것이다:

```sh
Ran 2 tests in 0.016s

OK
```

실패하지 않은 테스트는 항상 의심스럽기 때문에, 의도적으로 이를 실패시켜 본다.

*lists/tests.py*

```py
self.assertTemplateUsed(response, 'wrong.html')
```

이를 통해서 우리는 에러 메시지가 어떠할지 배울 수 있다:

```sh
AssertionError: False is not true : Template 'wrong.html' was not a template
used to render the response. Actual template(s) used: home.html
```

이는 매우 도움이 된다! assert가 정상이었던 이전으로 되돌리자. 우리는 우리의 이전 어설션들을 지울 수 있다. 또한 우리는 오래된 test_root_url_resolves 테스트를 지울 수 있다. 왜냐하면 이는 Django Test Client에 의해 이미 테스트가 되었기 때문이다. 우리는 두 긴 테스트를 하나로 합친 것이다!

*lists/test.py (ch041010)*

```sh
from django.test import TestCase
class HomePageTest(TestCase):
	def test_uses_home_template(self):
		response = self.client.get('/')
		self.assertTemplateUsed(response, 'home.html')
```

여기서 중요한 것은, 상수를 테스트 하는 것이 아니라 우리는 우리의 구현을 테스트 한다는 것이다.

### 왜 진작 Django Test Client를 사용하지 않았나?

당신은 왜 시작부터 Django Test Client를 보여주지 않았나 하고 궁금해할 수도 있다. 실무에서는 필자도 그렇게 할 것이다. 하지만 다음과 같은 이유로 직접 손이 많이 가는 작업을 보여주고 싶었다. 첫째, 개념을 하나하나 설명할 수 있었고, 러닝 커브를 가능한 낮게 유지할 수 있었기 때문이다. 두번째, 항상 Django를 사용하는 것은 아닐테고, 테스트 도구들은 언제든지 가능한 것은 아니다. 반면 함수를 직접 호출하고 응답을 조사하는 것은 언제든지 가능하다.

Django Test Client는 단점도 있다; 나중에 이 책에서는 완전히 분리된 단위 테스트들과 테스트 클라이언트가 "통합된" 테스트들과의 차이에 대해 이야기해 볼 것이다. 지금으로서는, 매우 실용적인 선택이다.

## 4-4 리팩터링에 관해

우선 시시한 리팩터링 테스트였다. 하지만 켄트 벡이 "테스트 주도 개발"에서 언급했듯이, "여러분이 이런 식으로 해야 한다는 것이 아니라, 이런 식으로 할 수 있다는 것을 보여주기 위함이다."

사실, 필자는 테스트를 먼저 본능적으로 수정하고 싶었다. render_to_string 함수를 바로 사용해서 필요 없는 3개 어설션을 삭제하고 예상 랜더링과 콘텐츠를 비교하는 부분만 남겨둔다. 그러고 나서 앱 코드를 수정하는 것이다. 하지만 이렇게 단계를 나누어 설명함으로 쉽게 이해할 수 있었을 것이다. 필자도 ```<html>```이나 ```<title>``` 태그 대신에, 임의로 여러 문자열들을 사용해서 템플릿을 만들 수 있었다.

```
리팩터링 시에는 앱 코드와 테스트 코드를 한번에 수정하는것이 아니라, 하나씩 수정해야 한다.
```

리팩터링 시에는 몇 가지 처리를 수정하기 위해 단계를 건너뛰는 경향이 있다. 하지만 반 이상의 파일을 수정하기 시작하면서 자신이 무엇을 수정했는지 모르게 되고 결국 아무것도 동작하지 않게 된다. 리팩터링 캣(Refactoring cat)처럼 되고 싶지 않다면, 작은 단계로 나누어 착실히 작업하도록 하자. 참고로 리팩터링과 기능 변경은 전혀 다른 개념인 것에 유의하자.

```
많은 것을 한꺼번에 바꾸거나 실행하려고 할 때, 어떤 문제가 발생하는지 보여주기 위해서 "리팩터링 캣"을 다시 다루게 될 것이다. 
앞에서 설명한 만화에 등작하는 어깨 위의 천사와 악마를 항상 생각하자. 
한쪽 어깨에는 테스팅 고트님이 있고 ,이에 대항하기 위해 다른 한쪽 어깨에는 리팩터링 캣이 있어서 다른 충고를 해 준다.
```

리팩터링 후에는 커밋을 해 주는 것이 좋다.

```sh
$ git status # see tests.py, views.py, settings.py 와 템플릿 폴더가 수정된 것을 볼 수 있다.
$ git add . # 템플릿 폴더를 버전 관리에 추가한다.
$ git diff --staged # 커밋하려고 하는 수정 내역을 확인한다.
$ git commit -m "템플릿을 이용하도록 홈페이지 뷰를 리팩터링"
```

## 4-5 메인 페이지 추가 수정

아직까지 기능 테스트가 실패하고 있는 상태다. 테스트를 통과하도록 수정해 보자. HTML이 이젠 템플릿 형태이기 때문에 추가적인 단위 테스트 없이 바로 수정할 수 있다. 현재 필요한 것은 ```<h1>```이다.

*lists/templates/home.html*

```html
<html>
 <head>
   <title>To-Do lists</title>
 </head>
 <body>
   <h1>Your To-Do list</h1>
 </body>
</html>
```
기능 테스트가 이 방식을 좋아하는지 확인해 보자.

```sh
selenium.common.exceptions.NoSuchElementException: Message: Unable to locate
element: [id="id_new_item"]
```

아직까지 실패다. 다음과 같이 수정해 보자.

*lists/templates/home.html*

```html
 [...]
     <h1>Your To-Do list</h1>
     <input id="id_new_item" />
 </body>
 [...]
 ```

 이번에는 괜찮을까?

 ```sh
 AssertionError: '' != 'Enter a to-do item'
 ```

placeholder 텍스트를 추가한다.

*lists/templates/home.html*

```html
<input id="id_new_item" placeholder="Enter a to-do item" />
```

이제 다음과 같은 결과를 확인할 수 있다.

```sh
selenium.common.exceptions.NoSuchElementException: Message: Unable to locate
element: [id="id_list_table"]
```

이제 페이지에 테이블을 추가하자. 현재는 테이블에 비어 있다.

*lists/templates/home.html*

```html
   <input id="id_new_item" placeholder="Enter a to-do item" />
   <table id="id_list_table">
   </table>
</body>
```

이제 기능 테스트 결과를 확인해 보자.

```sh
 File "functional_tests.py", line 43, in
test_can_start_a_list_and_retrieve_it_later
 any(row.text == '1: 공작깃털사기' for row in rows)
AssertionError: False is not true
```
원인이 분명치 않다. 문제를 찾기 위해 코드 넘버를 따라가보니 기능 테스트에 사용ㅎ나 함수에 문제가 있다는 것을 알았다.
정확히는 assertTrue라는 함수로 현재 자세한 실패 메시지를 출력하고 있지 않다. 대부분의 assertXXX 함수는 사용자 정의 메시지를 인수로 지정할 수 있다.

*functional_tests.py*

```py
self.assertTrue(
    any(row.text == '1: 공작깃털 사기' for row in rows),
    "신규 작업이 테이블에 표시되지 않는다."
    )
```

다시 FT를 실행하면 다음과 같은 메시지를 확인할 수 있다.

```sh
AssertionError: False is not true : New to-do item did not appear in table
```

이 문제를 해결하려면 사용자 폼(form) 제출 처리를 구현해야 하는데, 이것은 다음 장에서 다룰 내용이다.

일단 커밋을 해 두자.

```sh
$ git diff
$ git commit -am "템플릿을 이용한 메인 페이지 HTML 생성"
```

리팩터링에 감사하자. 이로서 템플릿을 랜더링하기 위한 뷰 설정을 완료했고, 상수를 더 이상 테스트하지 않아도 된다. 또한 사용자 입력 처리를 위한 준비가 마무리되었다.

# 정리 : TDD 프로세스

실습을 통해 TDD 프로세스의 주요 내용을 모두 살펴보았다.

- 기능 테스트(Functional tests)
- 단위 테스트(Unit tests)
- 단위 테스트-코드 주기(Unit test-code cycle)
- 리팩터링(Refactoring)

흐름도를 이용해서 TDD 프로세스를 정리해보자. 흐름도를 사용하는 것을 이해해주기 바란다. 경영 컨설턴트로 일한 몇년간이 필자를 망쳐놓아서 이런 습관이 생겼다. 다행히도 이런 흐름도를 사용하면 반복 처리나 재귀 처리를 표현하고 이해하기가 수월하다. 

전체적인 TDD 프로세스는 어떻게 될까? 그림 4-3을 보자.

**그림 4-3** 전체적인 TDD프로세스

![전체적인 TDD프로세스](https://www.obeythetestinggoat.com/book/images/twp2_0403.png)

테스트를 작성하고 그것이 실패하는 것을 확인한다. 그리고 문제를 작성하기 위한 최소 코드를 작성한다. 테스트를 통과할 때까지 이 과정을 반복한다. 필요에 따라선 코드를 리팩터링 한다. 리팩터링 후에는 다시 테스트 과정을 반복해야 한다.

기능 테스트 및 단위 테스트를 둘 다 해야 할 때는 이 흘므도를 어떻게 적용할 수 있을까? 기능 테스트를 상위 테스트 관점으로 생각하면 된다. 그리고 그림 4-3에 "최소 코드 작성" 부분이 단위 테스트를 이용하는 작은 TDD 주기가 되는 것이다. 그림 4-4를 보자.

**그림 4-4** 기능 테스트와 단위 테스트로 구성되는 TDD 프로세스

![기능 테스트와 단위 테스트로 구성되는 TDD 프로세스](https://www.obeythetestinggoat.com/book/images/twp2_0404.png)

먼저 기능 테스트를 작성하고 이것이 실패하는지 확인한다. "최소 코드 작성" 프로세스에서는 작은 TDD주기를 통해 테스트가 통과하도록 만든다. 이때 하나 또는 그 이상의 단위 테스트를 확인하고 이를 단위 테스트-코드 주기에 넣어서 통과할 때까지 주기를 반복한다. 통과하면 다시 FT로 돌아가서 애플리케잇녀 코드를 수정한다(리팩터링). 수정후에는 다시 단위 테스트를 실시해야 한다.

기능 테스트 관점의 리팩터링은 어떻게 해야 할까? 이것은 애플리케이션 동작을 확인하기 위해 기능 테스트를 사용하지만, 단위 테스트를 변경, 추가, 제거할 수 있음을 의미한다. 또한 단위 테스트 주기를 이용해서 실제 구현한 것을 변경한다.

기능 테스트는 애플리케이션이 동작하는지 아닌지를 판단하기 위한 궁극의 수단이다. 반면, 단위 테스트는 이 판단을 돕기 위한 툴이라고 할 수 있다.

이런 식으로 TDD를 표현한 것을 "이중 반복 TDD(Double-Loop TDD)"이라 부른다.

이후 장에서는 이 처리 흐름의 각 요소에 대해 좀더 자세히 다루도록 한다.



