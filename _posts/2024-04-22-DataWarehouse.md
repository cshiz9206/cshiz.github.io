---
title: Data Warehouse
date: YYYY-MM-DD HH:MM:SS +09:00
categories: [개념정립, Programmers-DevCourse]
tags:
  [
    Backend,
    Data Engineering
  ]
---

# Data Warehouse

- 회사에 필요한 모든 데이터를 저장
    + 고객이 아닌 내부 직원을 위한 데이터베이스
    + 처리속도가 아닌 처리 데이터의 크기가 더 중요해짐.
- SQL 기반의 관계형 데이터베이스
- 프로덕션 데이터베이스와는 별도이어야 함.
    + 프로덕션 데이터베이스 : OLTP(OnLine Transaction Processing) vs 데이터 웨어하우스 : OLAP(OnLine Analytical Processing)
    + 회사 내부에서의 데이터 활용 트래픽과 사용자 트래픽을 분리
    + 함께 구축할 시, 백엔드 등 프로덕션 관련 부서와 마찰이 발생할 수 있음. 
- ETL/데이터 파이프라인과 결합하여 데이터 인프라에서 사용됨.
    + ETL/데이터 파이프라인 : 외부에 존재하는 데이터를 데이터 웨어하우스에 저장해주는 코드들
- AWS Redshift(고정비용), Google Cloud BigQuery(가변비용), Snowflake(가변비용) 등이 대표적



<br/>
<br/>
<br/>

<hr/>

**ref.**<br/>
- Programmers DevCourse 3기

<hr/>