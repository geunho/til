---
author: "Kim, Geunho"
date: 2020-06-25
title: "Spark - csv 읽기시 header와 case class 함께 사용하기"
---

Spark 2의 [DataFrameReader.csv(paths)](https://spark.apache.org/docs/2.2.0/api/java/org/apache/spark/sql/DataFrameReader.html#csv-scala.collection.Seq-) 메서드에는 csv 형식을 읽기 위한 다양한 옵션들을 제공한다.

```
sep (default ,): sets the single character as a separator for each field and value.
encoding (default UTF-8): decodes the CSV files by the given encoding type.
quote (default "): sets the single character used for escaping quoted values where the separator can be part of the value. If you would like to turn off quotations, you need to set not null but an empty string. This behaviour is different from com.databricks.spark.csv.
escape (default \): sets the single character used for escaping quotes inside an already quoted value.
comment (default empty string): sets the single character used for skipping lines beginning with this character. By default, it is disabled.
header (default false): uses the first line as names of columns.
inferSchema (default false): infers the input schema automatically from data. It requires one extra pass over the data.
ignoreLeadingWhiteSpace (default false): a flag indicating whether or not leading whitespaces from values being read should be skipped.
ignoreTrailingWhiteSpace (default false): a flag indicating whether or not trailing whitespaces from values being read should be skipped.
nullValue (default empty string): sets the string representation of a null value. Since 2.0.1, this applies to all supported types including the string type.
nanValue (default NaN): sets the string representation of a non-number" value.
positiveInf (default Inf): sets the string representation of a positive infinity value.
negativeInf (default -Inf): sets the string representation of a negative infinity value.
dateFormat (default yyyy-MM-dd): sets the string that indicates a date format. Custom date formats follow the formats at java.text.SimpleDateFormat. This applies to date type.
timestampFormat (default yyyy-MM-dd'T'HH:mm:ss.SSSXXX): sets the string that indicates a timestamp format. Custom date formats follow the formats at java.text.SimpleDateFormat. This applies to timestamp type.
maxColumns (default 20480): defines a hard limit of how many columns a record can have.
maxCharsPerColumn (default -1): defines the maximum number of characters allowed for any given value being read. By default, it is -1 meaning unlimited length
mode (default PERMISSIVE): allows a mode for dealing with corrupt records during parsing. It supports the following case-insensitive modes.
PERMISSIVE : sets other fields to null when it meets a corrupted record, and puts the malformed string into a field configured by columnNameOfCorruptRecord. To keep corrupt records, an user can set a string type field named columnNameOfCorruptRecord in an user-defined schema. If a schema does not have the field, it drops corrupt records during parsing. When a length of parsed CSV tokens is shorter than an expected length of a schema, it sets null for extra fields.
DROPMALFORMED : ignores the whole corrupted records.
FAILFAST : throws an exception when it meets corrupted records.
columnNameOfCorruptRecord (default is the value specified in spark.sql.columnNameOfCorruptRecord): allows renaming the new field having malformed string created by PERMISSIVE mode. This overrides spark.sql.columnNameOfCorruptRecord.
multiLine (default false): parse one record, which may span multiple lines.
```

이 중 `header`와 `schema`, 그리고 `inferSchema` 옵션은 읽어들인 레코드의 형식과 스키마를 결정하는 역할을 한다.
일반적인 사용 예로는, 

# 파일 첫 줄에 header가 없을 때
1. `header` 옵션은 활성화하지 않고 `schema`에 직접 case class의 schema를 읽어들임
```scala 
case class Person(name: String, age: Int, weight: Double)
spark.read
    .schema(Encoders.product[Pserson].schema) // case class의 schema를 Encoders를 이용해 추출
    .csv("persons.csv")
    .as[Person] // Dataset[Person] 으로 변환
```

# 파일 첫 줄에 header가 있을 때
`header` 옵션을 활성화하고 `inferSchema` 옵션을 활성화 하여 레코드의 각 필드 형식을 유추
```scala
spark.read
    .option("header", true)
    .option("inferSchema", true) // 레코드 일부를 읽어들인 후, 레코드의 필드 형식 유추
    .csv("persons.csv")
```

# 특수 사례 
그런데 만약 header도 있고, header를 통해 schema를 생성한 후 case class의 Dataset으로 변환도 하고 싶다면 어떻게 해야 할까?

```scala
case class Person(name: String, age: Int, weight: Double)
spark.read
    .option("header", true)
    .schema(Encoders.product[Pserson].schema) // case class의 schema를 Encoders를 이용해 추출
    .csv("persons.csv")
    .as[Person] // Dataset[Person] 으로 변환
```

여기서 한번 더 나아가서, header 파일이 따로 저장되어 있고 이를 통해 schema를 생성하고 싶다면? 
이후 Dataset 형 변환은 어떻게 해야 할까?

몇 가지 조건이 추가되었는데 다음과 같은 트릭으로 해결할 수 있다.
```scala
case class Person(name: String, age: Int, weight: Double)

/**
 * 따로 저장된 header meta 파일을 읽어들인다.
 * 이때 schema 지정과 schema 유추 옵션이 없기 때문에 레코드의 모든 필드는 String으로 먼저 읽어들인다.
 * 
 * as[Person]으로 Person 형식으로 schema를 구성하기 위해 변환해주는데,
 * String -> 숫자로의 변환은 명시적으로 cast해주어야 오류가 발생하지 않는다. 이를 위해 withColumn으로 먼저 형식을 변경한다.
 */
val meta = spark.read
                .option("header", true)
                .csv("persons_meta_header") // name,age,weight 
                .withColumn("age", $"age".cast(IntegerType))
                .withColumn("weight", $"weight".cast(DoubleType))
                .as[Person]
spark.read
    .schema(meta.schema)
    .csv("persons.csv")
    .as[Person]
```

