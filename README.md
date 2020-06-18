# DB Note
DB의 효율성을 위한 방법, 쿼리 설계 및 Tip 정리  



# 효율적인 쿼리를 기록


## 색인기능, INDEX란

- 전체 테이블을 뒤져서 찾는 FULL-TABLE SCAN (별로)

- 그래서 `인덱스를 이용한 색인을 쓰자!`

- 특정 순서로 정렬된 테이블 내용에 대한 포인터를 포함하는 데이터 구조

- 페이지의 행과 유사하다.

- 쿼리의 WHERE 절, JOIN 절 또는 ORDER BY 절에 사용 된 열에 인덱스가 있으면 쿼리 성능 향상 


-인덱스는 열에 따라 테이블의 행을 정렬하여 읽기 쿼리 속도를 높이는 방법

-테이블의 모든 행을 검사하는 대신 서버는 인덱스에 대해 2 진 검색을 수행


### 장점 

행 수가 많을수록 성능 업업!! 
 

### 단점

쓰기 속도와 데이터베이스 크기

색인을 저장하면 공간이 필요합니다

INSERT, UPDATE => 색인 업데이트

그래도 `장점이 더 크다!!`


## INDEX는 양날의 검이다.

조회는 빨라지지만 반대로 등록은 느리다. 

## 프로시져 

저장 프로시저 또는 stored procedure는 일련의 쿼리를 

마치 하나의 함수처럼 실행하기 위한 쿼리의 집합니다.

이를 활용하면 쿼리전에 제약조건을 걸거나, 

쿼리 후에 함수를 실행하거나 하는 등의 행위가 가능하다.

실행의 개념 분리된다.


## 반정규화를 사용해서 성능을 잡자!

정규화가 심해지면 조인이 발생하면서 성능이 저하

릴레이션, 속성 역정규화 (릴레이션 = 테이블)

단 모든 정규화가 끝나고 고려하기 

데이터의 정합성에는 영향주지 않기 

# 주의 사항 

##### SELECT문으로 조회하는 것도 서버를 쓰기 때문에 돈이다.

##### 트래픽 과부하가 걸리게 된다. 

## 트렌젝션은 롤백을 가진다.

데이터가 모두 성공할때까지 기다린다.(메모리를 가지고 있는다.)

데이터를 살릴 수 있지만 메모리를 독차지 하기도 한다.

## 쿼리가 자바보다 빠르다 

자바 for 문으로 1만개 업데이트문 

업데이트 Select 

두 개의 차이는 어마어마하다.

단.. 정렬 같은 경우는 빼자 

간단하게 말하면 쿼리 요청이 곧 시간 

쿼리 요청을 생각하자!

## WHERE 비교 >< 

1개와 n개의 차이는 엄청 난다.

10만개 데이터 기준 

Select ~ ~ WHERE Adt >'201001'   - 0.1초
Select ~ ~ WHERE Adt >'201001' AND Bdt >'201001'   - 2초


# 쿼리 설계 및 Tip

## Group By는 확실히 

## WHERE 1=1을 쓰자

- `동적 쿼리를 사용할 때 조건문을 줄이기 위해서이다.`

- SELECT * FROM user;

- SELECT * FROM user WHERE 1=1;

- 둘의 결과는 동일하다.

- 하지만 동적 쿼리를 사용해 조건절에 다른 조건을 줘야 하는 경우는 후자가 더 좋다.


### &lt, &gt 

&lt 부등호(<)
&gt 부등호(>)

### 코드값 비교시에는 

ncode = '001' 보다는 ncode <> '001' 을 쓰자! 

### char vs varchar 

고정, 가변

EX) 둘 다 10으로 설정시 

char는 나머지를 공백으로 varchar는 가변적으로 

### union과 select 여러번

비교

### order by의 속도를 줄이자

자체적 정렬코드가 빠르다 
편리함보단 효율성

### ESCAPE 옵션 
jig_igigi@naver.com 찾고 싶으면

WHERE EMAIL LIKE '_ _ _ _%'; (x)

WHERE EMAIL LIKE '_ _ _#_%' ESCAPE '#'(o) 

### 새 컬럼 추가시 

기존 컬럼을 변경 안하는 선에서 

새 칼럼을 살리자 


### UPDATE 문을 잘쓰자!

update 문으로 쿼리를 줄일 수 있다.
효율적으로 쓰자!

### UPDATE도 최대한 범위를 줄이자 

