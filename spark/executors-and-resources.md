---
author: "Kim, Geunho"
date: 2020-07-09
title: "Executor 수와 executor당 메모리 할당 관련"
---

읽어들이는 파일이 수 천개 이상의 파티션으로 쪼개져 있을떄, 구동되는 executor 수가 많을 수록 읽어들이는 속도는 빨라진다.
그리고 join과 같은 연산을 수행할 떄 executor당 memory 할당을 높일 수록 연산 속도가 빨라진다.

그런데 클러스터의 자원은 한정되어 있기 때문에 executor의 memory 할당을 높이면 생성되는 executor 수가 줄어들게 된다.
처리할 파일의 구조와 어떤 처리를 하는지에 따라 이 수치는 달라질 수 있기 때문에 실험이 필요하다.

