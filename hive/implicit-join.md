---
author: "Kim, Geunho"
date: 2020-06-16
title: "Hive - implicit join 특성"
---

implicit join은 SQL 구문에서 join절을 직접 쓰지 않고 문자 그대로 암시적 구문으로써 join 하는 것을 뜻한다.

INNER JOIN
```sql
-- INNER JOIN
SELECT a.id, b.name
FROM a 
INNER JOIN b 
ON a.id = b.id

-- IMPLICIT INNER JOIN
SELECT a.id, b.name
FROM a, b
WHERE a.id = b.id
```

implicit join은 두 번째 쿼리와 같이 대상 테이블을 `FROM`절 이후 콤마`,`로 구분해서 나열하며,
`WHERE`절에 join 조건을 달게 된다.

두 쿼리가 매우 유사하지만, implicit join에서 이 `WHERE`절 조건에 따라 어떤 join이 될지 달라지게 된다.
예를 들어, `WHERE`절을 제외하면 `CROSS JOIN`([Cartesian product](https://en.wikipedia.org/wiki/Cartesian_product))이 된다.

Hive에서는 0.13.0 버전부터 implicit join을 제공한다. 위 쿼리에 이어서 각각의 join절은 implicit join절로 변경할 수 있는지 살펴본다.


CROSS JOIN 
```sql
-- CROSS JOIN 
SELECT a.id, b.name
FROM a 
CROSS JOIN b 

-- IMPLICIT CROSS JOIN
SELECT a.id, b.name
FROM a, b
```

LEFT OUTER JOIN
```sql
-- LEFT OUTER JOIN 
SELECT a.id, b.name
FROM a 
LEFT OUTER JOIN b 
ON a.id = b.id

-- IMPLICIT LEFT OUTER JOIN
SELECT a.id, b.name
FROM a, b
WHERE a.id = b.id OR b.id IS NULL
```

RIGHT OUTER JOIN 
```sql
-- RIGHT OUTER JOIN 
SELECT a.id, b.name
FROM a 
RIGHT OUTER JOIN b 
ON a.id = b.id

-- IMPLICIT RIGHT OUTER JOIN
SELECT a.id, b.name
FROM a, b
WHERE a.id = b.id OR a.id IS NULL
```
