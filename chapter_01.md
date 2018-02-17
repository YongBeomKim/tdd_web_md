# Chapter 01 기능 테스트를 이용한 Django 설치

TDD는 자연스럽게 오는 것이 아니다. 이는 무술과 같은 훈련이다. 그리고 쿵후 영화처럼, 당신은 성격 나쁘고 비합리적인 스승이 필요하다. 우리의 경우 테스트 고트님이 그 역할을 할 것이다.

## 1.1 테스트 염소님을 경배하라! 테스트를 하기 전까진 아무것도 하지 마라

비록 무엇을 할지 잘 모르겠지만, 일단 웹 사이트를 하나 만들어 보자. 일반적으로 웹 사이트를 할때 첫 번째 단계는 웹 프레잌워크를 설치하고 설정하는 것이다. *이것을 다운받고, 이것을 설치하고, 이것을 설정하고, 스크립트를 돌린다...* 하지만 TDD를 할 때에는 다른 마음가짐이 필요하다. TDD를 할 때에는 언제나 테스팅 고트님이 머리속에 있어야 한다.(염소처럼 한 가지만 생각해라) "테스트를 먼저 해, 테스트를 먼저 하라고!"

TDD에서 먼저 해야 하는 것은 언제나 같다. "테스트를 작성하라"

*먼저* 테스트를 작성한다; 그리고 실행해서 우리가 예상한 대로 실패하는지 확인한다. *오직 그러고 나서야* 그 다음 과정인 웹 구축 단계로 넘어갈 수 있다. 이것을 염수 같은 목소리로 반복해서 되뇌어라. 필자는 그렇게 하고 있다.

염소에 대해 알아두어야 할 다른 한 가지는, 염소는 한 번에 한 가지만 한다는 것이다. 이 때문에 염소는 산에서 떨어지거나 하는 일이 거의 없다. 이렇게 진행한다면, 그림 1-1처럼 아무리 경사가 심한 곳에 있더라도 문제가 없다.

\> **그림 1-1** 염소는 여러분이 생각하는 것보다 훨씬 민첩하다.

