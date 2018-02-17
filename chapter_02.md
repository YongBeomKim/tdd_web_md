# Chapter 02 unittest 모듈을 이용한 기능 테스트 확장

Django의 기본 페이지인 "it worked!" 페이지를 확인하는 대신 테스트를 확장해서 실제 사이트의 메인 페이지에서 확인해야 할 사항들을 반영하도록 한다.

이쯤에서 우리가 구축해야 할 웹 사이트가 어떤 것인지 공개한다. 바로 작업 목록(To-Do) 사이트이다. 몇 년 전까지만 해도 대부분 웹 튜토리얼이 블로그 만들기였던것을 고려한다면, 이 책음 나름 최신 경향을 반영한다는 것을 알 수 있다. 

작업 목록은 정말 좋은 예시이다. 매우 기본적이면서 간단해서(텍스트 리스트만 있으며 됨) "최소한의 실행 사능한" 애플리케이션을 구축하고 실행할 수 있기 때문이다. 또한 어떤 형태로든 확장할 수 있다. 마감일을 추가하거나, 알림 기능, 공유 기능 등을 추가할 수 있고, 클라이언트 측 UI도 개선할 수 있다. 이 예제는 "작업 목록" 에만 국한된 것은 아니다. 어떤 형태의 목록이든 적용할 수 있다. 중요한 것은 이 예제를 통해 웹 프로그래밍 전반과 TDD 적용 방법에 대해 배울 수 있다는 것이다. 

오늘의 할일(TO-DO LIST)에 관한 웹페이지를 만들어 보자.

TO-DO 리스트는 괜찮은 예제이다. 매우 간단하면서도 여러가지로 확장할 수도 있다. 또한 웹 프로그래밍의 여러 측면과 어떻게 TDD 가 동작하는지 알기에도 좋다.

## 2.1 기능 테스트를 이용한 최소 기능의 애플리케이션(Minimum Viable App) 설계

셀레니움을 사용한 테스트들은 실제 브라우저를 실행해서 애플리케이션이 어떻게 "동작"(functions)하는지 사용자 관점에서 확인할 수 있다. 이런 테스트를 **기능 테스트**(Functional Test)라 부르는 이유도 여기에 있다.

또한 이것은 FT자체가 애플리케이션 사양이 될 수 있음을 의미하기도 한다. **사용자**(User story)라고 하는 방식을 따르는 경향이 있는데, 특정 기능을 사용자가 어떻게 사용하며 이때 애플리케이션이 어떻게 반응해야 하는지를 확인하는 방식이다. 

```
Functional Test == Acceptance Test = End-to-End Test
다 같은 말이다. 중요한 건 이 테스트들이 전체 애플리케이션이 어떻게 동작하는지를 외부 사용자 관점에서 확인하는 테스트라는 것이다.
블랙 박스(Black Box)테스트라는 용어를 사용하는 경우도 있는데, 시스템 내부에서 전혀 알지 못하기 때문이다.
```
기능 테스트는 읽기 쉬워서 우리가 따라갈 수 있도록 해야 한다. 코멘트를 명시적으로 테스트 코드에 단다. 새로운 기능 테스트를 할 때에는 코멘트를  먼저 단다. 비전공자도 읽기 쉽도록 쉽게 작성해야 한다. 마치 앱의 요구사항이나 특징을 설명하듯이 말이다.

TDD와 애자일(Agile) 개발 방식은 종종 함께 구현되기도 하는데, 이때 언급되는 것이 최소 기능 애플리케이션이다. 가장 간단한 기능만으로 구성되지만 동작하는 애플리케이션을 의미한다. 이런 애플리케이션을 구축해서 테스트해보도록 하자.

최소 기능의 작업 목록 애플리케이션(Minimum viable App)에 필요한 것은 사용자가 작업을 입력하고 그것을 저장해두는 기능이다.

*functional_tests.py*파일을 열고 스토리를 아래와 같이 작성하자:

```py
from selenium import webdriver

browser = webdriver.Firefox()

# Edith 는 멋진 앱에 대해 들었다. 
# 그녀는 이 멋진 홈페이지에 들어가서 체크해 본다.
browser.get('http://localhost:8000')

# Edith는 페이지 타이틀과 헤더가 to-do lists들을 언급하고 있는 것을 알아챈다.
assert 'To-Do' in browser.title

# 그녀는 즉시 to-do 아이템을 입력할 창을 갖게 된다.

# 그녀는 "공작 깃털 사기"를 텍스트 박스에 입력한다.(Edith의 취미는
# 날치 낚시 미끼를 만드는 것이다)

# 엔터를 칠때 페이지가 업데이트 된다. 이제 페이지의 리스트로서
# "1: 공작 깃털 사기"가 to-do 리스트의 아이템으로 나온다.

# 여전히 텍스트 박스가 다른 아이템을 값을 입력받기를 기다리고 있다.
# 그녀는 "공작 깃털을 사용하여 플라이 낚시대를 만들어라" 를 입력한다.(Edith은 매우 체계적이다.)

# 페이지가 업데이트 되고, 이제 그녀의 두가지 아이템 모두를 보여준다.

# Edith는 사이트가 여전히 그녀의 리스트를 기억하고 있을지 궁금해 한다. 그러고 나서

# 사이트가 고유한 URL을 그녀를 위해 생성한 것을 본다 -- 그리고

# 이 효과에 대한 설명글이 있다.

# URL에 방문한다. 그녀의 to-do list가 여전히 존재한다.

# 만족해서, 그녀는 자러 간다.

browser.quit()
```

