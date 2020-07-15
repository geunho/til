---
author: "Kim, Geunho"
date: 2020-07-07
title: "Dataset - Broadcast Join"
---

Spark 2의 Dataset join시 broadcast join hint를 주는 방법은 다음과 같다:

```scala
product.join(broadcast(category), "cat_id")
...
```
join 대상인 dataset을 `broadcast()` 함수로 감싸주면 된다.

SparkContext로 부터 등록하는 `sc.broadcast(category)` 다음과 같은 코드는, join절에서 hint 없이 사용하게 되면 일반적인 SortMergeJoin으로 처리되므로 주의한다.
```scala
val cat = sc.broadcast(category)
product.join(cat.value, "cat_id") // 주의: SortMergeJoin으로 처리!
```