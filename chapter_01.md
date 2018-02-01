# Chapter 01 기능 테스트를 이용한 Django 설치

## 1.1 테스트 염소님을 경배하라! 테스트를 하기 전까진 아무것도 하지 마라

먼저 Django가 설치되었는지 확인을 해야 한다. 이를 확인하기 위해서는 Django 개발 서버를 실행시키고  우리 컴퓨터의 웹 브라우저에서 웹 페이지를 제공해 주고 있는지 확인해 보아야 한다. 우리는 이를 확인하기 위해 Selenium browser를 사용할 것이다.

*functional\_tests.py* 파일을 프로젝트 폴더에 생성하자.

```python
from selenium import webdriver

browser = webdriver.Firefox()
browser.get('http://localhost:8000')

assert 'Django' in browser.title
```

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

# 1.2 Django 실행시키기
먼저 Django를 실행시키고 *프로젝트*를 만들자. 이는 우리 사이트를 위한 메인 컨테이너가 될 것이다. Django는 아래와 같은 커맨드 라인 툴을 제공한다.
```
$ django-admin.py startproject superlists .
```

맨 뒤의 "." 이 중요하니 잊지 말자.

인느 *manage.py*파일을 현재 당신의 경로에 만들어낼 것이다. 그리고 서브폴더로 *superlist*를 비롯하여 여러 가지를  만들어낼 것이다.

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
Django의 개발 서버가 이제 열렸고 우리의 머신 위에서 실행되고 있다.

이제 이를 내버려두고 다른 커맨드 쉘 창을 키자. 프로젝트 폴더로 가서 아래의 테스트를 실행시키자.
```
python functional_tests.py
```

작동 화면이 뜰 것이다. 이를 확인하고 터미널에서 Ctrl + C 키를 통해서 종료해 주자.

<img src="https://www.obeythetestinggoat.com/book/images/twp2_0102.png">

## 1.3 Git 저장소 시작하기
```git init```을 사용해 준다.
```
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
<img src="https://www.obeythetestinggoat.com/book/images/twp2_0103.png">

