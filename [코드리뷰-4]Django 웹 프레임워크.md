# Django 웹 프레임워크

### 일반적인 특징

장고는 현재 가장 많이 사용되는 파이썬 웹 프레임워크입니다.
2003년 로렌스 저널-월드 신문을 만들던 웹 개발팀의 내부 프로젝트로 시작됐으며, 2005년 오픈소스 프로젝트로 공개됐고, 구글의 앱 엔진에서 장고를 사용하면서 많은 사람들이 사용하게 되었습니다.

장고는 기본적으로 MVC 패턴에 해당하는 MVT 패턴에 따라 개발하도록 설계되어 있습니다. 따라서 MVT 패턴에 따라 설명을 진행하고 동시에 예제를 통해 실습하도록 하겠습니다.

**MVC 패턴 기반 MTV**
장고는 Model, View, Controller 대신에 Model, View, Template를 가지고 있습니다.
MVC에서 View는 장고에서 Template이고, MVC에서 Controller는 장고에서 View입니다.

**객체 관계 매핑**
장고의 객체 관계 매핑(ORM)은 데이터베이스 시스템과 데이터 모델 클래스를 연결시키는 다리와 같은 역할을 합니다. 이런 ORM 기능 덕분에 데이터베이스를 조작하기 쉬워졌습니다.

**자동으로 구성되는 관리자 화면**
장고는 웹 서버의 콘텐츠, 즉 데이터베이스에 대한 관리 기능을 위하여 프로젝트를 시작하는 시점에 기본 기능으로 관리자 화면을 제공합니다. 그러므로 개발자가 별도로 관리 기능을 개발할 필요도 없습니다.

**우아한 URL 설계**
장고에서는 우아한 URL 방식을 채택하여 직관적이고 쉽게 표현할 수 있습니다. 또한, 각 URL 형태를 파이썬 함수에 1:1로 연결하도록 되어있어 개발이 편리하며, 이해하기도 쉽습니다.

**자체 템플릿 시스템**
화면 디자인과 로직에 대한 코딩을 분리하여 독립적으로 개발 진행이 가능합니다. 장고의 템플릿 시스템은 HTML과 같은 텍스트형 언어를 쉽게 다룰 수 있도록 개발되었습니다.

**캐시 시스템**
동적인 페이지를 만들기 위한 일은 서버에 엄청난 부하를 주는 작업입니다. 그래서 캐시 시스템을 사용하여 자주 이용되는 내용을 저장해 두었다가 재사용하면 성능을 높일 수 있습니다.
장고의 캐시 시스템은 캐시용 메모리를 메모리, 데이터베이스 내부, 파일 시스템 중 아무 곳에나 저장할 수 있습니다. 또한 캐시 단위를 페이지에서부터 사이트 전체 또는 특정 뷰의 결과, 템플릿의 일부 영역만을 지정하여 저장해 둘 수도 있습니다.

**다국어 지원**
장고는 동일한 소스코드를 다른 나라에서도 사용할 수 있도록 텍스트의 번역, 날짜/시간/숫자의 포맷, 타임존의 지정 등과 같은 다국어 환경을 제공합니다.

**풍부한 개발 환경**
장고는 개발에 도움이 될 수 있는 여러 가지 기능을 제공합니다. 대표적으로 테스트용 웹 서버를 포함하고 있어서 개발 과정에서 아파치 등의 웹 서버가 없어도 테스트를 진행할 수 있습니다.

**소스 변경사항 자동 반영**
장고에서는 \*.py 파일의 변경 여부를 감시하고 있다가 변경이 되면 실행 파일에 변경 내역을 바로 반영해줍니다. 그래서 장고 테스트용 웹 서버를 실행 중인 상태에서 소스 파일을 수정하더라도 웹 서버를 다시 시작할 필요 없이 자동으로 새로운 파일이 반영됩니다.

---

### 장고 프로그램 설치
windows
cmd 창에서 `pip install Django`

