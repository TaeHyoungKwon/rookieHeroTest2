# Two scoops of django



## 들어가기전에



### 핵심 개념

* #### 단순 명료하게 하라(Keep it Simple, Stupid)

* #### **모델은 크게**, **유틸리티는 모듈**로, **뷰는 가볍게**, **템플릿은 단순**하게

  * 뷰와 템플릿을 제외한 다른 부분에 더 많은 로직을 넣는 방법을 추천
  * 코드는 좀 더 깔끔해지고, 문서화도 잘 될 것이고, 중복되는 부분이 줄어들고,  재사용 측면에서 유용하다.

* #### 템플릿 태그와 필터는 가능한 최소의 로직을 포함하고 있어야 한다.

* #### 시작은 장고 기본 환경으로부터

  * '장고 코어 모듈을 교체할 때 주의점' 참고

* #### 장고의 디자인 철학을 이해하도록 한다.

* #### 12팩터 앱

  * 확장 가능하고 배포 가능한 앱을 만드는 방법론



## 1장 코딩 스타일

### 1.1 읽기 쉬운 코드를 만드는 것이 왜 중요한가?

* 축약적이거나 함축적인 변수명은 피한다.
* 함수 인자의 이름들은 꼭 써준다.
* 클래스와 메서드를 문서화 한다.
* 코드에 주석은 꼭 달도록 한다.
* **재사용 가능한 함수 또는 메서드 안에서 반복되는 코드들은 리팩토링을 해둔다.**
* **함수와 메서드는 가능한 한 작은 크기를 유지한다. 어림잡아 스크롤 없이 읽을 수 있는 길이가 적합하다.**



### 1.2 PEP 8

* 파이썬 공식 스타일 가이드
  * [PEP 8 코딩 관례](http://www.python.org/dev/peps/pep-0008)
    * 들여쓰기에는 스페이스를 네 칸 이용
    * 최상위 함수와 클래스 선언 사이를 구분 짓기 위해 두 줄을 띄운다.
    * 클래스 안에서 메서드들을 나구기 위해 한줄을 띄운다.
  * 코드 품질을 위해서 `flake8` 을 이용하자.
    * 코딩 스타일과 코드 품질, 논리적 에러를 점건하는데 매우 유용한 도구

#### 1.2.1 79 칼럼의 제약

* 한 줄 당 79글자를 넘어서는 안된다.



### 1.3 임포트에 대해

* 우선순위(PEP8 기준)

  1. 표준 라이브러리 임포트
  2. 연관 외부 라이브러리 임포트
  3. 로컬 애플리케이션 또는 라이브러리에 한정된 임포트

  ```python
  #표준 라이브러리 임포트
  from math import sqrt
  from os.path import abspath
  
  #코어 장고 임포트
  from djangodb import models
  from django.utilstranslation import ugettext_lazy as _
  
  # 서드 파티 앱 임포트
  from django_extensions.db.models import TimeStampedModel
  
  # 프로젝트 앱 임포트
  from splits.models import BananaSplit
  ```



* 우선순위(Django)
  1. 표준 라이브러리 임포트
  2. 코어 장고 임포트
  3. 장고와 무관한 외부 앱 임포트
  4. 프로젝트 앱



### 1.4 명시적 성격의 상대 임포트 이용하기

* 코드를 이동시키거나 이름을 변경하거나 버젼을 나누는 등의 재구성을 손쉽게 할 수 있도록 구성하는 것은 매우 중요하다.

* 명시적 성격의 상대 임포트를 통해 모듈의 패키지를 하드 코딩하거나 구조적으로 종속된 모듈을 어렵게 분리해야하는 경우들을 피해갈 수 있다.

* 하드 코딩된 임포트 문을 이용했을 때, 단지 앱의 이름을 바꿈으로써 모든 것이 해결되지는 않는다

* 왜 하드 코딩된 임포트문을 쓰면 안되는가?

  1. 앱의 이름을 바꾸어야 할 상황 이라서 수정이 필요할 떄, 임포트 문을 일일이 수정하기 힘들다.

* 요약

  | 코드                               | 임포트 타입     | 용도                                                         |
  | ---------------------------------- | --------------- | ------------------------------------------------------------ |
  | `from core.views import FoodMixin` | 절대 임포트     | 외부에서 임포트해서 현재 앱에서 이용할 떄                    |
  | `from .models import WaffleCone`   | **명시적 상대** | 다른 모듈에서 임포트해서 현재 앱에서 이용할 때               |
  | `from models import WaffleCone`    | 암묵적 상대     | 다른 모듈에서 임포트해서 현재 앱에서 이용할 때 쓰지만 **좋은 방법은 아니다.** |

  * 명시적 성격의 상대 임포트를 이용하는 습관을 길러두자.



### 1.5 import * 은 피하자



나쁜예제

```python
from django.forms import *
from django.db.models import *
```

* 다른 파이런 모듈의 이름공간들이 현재 우리가 작업하는 모듈의 이름 공간에 추가로 로딩되거나 기존 것 위에 덮여 로딩되는 일을 막기 위해서

* 예를들면,

  ```python
  #from django.db import models
  from django.db import *
  
  #의도치 않게 이러한 함수를 만들었다고 가정했을 때
  def models():
    print("django.db 에 있는 models와 해당 함수와 이름이 겹칠 수 있다.")
   
  ```

  ```python
  #forms, db.models 에 모두 CharField가 존재
  from django.forms import CharField
  from django.db.models import CharField
  
  #아래와 같이 * 을 했을 경우 위를 구분할 방법이 없다.
  from django.forms import *
  from django.db.models import *
  
  # 해결책으론 이렇게 써서, 구분해준다.
  from django import forms #forms.CharField
  from django.db import models #models.CharField
  
  ```



### 1.6 장고 코딩 스타일

* 내부적으로 PEP 8을 확장한 장고만의 스타일 가이드가 있다.
* [장고 코딩 스타일 가이드](<https://docs.djangoproject.com/en/2.0/internals/contributing/writing-code/coding-style/>)



#### 1.6.2 URL 패턴의 이름(name)에는 (-) 대신 (_)을 이용한다.

```python
patterns = [
  path('', views.home, name="example_home"), # O
  path('', views.home, name="example-home"), # X
  ...
]
```

* 주소 URL에 대시를 쓰는 것은 문제 없다.

  ex) https://www.example-home.com



#### 1.6.3 템플릿 블록 이름에 (-) 대신 (_)을 이용

* 위와 같은 이유로 템플릿 블록을 정의하는 이름을 만들 때도 밑줄을 쓰자
* `템플릿 블록`이라는 공식 표현이 존재하진 않고, 검색을 해봐도 명확히 정의 내린 곳을 찾지 못하였다
* 그래서  `템플릿 태그` ,  `render` 에서 인자로 넘어오는 `template_name` 등에  위와같은 규칙이 적용된다고 이해하였다.
  * Ex) `{% block main_content %}` , `{% load mptt_tags %}` 
  * Ex) `my_home.html`



### 1.7 자바스크립트 HTML CSS 스타일 선택하기

#### 생략



### 1.8 통합개발환경이나 텍스트 편집기에 종속되는 스타일의 코딩은 지양한다.

* 나와 같은 도구를 이용해서 개발하지 않는다는 것을 고려해야한다.





### 1.9 요약

* 일관된 코딩 스타일을 정한 후 일관성 있게 따라야 한다.



* #### 