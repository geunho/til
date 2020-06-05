---
author: "Kim, Geunho"
date: 2020-06-05
title: "GitHub - 코멘트에 접어두는 코드 삽입하기"
---

GitHub에서 마크다운 형식의 코멘트를 남기다보면 장황한 부가 설명은 접어두는 블럭으로 지정할 수 있다.

접어두는 블럭은 `details`, `summary` 태그를 코멘트에 직접 넣어주면 되는데 다음과 같이 사용할 수 있다.
```
<details>
 <summary>더 보기...</summary>
 장황한 부연 설명들
</details>

> 더 보기... (클릭해서 펼치고 접어둘 수 있음)
```

이때 코드 뭉치를 넣을 수 도 있는데, 한 줄 넘기고 작성하면 잘 동작한다.

```
<details>
 <summary>더 보기...</summary>
 
 
 [장황한 code block]
</details>
```