linux
터미널 창에서 `sudo pip install Django`
업그레이드는 `sudo pip install Django --upgrade`

pip 프로그램이 설치되어 있지 않다면, 수동으로 pip를 설치한다.
`cd /usr/local/src`에다가 `get-pip.py`파일을 옮겨 놓는다.
`get-pip.py`는 `curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py`로 가져올 수 잇다.
`python get-pip.py`를 입력하면 pip가 설치된다.

### 장고 프로그램 삭제

```
cd /usr/lib/python3.x/site-packages/
rm -rf django
rm -rf Django*
```
장고가 설치된 디렉토리를 알고 싶다면, `python -c "import django; print(django.__path)"`

---

###  MTV 코딩 순서

  **모델(Model)**은 데이터베이스에 저장되는 데이터를 의미하는 것이고,
  **템플릿(Template)**은 사용자에게 보여지는 UI 부분을 의미하는 것이고,
  **뷰(View)**는 실질적으로 프로그램 로직이 동작하여 데이터를 가져오고 적절하게 처리한 결과를 템플릿에 전달하는 역할을 수행합니다.

![img](https://t1.daumcdn.net/cfile/tistory/991AD1365B448DA702)

- 클라이언트로부터 요청을 받으면 `URLconf`를 이용하여 URL을 분석합니다.

- URL 분석 결과를 통해 해당 URL에 대한 처리를 담당할 `뷰`를 결정합니다.

- 뷰는 자신의 로직을 실행하면서, 만일 데이터베이스 처리가 필요하면 `모델`을 통해 처리하고 그 결과를 반환 받습니다.

- 뷰는 자신의 로직 처리가 끝나면 `템플릿`을 사용하여 클라이언트에 전송할 HTML 파일을 생성합니다.

- 뷰는 최종 결과로 HTML 파일을 클라이언트에게 보내 응답합니다.

  모델, 뷰, 템플릿 셋 중에서 무엇을 먼저 코딩해야 하는지에 대해 정해진 순서는 없습니다. MVT 방식에 따르면 화면 설계는 뷰와 템플릿 코딩으로 연결되고, 테이블 설계는 모델 코딩에 반영됩니다. 그렇기 때문에 독립적으로 개발할 수 있는 모델을 먼저 코딩하고, 뷰와 템플릿은 서로 영향을 미치므로 모델 이후에 같이 코딩하는 것이 일반적입니다.
  뷰와 템플릿의 코딩 순서도 굳이 정할 필요는 없지만, 필자는 UI 화면을 생각하면서 로직을 풀어나가는 것이 쉽기 때문에 보통은 템플릿을 먼저 코딩합니다. 다만 클래스형 뷰(CBV)처럼 뷰의 코딩이 매우 간단한 경우에는 뷰를 먼저 코딩하고, 그 다음 템플릿을 코딩합니다.
  이 책의 3장에서는 함수형 뷰를 사용하므로 모델, 템플릿, 뷰 순서로 코딩을 진행하고, 5장에서는 클래스형 뷰를 사용하므로 모델, 뷰, 템플릿 순서로 진행합니다. 그 외에도 프로젝트 설정 파일 및 URLConf 파일까지 포함하여, 3장인 경우는 다음 순서로 설명하겠습니다.


- 프로젝트 뼈대 만들기 : 프로젝트 및 앱 개발에 필요한 디럭토리와 파일 생성

- 모델 코딩하기 : 테이블 관련 사항을 개발(models.py, admin.py 파일)

- URLconf 코딩하기 : URL 및 뷰 매핑 관계를 정의(urls.py 파일)

- 템플릿 코딩하기 : 화면 UI 개발 (templates/ 디렉토리 하위의 \*.html 파일들)

- 뷰 코딩하기 : 애플리케이션 로직 개발 (views.py 파일)

이런 코딩 순서는 참고용이고, 물론 본인이 편한 순서대로 코딩하면 됩니다.

---

### 애플리케이션 설계하기

장고 프레임워크에서 프로젝트란 개발 대상이 되는 전체 프로그램을 의미합니다.
애플리케이션은 프로젝트를 몇 개의 기능 그룹으로 나누었을 때, 프로젝트 하위의 서브 프로그램을 의미합니다.
즉, 서브 프로그램인 애플리케이션을 개발하고, 이들을 모아서 프로젝트 개발을 완성하게 됩니다.

1. 애플리케이션의 로직 설계
**개발요구사항 정리** : 설문에 해당하는 질문을 보여준 후 질문에 포함되어 있는 답변 항목에 투표하면 그 결과를 알려주는 예제

**UI(User Interface) 설계**

![img](https://blogfiles.pstatic.net/MjAxOTAyMDhfMjQ5/MDAxNTQ5NjExMjIyMTM1.RoEqMKA40cC0UHDd0eDpqC0FgpA1rAFrAoMCOcvS5Isg.ZdbM7XsNGpZEG_puJQ64yl1w2ClLWA0aJe1QmGdrpfYg.JPEG.jjscan/2.jpg)

- **index.html** : 최근에 실시하고 있는 질문의 리스트를 보여줍니다.
- **detail.html** : 하나의 질문에 대해 투표할 수 있도록 답변 항목을 폼으로 보여줍니다.
- **results.html** : 질문에 따른 투표 결과를 보여줍니다.

다음은 요구사항에 따라 필요한 테이블을 추출하여 설계한 예시입니다.

Question 테이블 설계
| 컬럼명        | 타입         | 제약 조건                  | 설명           |
| ------------- | ------------ | -------------------------- | -------------- |
| id            | integer      | NotNull, PK, AutoIncrement | Primary Key    |
| question_text | varchar(200) | NotNull                    | 질문 문장      |
| pub_date      | datetime     | NotNull                    | 질문 생성 시각 |

Choice 테이블 설계
| 컬럼명        | 타입         | 제약 조건                  | 설명           |
| ---- | ---- | ---- | ---- |
| id | integer | NotNull, PK, AutoIncrement | Primary Key |
| choice_text | varchar(200) | NotNull | 답변 항목 문구 |
| votes | integer | NotNull | 투표 |
| question | integer | NotNull, FK (Question.id), Index | Foreign Key |

- Question 테이블 : 질문을 저장하는 테이블입니다.
- Choice 테이블 : 질문별로 선택용 답변 항목을 저장하는 테이블입니다.
- 모든 컬럼은 NotNull로 정의해서, 반드시 컬럼에 값이 있어야 합니다.
- Primary Key는 자동 증가 속성으로 지정했습니다.
- Choice 테이블의 question 컬럼은 Question 테이블과 Foreign Key 관계로 연결되도록 했고, 또한 Index를 생성하도록 하였습니다.

---

### 프로젝트 뼈대 만들기

코딩은 프로젝트 뼈대를 만드는 것에서부터 시작합니다. 즉 프로젝트에 필요한 디렉토리 및 파일을 구성하고, 설정 파일을 셋팅합니다. 그 외에도 기본 테이블을 생성하고, 관리자 계정인 슈퍼 유저를 생성하는 것이 필요합니다. 프로젝트가 만들어지면 그 하위에 애플리케이션 디렉토리 및 파일을 구성합니다. 장고는 이런 작업을 위한 **장고 쉘 커맨드**를 제공합니다.
![img](https://blogfiles.pstatic.net/MjAxOTAyMDhfMjIg/MDAxNTQ5NjExMTI1Mzk0.yaq_2N-MLqQ-QgStc0UZd0_x86KAZVBkk7bFkhr8S70g.-uLlaFmAwsK3kV0bqBP-85RX1_eOlHAQUw8OfG1O_TIg.JPEG.jjscan/1.JPG)
이외에도 프로젝트가 완성된 후에는 templates, static, logs 등의 디렉토리가 더 필요합니다. 또한 개발자가 필요하다고 판단되면 프로젝트 개발을 진행하면서 임의로 추가해도 무방합니다.

![img](https://blogfiles.pstatic.net/MjAxOTAyMDhfMTc4/MDAxNTQ5NjExNzI5OTM2.sml-BnHB5n-rreeEXlI4JcPbmZt186BYR65a1fL7Ah4g.ahrp_0epkV3av7E5z5aU3YV5SWhof4Xo0MsweD4JkSAg.JPEG.jjscan/3.JPG)

프로젝트 뼈대를 생성하기 위해서 아래와 같은 순서로 명령을 실행합니다.
```
django-admin startproject mysite // mystie라는 프로젝트를 생성함
python manage.py startapp polls // polls라는 애플리케이션을 생성함
notepad settings.py // 설정 파일을 확인 및 수정함
python manage.py migrate // 데이터베이스에 기본 테이블을 생성함
python manage.py runserver // 현재까지 작업을 개발용 웹 서버로 확인함
```

---

### 프로젝트 생성

mysite라는 프로젝트 생성 : `django-admin startproject mysite`


### 애플리케이션 생성

polls라는 애플리케이션을 만드는 명령 : `python manage.py startapp polls`

### 프로젝트 설정 파일 변경

프로젝트에 필요한 설정값들은 `settings.py` 파일에 저장합니다.
`notepad settings.py`
1. `ALLOWED_HOSTS = ['192.168.xxx.xxx', 'localhost', '127.0.0.1']`
2. 프로젝트에 포함되는 애플리케이션들은 모두 설정 파일에 등록되어야 합니다. 따라서 우리가 개발하고 있는 polls 애플리케이션도 등록해야 합니다. polls 앱의 설정 클래스는 startapp polls 명령 시에 자동 생성된 apps.py 파일에 PollsConfig 라고 정의되어 있습니다.

```
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'polls.apps.PollsConfig',
]
```

3. 프로젝트에 사용할 데이터베이스 엔진입니다. 장고는 기본으로 SQLite3 데이터베이스 엔진을 사용하도록 설정되어 있습니다. 다른 데이터베이스로 변경하고 싶다면 settings.py 파일에서 수정해주면 됩니다. 우리 예제에서는 SQLite3 데이터베이스를 사용할 것이므로, 설정 사항을 변경하지 않고 확인만 합니다.
```
# Database
# https://docs.djangoproject.com/en/2.0/ref/settings/#databases

DATABASES = [
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME':os.path.join(BASE_DIR, 'db.sqlite3'),
    }
]
```

4. 최초에는 세계표준시(UTC)로 되어있는 타임존을 한국 시간으로 변경합니다.
```
# TIME_ZONE = 'UTC'
TIME_ZONE = 'Asia/Seoul'
```
만일 `USE_TZ=True` 라고 설정하면 장고가 알아서 시간대를 조정해줍니다. 이렇게 사용하면, DB에는 UTC 시간으로 저장하고, UI에서 입력받는 폼처리 및 UI에 출력하는 템플릿 처리시에는 TIME_ZONE 항목에 설정한 시간대를 반영하여 처리하는 방식입니다.

### 기본 테이블 생성

`python manage.py migrate`
장고는 모든 웹 프로젝트 개발 시 반드시 사용자와 그룹 테이블 등이 필요하다는 가정 하에 설계됐습니다. 그래서 우리가 테이블을 전혀 만들지 않았더라도, 사용자 및 그룹 테이블 등을 만들어주기 위해서 프로젝트 개발 시작 시점에 이 명령을 실행하는 것입니다. 명령을 실행하면 db.sqlite3 파일이 생성된 것을 확인할 수 있습니다.

### 지금까지 작업 확인하기

장고에서는 개발 과정 도중에 현재 상태를 확인해 볼 수 있도록 `runserver`라고 하는 간단한 테스트용 웹 서버를 제공해줍니다. `python manage.py runserver 0.0.0:8000`

서버가 정상적으로 동작한다면, 웹 브라우저를 열고 주소창에 서버IP를 적어줍니다. (127.0.0.1:8000)

