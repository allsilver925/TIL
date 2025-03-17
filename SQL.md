## SQL 문법

```
 SELECT [ALL | DISTINCT] 컬럼명 [,컬럼명...]
 FROM 테이블명 [,테이블명...]
 [JOIN 테이블명 [ON | USING 조건식]]
 [WHERE 조건식]
 [GROUP BY 컬럼명 [HAVING 조건식]]
 [ORDER BY 컬럼명]
 GROUP BY 컬럼명[,컬럼명...]
 ORDER BY 컬럼명[,컬럼명...]
```

### SELECT 쿼리문의 실행 순서
> FROM - ON - JOIN - WHERE - GROUP BY - HAVING - SELECT - DISTINCT - ORDER BY <br>

### SELECT : 테이블에서 데이터를 추출하는 DML중 하나
```  SELECT [ALL | DISTINCT] 컬럼명 [,컬럼명...] ```
* SELECT절은 SELECT문에서 필수 구성되는 절로 지정 열을 설명하는 것. ``` SELECT * ```로 하면 모든 열이 표시됨.
* 산술 연산자, 그룹 함수를 사용할 수 있음
* ```SELECT ALL``` 은 중복되는 행이 있어도 모두 반환 (지정 안했을 때도, ALL이 선택), ```SELECT DISTINT```은 중복되는 행을 제거하고 하나씩만 반환

```
 FROM 테이블명 [,테이블명...]
 [JOIN 테이블명 [ON | USING 조건식]]
```
* FROM절은 SELECT문에서 필수 구성되는 절로 열 참조를 가진 테이블을 지정함.
* FROM절에서의 JOIN 형태(M*N)
  - INNER JOIN : JOIN조건에서 동일한 값이 있는 행만 반환, WHERE절에서 사용하던 JOIN조건을 FROM절에 사용하겠다는 뜻. USING, ON조건절을 필수적으로 사용해야 함
  - NATURAL JOIN : 별도의 컬럼을 지정하지 않아도 두 테이블에서 공통된 컬럼을 자동인식하여 JOIN처리. alias나 테이블명과 같은 접두사를 사용할 수 없음
  - CROSS JOIN : 테이블 간 JOIN조건이 없는 경우, 생길 수 있는 모든 데이터의 조합

``` [WHERE 조건식] ```
* WHERE절은 데이터를 추출하는 선택 조건식을 지정. 단일식을 이용하는 것 외에도 여러조건을 조회하는 경우도 많음. 테이블 간 결합시 결합관계를 지정함. WHERE절에서는 그룹함수(AVG,MAX,MIN,SUM,COUNT)를 사용할 수 없음
* Null값을 찾거나 제외하려면 IS NULL / IS NOT NULL을 써주어야 함

``` [GROUP BY 컬럼명 [HAVING 조건식]] ```
* GROUP BY절은 그룹화 열(컬럼)명을 포함하는 식을 지정. alias는 사용할 수 없고, GROUP BY절에 사용되는 컬럼이 아니어도 SELECT절에 사용되는 컬럼을 같이 작성해주어야 함.
* HAVING절은 GROUP BY절에 집계한 결과에 조건을 주는 절.. 그룹함수 사용이 가능하고, GROUP BY절과의 순서는 상관없음

``` [ORDER BY 컬럼명] ```
* ORDER BY절은 정렬할 컬럼이나 컬럼을 포함하는 식을 지정. 무조건 구문의 마지막에 써주며, 쉼표로 구분하여 여러 열을 지정할 수 있음.
  - ASC : 오름차순, DESC : 내림차순
  - NULL은 무한대의 값으로 간주되어 ASC의 경우 마지막에 DESC의 경우 처음에 표시됨
 

##### 단일행 함수 : 여러 행에 대해 각각 적용되어 결과를 반환하는 함수로 문자형, 숫자형, 날짜형, 변환형, NULL관련 함수들이 있으며 SELECT, WHERE, ORDER BY절에서 사용가능
##### 다중행 함수 : 여러 행을 입력으로 받아 단일 값을 결과로 반환하는 함수로 그룹함수, 집계함수, 윈도우함수 등이 있음
##### 그룹 함수 : 여러 행의 데이터를 가지고 한 번에 처리하여 단일 값을 반환하는 함수로 복수행 함수, 집계함수라고도 부른다. 일반적으로 NULL값을 제외하고 처리하며 WHERE절에는 사용할 수 없음, AVG, MAX, MIN, SUM, COUNT함수 등
##### 집합 연산 : 여러 개의 SELECT 쿼리 결과를 하나의 쿼리로 합쳐주는 연산. UNION(합집합-중복제거), UNION ALL(합집합-중복포함), INTERSECT(교집합), EXCEPT(예외), MINUS(차집합)

#### 서브쿼리 : 쿼리 안에 존재하는 쿼리로 여러가지 형태가 있음
* 서브쿼리는 메인쿼리에 종속적임. Join은 조인에 참여하는 모든 테이블이 대등한 관계에 있기 때문에 어느 위치에서라도 모든 테이블의 컬럼을 자유롭게 사용 가능함. but, 서브쿼리는 메인쿼리의 컬럼을 사용가능하지만 그 반대는 x
* SELECT절 서브쿼리 : ``` SELECT 컬럼명, ( SELECT 컬럼명 FROM 테이블명 WHERE 조건식...) FROM 테이블명 ...```
* FROM절 서브쿼리(인라인뷰) : 서브쿼리가 FROM절에서 사용되면 뷰처럼 결과가 동적으로 생성된 테이블로 사용할 수 있음(데이터베이스에는 저장되지 않음), 인라인뷰로 생성된 테이블이기 때문에 인라인뷰의 칼럼은 자유롭게 참조가 가능함.
  ```SELECT 컬럼명 FROM 테이블명, (SELECT 컬럼명 FROM 테이블명 WHERE 조건식..) WHERE 조건식... ```
* WHERE절 서브쿼리 :```SELECT 컬럼명 FROM 테이블명 WHERE 조건식 and (SELECT 컬럼명 FROM 테이블명 WHERE 조건식...)...```



