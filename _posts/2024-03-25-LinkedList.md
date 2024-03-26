---
title: Linked List
date: YYYY-MM-DD HH:MM:SS +09:00
categories: [개념정립, Programmers-DevCourse]
tags:
  [
    자료구조,
    Data Engineering
  ]
---

> ***추상적 자료구조***

<br/>

# 형태

- list의 첫번째 node는 head
- list의 마지막 node는 tail
- Link는 next를 가리키는 pointer
- 아래 그림에서 '[ ]'는 node를 나타냄

## Singly Linked List

```
[데이터 | Link] -> [데이터 | Link] -> ...
```

## Doubly Linked List

```
[Link | 데이터 | Link] -> [Link | 데이터 | Link] -> ...
```

<br/>
<br/>
<br/>

# 속성

- **저장** : 임의의 위치에 저장된다. 데이터를 연속한 위치에 저장하는 list와의 차이점이다.

<br/>
<br/>
<br/>

# 활용

- 삽입, 삭제가 유연해야 하는 경우

<br/>
<br/>
<br/>

# 연산

- 참조(k 번째)
  + 시간복잡도 : O(n)
- 순회
- 길이
- 삽입
  + 시간복잡도 : O(1) ~ O(n)
- 삭제
- 합치기

<br/>
<br/>
<br/>

# 구현

## Singly Linked List

### Solution_1

```python
class Node:
  def __init__(self, item):
    self.data = item
    self.next = None

class LinkedList:
  def __init__(self):
    self.nodeCount = 0
    self.head = None
    self.tail = None

  # 연산_참조
  def getAt(self, pos):
    if pos <= 0 or pos > self.nodeCount:
      return None
    
    i = 0
    curr = self.head
    while i < pos:
      curr = curr.next
      i += 1

  # 연산_순회
  def traverse(self):
      result = []
      
      curr = self.head
      while curr != None:
        result.append(curr.data)
        curr = curr.next
      
      return result

  # 연산_삽입
  def insertAt(self, pos, newNode):
    if pos <= 0 or pos > self.nodeCount + 1:
      return False
    
    if pos == 1:
      newNode.next = self.head
      self.head = newNode
    else:
      if pos == self.nodeCount + 1:
        prev = self.tail
      else:
        prev = self.getAt(pos - 1)
      newNode.next = prev.next
      prev.next = newNode

    if pos == self.nodeCount + 1:
      self.tail = newNode
    
    self.nodeCount += 1
    return True

  # 연산_삭제
  # 주석된 부분은 .getAt()의 사용(탐색의 중복)을 줄이기 위한 시도이다.(예외 처리에 안전하지 못한 미완성 코드이다.)
  def popAt(self, pos):
    if pos < 1 or pos > self.nodeCount:
      raise IndexError
        
    target = self.getAt(pos)
        
    if self.nodeCount == 1:
      self.head = None
      self.tail = None
      self.nodeCount -= 1
      return target.data
    
    if pos == 1:
      self.head = self.getAt(2)
    else:
      prev = self.getAt(pos - 1)
      if pos == self.nodeCount:
        self.tail = prev
        self.tail.next = None
      else:
        prev.next = target.next

    # i = 1
    # curr = self.head
    # prev = curr
    # while curr is not None:
    #   if i == pos:
    #     if pos == 1:
    #       self.head = curr.next
    #     else:
    #       if curr == tail:
    #         self.tail = prev
    #       else:
    #         prev.next = curr.next
    #     break
    #   prev = curr
    #   curr = curr.next
    #   i += 1
    
    self.nodeCount -= 1
    return target.data

  # 연산_합치기
  def concat(self, L):
    self.tail.next = L.head
    if L.tail:
      self.tail = L.tail
    self.nodeCount += L.nodeCount
    return True

  # 기능_표시
  def __repr__(self):
    if self.nodeCount == 0:
      return 'LinkedList: empty'
    
    s = ''
    curr = self.head
    while curr is not None:
      s += repr(curr.data)
      if curr.next is not None:
        s += ' -> '
      curr = curr.next

    return s
```

