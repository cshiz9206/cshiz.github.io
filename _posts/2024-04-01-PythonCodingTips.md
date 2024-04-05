---
title: Python Coding Tips
date: YYYY-MM-DD HH:MM:SS +09:00
categories: [개념정립]
tags:
  [
    Programming
  ]
---

> **변수 명명 규칙**<br/>
> 변수명: listOfArrayOfDict문자<br/>
> 의미: [({}, {}, ...), ({}, {}, ...), ...] 형태의 '문자'라는 이름을 갖는 변수

<br/>

# 비교

#### 동일 위치 비교

```python
for a, b in zip(listA, listB):
```

#### 차이 비교

```python
from collections import Counter
dictDiff = Counter(listA) - Counter(listB)
```

# 연결/통합

#### 이어붙이기

```python
import itertools
itertools.chain(listA, listB)
```

#### 동일 위치 묶기

```python
[list(a) for a in zip(*listOfArrayA)]
```

<br/>

# 추출

#### Permutation

```python
import itertools
listOfArray = itertools.permutations(listA, extractN)
```

#### Combination

```python
import itertools
listOfArray = itertools.combinations(listA, extractN)
```

#### Product(Cartesian product)

```python
import itertools import product
listOfArray = list(product(listA)) # extractN == len(listA)
```

<br/>

# 변형

#### Reverse

```python
reversed(listA)
```

# 집계

#### 카운트/순위

```python
from collections import Counter
counter = Counter(dictCountData)
counter.most_common(10)
```

<br/>
<br/>
<br/>

<hr/>

**ref.**<br/>
- Programmers DevCourse 3기

<hr/>