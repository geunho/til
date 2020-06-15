---
author: "Kim, Geunho"
date: 2020-06-15
title: "Maven - Java cannot find symbol: javax.annotation.Resource"
---

다음과 같은 빌드 오류가 발생했을 때 해결 방법을 정리한다.
```
Java cannot find symbol: javax.annotation.Resource 
```

javax.annotation.Resource 클래스를 찾을 수 없다는 오류인데, 이는 annotation으로 사용된다.
```
@Resource
Foo bar
```

# 해결 방안 
JDK 11 빌드 환경에서는 Resource 클래스를 찾을 수 있도록 추가하거나,

아래와 같이 Resource annotation 대신 Autowired, Qualifier를 사용한다.
```
@Autowired
@Qualifier("bar")
Foo bar
```

변경이 부득이한 경우에는, JDK 11 -> JDK 8 로 빌드 버전을 낮추면 해결된다.