<br/>

### Solution_2

Solution_1에서의 code를 더 간략하게 하고자 한 solution이다. 다음의 방법을 통해 이를 해결하고자 한다.
1. dummy head node(index 0의 가상 head)를 가정한다.
2. 예외처리와 핵심작업을 각 함수로 분리한다.

```python
class Node:
  def __init__(self, item):
    self.data = item
    self.next = None

class LinkedList:
  def __init__(self):
    self.nodeCount = 0
    self.head = Node(None)
    self.tail = None
    self.head.next = self.tail

  def traverse(self):
    result = []
    curr = self.head
    while curr.next:
        curr = curr.next
        result.append(curr.data)
    return result

  def getAt(self, pos):
    if pos < 0 or pos > self.nodeCount:
        return None

    i = 0
    curr = self.head
    while i < pos:
        curr = curr.next
        i += 1

    return curr

  def insertAfter(self, prev, newNode):
    newNode.next = prev.next
    if prev.next is None:
        self.tail = newNode
    prev.next = newNode
    self.nodeCount += 1
    return True

  def insertAt(self, pos, newNode):
    if pos < 1 or pos > self.nodeCount + 1:
        return False

    if pos != 1 and pos == self.nodeCount + 1:
        prev = self.tail
    else:
        prev = self.getAt(pos - 1)
    return self.insertAfter(prev, newNode)

  def popAfter(self, prev):
    target = prev.next
    prev.next = prev.next.next
    if prev.next is None:
        self.tail = prev
    self.nodeCount -= 1
    return target.data

  def popAt(self, pos):
    if pos < 1 or pos > self.nodeCount:
        raise IndexError

    prev = self.getAt(pos - 1)
    
    return self.popAfter(prev)

  def concat(self, L):
    self.tail.next = L.head.next
    if L.tail:
      self.tail = L.tail
    self.nodeCount += L.nodeCount
    return True
```

<br/>

## Doubly Linked List

dummy head node, dummy tail node를 가정한다. 이로써, 데이터를 담고 있는 node들을 모두 같은 모양으로 취급할 수 있다.
```python
class Node:

    def __init__(self, item):
        self.data = item
        self.prev = None
        self.next = None


class DoublyLinkedList:

    def __init__(self):
        self.nodeCount = 0
        self.head = Node(None)
        self.tail = Node(None)
        self.head.prev = None
        self.head.next = self.tail
        self.tail.prev = self.head
        self.tail.next = None


    def concat(self, L):
        self.tail.prev.next = L.head.next
        L.head.next.prev = self.tail.prev
        self.tail = L.tail
        self.nodeCount += L.nodeCount
        return True


    def traverse(self):
        result = []
        curr = self.head
        while curr.next.next:
            curr = curr.next
            result.append(curr.data)
        return result

    
    def getAt(self, pos):
        if pos < 0 or pos > self.nodeCount:
            return None

        if pos > self.nodeCount // 2:
            i = 0
            curr = self.tail
            while i < self.nodeCount - pos + 1:
                curr = curr.prev
                i += 1
        else:
            i = 0
            curr = self.head
            while i < pos:
                curr = curr.next
                i += 1

        return curr


    def insertAfter(self, prev, newNode):
        next = prev.next
        newNode.prev = prev
        newNode.next = next
        prev.next = newNode
        next.prev = newNode
        self.nodeCount += 1
        return True


    def insertAt(self, pos, newNode):
        if pos < 1 or pos > self.nodeCount + 1:
            return False

        prev = self.getAt(pos - 1)
        return self.insertAfter(prev, newNode)


def solution(x):
    return 0
```


<br/>
<br/>
<br/>

<hr/>

**ref.**<br/>
- Programmers DevCourse 3기

<hr/>