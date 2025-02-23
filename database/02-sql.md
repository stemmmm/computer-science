# SQL

### [문제 풀이](https://leetcode.com/studyplan/top-sql-50/)

<details>
<summary><h3>NULL을 일반 비교 연산자로 비교할 수 없는 이유에 대해 설명하세요.</h3></summary>

- NULL은 unknown, unavailable, not applicable의 의미를 가질 수 있어 단일한 값으로 취급하기 어려움
- 즉, 같은 NULL이라 하더라도 그 의미가 다를 수 있으므로 일반 비교 연산자로 비교할 경우, 비교 결과는 TRUE나 FALSE가 아니라 UNKNOWN이 됨
- 이렇듯 SQL은 비교/논리 연산의 결과로 TRUE, FALSE, UNKNOWN을 반환할 수 있으며, 이를 Three-valued logic이라 부름
- 따라서 WHERE 절 등에서 NULL 값을 올바르게 비교하기 위해서는 IS NULL 또는 IS NOT NULL 연산자를 사용해야함

<details>
<summary><h4>WHERE 절에서 NOT IN 연산 사용 시, NULL 값으로 인해 발생할 수 있는 문제와 이를 해결할 수 있는 방법을 설명하세요.</h4></summary>

```sql
select * 
from employees
where department_id not in (select department_id from departments);
```

- 만약 `departments` 테이블의 `department_id` 컬럼에 NULL 값이 하나라도 포함되어 있다면, 비교 과정에서 `employee.department_id = NULL`이 되며 UNKNOWN을 반환함
- 이로 인해, NOT IN 조건 전체가 UNKNOWN이 되어 원래 필터링 하려던 데이터가 전혀 반환되지 않을 수 있음
- 이를 해결하기 위해서는 서브쿼리 결과에 NULL이 포함되지 않도록 조건을 추가하거나(`where department_id is not null`), NOT IN 대신 NOT EXISTS 연산을 사용할 수 있음
</details>
</details>

<details>
<summary><h3>CROSS JOIN, SELF JOIN, NATURAL JOIN에 대해 설명하세요.</h3></summary>

#### CROSS JOIN
- 두 테이블의 카티지언 프로덕트를 반환함
- 두 테이블의 행 수가 많을 경우 결과 행 수가 기하급수적으로 증가할 수 있음

```sql
select A.*, B.* 
from TableA A
cross join TableB B;
```

<br>

#### SELF JOIN
- 동일한 테이블을 조인하여 테이블 내의 관계나 계층 구조를 조회할 때 사용됨

```sql
select E1.employee_id, E1.name, E2.name manager_name
from employees E1
left join employees E2 on E1.manager_id = E2.employee_id;
```

<br>

#### NATURAL JOIN
- 두 테이블에서 동일한 이름의 컬럼을 기준으로 자동 조인 조건을 생성하여 조인함
- 명시적으로 조인 조건을 작성하지 않아 의도치 않은 컬럼들이 매칭 조건으로 사용될 수 있음
- 따라서 명시적 조인을 사용하는 것이 권장됨

```sql
select *
from TableA
natural join TableB;
```
</details>

<details>
<summary><h3>UNION과 UNION ALL의 차이에 대해 설명하세요.</h3></summary>

#### UNION
- 여러 SELECT 문의 결과를 합친 후, 중복된 행을 제거한 하나의 결과 집합을 반환함

#### UNION ALL
- 여러 SELECT 문의 결과를 그대로 결합하여 반환함
- 중복된 행도 제거하지 않고 모두 포함함
- 중복 제거 과정이 없으므로 일반적으로 UNION에 비해 더 빠름 
</details>

<details>
<summary><h3>SQL의 개념적 실행 순서를 설명하세요.</h3></summary>

1. FROM
2. WHERE
3. GROUP BY
4. HAVING
5. SELECT
6. DISTINCT
7. ORDER BY
8. LIMIT, OFFSET
</details>

<details>
<summary><h3>CTE(Common Table Expression)에 대해 설명하세요.</h3></summary>

- WITH 키워드를 사용하여 정의하는 임시 결과 집합
- 하나의 쿼리 내에서만 재사용할 수 있음
- 재귀 쿼리도 지원함

