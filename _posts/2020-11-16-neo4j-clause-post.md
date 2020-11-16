---
title: "[Cypher] Clause 친해 지기"
categories: neo4j, cypher 
---

Cypher Query clause와 친해져 보기 
---

## Cypher 란?

SQL-Like 선언형 쿼리 언어로, 그래프 모델에 특화된 쿼리 언어
![Cypher 예시 그림](/assets/images/Property-Graph-Cypher.jpg)


## Recommended naming conventions

|                    |                                                    |               |
| ------------------ | -------------------------------------------------- | ------------- |
| Node labels        | Camel-case, beginning with an upper-case character | :VehicleOwner |
| Relationship types | Upper-case, using underscore to separate words     | :OWNS_VEHICLE |



## Sample data Load
Neo4j에서 제공하는 샘플 데이터를 사용해서 진행 가능 
```cypher
:Play movies 
```

## DML Clauses

| Clause         | Description                                           | etc  |
| -------------- | ----------------------------------------------------- | ---- |
| MATCH          | 일치하는 패턴 찾기                                    |      |
| OPTIONAL MATCH | 패턴이 일치 하지 않으면 NULL 값으로 대체 되어 나옴    |      |
| WHERE          | MATCH, OPTIONAL MATCH, WITH 절의  결과를 필터링       |      |
| RETURN         | 쿼리의 결과를 리턴 ( 일반적인 SQL에서 select에 해당 ) |      |
| ORDER BY       | RETURN, WITH 뒤에 위치하여 출력 결과를 정렬           |      |

### Find node example 1

```cypher
MATCH (nineties:Movie) 
WHERE nineties.released >= 1990 AND nineties.released < 2000 
RETURN nineties.title
```

| nineties.title         |
| ---------------------- |
| "The Matrix"           |
| "The Devil's Advocate" |
| "A Few Good Men"       |
| "As Good as It Gets"   |
| "What Dreams May Come" |

### Find node example 2

```cypher
MATCH (nineties:Movie) 
WHERE nineties.released >= 1990 AND nineties.released < 2000 
RETURN nineties.title
ORDER BY nineties.title DESC
```

| nineties.title         |
| ---------------------- |
| "You've Got Mail"      |
| "When Harry Met Sally" |
| "What Dreams May Come" |
| "Unforgiven"           |
| "Twister"              |

### Find relation example 1

```cypher
MATCH (tom:Person {name: "Tom Hanks"})-[:ACTED_IN]->(tomHanksMovies) 
RETURN tomHanksMovies.title
```

### MATCH vs OPTIONAL MATCH
#### MATCH
```cypher
MATCH (a:Movie { title: 'The Matrix' })
MATCH (a)-[r:ACTS_IN]->()
RETURN a.title, r
```

| Result                   |
| ------------------------ |
| (no changes, no records) |

#### OPTIONAL MATCH

```cypher
MATCH (a:Movie { title: 'The Matrix' })
OPTIONAL MATCH (a)-[r:ACTS_IN]->()
RETURN a.title, r
```

| Result       |        |
| ------------ | ------ |
| a.title      | r      |
| "The Matrix" | null |


## DML Sub Clauses

| Clause  | Description                                        |                                                              |
| ------- | -------------------------------------------------- | ------------------------------------------------------------ |
| WITH    | 쿼리 결과를 다음 쿼리로 전달 ( 결과 임시 저장 )    |                                                              |
| UNWIND  | List, Map과 같이 Collect 결과를 개별 행으로 변환   |                                                              |
| FOREACH | UNWIND와 비슷 ( 데이터를 업데이트 하기 위한 용도 ) | - 변수의 scope는 괄호 안에서만 유효<br />- MATCH 는 사용 할 수 없음 |
| UNION   | 여러 쿼리 결과를 결합                              |                                                              |
| SKIP    | 출력행 건너뛰기                                    | ORDER BY 를 사용하지 않으면 순서 보장 안됨                   |
| LIMIT   |                                                    |                                                              |
| USE     | 사용하는 DB 선택                                   | 주로 Fabric DB에서 사용                                      |

- Fabric DB란?
  - Neo4j 4.0 에 추가된 feature로 여러개의 DB에 저장과 검색을 가능하게 해주는 Virture DB ( 다음 기회에.. )
    ![Cypher 예시 그림](/assets/images/fabric-single-instance.jpg) 

### WITH example

```cypher
MATCH (p:Person)-[:ACTED_IN]->(Movie {title: 'The Matrix' })
WITH p
ORDER BY p.name DESC LIMIT 1
MATCH (p)-[:ACTED_IN]-(m)
RETURN p.name, m.title
```

| p.name               | m.title                  |
| -------------------- | ------------------------ |
| "Laurence Fishburne" | "The Matrix Revolutions" |
| "Laurence Fishburne" | "The Matrix Reloaded"    |
| "Laurence Fishburne" | "The Matrix"             |

#### UNWIND example 1

```cypher
UNWIND [NULL] as x
RETURN x, 'some_literal'
```

| x      | 'some_literal' |
| ------ | -------------- |
| null | "some_literal" |

#### UNWIND example 2

```cypher
UNWIND [5] as x
RETURN x

UNWIND 5 as x
RETURN x
```

| x    |
| ---- |
| 5    |

#### UNWIND example 3

```cypher
UNWIND [1, 2, 3, NULL ] AS x
RETURN x, 'val' AS y
```

