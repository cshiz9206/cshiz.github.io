---
title: Django API
date: YYYY-MM-DD HH:MM:SS +09:00
categories: [개념정립, Programmers-DevCourse]
tags:
  [
    Backend,
    Programming,
  ]
---

# 특징

- Django 내에서 time을 사용할 때에는 `from django.utils import timezone` 을 사용하는 것이 좋다.

<br/>

# Server 환경설정

1. 설치 : `py -m pip install Django`
2. 프로젝트 생성 : `django-admin startproject [NEW_PROJECT_NAME]`
3. 앱 생성 : `python manage.py startapp [NEW_APP_NAME]`
4. server 실행 : `python manage.py runserver`

<br/>

# API

1. 앱의 API 작성 : [APP_NAME] > views.py에 아래와 같이 작성 <br/>

```python
# views.py

############# Version 1. #############
# from django.http import HttpResponse

# def index(request):
#   pass

# def detail(request, data_id):
#   pass

# def some_url(request):
#     return HttpResponse("Some url을 구현해봤습니다.")

############# Version 2. #############
# from rest_framework.decorators import api_view
# from polls.models import Question
# from polls_api.serializers import QuestionSerializer
# from rest_framework.response import Response
# from rest_framework import status

# @api_view(['GET','POST'])
# def question_list(request):
#     if request.method == 'GET':
#         questions = Question.objects.all()
#         serializer = QuestionSerializer(questions, many = True)
#         return Response(serializer.data)
    
#     if request.method == 'POST':
#         serializer = QuestionSerializer(data=request.data)
#         if serializer.is_valid():
#             serializer.save()
#             return Response(serializer.data, status=status.HTTP_201_CREATED)
#         else:
#             return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

############# Version 3. #############
# from rest_framework.views import APIView

# class QuestionList(APIView):
#     def get(self, request):
#         questions = Question.objects.all()
#         serializer = QuestionSerializer(questions, many=True)
#         return Response(serializer.data)

#     def post(self, request):
#         serializer = QuestionSerializer(data=request.data)
#         if serializer.is_valid():
#             serializer.save()
#             return Response(serializer.data, status=status.HTTP_201_CREATED)
#         return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

# class QuestionDetail(APIView):
#     def get(self, request, id):
#         question = get_object_or_404(Question, pk=id)
#         serializer = QuestionSerializer(question)
#         return Response(serializer.data)

#     def put(self, request, id):
#         question = get_object_or_404(Question, pk=id)
#         serializer = QuestionSerializer(question, data=request.data)
#         if serializer.is_valid():
#             serializer.save()
#             return Response(serializer.data, status=status.HTTP_200_OK)
#         else:    
#             return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
        
#     def delete(self, request, id):
#         question = get_object_or_404(Question, pk=id)
#         question.delete()
#         return Response(status=status.HTTP_204_NO_CONTENT)

############# Version 4. #############
# from polls.models import Question
# from polls_api.serializers import QuestionSerializer
# from rest_framework import mixins
# from rest_framework import generics

# class QuestionList(mixins.ListModelMixin,
#                   mixins.CreateModelMixin,
#                   generics.GenericAPIView):
#     queryset = Question.objects.all()
#     serializer_class = QuestionSerializer

#     def get(self, request, *args, **kwargs):
#         return self.list(request, *args, **kwargs)

#     def post(self, request, *args, **kwargs):
#         return self.create(request, *args, **kwargs)

# class QuestionDetail(mixins.RetrieveModelMixin,
#                     mixins.UpdateModelMixin,
#                     mixins.DestroyModelMixin,
#                     generics.GenericAPIView):
#     queryset = Question.objects.all()
#     serializer_class = QuestionSerializer

#     def get(self, request, *args, **kwargs):
#         return self.retrieve(request, *args, **kwargs)

#     def put(self, request, *args, **kwargs):
#         return self.update(request, *args, **kwargs)

#     def delete(self, request, *args, **kwargs):
#         return self.destroy(request, *args, **kwargs)

############# Version 5. #############
from polls.models import Question
from polls_api.serializers import QuestionSerializer
from rest_framework import generics

class QuestionList(generics.ListCreateAPIView):
    queryset = Question.objects.all()
    serializer_class = QuestionSerializer

class QuestionDetail(generics.RetrieveUpdateDestroyAPIView):
    queryset = Question.objects.all()
    serializer_class = QuestionSerializer
```

2. 앱의 API 등록 : [APP_NAME] > urls.py에 아래와 같이 등록 <br/>

```python
from django.urls import path
from . import views

############# Version 1. #############
# urlpatterns = [
#     path('', views.index, name='index'),
#     path('<[자료형]:[DATA_ID]>/', views.details, name='detail'),
#     path('some_url', views.some_url)
# ]

############# Version 4. #############
from django.urls import path
from .views import *

urlpatterns = [
    path('question/', QuestionList.as_view(), name='question-list'),
    path('question/<int:pk>/', QuestionDetail.as_view(), name='question-detail'),
]
```

3. 프로젝트에 앱의 API 등록 : [PROJECT_NAME] > urls.py에 아래와 같이 등록 <br/>

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('[APP_NAME]/', include('[APP_NAME].urls')),
    path('admin/', admin.site.urls)
]
```

<br/>

## Serialize

**Serialize** : 모델 인스턴스나 QuerySet과 같은 데이터를 JSON 형식의 파일로 변환하는 작업 <br/>
**DeSerialize** : JSON 형식의 데이터를 정의된 포맷에 맞추어 다시 모델 인스턴스로 변환하는 작업 <br/>

### Serializer의 정의

```python
# [APP_NAME]_api/serializers.py

