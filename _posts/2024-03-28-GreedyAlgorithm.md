---
title: Greedy Algorithm
date: YYYY-MM-DD HH:MM:SS +09:00
categories: [개념정립, Programmers-DevCourse]
tags:
  [
    Algorithm,
    Data Engineering
  ]
---

## 접근법
---
- 알고리즘의 각 단계에서 그 순간에 최적이라고 생각되는 것을 선택
- 앞 단계에서의 선택이 이후 단계에서의 동작에 의한 해의 최적성에 영향을 주지 않음

## 활용
---
Greedy Algorithm이 흥미로운 점은 강화학습의 기본 이론에서 사용되는 알고리즘이라는 점이다.<br/>
사람 또는 동물의 학습 방법을 모방하는 강화학습에서 Greedy Algorithm이 작동하는 것을 보다보면, 어떤 선택이 정답이 아닐지라도 매순간에 최선을 다하는 에이전트(지능체)의 모습에 자신이 비춰진다.<br/>
현재 LLM의 Hallucination을 너무 뭐라하지 말자. 그 모델도 최선을 다한 것임을, 사람도 항상 최고의 정답을 내놓을 수는 없음을 생각해보게 된다.

## 문제
---
### 체육복 문제

#### 입출력 예시

| n | lost | reserve | return|
| --- | --- | --- | --- |
| 5 | [2, 4] | [1, 3, 5] | 5 |
| 5 | [2, 4] | [3] | 4 |
| 3 | [3] | [1] | 2 |

#### 구현

필요한 여벌을 있는대로 가져가는 것이 문제가 목표로 하는 분실자의 최소화를 이루는 데 적합한 해결 방법이라고 볼 수 있다는 점에서 이 문제는 Greedy한 접근이 필요하다.

##### 시간복잡도

lost와 reserve를 순회하며 비교하는 방식으로 구현할 시, 시간복잡도는 O(n)과 같이 lost의 길이에 선형적으로 영향을 받는다.

O(n)과 같은 선형적 시간복잡도는 데이터가 커질수록 처리시간이 증가한다는 것을 의미한다. 데이터의 크기에 영향을 더 적게 받는 시간복잡도에는 O(log n)이 있다.
O(log n)은 로그함수를 따른다. 즉, 데이터가 작은 경우 일부에 대해서만 O(n)보다 큰 시간복잡도를 갖고, 그 외에 특히 데이터가 커진다면 시간복잡도의 증가율은 선형적 증가보다 점점 더 작아진다.

이러한 선형적 시간복잡도의 특성을 고려하여 더 최적화하기 위한 방안으로, lost와 reserve에 동시에 존재하는 원소를 먼저 해결하여 lost, reserve의 길이를 k로 줄인다. 또한 O(1)의 상수 시간복잡도를 갖는 탐색을 사용하기 위해 set을 도입한다.

```python
def solution(n, lost, reserve):
  answer = 0
  
  # list to set -> 시간복잡도 : O(n)
  inst = set(lost) & set(reserve)
  l = set(lost) - inst
  r = set(reserve) - inst
  
  # sort -> 시간복잡도 : O(k log k)
  # for -> 시간복잡도 : O(k)
  for x in sorted(r):
    # in 탐색 -> 시간복잡도 : O(1)
    if x - 1 in l:
      # remove -> 시간복잡도 : O(k)
      l.remove(x - 1)
    elif x + 1 in l:
      l.remove(x + 1)
  
  answer = n - len(l)
  return answer
```


<br/>
<br/>
<br/>

<hr/>

**ref.**<br/>
- Programmers DevCourse 3기
- [파이썬 자료형 별 주요 연산자의 시간 복잡도 (Big-O)](https://wayhome25.github.io/python/2017/06/14/time-complexity/)

<hr/>