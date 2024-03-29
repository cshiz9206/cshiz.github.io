---
title: Dynamic Programming
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
- 문제를 재귀적인 방식으로 보다 작은 부분 문제로 나누어 풀고, 각 부분 문제의 해를 조합하여 전체 문제의 해답에 이르는 방식(Devide&Conquer)
- Algorithm의 진행에 따라 탐색해야 할 범위를 동적으로 결정함으로써 탐색 범위를 한정할 수 있다.

## 활용
---
- 재귀적 방법의 시간복잡도 개선

## 문제
---
### Knapsack Problem

대표적 문제로, 풀어볼 계획.

### N으로 표현

#### 입출력 예시

| N | number | return |
| --- | --- | --- |
| 5 | 12 | 4 |
| 2 | 11 | 3 |

#### 구현

- N의 수에 따라 문제를 나누어 생각해볼 수 있다.
N을 n(n은 n <= 8인 임의의 수)개 사용할 때 만들 수 있는 수의 개수가 N을 (n - 1)개 사용할 때 만들 수 있는 수의 개수를 포함한다.
- 같은 수가 결과로 나온 경우, 중복을 제거한다. 
- 위의 접근으로 인해 모든 순서쌍의 경우의 수가 고려되어, 괄호는 무의미해진다.

##### 자료구조 선정

즉, 다음과 같은 자료구조가 필요하다.
- n마다의 결과를 모을 수 있고 iterative한 자료구조(n을 index로 하고 결과를 원소로 저장할 수 있는 list)가 필요하다.
- 중복을 허용하지 않는 자료구조(set)

##### 시간복잡도

재귀적 방법을 사용할 경우 N을 사용하는 개수에 비례하여 기하급수적으로 시간복잡도가 증가한다. 이를 해결하기 위해 Dynamic Programming 접근을 통해 각 부분에 대해 이전 결과를 재활용함으로써 시간복잡도의 증가를 비교적 변화량이 작은 선형적인 시간복잡도로 해결 가능하다.

```python
def solution(N, number):
  s = [set() for _ in range(8)]
  for i, v in enumerate(s, start=1): # (1)
    v.add(int(str(N) * i))
  for i in range(len(s)):
    for j in range(i): # (2)
      for ps1 in s[j]:
        for ps2 in s[i - j - 1]: # (3)
          s[i].add(ps1 + ps2)
          s[i].add(ps1 - ps2)
          s[i].add(ps1 * ps2)
          if ps2 != 0:
            s[i].add(ps1 // ps2)
    if number in s[i]:
      answer = i + 1 # (4)
      break
  else:
    answer = -1
  
  return answer
```
- (1) : N을 0~8번 사용했을 때의 결과들을 중복없이 저장할 수 있는 저장소 s에서 각각 N을 최대 개수로 사용할 때(연산자를 사용하지 않을 때)의 결과를 저장소에 모두 하나씩 넣어주고 시작한다.
- (2) : 각 결과 배열 간 조합의 모든 경우의 수를 순회하기 위해 i, j index를 선언해준다. 사용할 수 있는 N의 수가 한 번에 8개로 한정되어 있으므로, j를 i보다 작게 하고 (3)에서의 i에서 j를 뺀 수로 index를 수행하여 한정된 범위에서 모든 경우를 순회할 수 있도록 한다.
- (3)의 i - j - 1에서 -1을 한 것과 (4)에서 +1을 한 것은 s를 만들 때 index를 (사용하는 N 개수 - 1)로 설정하여 그렇다.


<br/>
<br/>
<br/>

<hr/>

**ref.**<br/>
- Programmers DevCourse 3기

<hr/>