5000개의 데이터 중 4990개가 0 나머지가 아닐경우 
모두 0으로 만들때
5000개를 0으로 바꾸기 보단 나머지 10개를 조건을 줘서
수정하자!

### ORACLE TOP N 방법

SELECT TOP 100 * FROM Table A  

SELECT * FROM Table A WHERE ROWNUM <100 

###  WHEREin 

조건에서 어떤거 포함이다.
Where A = '1 ' AND ~~~ (x)
A in ('1', '2')

### LEFT OUTER JOIN

SELECT 
* 
FROM 테이블1 A
LEFT OUTER JOIN 테이블2 B
ON A.no = B. no

### Cross Join

크로스 조인은 두개의 테이블에서 CATESIAN PRODUCT 연산의 결과를 출력

CATESIAN PRODUCT란 행과 행의 나열하는 결과를 보여주는 것

A 테이블      B 테이블
   1             4
   2             5
   3             6 
   
   크로스 조인시 
   
   14 
   15 
   16 
   24
   25
   26
   34 
   35 
   36


## DECODE 

sql 일종의 if - else if - else 문

## 2번째 글짜를 검색조건 

name LIKE '_T%'

## 문자열 함수

UPPER : 대문자
LOWER : 소문자
INITCAP : 처음만 대문자

## STUFF

해당 범위의 문자를 대체할때 좋다.

## CASE WEHN THEN ELSE END 

SELECT CASE WHEN num <10 THEN 'OK'
       WHEN num =10 THEN 'Good'
       ELSE 'Bad' END
FROM Number 

## 날짜 가공 팁 

3번째 인자값에 1~ 넣어주면 정해지 포맷대로 나옴 

SELECT시 사용하기 좋은 경우

CONVERT(VARCHAR,[RegiDate], 20)

INSERT시 사용하기 좋은 경우

CONVERT(VARCHAR,GETDATE(), 1)

## 마지막 날짜

yyyy= 2020 
mm= 04
Date(yyyy,mm-1,1)// 2020 04 01

Date(yyyy,mm-1,0)// 2020 03 31 

Date(yyyy,mm,0)// 2020 04 31 // 찾고자하는 마지막 날짜..



### interect

공통으로 나오는 하나의 부분을 검색하기위해서 

SELECT igno FROM DEPT
interect
SELECT igno FROM EMP

### 쿼리는 길다고 나쁜게 아니다

### NOT IN, IN 을 잘쓰자 

A.idx NOT IN ('007','008','009','010')

### WHERE절에 CASE문 
CASE WHEN  A.pidx IN ('002','003') THEN '사람'  
				     ELSE '동물'
				END group,

### 백업하는법 
CREATE TO 로 변경테이블을 기존테이블 _ 날짜로 생성

INSERT INTO 변경테이블 SELECT * FROM 기존테이블

## ROUND, TRUNC

조건이 양수이면 소숫점, 음수이면 정수자리 
반올림
버림

## sql != 사용불가

단순 한정어(predicate)은 =, <>, >, >=, <, <=, IN, BETWEEN, LIKE, IS NULL 만 사용가능

## 엑셀 일괄 조건부 서식

=D2:D1001&"**"

## MongoDB

몽고디비의 키값은 존재하는가? X

아이디(_id)는 기본키이며 ObectId라고 한다. ObjectId는 고유한 값으로 자동으로 생성되며 크기는 12바이트이다.

자체적인 일련번호를 달 수 도 있다.

# ORACLE, SQL 함수 비교

length , len

substr , substring

LPAD , replicate 

instr, charindex

# 쿼리연습 

### WHERE 서브쿼리 

##### SMITH 사원과 같은 부서(deptno)에 근무하는 사원의 empno, ename, deptno를 조회

```
SELECT empno, ename, deptno FROM EMP 
WHERE deptno IN (SELECT deptno
                          FROM EMP 
                          WHERE ename = 'SMITH');
```


##### JONES 사원보다 급여(sal)가 많은 사원의 empno, ename, sal을 조회하는 
```
SELECT empno, ename, sal FROM EMP 
WHERE sal < (SELECT sal
                          FROM EMP 
                          WHERE ename = 'JONES');
```

##### 	emp 테이블에서 empno, ename, hiredate, hiredate의 요일을 조회

```
SELECT empno,ename, hiredate TO_CHAR((TO_DATE(hiredate)),'day')
FROM emp
```


