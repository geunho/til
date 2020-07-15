---
author: "Kim, Geunho"
date: 2020-07-15
title: "Join 성능 높이기" 
---

Spark의 join 연산은 전체 dataset의 sort와 shuffle을 수행하기 때문에 매우 값 비싼 연산이다.

join 연산 성능을 올리는 것은 sort, shuffle의 양을 얼마나 줄이는지가 관건이다.

* 대상 dataset이 한 프로세스 내에서 수행될 수 있도록 동일한 개수와 join key로 re-partitioning 한다.
* join 후 사용할 컬럼을 join전 미리 select 한다.
* join 후 버려질 로우를 join전 미리 filtering 한다.
* 컬럼 수가 많은 dataset부터 먼저 join 한다.
* broadcast join 연산은 가장 마지막에 수행한다. (join 전 연산시 broadcast join으로 인해 증가한 데이터량만큼 느려지게 된다.)
* `explain`으로 실행 계획을 확인하여 `Sort` 연산이 가장 적게 나오는 방향으로 join 순서를 조정한다. (Catalyst가 최적화하면서 일반 join을 broadcast join으로 변경하는 경우가 있는데 이 연산이 다른 join 사이에 있다면 `Sort`가 한번 더 발생하게 된다)
* parquet 같은 파일 포맷을 사용한다면 조건절이 pushdown되어 실행 계획에서 `PushedFilters`까지 탔는지 확인한다. UDF 연산은 pushdown 되지 않고, 따라서 사용이 권장되지 않는다.
* 서로 다른 key에 대한 다중 broadcast join은 대상 dataset 크기가 작더라도 전체 연산 속도를 매우 더디게 한다. 이러한 경우 `Map<K, V>`으로 변환 후 broadcast variable로 등록하고 RDD API를 이용하는 것이 나을 수 있다.
