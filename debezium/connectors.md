---
author: "Kim, Geunho"
date: 2020-06-10
title: "Debezium - CDC를 위한 Connector 정리"
---

Debezium은 변경 데이터 캡처(change data capture, 이하 CDC)를 구현하는 분산 플랫폼이다.

과거에는 데이터베이스의 변경 로그를 내부 구현으로만 가지고 있었기 때문에 사용자는 직접 주기적인 쿼리를 하거나, 비용이 값비싼 트리거와 같은 방식을 이용해야 했다.
Debezium을 비롯해 여러 솔루션들은 이러한 방식에서 직접 데이터베이스의 복제 로그를 추적해서 스트림으로 제공하는 방식으로 CDC를 구현하고 있다.

Debezium은 이러한 스트림을 제공하기 위해, Kafka 플랫폼의 엔터프라이즈 솔루션을 제공하는 Confluent사에서 개발한 [Kafka Connect](https://docs.confluent.io/current/connect/index.html)의 Connector 구현체로 CDC를 제공한다.

![Debezium architecture](https://user-images.githubusercontent.com/4519301/84282307-8a1e0280-ab74-11ea-9fb8-689a05741d33.png) _Debezium Connector는 Kafka Connect 기반으로 동작한다._

공식 문서상 현재 제공되는 Connector는 다음과 같다:
* MySQL
* MongoDB
* PostgreSQL
* Oracle
* SQL Server
* Db2
* Cassandra

# PostgreSQL Connector
이 중 PostgreSQL은 Connector가 처음 구동되면 각 데이터베이스 스키마의 스냅샷을 떠서 저장한 후, 쓰기 전 로그(write-ahead log, 이하 WAL)를 지속적으로 읽어들여변경 사항을 감지한다.
이는 PostgreSQL이 WAL 세그먼트를 일정한 시간 간격으로 퍼지하기 때문에 WAL만으로는 변경 사항의 모든 부분은 알 수 없기 때문이다.

또한 이 Connector를 사용시에는 [PostgreSQL의 논리적 디코딩 기능](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html)으로 인해 몇 가지 주의사항이 있다:
* 논리적 디코딩은 DDL 변경은 지원하지 않는다. 즉, DDL 변경이 일어나더라도 변경 이벤트를 읽어들이는 Consumer 단에서는 알 수 없다.
* 논리적 디코딩 복제는 primary 서버에 한에 제공된다. 만약 PostgreSQL가 클러스터로 구성되어 있다면, Connector는 active primary 서버로만 동작한다. 만약 primary에 문제가 생겨 demoted되면, connector는 중지한다. 다시 복구 되었을 때 connector도 직접 재시작 해야 하는데 만약 다른 노드가 primary로 승급된다면 반드시 connector 설정도 그 노드 정보로 바꿔줘야 하는 한계가 있다.
* 마지막으로 UTF-8 인코딩된 데이터베이스만 지원한다.