```sql
with ActiveEmployees as (
    select employee_id, manager_id, name
    from employees
    where active = 1
)
select *
from ActiveEmployees
where manager_id is not null;
```
</details>

<details>
<summary><h3>SQL Injection에 대해 설명하세요.</h3></summary>

- 애플리케이션에서 사용자 입력을 검증이나 이스케이프 없이 바로 SQL 쿼리에 포함시킬 때 발생하는 보안 취약점
- Java에서는 `PreparedStatement` 등을 사용하여 SQL Injection 공격을 예방할 수 있음
</details>

<details>
<summary><h3>Stored Function과 Stored Procedure의 차이에 대해 설명하세요.</h3></summary>

#### Stored Function
- DB에 저장되어 SQL 문 내에서 호출할 수 있는 사용자 정의 함수
- 주로 단순 연산이나 데이터 변환 등 단순한 작업에 적합함 

#### Stored Procedure
- DB에 저장하여 실행할 수 있는 사용자 정의 프로그램
- SQL 문 내에서 직접 호출할 수 없음

<details>
<summary><h4>Stored Procedure의 장단점에 대해 설명하세요.</h4></summary>

##### 장점
- 추상화: 비즈니스 로직을 stored procedure 내부에 캡슐화하고 애플리케이션에서는 호출만 하면 됨 
- 재사용성: 여러 애플리케이션 서비스에서 하나의 stored procedure를 재사용할 수 있음
- 네트워크 트래픽: 여러 SQL 문을 하나의 stored procedure로 묶어 실행함으로써 클라이언트와 DB 서버 간의 통신 횟수를 줄일 수 있음
- 보안: 민감 정보에 직접 접근하지 않고 stored procedure를 통해 접근하도록 함으로써, DB 권한 관리 및 접근 제어 가능

##### 단점
- 추상화: 비즈니스 로직이 여러 계층에 분산되어 있으면 전체 시스템의 유지보수나 디버깅이 어려워짐
- 재사용성: 잘못된 재사용은 여러 시스템 간의 결합도를 높여 하나의 변경이 광범위한 영향을 미칠 수 있음
- 네트워크 트래픽: 애플리케이션 레벨에서 여러 쿼리를 묶어 요청하거나, 캐시를 활용하는 방법으로도 네트워크 트래픽을 최적화할 수 있음
- 보안: 민감 정보에 대한 접근을 완벽히 제한하기는 어려움
- DB 부하: 모든 로직을 DB 서버에 집중시키면, DB 서버에 부하가 몰려 전체 시스템 성능에 영향을 줄 수 있음
</details>
</details>

<details>
<summary><h3>Trigger에 대해 설명하세요.</h3></summary>

- DB에서 특정 이벤트(`INSERT`, `UPDATE`, `DELETE` 등)가 발생할 때 자동으로 실행되는 stored procedure

<details>
<summary><h4>Trigger 사용 시 발생할 수 있는 문제에 대해 설명하세요.</h4></summary>

- 숨겨진 로직: 애플리케이션 코드 상에서 trigger 내부 로직을 알 수 없어 어떤 작업이 수행되는지 파악이 어렵고, 이로 인해 문제가 발생했을 때 디버깅이 어려움
- 복잡성 증가: 여러 trigger가 존재하면 데이터 변경 흐름을 파악하기 어려워지고, 심한 경우 순환 참조가 발생할 수 있음
- 성능 문제: 과도한 trigger 사용은 DB에 부담을 주고 응답 속도를 느리게 할 수 있음
</details>
</details>

<details>
<summary><h3>View에 대해 설명하세요.</h3></summary>

- DB에서 하나 이상의 테이블에 대한 `SELECT` 쿼리 결과를 저장하는 가상의 테이블
- 실제 데이터를 저장하지 않고 테이블의 데이터를 실시간으로 조회함
- 테이블에 직접 접근하지 않고 필요한 데이터만 노출함으로써, 복잡한 쿼리 결과를 단순화하고 민감 정보를 보호할 수 있음
- 대부분의 view는 읽기 전용으로 사용됨
</details>