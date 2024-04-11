---
title: Django User
date: YYYY-MM-DD HH:MM:SS +09:00
categories: [개념정립, Programmers-DevCourse]
tags:
  [
    Backend,
    Programming,
  ]
---

## User와 Model의 연결

- User 또한 Model로 관리되므로 두 Model의 연결이라고 볼 수 있다.

**[APP_NAME]/model.py**

```python
class Question(models.Model):
    question_text = models.CharField(max_length=200, verbose_name='질문')
    pub_date = models.DateTimeField(auto_now_add=True, verbose_name='생성일')  
    owner = models.ForeignKey('auth.User', related_name='questions', on_delete=models.CASCADE, null=True)
                              
    @admin.display(boolean=True, description='최근생성(하루기준)')
    def was_published_recently(self):
        return self.pub_date >= timezone.now() - datetime.timedelta(days=1)
    
    def __str__(self):
        return f'제목: {self.question_text}, 날짜: {self.pub_date}'
    ...
```

**Django Shell**

- django.contrib.auth : [PROJECT_NAME]/settings.py의 INSTALLED_APPS에 위치

```shell
> from django.contrib.auth.models import User
> User
<class 'django.contrib.auth.models.User'>
> User._meta.get_fields()
> User.objects.all()
<QuerySet [<User: admin>]>

> from polls.models import * 
> user = User.objects.first()
> user.questions.all() 
> print(user.questions.all().query)
SELECT "polls_question"."id", "polls_question"."question_text", "polls_question"."pub_date", "polls_question"."owner_id" FROM "polls_question" WHERE "polls_question"."owner_id" = 1
```

<br/>

## User API

- Model에 대한 API 등록 절차와 동일

1. Serializer 등록

```python
# [APP_NAME]/serializers.py

from django.contrib.auth.models import User

class UserSerializer(serializers.ModelSerializer):
    # 예외적으로 명시를 해주는 이유 : User와 Questions의 관계는 one to many이므로 query 시 Questions에서 작업이 이루어지게 된다.
    questions = serializers.PrimaryKeyRelatedField(many=True, queryset=Question.objects.all())
    
    class Meta:
        model = User
        fields = ['id', 'username', 'questions']
```

2. View 등록

```python
# [APP_NAME]/views.py

from django.contrib.auth.models import User
from polls_api.serializers import UserSerializer

class UserList(generics.ListAPIView):
    queryset = User.objects.all()
    serializer_class = UserSerializer
    
class UserDetail(generics.RetrieveAPIView):
    queryset = User.objects.all()
    serializer_class = UserSerializer
```

3. url 등록

```python
# [APP_NAME]/urls.py

from django.urls import path
from .views import *

urlpatterns = [
    path('question/', QuestionList.as_view(), name='question-list'),
    path('question/<int:pk>/', QuestionDetail.as_view()),
    path('users/', UserList.as_view(),name='user-list'),
    path('users/<int:pk>/', UserDetail.as_view()),

]
```

<br/>

## 새로운 User 등록(Sign up)

### Form을 이용하여 구현하는 방법

1. View 등록

```python
# [APP_NAME]/views.py

from django.views import generic
from django.urls import reverse_lazy
from django.contrib.auth.forms import UserCreationForm

class SignupView(generic.CreateView):
    form_class = UserCreationForm
    success_url = reverse_lazy('user-list') # form action 속성의 {% url %}과 같은 역할을 함.
    template_name = 'registration/signup.html'
```
- UserCreationForm : 회원가입 폼 자동 생성
  + username, password, password 확인 필드 제공
- reverse_lazy의 url 확인(on Django shell)
  ```shell
  > from django.urls import reverse_lazy
  > reverse_lazy('user-list')
  '/rest/users/'
  ```

2. Template 생성

```html
<!--[APP_NAME]/templates/registration/signup.html>-->

<h2>회원가입</h2>
 <form method="post">
   {% csrf_token %}
   {{ form.as_p }} <!--UserCreationForm 렌더링 결과 출력-->
   <button type="submit">가입하기</button>
</form>
```

3. url 등록

