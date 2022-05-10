---
author: "Kim, Geunho"
date: 2022-02-07
title: "null 처리시 주의 사항"
---

null이 올 수 있는 컬럼은 다른 컬럼과 연산시 항상 주의해야 한다.

```scala
scala> spark.sql("SELECT 1 + null, 1 - null, 1 * null, 1 / null") show
warning: there was one feature warning; re-run with -feature for details
+-----------------------+-----------------------+-----------------------+------------------------------------------+
|(1 + CAST(NULL AS INT))|(1 - CAST(NULL AS INT))|(1 * CAST(NULL AS INT))|(CAST(1 AS DOUBLE) / CAST(NULL AS DOUBLE))|
+-----------------------+-----------------------+-----------------------+------------------------------------------+
|                   null|                   null|                   null|                                      null|
+-----------------------+-----------------------+-----------------------+------------------------------------------+
```

위 예시는 사칙연산 결과를 보여주는데 null 연산시 모두 null을 반환한다.
이를 방지하기 위해서 `coalesce` 함수를 활용해서 적절히 초기화할 필요가 있다.
 
```scala
scala> spark.sql("SELECT 1 + coalesce(null, 0), 1 - coalesce(null, 0), 1 * coalesce(null, 1), 1 / coalesce(null, 1)") show
warning: there was one feature warning; re-run with -feature for details
+------------------------------------+------------------------------------+------------------------------------+--------------------------------------------------------------------+
|(1 + coalesce(CAST(NULL AS INT), 0))|(1 - coalesce(CAST(NULL AS INT), 0))|(1 * coalesce(CAST(NULL AS INT), 1))|(CAST(1 AS DOUBLE) / CAST(coalesce(CAST(NULL AS INT), 1) AS DOUBLE))|
+------------------------------------+------------------------------------+------------------------------------+--------------------------------------------------------------------+
|                                   1|                                   1|                                   1|                                                                 1.0|
+------------------------------------+------------------------------------+------------------------------------+--------------------------------------------------------------------+
```

사실 spark에서 뿐만 아니라 rdb에서도 동일하다.

```sql
/* pg */
SELECT 1 + null, 1 - null, 1 * null, 1 / null
+--------+--------+--------+--------+
|?column?|?column?|?column?|?column?|
+--------+--------+--------+--------+
|NULL    |NULL    |NULL    |NULL    |
+--------+--------+--------+--------+

SELECT 1 + coalesce(null, 0), 1 - coalesce(null, 0), 1 * coalesce(null, 1), 1 / coalesce(null, 1)
+--------+--------+--------+--------+
|?column?|?column?|?column?|?column?|
+--------+--------+--------+--------+
|1       |1       |1       |1       |
+--------+--------+--------+--------+
```

그러하다..