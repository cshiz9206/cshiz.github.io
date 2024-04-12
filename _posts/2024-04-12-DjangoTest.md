---
title: Django Test
date: YYYY-MM-DD HH:MM:SS +09:00
categories: [개념정립, Programmers-DevCourse]
tags:
  [
    Backend,
    Programming,
  ]
---

## 작성 예시

1. 테스트 케이스 작성

```python
# [APP_NAME]/test.py

# View Test
from rest_framework.test import APITestCase
from django.urls import reverse
from rest_framework import status
from django.utils import timezone

class QuestionListTest(APITestCase):
    def setUp(self):
        self.question_data = {'question_text': 'some question'}
        self.url = reverse('queston-list')
    
    def test_create_question(self):
        user =User.objects.create(username='testuser', password='testpass')
        self.client.force_authenticate(user=user)
        response = self.client.post(self.url, self.question_data)
        self.assertEqual(response.status_code, status.HTTP_201_CREATED)
        self.assertEqual(Question.objects.count(), 1)
        question = Question.objects.first()
        self.assertEqual(question.question_text, self.question_data['question_text'])
        self.assertEqual((timezone.now - question.pub_date).total_seconds(), 1)
    
    def test_create_question_without_authentication(self):
        response = self.client.post(self.url, self.question_data)
        self.assertEqual(response.status_code, status.HTTP_403_FORBIDDEN)
 
    def test_list_question(self):
        question = Question.objects.create(question_text='Question1')
        choice = Choice.objects.create(question=question, choice_text='Question1')
        Question.objects.create(question_text='Question2')
        response = self.client.post(self.url)
        self.assertEqual(response.status_code, status.HTTP_200_OK)
        self.assertEqual(len(response.data), 2)
        self.assertEqual(response.data[0]['choices'][0]['choice_text'], choice.choice_text)

# Serializer Test
class VoteSerializerTest(TestCase):
    def setUp(self):
        self.user = User.objects.create(username='testuser')
        self.question = Question.objects.create(
            question_text='abc',
            owner=self.user,
        )
        self.choice = Choice.objects.create(
            question=self.question
            choice_text='1'
        )    
    
   def test_vote_serializer(self):  
        self.assertEqual(User.objects.all().count(), 1)
        data = {
            'question': self.question.id
            'choice': self.choice.id
            'voter': self.user.id
        }
        serializer = VoteSerializer(data=data)
        self.assertTrue(serializer.is_valid())
        vote = serializer.save()
    
        self.assertEqual(vote.question, self.question)
        self.assertEqual(vote.choice, self.choice)
        self.assertEqual(vote.voter, self.user)
    
    def test_vote_serializer_with_duplicate_vote(self):
        self.assertEqual(User.objects.all().count, 1)
        choice1 = Choice.objects.create(
            quetsion=self.question,
            choice_text='2'
        )
        Vote.objects.create(question=self.question, choice=self.choice, voter=self.user)
    
        data = {
            'question': self.question.id
            'choice': self.choice.id
            'voter': self.user.id
        }
        serializer = VoteSerializer(data=data)
        self.assertTrue(serializer.is_valid())
    
    def test_vote_serilaizer_with_unmatched_question_and_choice(self):
        question2 = Question.objects.create(
            question_text='abc',
            owner=self.user,
        )
    
        choice2 = Choice.objects.create(
            quetsion=question2,
            choice_text='1'
        )
        data = {
            'question': self.question.id
            'choice': self.choice.id
            'voter': self.user.id
        }
        serializer = VoteSerializer(data=data)
        self.assertTrue(serializer.is_valid())
```
- setUp : 해당 클래스의 모든 테스트에 공통적으로 필요한 준비를 수행할 수 있게 하는 method이며, 각 테스트를 수행할 때마다 독립적으로 setUp이 실행된다. 즉, 이전 테스트의 setUp 내용이 이후 테스트에 영향을 미치지 않는다.

2. 테스트 실행 : `python manage.py test`

<br/>

> **Code Coverage 평가** <br/>
> `pip install coverage` <br/>
> `coverage run manage.py test` <br/>


<br/>
<br/>
<br/>

<hr/>

**ref.**<br/>
- Programmers DevCourse 3기

<hr/>