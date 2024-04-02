---
title: Crawling & Scraping
date: YYYY-MM-DD HH:MM:SS +09:00
categories: [개념정립, Programmers-DevCourse]
tags:
  [
    FrontEnd,
    Data Engineering
  ]
---

## Web Crawling vs Scraping

**Crawling** : 웹사이트의 정보들을 탐색/색인. URL을 타고다니며 반복적으로 데이터를 가져오는 과정 <br/>
**Scraping** : 웹페이지의 정보를 추출. 특정한 목적으로 특정 웹 페이지에서 데이터를 추출하는 것

<br/>

## 윤리적 HTTP Request

***1. Web Scraping/Crawling을 통해 어떤 목적을 달성하고자 하는가?***

- 저작권, 데이터베이스권에 위배되지 않는가?
  - REP(Robot Exclusion Protocol)

<br/>

> *Robots.txt* : 제공자가 User-agent, Disallow, Allow 등의 Keyword를 사용하여 명시한 정보 제한 정책
> - URL/robots.txt로 요청하여 확인 가능
> - User-agent : crawler, scraper를 대표하는 program(규칙 적용 대상 요청자)
> - Allow, Disallow : 거부/허가 범위

<br/>

***2. 나의 Web Scraping/Crawling이 Server에 영향을 미치지는 않는가?***

- 요청하고자 하는 서버에 과도한 부하를 주지 않는가?
  - ex. DDOS


<br/>
<br/>
<br/>

<hr/>

**ref.**<br/>
- Programmers DevCourse 3기

<hr/>