---
author: "Kim, Geunho"
date: 2020-06-25
title: "Spark - csv 읽기/쓰기시 공백 제거 주의사항"
---

Spark 2의 csv 패키지를 사용할 때 다음과 같은 주의사항이 있다.
* 읽기: 필드의 앞, 뒤 공백을 유지함
* 쓰기: 앞, 뒤 공백을 제거함

읽기와 쓰기 작업시 공백을 처리하는 옵션이 서로 다르다.
관련 옵션은 다음과 같다:
* `ignoreLeadingWhiteSpace`: 필드의 앞 공백을 제거할 것인가
* `ignoreTrailingWhiteSpace`: 필드의 뒷 공백을 제거할 것인가


이에 대해 읽기 혹은 쓰기에 대해 공백 제거 옵션을 맞춰야 다른 작업(예. MR, Hive 등)과 파이프라인을 구성할 때 문제가 발생하지 않는다.

# 읽기시 공백 제거
읽기시 공백을 제거하려면 `read`에 다음과 같이 옵션을 추가한다.
```scala
 spark.read
    .option("ignoreLeadingWhiteSpace", true)
    .option("ignoreTrailingWhiteSpace", true)
    .option("sep", "\u0001")
    .schema(schema)
    .csv(path)
    .as[Model]
```

# 쓰기시 공백 유지
쓰기시 공백을 유지하려면 `write`에 다음과 같이 옵션을 추가한다.
```scala
joined.write
    .option("ignoreLeadingWhiteSpace", false)
    .option("ignoreTrailingWhiteSpace", false)
    .option("sep", "\u0001")
    .mode("overwrite")
    .csv(path)
```

