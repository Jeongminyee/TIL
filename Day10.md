# Database

크롤링이나 스크래핑을 통해 데이터들을 긁어왔다면, 이제는 그 안에서 유의미한 데이터들을 추출할 수 있어야 한다. 데이터베이스는 테이블이 저장되는 장소이며, DBMS(Data Base Management System)은 데이터베이스를 관리하는 시스템 혹은 소프트웨어를 의미한다. SQL(Structured Query Language)은 데이터베이스를 다룰 수 있는 프로그래밍 언어이다.

## DBMS의 특징

- 데이터의 무결성
- 데이터의 독립성
- 보안
- 데이터 중복 최소화
- 응용 프로그램의 제작 및 수정이 쉬워짐
- 데이터의 안전성 향상

## SQL

**SQL의 특징**

1. DBMS를 제작하는 회사로부터 독립적이다.(즉, 어디에서나 사용이 가능하다.)
2. 다른 시스템으로의 이식성이 좋다.
3. 표준이 계속 발전한다.
4. 대화식 언어이다.
5. 분산형 클라이언트/서버 구조이다.

## SQL 설치하기

My-SQL을 설치한다. 맥북에서는 My-SQL 사이트를 사용해서 다운로드 받는 것 대신 잘 설명되어있는 이 [사이트](https://shanepark.tistory.com/41)를 참고하여 설치했다. 실행은 `mysql.server start`	를 터미널에 입력하면된다.

### - Mac에서 My-SQL Workbench 설치하기

DBMS를 다루기 위한 프로그램으로 My-SQL의 Workbench를 사용한다. 설치는 이 [사이트](https://kagus2.tistory.com/17)를 참고하였다.

---

# SQL 다루기

SQL 명령어는 DML, DDL, DCL 로 나뉘어져 있다.

- **DML**(Data Manipulation Language): 데이터 조작어 *예) SELECT, INSERT, UPDATE, DELETE*
- **DDL**(Data Definition Language): 데이터 정의어 *예) CREATE, DROP. ALTER*
- **DCL**(Data Control Language): 데이터 제어어 *예) GRANT, REVOKE, DENY*

## DML

DML은 Data Management Language로, 데이터를 관리하는 SQL의 명령어를 의미한다. 대표적으로 가장 많이 사용하는 Select문이 있는데, 대표적인 DML은 아래와 같다.

``` sql
select select_expr
	from table_reference
	where where_condition
	group by column_name
		having where_condition2
	order by column_name
```

### 1. 데이터 살펴보기

데이터베이스에서 내가 원하는 데이터를 추출하기 전에, 전체적인 데이터들의 형태를 알아볼 수 있는 명령어들이 있다. 주로 사용하는 명령어는 아래와 같으며, 주석을 통해서 내용을 정리했으니 참고하자.

``` sql
SHOW DATABASES; -- 내 컴퓨터에 있는 모든 데이터베이스를 보여줌
USE empoyees; -- employees 테이블을 사용하겠다고 선언
SHOW TABLE STATUS; -- 데이터베이스 내 테이블들의 이름, 행갯수 등을 알 수 있음
DESCRIBE employees; -- 특정 테이블의 내용을 보고싶을 때. DESC로도 사용 가능
```

### 2. 기본 구문 형식

SQL의 기본 구문 형식은 *SELECT ~ FROM* 이다. 이때, 사용하고자 하는 데이터베이스에 들어가 있지 않은 경우에는 구문 맨 위에 *USE 데이터베이스_이름* 을 넣어 명시해주는 것이 좋다.

``` sql
select * -- 전체(아스타)를 가져오겠다.
from employees; -- employees의 데이터베이스에서
```

**별칭**을 사용하여 쿼리를 작성할 때 구문을 줄일 수도 있다. 별칭을 사용하고자 할 때는 *as*를 사용해주어 지정한다.

``` sql
select userid as id -- userid의 별칭을 id로 설정
from employees
where id like '010%'; -- id가 010으로 시작하는 사람들의 userid 조회
```

### 3. DB, 테이블 다루기

#### - 테이블 생성하기

데이터베이스 생성은 *CREATE DATABASE 데이터베이스_이름* 을 사용하면 된다. 동일한 방식으로, 원하는 테이블 생성은 *CREATE TABLE `테이블명`* 을 이용할 수 있다.

``` sql
DROP DATABASE IF EXISTS sqldb; -- 만약 sqldb가 존재하면 삭제하고
CREATE DATABASE sqldb; -- sqldb를 새로 생성한다.
```

