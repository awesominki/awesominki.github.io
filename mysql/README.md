---
layout: page
title: MYSQL 정리 노트
description: >
  SQL 코테 뿌시자 아자아자 :)
hide_description: true
sitemap: false
permalink: /mysql/ 
---

### 1. SELECT

```sql
SELECT 컬럼명 또는 *
FROM 테이블명
WHERE 조건
```

### 2. SUM, MAX, MIN, AVG, COUNT (집계 함수)

```sql
SUM(컬럼명)
MAX(컬럼명)
MIN(컬럼명)
```

SELECT 문과 같이 쓰는 법
```sql
SELECT SUM(컬럼명)
FROM 테이블명
```

### 3. GROUP BY

```sql
SELECT a.column, sum(b.column)
FROM table a, table b
WHERE 조건
GROUP BY 그룹화할 컬럼명
HAVING GROUP BY 조건
```

- HAVING은 GROUP BY가 수행되기 전에 실행되고, WHERE은 GROUP BY가 모두 완료된 후에 수행된다는 점이 다르다.
- group by를 사용하기 위해서는 집계 함수를 필수로 사용해야 한다.

### 4. IFNULL, IS NULL

1. IFNULL

IFNULL로 IFNULL은 설정한 column이 null일 때, 대체 텍스트를 설정할 수 있다.
```sql
IFNULL(column, 'text')
```
null이 발생할 수 있는 column을 넣어준 후, 해당 column에 null이 발생하면, 설정한 text로 대신 바뀌어서 출력되는 방식이다.

2. IS (NOT) NULL

```sql
WHERE 조건 is (not) null
```

### 5. JOIN

![JOIN의 종류](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FJGMc3%2FbtszKyBLSbL%2FhdGlRRjlcPQa1ENYRSHKr1%2Fimg.png)

```sql
SELECT a.column, b.column
FROM tabl1 a JOIN table2 b ON a.column = b.column
WHERE 조건
```

- 위의 코드는 기본적인 Inner Join의 사용법만 다루었다.

### 6. STRING, DATE

문자열을 검색하기 위해서 SQL에서 LIKE 키워드를 사용할 수 있다.
```sql
column LIKE '조건 string'
```
- column 데이터에서 조건 string을 통해 데이터를 검색하는 명령어이다. like가 먼저 오는 게 아니므로 순서를 헷갈리는 일이 없도록 하자!
- %: 임의의 길이의 문자열을 가정하고 데이터를 검색한다.(%12%: 문자열 중 12가 있으면 출력, %12: '라면'이라는 문자열이 데이터 문자열의 맨 뒤에 있으면 출력)
- _(백슬래시): 한 개의 문자가 있음을 가정하고 데이터를 검색한다.(_11: 한 글자 뒤에 11이 있으면 출력, n번쨰 글자의 11을 출력하고자 한다면 '_'을 n개 작성 후 11을 작성한다.)

날짜 데이터를 활용해야 할 문제들도 특정 날짜 형식으로 출력되도록 설정해야 하는 상황이 많을 것 같다.

그러면 DATE_FORMAT 명령어를 이용해 보자.

DATE_FORMAT 명령어는 다음과 같이 사용할 수 있다.
```sql
DATE_FORMAT(date string, '날짜 형식')
```

date string을 자신이 설정한 날짜 형식으로 바꿔줄 수 있다.

날짜 형식으로 사용하는 문자들은 다음과 같다.

- %Y: 연도 4자리
- %y: 연도 2자리
- %M: 긴 월(영어, 예: July)
- %b: 짧은 월(영어, 예: Jul)
- %W: 긴 요일 이름(영어, 예: Monday)
- %a: 짧은 요일 이름(영어, 예: Mon)
- %i: 분
- %T: hh:mm:SS
- %m: 숫자 월(두 자리)
- %c: 숫자 월(한 자리)
- %d: 일자(두 자리)
- %e: 일자(한 자리)
- %I: 시간(12시간)
- %H: 시간(24시간)
- %r: hh:mm:ss AM,PM
- %s: 초

위의 문자들을 적절히 활용해서 날짜 형식을 만들어 보자.

SQL 코딩 문제 은근히 재밌다 ..