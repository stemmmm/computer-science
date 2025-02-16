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

---
- Stored Function, Stored Procedure, Trigger, View