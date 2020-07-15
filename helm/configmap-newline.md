---
author: "Kim, Geunho"
date: 2020-06-26
title: "Helm - ConfigMap 정의시 newline 주의사항"
---

newline 유지
```yml
data:
    start.sh: |
    #!/bin/bash
    blahblah..

```
newline 제거
```yml
data:
    start.sh: |-
    #!/bin/bash
    blahblah..

```
`|-` 의 마이너스 기호가 추가되면 정의한 리소스의 마지막 공백과 newline이 제거된다.

https://stackoverflow.com/questions/56395758/what-is-the-difference-between-and-when-creating-a-configmap-from-a-file-i