# 트랜잭션

<details>
<summary><h3>트랜잭션에 대해 설명하세요.</h3></summary>

- 여러 개의 SQL 문을 하나의 논리적 작업 단위로 묶어 실행하는 것
- 이렇게 함으로써 DB에 대한 일련의 변경 작업이 모두 성공하거나, 하나라도 실패할 경우 전체 작업을 취소하여 데이터 무결성을 유지할 수 있음

<details>
<summary><h4>트랜잭션과 관련된 SQL 문법에 대해 설명하세요.</h4></summary>

##### `START TRANSACTION`
- 트랜잭션의 시작을 알리는 명령어
- 이 명령어가 실행되면 DB는 autocommit 모드를 자동으로 비활성화하며, 이후 실행되는 SQL 문들을 하나의 트랜잭션 단위로 묶음

##### `COMMIT`
- 트랜잭션 내에서 수행한 모든 작업을 DB에 영구적으로 반영
- 모든 작업이 성공적으로 완료되었을때 호출하여 트랜잭션을 종료함

##### `ROLLBACK`
- 트랜잭션 내의 모든 작업을 취소하고 트랜잭션 시작 이전의 상태로 복구함
- 오류나 예외 상황 발생 시 호출하여 트랜잭션을 종료함

##### `AUTOCOMMIT`
- 대부분의 DBMS에서는 기본적으로 각 SQL 문을 자동으로 하나의 트랜잭션으로 처리하여 성공 시 commit하고, 실패 시 rollback 함
- 단, `START TRANSACTION`을 사용해 명시적으로 트랜잭션을 시작하는 경우 autocommit 모드는 비활성화됨 
</details>
</details>

<details>
<summary><h3>트랜잭션의 특성인 ACID에 대해 설명하세요.</h3></summary>

#### Atomicity(원자성)
- 트랜잭션 내의 모든 작업은 하나의 단위로 실행되며, 전부 성공하거나 전부 실패해야 함
- 만약 한 작업이라도 실패하면, 이전에 수행된 작업들까지 모두 취소되며 트랜잭션 전체가 롤백됨(all-or-nothing)

#### Consistency(일관성)
- 트랜잭션 실행 전후에 DB는 모든 제약 조건과 규칙을 만족해야 함
- 이를 통해 트랜잭션이 데이터 무결성을 해치지 않고, 항상 올바른 상태로 DB를 유지할 수 있음

#### Isolation(격리성)
- 동시에 실행되는 여러 트랜잭션은 서로 간섭 없이 독립적으로 처리되어야 함
- 한 트랙잭션의 중간 결과가 다른 트랜잭션에 노출되지 않아 데이터 불일치를 방지함

#### Durability(지속성)
- 트랜잭션이 성공적으로 커밋된 후에는 시스템 장애나 오류가 발생하더라도 그 결과가 영구적으로 저장되어야 함
- DB는 로그나 백업 등의 매커니즘을 통해 이 특성을 보장함
</details>

<details>
<summary><h3>Schedule에 대해 설명하세요.</h3></summary>

#### 정의
- 여러 트랜잭션이 동시에 실행될 때, 각 트랜잭션에 속한 연산(operation)들이 실행되는 순서

<br>

#### 종류
##### Serial Schedule
- 트랜잭션이 겹치지 않고 순차적으로 실행되는 스케줄
- 동시성 문제는 발생하지 않지만 성능 면에서 비효율적임

##### Non-Serial Schedule
- 트랜잭션이 겹쳐서 실행되는 스케줄
- 동시성 문제는 발생할 수 있지만, 동시에 더 많은 트랜잭션을 처리할 수 있어 성능이 향상됨

<br>

##### Unrecoverable Schedule
- 한 트랜잭션이 다른 트랜잭션이 write한 데이터를 읽은 후, 원본 트랜잭션이 롤백되었는데도 읽은 트랜잭션이 커밋되는 경우
- 이 경우, 롤백된 데이터에 의존한 데이터를 커밋하게 되어 DB를 이전 상태로 완전히 복구하기 어려울 수 있으므로 허용되지 않음

##### Recoverable Schedule
- 한 트랜잭션이 다른 트랜잭션이 write한 데이터를 읽은 경우, 읽은 트랜잭션은 해당 데이터의 원본 트랜잭션이 커밋되거나 롤백될 때까지 자신도 커밋하지 않는 경우
- 이 경우, 원본 트랜잭션이 나중에 롤백되더라도 읽은 트랜잭션의 커밋이 지연되므로 데이터의 일관성을 보장할 수 있음

<br>

##### Cascadeless Schedule
- 어떤 트랜잭션도 커밋되지 않은 트랜잭션이 write한 데이터를 읽지 않도록 하는 스케줄

##### Strict Schedule
- 어떤 트랜잭션도 커밋되지 않은 트랜잭션들이 write한 데이터는 읽거나 쓰지 못하도록 하는 스케줄
</details>

<details>
<summary><h3>Conflict에 대해 설명하세요.</h3></summary>

#### 정의
- 두 연산이 다음 세 가지 조건을 모두 만족할 경우 두 연산이 충돌(conflict)한다고 함
1. 서로 다른 트랜잭션에 속함
2. 동일한 데이터에 접근함
3. 최소 하나는 write 연산임

#### Conflict Equivalent
- 두 스케줄이 동일한 트랜잭션 집합을 가지며, 모든 충돌 연산의 순서가 양쪽 스케줄에서 동일한 경우

#### Conflict Serializable
- Serial 스케줄과 conflict equivalent한 경우
- 트랜잭션들이 동시에 실행되어도 결과적으로 serial 스케줄과 동일하므로 non-serial 스케줄이어도 동시성 문제가 발생하지 않음
- DBMS는 여러 트랜잭션을 동시에 실행해도 스케줄이 conflict serializable 하도록 보장하는 프로토콜을 적용함
</details>

<details>
<summary><h3>Serializability에 대해 설명하세요.</h3></summary>

- 여러 트랜잭션이 동시에 실행되어도 결과가 serial schedule과 동일하도록 보장하는 성질
- Conflict equivalent 관계를 통해 non-serial 스케줄에서도 안전한 동시 실행이 가능함
</details>

<details>
<summary><h3>Recoverability에 대해 설명하세요.</h3></summary>

- 트랜잭션 간의 의존성에 의해 발생할 수 있는 복구 문제를 방지하는 개념
- 한 트랜잭션이 다른 트랜잭션의 write 작업에 의존하는 경우, 원본 트랜잭션이 커밋되거나 롤백될 때까지 의존하는 트랜잭션이 커밋되지 않도록 하여 데이터의 일관성을 보장함
</details>

<details>
<summary><h3>트랜잭션 격리 수준에 대해 설명하세요.</h3></summary>

- [트랜잭션 격리 수준](https://stemmm.tistory.com/4)
</details>

<details>
<summary><h3>Snapshot Isolation에 대해 설명하세요.</h3></summary>

- [스냅샷 격리](https://stemmm.tistory.com/5)
</details>