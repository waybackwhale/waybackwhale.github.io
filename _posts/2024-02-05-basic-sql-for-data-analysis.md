---
layout: posts
title:  "SQL 배우려 했는데 시작도 못한 사람들 드루와요"
tags: 
    - Bigquey
    - SQL
date:   2024-02-05 07:24:00 +0900
categories: SQL Bigquery
---

SQL은 영어와 문법 구조가 동일한 간단한 명령문입니다. SQL(Structured Query Language)이라는 이름이 붙은 이름도 문법 구조를 맞춰서(structured) 질의(query)한다는 이유였습니다. 알아야 하는 '단어'는 매우 적습니다. 기초 쿼리는 여섯개면 충분합니다.

SQL로 쿼리하는 데이터베이스는 엑셀과 크게 다르지 않습니다. 열과 행으로 구성된 데이터 집합이기 때문입니다. 분석 용도로만 사용한다면 엑셀이라고 생각하고 시작해도 괜찮은 정도입니다.

이 글에서는 데이터베이스 용어도 잠시 제쳐둡니다. 엑셀 데이터를 시스템에 간단한 명령어로 불러온다 생각하고 따라와주세요.

## 0. 회사 사람들의 MBTI 검사 결과를 분석해 봅시다. 

데이터 분석가 고래씨네 회사 인트라넷에 MBTI 검사 기능이 업로드 됐습니다. 회사 사람들은 검사에 응했습니다. 재미로요! 'MBTI조사' 시트에 사원번호, 이름, 성별, MBTI 정보가 차곡차곡 저장됐습니다. 고래씨는 질문을 먼저 정리해봤습니다.

1. 나와 같은 INFP인 사람들은 누구일까?
2. 우리 회사에서 가장 많은 유형은 뭘까?
3. 유형별로 순위를 정리해도 재미있겠지.
4. 개발팀에는 정말 I와 T가 많을까?
5. 개발팀과 상반되는 분위기의 마케팅팀에는 E와 F가 많을까?


## 1. 조회의 기초부터 배워보겠습니다.

### 1.1. 이름, MBTI 조회하기 - SELECT, FROM

고래씨는 먼저 데이터가 잘 저장됐는지 궁금했습니다.

> 'MBTItest' 시트에 있는 이름, MBTI 정보를 조회해보자.
 
쿼리로 작성할 때는 `SELECT`와 `FROM`을 이용합니다. 시트에서 원하는 열을 지정해서 새로운 시트를 하나를 만든다고 생각해주세요.

```sql
SELECT name, MBTI FROM mbtitest
```

![image](/assets/img/sql_start.002.jpeg)

`SELECT` 뒤에는 선택하는 열 이름을 써 주고 `FROM` 뒤에는 시트 이름을 써 주면 됩니다. "'MBTItest'에서 이름과 MBTI 정보를 선택해줘."라고 번역해도 의미가 그대로 다가옵니다.

### 1.2. INFP만 볼 수도 있을까? - WHERE

INFP인 고래씨는 같은 유형의 동료는 누가 있는지 궁금했습니다. 데이터를 걸러서 봐야겠죠. 필터링한다고 이야기 하기도 합니다. `WHERE`를 이용합니다. 엑셀 시트에서 필터링 기능을 사용해보신 분들도 있을텐데요. 같은 기능이라고 보시면 되겠습니다. 

```sql
SELECT name, MBTI 
FROM MBTItest 
WHERE MBTI=INFP?
```
![image](/assets/img/sql_start.003.jpeg)

구마다 줄바꿈을 했습니다. 쿼리가 길어지면 가독성 때문에 보통 이렇게 정리합니다. `WHERE`에는 예, 아니오로 답할 수 있는 표현이 들어갑니다. 시스템은 조건이 '예'인 경우만 걸러서 보여주게 되는 것이죠.

### 1.3. 이름 abc 순으로 정렬하자 - ORDER BY

결과로 얻은 시트가 정렬되어 있지 않았습니다. 고래씨는 이름 순으로 시트를 정렬하고 싶어습니다. '이름 abc 순'으로 하면 보기 편하겠죠. 이럴 때는 `ORDER BY`를 사용합니다. 

```sql
SELECT name, MBTI 
FROM MBTItest
WHERE MBTI=INFP?
ORDER BY name abc
```

![image](/assets/img/sql_start.004.jpeg)

`ORDER BY` 뒤에는 기준이 되는 열 이름을 넣습니다. 이름표를 정렬했으면 사람도 움직여야 겠지요. 전체 행은 같이 움직입니다. 

조회의 기초는 이게 전부입니다. 이제 쿼리 전체 의미를 우리말로 풀어 써 볼까요.

> 'MBTItest' 시트에서 이름과 MBTI 정보를 불러와줘. INFP만 볼거고 이름 abc 순으로 정리해줘.

## 2. 우리는 데이터를 '분석' 해야 합니다.

고래씨가 해야 할 일은 데이터 '분석'입니다. 분석의 기본은 요약이고요. 집계한다는 표현을 쓰기도 합니다.

### 2.1. 몇 명의 직원이 MBTI 검사를 했을까? - 집계

SQL에는 다섯가지 집계 유형이 있습니다.

1. 데이터 수 세기 - `count`
2. 더하기 - `sum`
3. 평균 내기 - `avg`
4. 최댓값 구하기 - `max` 
5. 최솟값 구하기 - `min`

시트를 조회해보던 고래씨는 검사에 응한 사원이 몇 명인지 알고 싶었습니다. 데이터 수를 세야겠죠. `count`를 사용하면 됩니다.