무엇을 하는지에 관해 코드를 그저 반복하는 것은 무의미하다. 가령 아래처럼 말이다.
```python
# increment wibble by 1
wibble += 1
```
이러한 코드는 특히 나중에 코드를 업데이트 하거나 하면 문제가 생길 수 있다. 

눈치 챘겠지만, ```assert```코드가 "Django" 대신 "To-Do"리스트를 찾도록 업데이트 하였다. 그 뜻은 우리가 테스트를 실패하기를 기대한다는 것이다. 실행해 보자.

먼저 서버를 시작한다:
```sh
python manage.py runserver
```

그리고 다른 쉘에서는 테스트를 실행시킨다:
```sh
$ python functional_tests.py
Traceback (most recent call last):
  File "functional_tests.py", line 10, in <module>
    assert 'To-Do' in browser.title
AssertionError
```
이것을  *예상한 실패(expected fail)라고 한다. 사실 이는 좋은 소식이다. 물론 테스트를 통과한 것 만큼 좋다는 것은 아니지만, 최소한 올바른 이유로 실패하였기 때문이다. 이제 우리는 테스트 코드를 올바르게 적었다는 것에 대한  확신이 생겼다.

## 2.2 파이썬 기본 라이브러리의 unittest 모듈
여기 조금 귀찮은 점들이 있다. 첫째로는, 메시지 "AssertionError"는 매우 효과적이지 않다-테스트가 브라우저 타이틀에서 실제로 찾은 것이 무엇인지 알려준다면 좋을 것이다. 또한, 이 테스트는 파이어폭스 윈도우가 데스크톱에서 돌아다니는 채로 내버려 두었다. 이 부분이 자동으로 정리된다면 좋을 것이다.

이를 위한 한가지 방법은 두번째 파라미터(매개변수)를 assert키워드에 추가하는 것이다. 예를 들자면
```python
assert 'To-Do' in browser.title, "Browser title was " +browser.title
```

열려있는 파이어폭스 창을 닫아주기 위해서는 ```try/finally```를 사용해도 되지만,  이러한 문제들을 테스트에서 꽤 흔하다. 그래서 이미 이를 위해 표준 라이브러리가 존재한다. ```unittest```모듈이다. 이를 *functional\_test*에서 사용해 보자.

*functional_tests.py*

```python
from selenium import webdriver
import unittest

class NewVisitorTest(unittest.TestCase):  # 1번

    def setUp(self):  # 3번
        self.browser = webdriver.Firefox()

    def tearDown(self):  # 3번
        self.browser.quit()

    def test_can_start_a_list_and_retrieve_it_later(self):  #2번
        # Edith has heard about a cool new online to-do app. She goes
        # to check out its homepage
        self.browser.get('http://localhost:8000')

        # She notices the page title and header mention to-do lists
        self.assertIn('To-Do', self.browser.title)  # 4번
        self.fail('Finish the test!')  # 5번

        # She is invited to enter a to-do item straight away
        [...rest of comments as before]

if __name__ == '__main__':  #6번
    unittest.main(warnings='ignore')  #7번
```

눈치 챈 점이 몇 가지 있을 것이다.
1. ```unittest.TestCase```를 상속해서 테스트를 클래스 형태로 만든다.

2. 테스트의 메인 코드는 ```test_can_start_a_list_and_retrieve_it_later```라 불리는 메서드이다. ```test```라는 이름으로 시작하는 모든 메소드는 테스트 메소드이며 테스트 실행자에 의해 실행된다. 클래스당 하나 이상의 테스트 메소드를 작성할 수 있따. 가능한 테스트 내용을 알 수 있는 테스트 메소드 명칭을 사용하는 것이 가장 좋다.

3. ```setUP```과 ```tearDown```모두 특수한 메소드로, 각 테스트 시작 전, 후에 실행된다. 필자는 브라우저를 시작하고 멈추기 위해 사용한다. try/except와 비슷한 구조로 테스트에 에러가 발생해도 ```tearDown```이 실행된다. 이를 활용하면 파이어폭스 창이 쓸때없이 떠다니는 것을 방지할 수 있다.

