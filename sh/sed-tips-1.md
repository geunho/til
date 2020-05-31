---
author: "Kim, Geunho"
date: 2020-05-31
title: "sh - sed로 마지막 N개 문자열 제거하기"
---

쉘에서 `sed` (stream editor) 도구를 사용하면 다채로운 문자열 조작을 할 수 있다.

# 사용방법
아래는 마지막 2개 문자열을 제거하는 예제이다.
```bash
# sed s/regexp/replacement/
sed 's/.\{2\}$//' <<< "Hello, Bash"
Hello, Ba

# 위 예시는 마지막 2개 문자를 공백으로 치환한 것. 따라서 다른 문자열로 치환도 가능하다.
sed 's/.\{2\}$/bo/' <<< "Hello, Bash"
Hello, Babo
```

# 참고
https://linux.die.net/man/1/sed
