---
title: BeautifulSoup
date: YYYY-MM-DD HH:MM:SS +09:00
categories: [개념정립, Programmers-DevCourse]
tags:
  [
    Extraction,
    Data Engineering
  ]
---

> **BeautifulSoup** : 정적 web page scraping을 위한 library

<br/>

## HTML Locator

- tagname : tag의 이름
- id : 하나의 고유 tag를 가리키는 label. unique하다.
- class : 여러 tag를 묶는 label

### Web Scraping

```python
import requests
from bs4 import BeautifulSoup

# html 가져오기
res = requests.get("Target URL")
# header를 지정하여 html 가져오기
# user_agent = {"User-Agent": "Mozilla/5.0 ... 와 같은 개인 user agent name"}
# res = requests.get("Target URL", user_agent)

# DOM 객체 준비
BeautifulSoup(res.text, "html.parser")

# Locator를 사용하여 Scraping
soup.find("tag", id="idName")
soup.find("tag", "className").tag.text
soup.find("tag", "className").find("tag", "className").h4
soup.find_all("tag", "className")
```

<br/>

## Pagination

많은 정보를 Index로 구분하는 기법
- Query String

### Web Scraping

```python
# Pagination으로 Query string을 사용하는 사이트에 대한 scraping
for i in range(startPageNum, endPageNum):
  res = requests.get("URL/?page={}".format(i), user_agent)

  questions = soup.find_all("tag", "className")

  time.sleep(0.5) # 요청 간 interval
```


<br/>
<br/>
<br/>

<hr/>

**ref.**<br/>
- Programmers DevCourse 3기

<hr/>