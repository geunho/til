---
author: "Kim, Geunho"
date: 2020-06-08
title: "Maven - 의존성 관리시 주의사항"
---

Maven 프로젝트는 외부 모듈을 참조할때 maven repository로부터 참조 대상을  POM 파일의 의존성 항목에 추가해서 관리한다.

```xml
<dependencies>
    <dependency>
        <groupId>$groupId</groupId>
        <artifactId>$artifactId</artifactId>
        <version>$version</version>
    </dependency>
</dependencies>
```

참조로 하는 모듈은 maven import시 프로젝트의 classpath에 참조 대상 라이브러리로 추가되고,
만약 그 모듈이 다시 참조하는 모듈이 있는 경우 그 모듈도 classpath에 함께 추가된다.

이로인해 발생하는 주의해야 할 부분이 발생한다.
* classpath에 참조의 하위 참조도 추가되므로 root 프로젝트에서 해당 모듈에 접근할 수 있음
* 만약 root 프로젝트에서 하위 참조를 즉시 참조로 추가해서 사용할 경우 버전 충돌이 일어날 수 있음
* 만약 즉시 참조없이 사용할 경우 상위 모듈 참조를 제거했을 때 빌드 오류가 발생할 수 있음

따라서 어떤 모듈을 의존성에 추가할때, 하위 참조 모듈은 classpath에 추가되지 않도록 설정하거나 -(1)
상위 모듈을 제거할 때 하위 참조 모듈을 명시적으로 의존성에 추가 -(2) 해줘야 한다.

(1)의 경우
```xml
<dependency>
    <groupId>$groupId</groupId>
    <artifactId>$artifactId</artifactId>
    <version>$version</version>
    <exclusions>
    <exclusion>
        <groupId>$child_groupId</groupId>
        <artifactId>$child_artifactId</artifactId>
    </exclusion>
    </exclusions>
</dependency>
```
`exclusion` 항목에 모듈 groupId, artifactId를 지정하면 된다.

(2)의 경우
```xml
<dependency>
    <groupId>$child_groupId</groupId>
    <artifactId>$child_artifactId</artifactId>
    <version>$child_version</version>
</dependency>
```
이와 같이 하위 모듈을 의존성에 추가하고 상위 모듈은 제거한다.

# 교휸
POM의 의존성을 변경하거나 제거할 때에는 추가할 때 보다 참조 오류가 발생하지 않을지 신중히 작업해야 한다.
커밋을 하기 전, Maven 캐시와 먼저 빌드된 타겟 디렉토리는 완전히 삭제하고 제빌드해서 빌드 오류 여부를 반드시 확인한다.
(IDE에서 maven reimport, rebuild proejct 두 가지만 확실히 해줘도 충분할 듯 하다. 😂)