``` sql
CREATE TABLE buytbl(
	num INT AUTO_INCREMENT NOT NULL PRIMARY KEY,
	user_id CHAR(8) NOT NULL, -- varchar보다 저장공간을 적게 차지하고 속도가 빠르다.
	prductName CHAR(6) NOT NULL,
	groupName CHAR(4),
	price INT NOT NULL, -- 자료형의 크기 4바이트
	amount SMALLINT NOT NULL, -- smallint: 자료형의 크기 2바이트
	FOREIGN KEY (user_id) REFERENCES usertbl(user_id) -- user_id를 외래키로 설정
	);
```

- *AUTO_INCREMENT* : Insert가 일어날 때마다 자동으로 num값이 증가되도록 한다. 때문에 Insert를 지정할 때 따로 num값을 적어줄 필요가 없다.

#### - 테이블 복사하기

기존에 있는 테이블을 동일하게 사용하고 싶을 때, 테이블을 복사하여 사용할 수 있다. 물론, 일부분만 가져오는 것도 가능하다.

``` sql
CREATE TABLE new_tbl (SELECT 복사할열 FROM 기존테이블);
```

### 4. 데이터 조회와 필터링

select ~ from 절에서 다양한 SQL문을 사용해 특정 데이터를 조회할 수 있다.

#### - BETWEEN ~ AND

where 절에서 특정 값이 어떤 범위 안에 주어졌을 경우, *between ~ and* 절을 사용해 값을 추출할 수 있다.

``` sql
select name, height -- 이름과 키를 추출한다.
from usertbl -- usertbl에서
where height between 170 and 180; -- 키가 170과 180사이인 사람들의
```

#### - IN

필터링 하고자 하는 데이터가 이산형 범주일 때에는 in을 사용하여 찾아낼 수 있다.

``` sql
select name, address -- 이름과 주소를 추출한다.
from usertbl -- usertbl에서
where address in ('서울', '경기', '인천'); -- 주소가 서울, 경기, 인천인 사람들의
```

#### - LIKE

찾아내고자 하는 값이 일부만 공통적으로 적용되는 요소라면 like를 사용한다. 이중 *%* 는 자릿수가 상관없는 것을 의미하고, *_* 는 한 자리수를 의미한다.

``` sql
select name, mobile -- 이름과 전화번호를 추출한다.
from usertbl -- usertbl에서
where mobile like '011%' -- 전화번호가 011로 시작하는 사람들의
```

``` sql
select name, birthday -- 이름과 생일을 추출한다.
from usertbl -- usertbl에서
where birthday like '9407__'; -- 생일이 94년 7월 __일생인 사람들의
```

#### - ANY (= SOME)

any는 여러가지 값이 나오게 되는 sql문 중 어느 하나라도 만족한다면 데이터가 추출되도록 처리해준다.

``` sql
-- 인천 사람의 키보다 크거나 같은 사람들의 데이터를 추출해보기
select name, height
from usertbl
where height >= any(select height from usertbl where address = '인천');
```

#### - ALL 

all은 any와 같은 방식으로 쓰이지만, 여러가지 값들이 나왔을 때 그 값들을 모두 만족하는 데이터를 추출할 때 사용된다. 사용 방식은 any와 같으므로 생략한다.

#### - DISTINCT

데이터를 읽을 때, 중복되는 값들은 불필요할 때가 있다. 그럴 때 *select distinct* 문을 사용하면 중복되는 값들을 제거하여 준다.

``` sql
select distinct address -- 주소를 중복제거하여 보여준다.
from usertbl;
```

#### - LIMIT

데이터를 특정 개수만큼 읽고 싶을 땐 *limit* 를 써준다. 마지막에 사용해주어 원하는 데이터값의 갯수를 지정해준다.

``` sql
select id, hire_date -- 아이디와 고용일을 추출한다.
from employees -- employees 테이블에서
order by hire_date asc -- 고용일을 오름차순으로 설정
limit 100; -- 오름차순을 한 순서대로 100개만 보여준다.
```

#### - ORDER BY

데이터를 정렬해줄 때 사용하는 문으로, 뒤에 *asc* 를 사용하면 오름차순, *desc* 를 사용하면 내림차순으로 정렬할 수 있다. 따로 *asc, desc* 를 입력해주지 않았을 때의 기본값은 오름차순(*asc*)다. order by는 select, from, where, group by, having, order by 중 맨 뒤에 와야 한다. order by문은 MySQL의 성능을 떨어뜨릴 소지가 있으므로 꼭 사용이 필요한 경우에만 사용하주도록 하자.

``` sql
select name, birthday
from usertbl
order by birthday desc; -- 생일이 가장 늦은(최근)인 순서로 정렬
```
