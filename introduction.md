# Prerequisites

## Geckodriver설치

Gecko driver 다운받아 설치 https://github.com/mozilla/geckodriver/releases.

다운받은 파일 압축 풀기

tar -zxvf geckodriver-v0.19.1-linux64.tar.gz

파일 이동 mv geckodriver /usr/local/bin

```
$ geckodriver --version
geckodriver 0.19.1

The source code of this program is available at
https://github.com/mozilla/geckodriver.

This program is subject to the terms of the Mozilla Public License 2.0.
You can obtain a copy of the license at https://mozilla.org/MPL/2.0/.
```

pyenv, virtual env 를 통한 Python 3.6설치

```
pyenv install 3.6.0 # 파이썬 3.6.0 설치
pyenv virtualenv 3.6.0 superlist # 설치한 파이썬 3.6.0을 기반으로 환경 superlist생성
pyenv local superlist 현재 폴더를 superlist환경으로 고정
```

pip install "django<1.12" "selenium<4"
```
Collecting django<1.12
  Downloading Django-1.11.9-py2.py3-none-any.whl (6.9MB)
    100% |████████████████████████████████| 7.0MB 278kB/s 
Collecting selenium<4
  Downloading selenium-3.8.1-py2.py3-none-any.whl (942kB)
    100% |████████████████████████████████| 952kB 1.3MB/s 
Collecting pytz (from django<1.12)
  Using cached pytz-2017.3-py2.py3-none-any.whl
Installing collected packages: pytz, django, selenium
Successfully installed django-1.11.9 pytz-2017.3 selenium-3.8.1

```