![testing_goat](https://www.obeythetestinggoat.com/book/images/twp2_0101.png)

우리는 단계를 작게 나누어 조금씩 진행해 가도록 한다. 먼저 유명한 파이썬 웹 프레임워크인 Django를 이용해서 애플리케이션을 개발하는 과정을 보자.

첫 번째 단계는 Django가 제대로 설치됐는지, 사용할 준비가 됐는지 확인하는 것이다. 이것을 확인하게 위해서, Django 개발 서버를 가동해서 이 서버에 있는 웹 페이지를 로컬 PC상의 브라우저로 접속해야 한다. *셀레니움* 브라우저 자동화 툴을 이용해서 이 동작을 구현하도록 한다.

코드를 저장할 프로젝트 폴더를 하나 만들고 거기에 functional_test.py라는 파이썬 파일을 생성한다. 그리고 다음 코드를 입력하자. 기분이 내킨다면, 염소 소리를 내어보자, 도움이 될 것이다:

*functional\_tests.py* 파일을 프로젝트 폴더에 생성하자.

```py
from selenium import webdriver

browser = webdriver.Firefox()
browser.get('http://localhost:8000')

assert 'Django' in browser.title
```
우리가 만든 첫 **기능 테스트**(Functional Test, FT)이다. 뒤에서는 기능 테서트가 무엇인지 그리고 단위(유닛)테스트와 어떻게 다른지에 대해 자세히 설명하도록 하겠다. 여기서는 이 코드가 어떤 역할을 하는지만 이해하고 넘어가면 된다:

- 셀레니움을 실행하여 실제 Firefox 브라우져가 열리도록 한다.
- localPC의 서버 웹 페이지를 연다
- 페이지 타이틀에 "Django" 가 있는지 확인한다.

```sh
(superlists)  jin009@jin009-900X5N  ~/projects/python-tdd-book  python functional_tests.py
Traceback (most recent call last):
  File "functional_tests.py", line 4, in <module>
    browser.get('http://localhost:8000')
  File "/home/jin009/.pyenv/versions/superlists/lib/python3.6/site-packages/selenium/webdriver/remote/webdriver.py", line 324, in get
    self.execute(Command.GET, {'url': url})
```

브라우저에서 "Unable to connect" 에러 페이지를 볼 수 있다. 콘솔창에서는 셀레니움이 에러를 만들었다며 무시무시한 에러 메시지를 보여주고 있다. 파이어 폭스가 망가진 채로 있지만 이는 나중에 해결하자.

이것으로 테스트 실패 결과를 확인했다. 이것은 애플리케이션 구축을 시작해도 좋다는 의미이기도 하다.

# 1.2 Django 가동 및 실행
"전제조건 및 가정"을 읽고 지시받은 대로 프로그램을 설치했다면, 이미 Django가 설치되어 있을 것이다. 첫 번ㅉ ㅐ단계는 웹 사이트의 메인 컨테이너가 될 프로젝트를 만들기 위해 Django를 가동하고 실행하는 것이다. 이를 위해 Django에서 커맨드라인 툴을 제공한다. 

```sh
$ django-admin.py startproject superlists .
```

맨 뒤의 "." 이 중요하니 잊지 말자.

이는 *manage.py*파일을 현재 당신의 경로에 만들어낼 것이다. 그리고 서브폴더로 *superlist*를 비롯하여 여러 가지를 만들어낼 것이다.

```sh
├── functional_tests.py
├── geckodriver.log
├── manage.py
└── superlist
    ├── __init__.py
    ├── settings.py
    ├── urls.py
    └── wsgi.py
```
*superlists* 폴더는 사이트의 글로벌 configuration 정보를 저장하는  *settings.py*와 같은 파일을 담는다. 전체 프로젝트를 위한 폴더이다. 

여기서 *manage.py*에 주목하자. 이는 Django의 스위스 군대 칼과도 같다. 이를 통해 개발 서버를 실행시킬 수 있다. 이를 실행해 보자

```sh
$ python manage.py runserver
Performing system checks...

System check identified no issues (0 silenced).

You have 13 unapplied migration(s). Your project may not work properly until
you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.

Django version 1.11.3, using settings 'superlists.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```
이것을 실행 상태로 둔 채 별도의 커맨드라인 창을 열어서 테스트 코드를 다시 실행해 보자(functional_test.py 파일이 있는 곳에서 실행한다.)

```sh
python functional_tests.py
```

커맨드상에서 별다른 변화가 없지만, 두 가지 사항을 확인할 수 있다: 먼저, 더이상 AssertionError가 발생하지 않는다. 둘째, 셀레니움에 의해 파이어폭스 창이 뜨고 그 전과는 다른 페이지를 보여준다.

별것 아닌것 처럼 보인다 하더라도, 우리가 통과한 첫 번째 테스트다. 만세!

이 모든 작업이 마법같이 느껴진다면, 수동으로 웹 브라우저를 열어서 개발 서버(http://localhost:8000)에 접속해서 살펴보는 것도 좋다. 아래 그림 1-2와 같은 화면을 볼 수 있을 것이다.

\> **그림 1-2** 동작한다!

<img src="https://www.obeythetestinggoat.com/book/images/twp2_0102.png">

작동 화면이 뜰 것이다. 이를 확인하고 터미널에서 Ctrl + C 키를 통해서 종료해 주자.

## 1.3 Git 저장소 시작하기
**git init**을 사용해 준다.
```sh
git init . 
Initialised empty Git repository in ...python-tdd-book/.git/
```


우리의 작업 경로(Working Directory)는 항상 manage.py를 포함하고 있는 폴더이다. 
경로에 대한 말이 없으면 이 경로와 연관이 있다는 것이다.

```
(superlists)  jin009@jin009-900X5N  ~/projects/python-tdd-book  ls
db.sqlite3  functional_tests.py  geckodriver.log  manage.py  superlist
```
이 중에서 db.sqlite3과 같은 db파일이나, geckodriver.log처럼 Selenium 디버그 결과물을 가진 파일들 등은 버전 컨트롤 하에 두고 싶지 않다. 이를 제외하기 위해 .gitignore파일을 사용한다.

```sh
$ echo "db.sqlite3" >> .gitignore
$ echo "geckodriver.log" >> .gitignore
$ echo "virtualenv" >> .gitignore
```
```sh
$ git add .
$ git status
On branch master

Initial commit

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

        new file:   .gitignore
        new file:   functional_tests.py
        new file:   manage.py
        new file:   superlists/__init__.py
        new file:   superlists/__pycache__/__init__.cpython-36.pyc
        new file:   superlists/__pycache__/settings.cpython-36.pyc
        new file:   superlists/__pycache__/urls.cpython-36.pyc
        new file:   superlists/__pycache__/wsgi.cpython-36.pyc
        new file:   superlists/settings.py
        new file:   superlists/urls.py
        new file:   superlists/wsgi.py
```
많은 pyc파일이 생성된 것을 볼 수 있다. 이를 제거해 주고 gitignore에도 추가해 주자.
```sh
$ git rm -r --cached superlists/__pycache__
rm 'superlists/__pycache__/__init__.cpython-36.pyc'
rm 'superlists/__pycache__/settings.cpython-36.pyc'
rm 'superlists/__pycache__/urls.cpython-36.pyc'
rm 'superlists/__pycache__/wsgi.cpython-36.pyc'
$ echo "__pycache__" >> .gitignore
$ echo "*.pyc" >> .gitignore

```
```
$ git add .gitignore
$ git commit
```
\> **그림 1-3** 첫 번째 Git 커밋

<img src="https://www.obeythetestinggoat.com/book/images/twp2_0103.png">

축하한다! 이제 여러분은 셀레늄을 이용해서 기능 테스트를 작성했고 Django 설치 및 실행까지 끝냈다. 고트님이 인증한 테스트 우선 TDD방법을 구현한 것이다. 스스로 잘했다고 다독여 준 후 다음 장으로 넘어가자.
