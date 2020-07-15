---
author: "Kim, Geunho"
date: 2020-07-08
title: "UDF - Broadcast Map과 UDF를 활용한 값 참조"
---

크기가 크지 않은 테이블은 broadcast join을 하는 대신, `(K, V)`의 Map 구조로 broadcast하고 `K`를 UDF에서 조회하여 `Column` 구문에서 활용할 수 있다.

방법은 다음과 같다.

```scala
// Category Dataset을 Map(cat_id, category) 형식으로 변환 후 broadcast 한다.
val category = ds.map(r => (r.cat_id, r))
                 .collect
                 .toMap
val cat = spark.sparkContext.broadcast(category)

// cat_id로 category를 반환하는 UDF 정의
def get_cat = udf[Category, String]((cat_id: String) => cat.value.getOrElse(cat_id, null))

// 사용
ds.select(
    $"id",
    $"cat_id",
    get_cat($"cat_id")("cat_nm")
)
```

`select` 내에서 사용한 `get_cat(String)` 메서드의 반환 결과는 `Column`이다. 이에 "cat_nm" 문자열로 접근하면 그에 대응하는 case class의 필드 값을 참조할 수 있다.

`explain` 으로 확인해보면 다음과 같이 실행 계획이 잘 적용되는 것을 확인할 수 있다.
```scala
ds.explain

== Physical Plan ==
*Project [nv_mid#2795, cat_id#2795, UDF(cat_id#2839).cat_nm]
```