```python
# [APP_NAME]/urls.py

from django.urls import path
from . import views
from .views import *

app_name = 'polls'
urlpatterns = [
    path('',views.index, name='index'),
    path('<int:question_id>/', views.detail, name='detail'),
    path('<int:question_id>/vote/', views.vote, name='vote'), 
    path('<int:question_id>/result/', views.result, name='result'), 
    path('signup/', SignupView.as_view(), )
]
```

### Serializer를 이용하여 구현하는 방법

1. Serializer 등록

```python
# [APP_NAME]/serializers.py

class RegisterSerializer(serializers.ModelSerializer):
    password = serializers.CharField(write_only=True, required=True, validators=[validate_password])
    password2 = serializers.CharField(write_only=True, required=True)
    
    def validate(self, attrs):
        if attrs['password'] != attrs['password2']:
            raise serializers.ValidationError({"password": "두 패스워드가 일치하지 않습니다."})
        return attrs
    
    def create(self, validated_data):
        user = User.objects.create(username=validated_data['username'])
        user.set_password(validated_data['password'])
        user.save()
        
        return user
    
    class Meta:
        model = User
        fields = ['username', 'password','password2']
```

2. View 등록

```python
# [APP_NAME]/views.py

from polls_api.serializers import RegisterSerializer

class RegisterUser(generics.CreateAPIView):
    serializer_class = RegisterSerializer
```

3. url 등록

```python
# [APP_NAME]/urls.py

from django.urls import path
from .views import *

urlpatterns = [
    path('question/', QuestionList.as_view(), name='question-list'),
    path('question/<int:pk>/', QuestionDetail.as_view()),
    path('users/', UserList.as_view(),name='user-list'),
    path('users/<int:pk>/', UserDetail.as_view()),
    path('register/', RegisterUser.as_view()),
]
```

<br/>

## 권한 관리

### Sign in 버튼 생성

1. url 등록

```python
# [APP_NAME]/urls.py

from django.urls import path,include
from .views import *

urlpatterns = [
    # 기존 urls
    path('question/', QuestionList.as_view(), name='question-list'),
    path('question/<int:pk>/', QuestionDetail.as_view()),
    path('users/', UserList.as_view(),name='user-list'),
    path('users/<int:pk>/', UserDetail.as_view()),
    path('register/', RegisterUser.as_view()),

    # sign in을 위한 url
    path('api-auth/', include('rest_framework.urls'))
]
```

2. redirect url 등록

```python
# [PROJECT_NAME]/settings.py

from django.urls import reverse_lazy
LOGIN_REDIRECT_URL = reverse_lazy('question-list')
LOGOUT_REDIRECT_URL = reverse_lazy('question-list')
```

### 권한 제한

1. Model에 생성자(User) 등록 기능 추가

```python
# [APP_NAME]/serializers.py

class QuestionSerializer(serializers.ModelSerializer):
    owner = serializers.ReadOnlyField(source='owner.username')

    class Meta:
        model = Question
        fields = ['id', 'question_text', 'pub_date', 'owner']
```

2. 권한 생성

```python
# [APP_NAME]/permissions.py

from rest_framework import permissions

class IsOwnerOrReadOnly(permissions.BasePermission):
    def has_object_permission(self, request, view, obj):
        if request.method in permissions.SAFE_METHODS:
            return True
        
        return obj.owner == request.user
```

3. View에 권한 추가

```python
# [APP_NAME]/views.py

from rest_framework import generics,permissions
from .permissions import IsOwnerOrReadOnly

class QuestionList(generics.ListCreateAPIView):
    queryset = Question.objects.all()
    serializer_class = QuestionSerializer
    permission_classes = [permissions.IsAuthenticatedOrReadOnly]
    
    def perform_create(self, serializer):
        serializer.save(owner=self.request.user)

class QuestionDetail(generics.RetrieveUpdateDestroyAPIView):
    queryset = Question.objects.all()
    serializer_class = QuestionSerializer
    permission_classes = [permissions.IsAuthenticatedOrReadOnly, IsOwnerOrReadOnly]
```
- save method는 read only로 지정된 field에 대해서도 변경을 수행할 수 있는 method이다.

<br/>

## API Headers of Sign-in State

```json
- Content-Type: application/json
- Cookie: sessionid=
- X-CSRFToken:
- Cookie: csrftoken=
```


<br/>
<br/>
<br/>

<hr/>

**ref.**<br/>
- Programmers DevCourse 3기

<hr/>