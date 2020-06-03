---
author: "Kim, Geunho"
date: 2020-06-03
title: "from_timestamp - Impala 날짜 형식 사용하기"
---

일반적인 RDB에 내장된 `to_char(timestamp, text)` 함수가 Impala에는 없다.
대신, 같은 역할을 하면서 이름만 다른 `from_timestamp(TIMESTAMP datetime, STRING pattern)` 함수를 사용하면 된다.

```sql
# ANSI
SELECT to_char(now(), 'yyyy-MM-dd, HH:mm') 
```

```sql
# Impala
SELECT from_timestamp(now(), 'yyyy-MM-dd, HH:mm')
```

# 참고
* [Impala Date and Time Functions#FROM_TIMESTAMP, Impala docs](https://impala.apache.org/docs/build/html/topics/impala_datetime_functions.html#datetime_functions__from_timestamp)
* [9.8. Data Type Formatting Functions, Postgresql docs](https://www.postgresql.org/docs/12/functions-formatting.html)