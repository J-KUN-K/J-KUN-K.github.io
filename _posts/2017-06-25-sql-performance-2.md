---
layout: post
title:  "[SQL Performance] - 1. 식별자 관계와 비 식별자 관계(비진행중)"
date:   2017-09-15 01:30:13 +0800
categories: data
tags: sql-performance
img: sql-performance-1-1.png
---

**PK 구성 INDEX 이용이 성능에 미치는 영향을 살펴봅니다.**


##### PK컬럼 순서가 성능에 미치는 영향

SQL 테이블 생성시 설계 단계 이후 완성된 데이터 모델을 토대로, 물리적인 테이블을 정해진 DBMS에 맞게 생성한다. 이때 보통 상용화된 툴을 이용해 모델을 만들고 DDL을 생성하므로 PK 컬럼 순서를 그다지 신경 안쓰는 경우가 많이 있다. 문제는 있는 그대로 컬럼을 생성하면
다음과 같은 문제가 발생할 수 있다.

