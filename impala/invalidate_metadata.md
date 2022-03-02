---
author: "Kim, Geunho"
date: 2022-03-02
title: "INVALID METADATA - Impala 메타데이터 캐시 삭제하기"
---

impala에서 HMS(Hive Metastore) 정보는 내부적으로 캐싱되어 있는데 테이블 정보가 변경되었을때 HMS와 캐시가 불일치하는 경우가 있다.
(예. external table의 LOCATION 속성)

이때 아래와 같이 캐시를 삭제하면 된다.

```sql
INVALIDATE METADATA db_name.table_name;
```

# 참고
* [INVALIDATE METADATA Statement](https://impala.apache.org/docs/build/html/topics/impala_invalidate_metadata.html)
