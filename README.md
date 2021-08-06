### 설치 하기

난 Mac에서 작업했기 때문에 기본으로 Python3가 설치 되어 있다. 하지만 가상환경을 만들어야 작업하기 편하기 때문에 pyenv를 설치하고 pyenv-virtualenv까지 설치하여 튜토리얼을 진행하는데 갑자기 pyenv가 제대로 작동하지 않았다. 재설치를 했지만 문제가 해결 되지 않았고 구글 검색을 해보니 나와 같은 오류가 많은것을 확인했다. 해결책도 소용없었다. 그래서 venv를 사용하기로 했다. venv는 python3에 기본으로 설치 되어 있다. venv 사용법은 간단하다.

```bash
# 새 프로젝트 폴더 생성
$ mkdir mysite
$ cd mysite

# 가상환경 생성
$ python -m venv .venv

# 가상환경 실행
$ source .venv/bin/activate

# 가상환경 종료
$ deactivate
```

### Django 설치

가상환경을 실행한 상태에서 아래 명령어를 수행한다.

```bash
# django 설치
$ python -m pip install Django

# 설치 확인
$ python -c "import django; print(django.get_version())"
3.2.6
```

### 프로젝트 생성

위에서 생성한 가상환경이 있는 mysite 폴더에서 프로젝트를 생성하고 서버를 실행시켜본다.

```bash
# 프로젝트 생성
$ django-admin startproject mysite .

# 서버 실행
$ python manage.py runserver
```

### 투표 앱 생성

```bash
$ python manage.py startapp polls
```

### 데이터베이스 세팅

장고에서는 기본으로 sqlite가 사용된다. 다른 데이터베이스를 사용하려면 해당 DBMS의 클라이언트를 설치하고 settings.py의 DATABASES항목을 세팅해준다. 또한 TIME_ZONE을 세팅해준다.

```bash
# mysite/settings.py
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',
    }
}

TIME_ZONE = 'Asia/Seoul'
USE_TZ = False

```

세팅이 끝나면 기본으로 등록된 앱들의 정보를 migrate 한다. 사용하고 싶지 않은 앱이 있다면 INSTALLED_APPS 항목에서 제거하면 된다. migrate 명령을 실행하면 데이터베이스에 스키마가 생성된다.

```bash
$ python manage.py migrate
```

### 투표 모델 생성후 migration

투표 모델 코드를 작성후 INSTALLED_APPS 항목에 등록하고 migrations을 진행한다.

```bash
#mysite/settings.py
INSTALLED_APPS = [
    'polls.apps.PollsConfig',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]

$ python manage.py makemigrations polls
$ python manage.py migrate
```

이제 데이터베이스에 스키마가 생성 되었다. 쉘을 통해서 테스트 할 수 있다.

```bash
$ python manage.py shell

>>> from polls.models import Choice, Question
>>> Question.objects.all()
>>> from django.utils import timezone
>>> q = Question(question_text="What's new?", pub_date=timezone.now())
>>> q.save()
>>> q.question_text = "What's up?"
>>> q.save()
```

### Admin 사용하기

장고 어드민을 사용하기 위해 슈퍼유저를 생성한다.

```bash
$ python manage.py createsuperuser
```

서버 실행후 [http://127.0.0.1:8000/admin/](http://127.0.0.1:8000/admin/) 를 접속하여 어드민 페이지를 사용할 수 있다. 추가한 투표 모델을 사용하려면 admin.py에 등록해야 한다.

```python
# polls/admin.py
from django.contrib import admin
from .models import Question

admin.site.register(Question)
```

### django shell에서 ipython 사용

```bash
$ pip install django-extensions
$ pip install ipython

# settings.py
INSTALLED_APPS = [
'django_extensions',
]

$ python manage.py shell
```