from rest_framework import serializers
from polls.models import Question

class QuestionSerializer(serializers.ModelSerializer):
    class Meta:
        model = Question
        fields = ['id','question_text', 'pub_date']

# 위 코드는 아래와 같은 기능을 자동생성 함.

# class QuestionSerializer(serializers.Serializer):
#     id = serializers.IntegerField(read_only=True)
#     question_text = serializers.CharField(max_length=200)
#     pub_date = serializers.DateTimeField(read_only=True)

#     def create(self, validated_data):
#         return Question.objects.create(**validated_data)
#         # Question.objects.create(key=validated_data.key, value=validated_data.value)

#     def update(self, instance, validated_data):
#         instance.question_text = validated_data.get('question_text', instance.question_text)
#         instance.save()
#         return instance
```

### Field의 활용

```python
# [APP_NAME]/serializers.py

class ChoiceSerializer(serializers.ModelSerializer):
    votes_count = serializers.SerializerMethodField()
    
    class Meta:
        model = Choice
        fields = ['choice_text', 'votes_count']
        
    def get_votes_count(self, obj):
        return obj.vote_set.count()

class QuestionSerializer(serializers.ModelSerializer):
    owner = serializers.ReadOnlyField(source='owner.username')
    choices = ChoiceSerializer(many=True, read_only=True)
    
    class Meta:
        model = Question
        fields = ['id', 'question_text', 'pub_date', 'owner', 'choices']

class UserSerializer(serializers.ModelSerializer):
    #questions = serializers.PrimaryKeyRelatedField(many=True, queryset=Question.objects.all())
    #questions = serializers.StringRelatedField(many=True, read_only=True)
    #questions = serializers.SlugRelatedField(many=True, read_only=True, slug_field='pub_date')
    questions = serializers.HyperlinkedRelatedField(many=True, read_only=True, view_name='question-detail')
    
    class Meta:
        model = User
        fields = ['id', 'username', 'questions']
```

```python
# [APP_NAME]/urls.py

from django.urls import path,include
from .views import *

urlpatterns = [
    path('question/', QuestionList.as_view(), name='question-list'),
    path('question/<int:pk>/', QuestionDetail.as_view(),name='question-detail'),
    path('users/', UserList.as_view(),name='user-list'),
    path('users/<int:pk>/', UserDetail.as_view()),
    path('register/', RegisterUser.as_view()),
    path('api-auth/', include('rest_framework.urls'))
]
```

```python
# [APP_NAME]/models.py

class Choice(models.Model):
    question = models.ForeignKey(Question, related_name='choices', on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)

    def __str__(self):
        return self.choice_text

class Vote(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice = models.ForeignKey(Choice, on_delete=models.CASCADE)
    voter = models.ForeignKey(User, on_delete=models.CASCADE)
    
    class Meta:
        constraints = [
            models.UniqueConstraint(fields=['question', 'voter'], name='unique_voter_for_questions')
        ]
```

### Validation

```python
# [APP_NAME]/serializers.py

from rest_framework.validators import UniqueTogetherValidator

class VoteSerializer(serializers.ModelSerializer):
    def validate(self, attrs):
        if attrs['choice'].question.id != attrs['question'].id:
            raise serializers.ValidationError("Question과 Choice가 조합이 맞지 않습니다.")
        
        return attrs
    
    class Meta:
        model = Vote
        fields = ['id', 'question', 'choice', 'voter']
        validators = [
            UniqueTogetherValidator(
                queryset=Vote.objects.all(),
                fields=['question', 'voter']
            )
        ]
```

```python
# [APP_NAME]/views.py

from rest_framework import status
from rest_framework.response import Response

class VoteList(generics.ListCreateAPIView):
    serializer_class = VoteSerializer
    permission_classes = [permissions.IsAuthenticated]
    
    def get_queryset(self, *args, **kwargs):
        return Vote.objects.filter(voter=self.request.user)
    
    def create(self, request, *args, **kwargs):
        new_data = request.data.copy()
        new_data['voter'] = request.user.id
        serializer = self.get_serializer(data=new_data)
        serializer.is_valid(raise_exception=True)
        self.perform_create(serializer)
        headers = self.get_success_headers(serializer.data)
        return Response(serializer.data, status=status.HTTP_201_CREATED, headers=headers)
    
class VoteDetail(generics.RetrieveUpdateDestroyAPIView):
    queryset = Vote.objects.all()
    serializer_class = VoteSerializer
    permission_classes = [permissions.IsAuthenticated, IsVoter]
    
    def perform_update(self, serializer):
        serializer.save(voter=self.request.user)
```

### Serializer의 사용 on Shell

```shell
> from polls.models import Question
> from polls_api.serializers import QuestionSerializer
> q = Question.objects.first()
> serializer = QuestionSerializer(q, many=False)
> serializer.data

> from rest_framework.renderers import JSONRenderer
> json_str = JSONRenderer().render(serializer.data)
> json_str

> import json
> data = json.loads(json_str)
> serializer = QuestionSerializer(data=data)
> serializer.is_valid() # 유효성 검사(Mapping 가능한지 스키마 검사)
> serializer.validated_data
> new_question = serializer.save()
> new_question
```


<br/>
<br/>
<br/>

<hr/>

**ref.**<br/>
- Programmers DevCourse 3기

<hr/>