| x    | y       |
| :--- | :------ |
| 1  | "val" |
| 2  | "val" |
| 3  | "val" |
| null | "val" |


#### FOREACH example

```cypher
MATCH p = (tom:Person {name: "Tom Hanks"})-[:ACTED_IN]->(m:Movie) 
FOREACH (n IN nodes(p)| SET n.marked = TRUE )
```

| Result                                    |
| ----------------------------------------- |
| Set 24 properties, completed after 20 ms. |



#### UNION example
##### UNION ALL

```cypher
MATCH (n:Actor)
RETURN n.name AS name
UNION ALL 
MATCH (n:Movie)
RETURN n.title AS name
```

| name                |
| :------------------ |
| "Anthony Hopkins" |
| "Helen Mirren"    |
| "Hitchcock"       |
| "Hitchcock"         |

##### UNION

```cypher
MATCH (n:Actor)
RETURN n.name AS name
UNION
MATCH (n:Movie)
RETURN n.title AS name
```

| name                |
| :------------------ |
| "Anthony Hopkins" |
| "Helen Mirren"    |
| "Hitchcock"         |


#### SKIP example 1

```cypher
MATCH (m:Movie)
RETURN m.title
ORDER BY m.title
```

| m.title                 |
| ----------------------- |
| "A Few Good Men"        |
| "A League of Their Own" |
| "Apollo 13"             |
| "As Good as It Gets"    |
| "Bicentennial Man"      |

#### SKIP example 2

```cypher
MATCH (m:Movie)
RETURN m.title
ORDER BY m.title
SKIP 3
```

| m.title                |
| ---------------------- |
| "As Good as It Gets"   |
| "Bicentennial Man"     |
| "Cast Away"            |
| "Charlie Wilson's War" |

## DDL Clause

| Clause | Description                   |      |
| ------ | ----------------------------- | ---- |
| CREATE | Node, Relationships를 생성    |      |
| MERGE  | MATCH & CREATE ( SQL upsert ) |      |
| SET    | Property 설정                 |      |
| DELETE | Node, Relationships를 삭제    |      |
| REMOVE | Property, label 삭제          |      |

### CREATE example
##### Node

```cypher
CREATE (a { name: 'Andy' })
RETURN a.name
```

##### Relationships

```cypher
MATCH (a:Person),(b:Person)
WHERE a.name = 'A' AND b.name = 'B'
CREATE (a)-[r:RELTYPE { name: a.name + '<->' + b.name }]->(b)
RETURN type(r), r.name
```

#### MERGE example

```cypher
MERGE (michael:Person { name: 'Michael Douglas', bornIn: 'New Jersey' })
RETURN michael.name, michael.bornIn
// 결과 확인 ( bornIn property가 추가 되면서 새로운 노드가 생성)
MERGE (michael:Person { name: 'Michael Douglas' })
RETURN michael.name, michael.bornIn
```

| michael.name      | michael.bornIn |
| ----------------- | -------------- |
| "Michael Douglas" | null         |
| "Michael Douglas" | "New Jersey"   |

#### MERGE example

```cypher
MERGE (keanu:Person { name: 'Michael Douglas' })
ON CREATE SET keanu.created = timestamp()
ON MATCH SET keanu.lastSeen = timestamp()
RETURN keanu.name, keanu.created, keanu.lastSeen
```

### DELETE example

```cypher
MATCH (n:Person { name: 'Michael Douglas' })
DELETE n
```

### REMOVE example

```cypher
MATCH (michael:Person { name: 'Michael Douglas' })
REMOVE michael.bornIn
RETURN michael.name, michael.bornIn
```


## ETC Clause

| Clause             | Description              |                                                              |
| ------------------ | ------------------------ | ------------------------------------------------------------ |
| LOAD CSV           | CSV File load            | - 지정된 파일 위치 경로의 하위 디렉토리 만 가능              |
| CALL {} (subquery) | subQuery 실행            | - RETURN으로 끝나야함<br />- 바깥 쪽 쿼리의 변수와 동일 이름의 변수를 리턴 할 수 없음<br />- WITH를 통해 바깥쪽 변수를 참조 할 수 있음 |
| CALL procedure     | Procedure( plugin ) 실행 | - 기본 프로시저 이외에 plugin( jar )의 형태로 추가 할 수 있음 |


### LOAD CSV example

```
1,ABBA,1992
2,Roxette,1986
3,Europe,1979
4,The Cardigans,1992
```

```cypher
LOAD CSV FROM '{csv-dir}/artists.csv' AS line
CREATE (:Artist { name: line[1], year: toInteger(line[2])})
```

### CALL {} (subquery) example

```cypher
UNWIND [0, 1, 2] AS x
CALL {
  WITH x
  RETURN x * 10 AS y
}
RETURN x, y
```

| x    | y    |
| ---- | ---- |
| 0    | 0    |
| 1    | 10   |
| 2    | 20   |

- WITH 제약조건
  - 단순한 참조만 가능 
    - 지원 예시 : WITH x, y, z / WITH a AS b
    - 미지원 예시 : WITH a+1 as b  

### CALL procedure example

```cypher
CALL dbms.procedures() YIELD name, signature
WHERE name='dbms.listConfig'
RETURN signature
```

| signature                                                    |
| ------------------------------------------------------------ |
| "dbms.listConfig(searchString = :: STRING?) :: (name :: STRING?, description :: STRING?, value :: STRING?, dynamic :: BOOLEAN?)" |