```sql
SELECT count(employee_id) FROM MBTItest
```
![image](/assets/img/sql_start.005.jpeg)

결과는 하나의 숫자로 요약됩니다. 조회 할 때는 변형 없이 데이터를 갖고 오는데 집계 할 때는 완전히 변합니다. 

만약 INFP 직원 수만 세고 싶다면 `WHERE` 절을 추가해 필터링 하면 됩니다.

```sql
SELECT count(employee_id)
FROM MBTItest 
WHERE MBTI=INFP?
```

### 2.2. MBTI 유형 별로 몇 명씩 있을까? - GROUP BY와 집계

고래씨는 MBTI 유형 별로 몇 명씩 있는지 알고 싶었습니다. 순위를 매기면 재밌는 자료가 될 것 같다고 생각했습니다. 유형이 16개니까 16개의 숫자를 얻어내야 합니다. 그런데 지금까지 했던 방식으로 하면 16번 쿼리를 해야 합니다.

이럴때 `GROUP BY`를 사용할 수 있습니다. 그루핑 또는 묶는 일이 되겠지요. 그룹을 만들었으면 대표값을 뽑아야 합니다. 위에 나열했던 다섯가지 유형 중 하나로 집계하면 됩니다.

```sql
SELECT MBTI, count(employee_id)
FROM MBTItest
GROUP BY MBTI
```

`GROUP BY` 뒤에는 묶는 기준이 되는 열 이름이 들어갑니다. mbti 유형이 아니라 성별로 묶고 싶다면 'MBTI' 대신 'gender'를 넣어주면 됩니다.

![image](/assets/img/sql_start.008.jpeg)

## 3. 다른 시트 정보가 필요할 수도 있습니다.

### 3.1. 개발 부서에는 정말 I와 T가 많을까? - JOIN

고래씨는 이번에는 개발부서 사람들의 MBTI 유형이 궁금해졌습니다. 소문대로 I와 T가 많을까요? 부서 이름으로 필터링 해서 쿼리를 하면 될 것 같습니다. 그런데 문제가 있습니다. 'MBTItest' 시트에 부서 정보가 없습니다. 찾아보니 'employee' 시트에 부서 이름이 있습니다. 이 시트에는 마침 'employee_id' 열도 있습니다. 'MBTItest' 시트에 있는 사원번호와 같은 것입니다. 이 것을 이용해 두 시트를 합쳐서 볼 수 있을 것 같습니다.

> 'MBTItest' 시트와 'employee' 시트를 합쳐서 사원번호, MBTI, 부서이름을 출력해보자.

시스템에 쿼리하고 싶다면 `JOIN` 키워드를 이용해야 합니다. 두개의 시트를 합친다는 의미입니다.

```sql
SELECT employee_id, MBTI, department
FROM employee
JOIN MBTItest ON employee_id
```

`JOIN`뒤에 결합할 시트 이름을 넣고 `ON` 뒤에 결합에 이용할 열 이름을 넣어줬습니다. 두 시트에 공통으로 있는 데이터를 이용해야 합니다.

![image](/assets/img/sql_start.009.jpeg)
![image](/assets/img/sql_start.010.jpeg)

이제 개발부서의 MBTI 정보를 확인해볼까요?

```sql
SELECT MBTI, count(employee_id)
FROM employee
JOIN MBTItest ON employee_id
WHERE department='개발팀'
GROUP BY MBTI
ORDER BY '사람 많은 순서'
```


`WHERE`로 개발부서를 필터링 하고 MBTI로 그루핑(`GROUP BY`)해서 사원수를 세었습니다. 그리고 사람 수가 많은 유형부터 보고 싶어서 `ORDER BY`로 정렬도 했습니다.

### 3.2. MBTI 검사를 하지 않은 직원들에게 이메일을 보내자. - LEFT JOIN

고래씨는 더 많은 직원의 MBTI 검사 결과를 분석하고 싶어졌습니다.

> 조사에 응하지 않은 직원들에게 메일을 보내야겠다.. 그런데 리스트를 어떻게 뽑지?

사원 시트에는 있는데 MBTI 조사 결과가 없는 경우를 찾으면 되겠지요. 이럴 때 `LEFT JOIN`을 사용할 수 있습니다. 왼쪽 데이터는 오른쪽에 데이터가 없어도 모두 남게 됩니다. 오른쪽 데이터는 비워진 상태로요.

```sql
SELECT employee_id, name, email
FROM employee
LEFT JOIN MBTItest ON employee_id
WHERE 'MBTI 검사 결과가 비어 있는 직원'
```

![image](/assets/img/sql_start.011.jpeg)

고래씨는 MBTI 조사 결과가 없는 사원 정보를 추려내 메일을 보낼 수 있었습니다.

`JOIN`과 `LEFT JOIN`은 이렇게 다릅니다.

![image](/assets/img/sql_start.012.jpeg)

## 4. 맺으면서

이 글에서는 데이터베이스 용어를 일부러 쓰지 않았습니다. 용어 설명부터 시작해서 힘을 빼고 싶지 않았습니다. 개념 전달에 집중하려고 쿼리도 실제 코드와는 다르게 작성했습니다. 조금 단순하게 바꾼 것 뿐이지만요. 표현도 필요한만큼만 소개했습니다. 하지만 더 배워야 할 것이 많지는 않습니다.

SQL에 막연한 두려움을 갖고 계셨던 분들은 이 글을 통해 용기를 얻으셨길 바랍니다. SQL은 쉽습니다.