#####  emp 테이블에서 2월에 입사(hiredate)한 사원의 ename, hiredate를 조회.
```
SELECT ename,hiredate FROM emp
    WHERE TO_CHAR(hiredate, ‘MM’) = 02
```

##### 부분 업데이트 

```
UPDATE St_HMedia
SET ChargeQtyYN ='Y', ModifyUser ='jig', ModifyDate =GetDate()
WHERE HReaderNo in (Select HReaderNo From St_ChargeY)
```


##### INSERT INTO SELECT + 다른값

```
INSERT INTO A (
a,
b,
c
)  
SELECT 
B.a,
입력1,
Date()
FROM
B

입력1과 Date()는 아무 관련 없는값인데 그냥 B Select 조건 조회값에 넣으면 순서대로 알아서 똑똑하게 참,,, 

```
##### UNION ALL ORDER BY 사용 

A B 등 쿼리 명칭을 해야 구분을 함 

```
SELECT * FROM 

(Select TOP 100 * FROM St_HBoard WHERE RegiGu = '지국')A

UNION ALL 

SELECT * FROM 

(Select TOP 100 * FROM St_HBoard WHERE RegiGu != '지국' ORDER BY RegiDate)B
```


##### UNION ALL ORDER BY 정리

UNION ALL은 합치기 전 두 쿼리에 대해서 정렬을 무시해버린다. 

Select * FROM A ORDER BY A.idx UNION ALL Select * FROM B ORDER BY B.idx (오류)

```
Select * FROM A ORDER BY A.idx UNION ALL Select * FROM B ORDER BY B.idx (오류)

// 편법으로 바꿔 버리자 (성공, mssql 빼고)
=> Select * FROM (SELECT * FROM A ORDER BY A.idx) 
UNION ALL 
Select * FROM (Select * FROM B ORDER BY B.idx) 

// mssql은 위에 쿼리를 인식하지 못한다. 

//TOP, OFFSET 또는 FOR XML을 함께 지정하지 않으면 뷰, 인라인 함수, 파생 테이블, 
하위 쿼리 및 공통 테이블 식에서 ORDER BY 절을 사용할 수 없습니다.

// 서브쿼리 부분에 ORDER BY가 있으면 해당 쿼리로 바꿔주자.!!
SELECT * FROM => SELECT TOP (SELECT count(*) FROM St_HBoard) * 


```
##### MSSQL UPDATE SELECT 

```
// 특이하다... ! 

UPDATE A
 SET A.JobTitle = B.UserJobTitle
FROM BackDatabase.dbo.DATTACHINFO A, Appro.dbo.ATTACHINFO B
where A.DocID=B.DocID
and A.FileSN=B.FileSN 

```

##### UPDATE + SELECT + GROUP BY + SUM

```
 UPDATE [변경 테이블] 

SET
     [SalePayAmt] = B.SalePayAmt

FROM 
변경 테이블 A, 
(SELECT Company,Date,SUM(price) AS price FROM 입금테이블 GROUP BY Company,Date) B

WHERE A.Date = B.Date
AND A.Company = B.Company 
AND A.CloseYM ='201606'
AND A.price != B.price

```

##### SELECT + GROUP BY + SUM

```

SELECT a.BfPAmt, a.totPamt, a.ChargePAmt, a.SalePAmt,
SUM(b.chargePamt) b_chargepamt, SUM(b.salepamt) b_salepamt, SUM(b.chargepamt+b.salepamt) b_hap,
 a.totpaamt - (sum(b.chargepamt+b.salepamt))  -a.TotJanAmt cha,
 a.ChargePAmt+a.SalePAmt - (sum(b.chargepamt+b.salepamt))  as cha2,
  a.totpayamt - (sum(b.chargepayamt+b.salepayamt))   cha3,

 a.BfPAmt - (SUM(b.chargepamt+b.salepamt)) b_cha

 FROM Mon a, Pay b

WHERE a.YM =  b.YM
  and a.JCd = b.JCd
  and a.YM = '202002'

  GROUP BY  a.JCd, a.BfPAmt, a.totpamt, a.ChargePAmt, a.SalePAmt, a.JCd,a.TotJAmt 
  
  ORDER BY b_cha, cha, cha2, cha3

```

##### 하나의 컬럼 특정 단어로 잘라서 저장 charindex

```
A = 123index144444width255555height 
substring(A, CAST(CHARINDEX('width', A)+4 AS INT), CAST(CHARINDEX('height', A) AS INT) - CAST(CHARINDEX('width', A)+4 AS INT) ) AS 추출컬럼,

```
