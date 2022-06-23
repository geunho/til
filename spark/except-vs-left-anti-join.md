---
author: "Kim, Geunho"
date: 2022-06-23
title: "except vs left-anti join"
---

except는 집합에서 빼기 연산에 해당한다. `a except b`를 수행하면 a에서 두 데이터셋이 동일한 레코드를 제외해서 결과를 반환한다.
이를 위해 레코드의 모든 컬럼 값에 대한 해시파티셔닝을 수행한다.

실행 계획을 보면 내부적으로 left-anti join으로 실행 계획을 잡고 계산한다.

```scala
scala> val A = Seq(1,2,3,4,5).toDF
A: org.apache.spark.sql.DataFrame = [value: int]

scala> val B = Seq(3,5).toDF
B: org.apache.spark.sql.DataFrame = [value: int]

scala> val R = (A except B)
R: org.apache.spark.sql.Dataset[org.apache.spark.sql.Row] = [value: int]

scala> R explain
== Physical Plan ==
AdaptiveSparkPlan isFinalPlan=false
+- HashAggregate(keys=[value#279], functions=[])
   +- Exchange hashpartitioning(value#279, 200), ENSURE_REQUIREMENTS, [id=#370]
      +- HashAggregate(keys=[value#279], functions=[])
         +- BroadcastHashJoin [coalesce(value#279, 0), isnull(value#279)], [coalesce(value#284, 0), isnull(value#284)], LeftAnti, BuildRight, false
            :- LocalTableScan [value#279]
            +- BroadcastExchange HashedRelationBroadcastMode(List(coalesce(input[0, int, false], 0), isnull(input[0, int, false])),false), [id=#366]
               +- LocalTableScan [value#284]
```

브로드캐스트 해시 조인으로 처리됨에도 동일 레코드를 제거하기 위해 해시파티셔닝해서 셔플링이 발생하는 것을 볼 수 있다.
