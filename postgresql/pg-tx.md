---
author: "Kim, Geunho"
date: 2022-06-10
title: "Postgresql - 트랜잭션 처리 과정"
---

1. checkpoint를 WAL에 기록
2. 변경 대상 페이지를 로드
3. 로드된 페이지에 변경 적용
4. WAL 버퍼에 내용 기록
5. 변경된 페이지 커밋 시작, WAL 버퍼에 내용 기록
6. WAL 버퍼 flush (WAL 파일에 기록)
7. 끝

https://www.postgresql.org/docs/13/wal-intro.html