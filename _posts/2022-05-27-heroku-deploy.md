---
layout: single
title: "deplying Python application on Heroku"
categories: tech
tags: [heroku, python]
toc: true
author_profile: false
sidebar:
  nav: "docs"
header:
  overlay_image: "https://miro.medium.com/max/1400/1*IdXgBSagHPsp_3E8JwMT4Q.png"
  overlay_filter: 0.5
---

애플리케이션을 손쉽게 배포 할 수 있는 플랫폼 중, Heroku 라고 하는 관리형 서비스가있다.

## 1. Create new app in Heroku

- Access to this page https://dashboard.heroku.com/apps
- click the **[Create New App]**
  ![image](/screenshots/In-heroku-pic1.jpg)
- enter application name and click **[create app]**

**_만약 헤로쿠 커맨드라인 툴이 구성되어있다면 바로 2번 스텝으로 넘어가셔도 좋습니다. 하지만 CLI가 구성되어있지 않으면 아래 절차를 따라서 설치 해 주세요_**

```bash
]$ brew tap heroku/brew & brew install heroku
```

If install has done, login to heroku

```bash
]$ heroku login
```

![image](/screenshots/In-heroku-pic2.jpg)

## 2. Create Django app

- In your local, Create new django application

```bash
]$ django-admin startproject mysite
]$ ls -al
drwxr-xr-x  5 sookyung.kim  staff  160 Oct 26 13:49 .
drwxr-xr-x  8 sookyung.kim  staff  256 Oct 26 13:00 ..
-rw-r--r--  1 sookyung.kim  staff  483 Oct 26 13:39 Agent_integration.md
drwxr-xr-x  5 sookyung.kim  staff  160 Oct 26 13:54 mysite
```

- move to application directory and start up your application

```bash
]$ cd mysite
]$ python3 manage.py runserver
```

- If your application started up well, when you access to localhost webpage, you can see the rocket is flying
  ![image](/screenshots/In-heroku-pic3.jpg)

## 3. Django project 수정하기

- In the project directory (same level with manage.py), make "Procfile" with content like this :

```bash
]$ vi Procfile
```

```bash
web: gunicorn mysite.wsgi --log-file -
```

위에 내용에서 mysite.wsgi 부분을 자신의 프로젝트 이름에 맞게 수정합니다.([project name].wsgi) 우리는 gunicorn이라는 파이썬 HTTP 서버(Python WSGI HTTP Server)를 사용하여 웹 서비스할 것임을 헤로쿠(Heroku)에게 알려주었습니다.

- 아래 명령어로 필요한 모듈을 설치합니다

```bash
]$ pip install gunicorn whitenoise django-heroku
]$ pip install psycopg2
```

- 설치가 완료되면 사용할 모듈을 정의하기 위해 requirements.txt를 갱신 해 줍니다

```bash
]$ pip freeze > requirements.txt
```

- mysite/settings.py 파일을 아래와 같이 수정합니다

```bash
...
import django_heroku
...

DEBUG = True

ALLOWED_HOSTS = ['*']
...
MIDDLEWARE = [
    'whitenoise.middleware.WhiteNoiseMiddleware',
    ...
]
...
# Activate Django-Heroku.
django_heroku.settings(locals())
```

![image4](/screenshots/In-heroku-pic4.jpg)
![image5](/screenshots/In-heroku-pic5.jpg)

- 지금까지 수행한 내역을 github에 저장합니다.
  헤로쿠의 배포방식은 github에 커밋된 내용을 heroku에서 가져와서 배포하는 방식이므로, git 저장소가 지정되어있지 않다면 init 명령어를 통해 새로운 레포지토리를 지정 해 주고 커밋합니다

```bash
]$ git add .
]$ git commit -m 'django for heroku'
]$ git push origin master
```

## 4. Heroku에 배포하기

- 아래 명령어를 통해 헤로쿠 애플리케이션을 연결 해 줍니다.

```bash
]$ heroku git:remote -a sookim-django-test
```

**_"-a" 다음에 들어 갈 옵션값은 실제 1번 단계에서 헤로쿠 웹페이지에서 생성했던 앱의 이름을 지정 해주어야 성공적으로 작업이 마무리 됩니다._**

- 아래처럼 제대로 레포지토리가 지정되어있는지 확인합니다.

```bash
]$ git remote -v
```

![image6](/screenshots/In-heroku-pic6.jpg)

- 아래 명령어로 애플리케이션을 배포합니다

```bash
]$ git push heroku master
```

- 데이터베이스를 새로 구축하고 관리자 계정을 만들어 줍니다.

```bash
]$ heroku run python manage.py migrate
]$ heroku run python manage.py createsuperuser
```

## 5. 배포 확인

```bash
]$ heroku open
```

![image7](/screenshots/In-heroku-pic7.jpg)

https://dev-yakuza.posstree.com/ko/django/heroku/
