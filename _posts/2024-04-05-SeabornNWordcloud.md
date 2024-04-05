---
title: Seaborn & Wordcloud
date: YYYY-MM-DD HH:MM:SS +09:00
categories: [개념정립, Programmers-DevCourse]
tags:
  [
    Extraction,
    Data Engineering
  ]
---

## Seaborn

> Seaborn : 데이터 시각화 라이브러리
> - matplotlib를 기반으로 만들어졌다. -> matplotlib.pyplot의 속성을 변경해서 그래프에 다양한 요소를 변경/추가할 수 있다.

```python
sns.lineplot(xList, yList)
sns.barplot(xList_binaryData, yList_continousData)

# matplotlib를 활용한 속성 변경
sns.barplot(...)
plt.xlabel("X Label")
plt.ylabel("Y Label")
plt.title("title")
plt.show()

sns.lineplot(...)
plt.xlim(rangeStart, rangeEnd)
plt.ylim(rangeStart, rangeEnd)
plt.figure(figsize=(그래프가로크기, 그래프세로크기))
plt.show()
```

<br/>

## Wordcloud

> wordcloud : python의 Text Cloud Library<br/>
> konlpy : 한국어 형태소 분석기 Library

```python
# 문장에서 명사를 추출하는 형태소 분석 라이브러리
from konlpy.tag import Hannanum
hannanum = Hannanum()
nouns = hannanum.nouns("context")

wordcloud = WordCloud(
  fonts="path_font",
  width=,
  height=,
  background_color=
)
img = wordcloud.generate_from_frequencies("count에 대한 Counter 객체")
```

<br/>
<br/>
<br/>

<hr/>

**ref.**<br/>
- Programmers DevCourse 3기

<hr/>