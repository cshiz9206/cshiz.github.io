---
title: Clean Code
date: YYYY-MM-DD HH:MM:SS +09:00
categories: [개념정립]
tags:
  [
    Programming,
  ]
---

## Clean Code?
- 깔끔한 코드는 읽고, 이해하고, 수정하기 쉬움
- 코드는 명확한 이름, 일관된 형식, 의미 있는 주석으로 체계적으로 구성
- 좋은 코드는 테스트가 가능하며 코드와 함께 유닛 테스트를 작성해야 함
- 클래스와 함수는 분명한 하나의 일을 하도록 구현해야 함
- 코드는 모듈화되어야 하며 중복을 피해야 함
- 오류 처리는 코드베이스 전체에서 철저하고 일관성 있게 이루어져야 함(로깅)
- 코드는 작성자가 아닌 사용자를 염두에 두고 설계해야 함
- 단 중요한 일에 더 완벽을 기할 것

<br/>

## 코드 작성 원칙
- DRY(Don't Repeat Yourself)
  - 반복되는 작업이라면 함수로 만들 것
  - 동일한 일이 반복된다면 루프로 만들 것
  - 비슷한 클래스를 반복해서 만든다면 클래스 계승 구조를 사용해볼 것
- KISS(Kepp It Simple, Stupid)
  - 불필요한 복잡성을 피하고 코드를 가능한 한 단순하게 유지
  - 너무 많은 기능을 하나의 함수로 구현하지 말 것

### 일반적인 원칙(코딩 컨벤션)
- 일관된 포맷 및 이름 지정 규칙
- 의미있고 설명적인 변수 이름 사용
- 적절한 주석 및 문서화
- 제어 구조와 알고리즘의 효율적인 사용

[구글 파이썬 스타일링 가이드](https://google.github.io/styleguide/pyguide.html) <br/>
[구글 파이썬 주석 예시](https://sphinxcontrib-napoleon.readthedocs.io/en/latest/example_google.html)

```python
num_items # the number of items
is_valid # something is valid or not
total_cost # the total cost of something
max_attempts # the maximum number of attempts
has_permission # a user has permission to do something
error_message # an error message
list_of_names # a list of names
is_active # something is currently active
input_value # an input value
```

<br/>

## 코드리뷰를 해야하는 이유
Reasons for code reviews at Google
- education
- accident prevention
- gatekeeping
- tracing & tracking
- readable code

<br/>

## 좋은 코드리뷰 방법
- 코드리뷰를 요청하는 사람
  - 요청시 되도록이면 조금씩 자주 요청. Unit test와 같이 요청하면 최상
  - 주석을 최대한 추가하고 무슨 이유에서 뭘 하려고 하는 것인지 설명
  - 리뷰에 대한 피드백을 너무 감정적으로 받아들이지 않기
- 코드리뷰를 하는 사람
  - 코딩 스타일에 대한 것보다는 코드 자체에 대해 이야기
  - 객관적으로 쓰고 비판하는 어조는 피하기
  - 충분히 시간을 들여 도움이 되는 리뷰를 제공
- 코드리뷰에 편리한 툴 사용

*코드리뷰의 단점은 리뷰를 해야하는 사람들이 이미 바쁜 사람들이라는 점이다.
- 스프린트 플래닝시 이를 고려하여 태스크 할당

<br/>
<br/>
<br/>

<hr/>

**ref.**<br/>
- Programmers DevCourse 3기

<hr/>