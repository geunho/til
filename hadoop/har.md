---
author: "Kim, Geunho"
date: 2020-06-02
title: "har - Hadoop archive 도구로 압축하기"
---

`hadoop` 명령어에는 HDFS 상 파일들을 압축하기 위한 `archive` 도구가 있다.
명령어 한 줄로 여러 경로의 입력들을 하나로 압축할 수 있다.

HDFS의 블록 크기보다 작은 다수 개의 파일은 namenode의 메모리 공간을 낭비하는데, 이러한 파일들을 har 압축하면 사용량을 줄일 수 있다.

단, 압축시 원본에 대해 복사본을 생성하는 것이므로 원본 파일 용량만큼의 공간은 확보되어 있어야 한다.
그리고 하나의 파일로 압축하더라도, MR 입력시 har 단일 파일로 인식하지 않고 원본 파일 개수 만큼을 입력으로 가지므로 MR의 작업 효율이 좋아지지는 않는다.

```bash
# 마지막 인자의 경로가 har 파일의 출력 경로가 된다.
#
# -p : 입력의 부모 경로
# -r : 복제 계수
# a b c : 입력 경로
# /user/me/output : 출력 경로
hadoop archive -archiveName myFiles.har -p /user/me -r 2 a b c /user/me/output
```

MR 작업이 시작되고 다음과 같이 압축된 출력 결과를 확인할 수 있다.
```
hadoop fs -ls /user/me/output/myFiles.har
Found 4 items
-rw-r--r--   3 root supergroup          0 2020-06-02 14:00 /user/me/output/myFiles.har/_SUCCESS
-rw-r--r--   2 root supergroup        197 2020-06-02 14:00 /user/me/output/myFiles.har/_index
-rw-r--r--   2 root supergroup         17 2020-06-02 14:00 /user/me/output/myFiles.har/_masterindex
-rw-r--r--   2 root supergroup          0 2020-06-02 14:00 /user/me/output/myFiles.har/part-0
```

