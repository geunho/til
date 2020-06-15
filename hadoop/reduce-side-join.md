---
author: "Kim, Geunho"
date: 2020-06-15
title: "MapReduce - Reduce-side join"
---

SQL에서는 단일 구문 하나로 여러 테이블을 쉽게 join 할 수 있다. 구문 분석기가 데이터를 다루는 복잡한 부분은 알아서 처리해주는 덕분인데,
 MapReduce(이하 MR)로 join을 구현하려면 이러한 "알아서 처리해주는" 일을 직접 코드로 다뤄야 한다.

MR join 중 가장 간단히 모든 join을 구현할 수 있는 방법이지만 비용이 비싼 Reduce-side join을 정리한다.

# Reduce-side join
reduce 단계에서 데이터 셋을 join하는 처리 방식을 reduce-side join이라 한다.

데이터 크기와 상관없이 inner join, left outer join, right outer join, full outer join, exclusive join, 그리고 Cartesian product까지 모든 종류의 join을 구현할 수 있다.
* 각 데이터 셋의 mapper에서는 join의 외래키로 사용할 필드를 key로, 나머지는 value로 출력한다. 이때 데이터 셋이 어디로부터 온 것인지 알 수 있도록 구분자를 추가하거나, 각 레코드가 클래스 인스턴스로 변환하여 구분될 수 있도록 처리한다.
* 외래키 간 서로 다른 형식의 레코드가 하나의 reducer에서 입력 받는다.
* reducer에서는 각 데이터 셋의 유무에 따라 join 처리를 한다.

이렇듯 구현은 아주 간단한데, 대신 다음과 같은 단점들이 있다:
* 입력 레코드의 대부분을 reduce 단계로 보내야 하기 때문에 네트워크 대역폭을 많이 사용
* 따라서 처리 속도가 느림