4. ```self.assertIn```을 그냥 ```assert```대신 사용한다. ```unittest```은 많은 이러한 도움 함수(helper function)를 test assertion에 사용한다. ```assertEqual```, ```assertTrue```, ```assertFalse```가 그것이다. 더 자세한 점은 <u>```unittest```</u>[다큐먼트](http://docs.python.org/3/library/unittest.html)에서 찾을 수 있다.

5. ```self.fail```은 강제적으로 테스트 실패를 발생시켜서 에러 메시지를 출력한다. 필자는 테스트가 끝났다는 것을 알리기 위해 사용하고 있다.

6. 마지막으로, 우리는 ```if __name__=='__main__'```부분이다(파이썬 스크립트가 다른 스크립트에 임포트된 것이 아니라 커맨드라인을 통해 실행됐다는 것을 확인하는 코드다). 우리는 ```unittest.main()```을 호출해서 ```unittest```테스트 실행자를 가동한다. ```unittest```는 자동으로 파일 내 테스트 클래스와 메소드를 찾아서 실행해주는 역할을 한다.

7. ```warnings='ignore'``` 은 테스트 작성 시에 발생하는 불필요한 리소스 경고를 제거하기 이한 것이다. 이 부분을 읽을 때쯤이면 이미 리소스 경고 문제가 발생하지 않았기 때문에 삭제해도 상관없다. 

```sh
$ python functional_tests.py
F
======================================================================
FAIL: test_can_start_a_list_and_retrieve_it_later (__main__.NewVisitorTest)
 ---------------------------------------------------------------------
Traceback (most recent call last):
  File "functional_tests.py", line 18, in
test_can_start_a_list_and_retrieve_it_later
    self.assertIn('To-Do', self.browser.title)
AssertionError: 'To-Do' not found in 'Welcome to Django'

 ---------------------------------------------------------------------
Ran 1 test in 1.747s

FAILED (failures=1)
```

이전보다 조금 나아진 것을 느낄 수 있을 것이다. 테스트가 파이어폭스 창과 연동돼고, 제대로 된 형태의 결과 메시지가 출력된다. 몇 개의 테스트가 실행됐고 그중에서 몇 개가 실패했는지 보여주며, assertIn 은 유용한 디버깅 정보와 함께 도움이 되는 에러 메시지를 출력하고 있다.

## 2.3 커밋

커밋하기 적절한 타이밍이다. 매우 명확한 변경 내용을 반영할 수 있다. 기능 테스트에 우리가 설정한 스토리를 주석으로 포함시켰다. 또한 파이썬 unittest 모듈과 모듈에 포함된 다양한 테스트 함수들을 추가했다.


```git status```를 사용해 보자. 이를 보면 *functional\_test.py*만 바뀐 것을 알 수 있다. 그리고 ```git diff```를 사용하면 마지막 커밋과 현재 데스크에 있는 것의 차이를 알 수 있다. 

```sh
$ git diff
diff --git a/functional_tests.py b/functional_tests.py
index d333591..b0f22dc 100644
--- a/functional_tests.py
+++ b/functional_tests.py
@@ -1,6 +1,45 @@
 from selenium import webdriver
 +import unittest

 -browser = webdriver.Firefox()
 -browser.get('http://localhost:8000')
 +class NewVisitorTest(unittest.TestCase):

 -assert 'Django' in browser.title
 +    def setUp(self):
 +        self.browser = webdriver.Firefox()
 +
 +    def tearDown(self):
 +        self.browser.quit()
 [...]
```

이제 추적되고 있는 파일들 중에서 변한 부분을 git에 알려주자
```sh
git commit -a
```
-a는 자동적으로 추적되는 파일들 중에 어떠한 변화라도 추가하라는 뜻을 가지고 있다. 여기서 추적되는 파일(tracked files)이란 이전에 커밋된 적이 있다는 말이다. 어떠한 새 파일도 추가되지 않을 것이지만(```git add```를 통해서 직접 명시적으로 추가를 먼저 해야 한다), 종종 어떠한 새 파일이 없는 경우에는 유용한 단축 명령어이다.

에디터가 나타나면, 커밋 메시지를 “첫 기능 테스트 주석 추가 및 unittest 사용” 과 같이 남겨두자.

이제 우리는 우리 리스트 앱을 위해 실제 코드를 작성할 준비가 잘 되어 있다. 계속 분발하자. Read on!

## 유용한 TDD 개념

**사용자 이야기(User Story)**

- 사용자 관점에서 어떻게 애플리케이션이 동작해야 하는지 기술한 것이다. 기능 테스트 구조화를 위해 사용한다. 

**예측된 실패(Expected failure)**

- 의도적으로 구현한 테스트 실패를 의미한다.
