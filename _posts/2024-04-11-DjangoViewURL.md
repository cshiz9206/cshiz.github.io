---
title: Django View & URL
date: YYYY-MM-DD HH:MM:SS +09:00
categories: [개념정립, Programmers-DevCourse]
tags:
  [
    Backend,
    Programming,
  ]
---

## View & Template

1. template 작성
  - templates/[APP_NAME] 위치에 index.html과 같이 작성

![alt text](./figures/image.png)

2. [APP_NAME]/views.py 작성

```python
from .models import *
from django.shortcuts import render

def index(request):
  latest_data_list = [MODEL_NAME].objects.order_by('-[FIELD_NAME]')[:5]
  context = {'datas': latest_data_list}
  return render(request, '[APP_NAME]/index.html', context)
```



<br/>
<br/>
<br/>

<hr/>

**ref.**<br/>
- Programmers DevCourse 3기

<hr/>