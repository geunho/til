---
author: "Kim, Geunho"
date: 2022-05-10
title: "Gradle - 로컬 repository 캐시 제거하고 빌드하기"
---

여러가지 이유로 로컬 repository 캐시를 바라보지 않고 새로 내려받는 테스트가 필요할때 빌드 옵션으로 아래와 같이 추가한다.

```sh
gradle build --refresh-dependencies
```

하지만 해당 옵션으로 모든 의존성을 강제로 다운로드하도록 하는 것은 아니다.

> In other words, refreshing dependencies only has an impact if you actually use dynamic dependencies or that you have changing dependencies that you were not aware of (in which case it is your responsibility to declare them correctly to Gradle as changing dependencies).
> 
> It’s a common misconception to think that using --refresh-dependencies will force download of dependencies. This is not the case: Gradle will only perform what is strictly required to refresh the dynamic dependencies. This may involve downloading new listing or metadata files, or even artifacts, but if nothing changed, the impact is minimal.
> 
> https://docs.gradle.org/current/userguide/dependency_management.html#sec:refreshing-dependencies

즉, 메타데이터 파일을 참조해서 변경이 발생한 의존성, 혹은 동적 추가되는 의존성에 대해 새로 내려받는 것.
완전히 삭제하기 위해서는 캐시 파일을 삭제해야 한다.

```sh
rm -rf $HOME/.gradle/caches
```

