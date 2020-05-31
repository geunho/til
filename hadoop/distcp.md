---
author: "Kim, Geunho"
date: 2020-05-29
title: "distcp - HDFS 클러스터 간 파일 복제하기"
---

`distcp`는 distributed copy의 약어로, 서로다른 HDFS 클러스터 간 파일을 복제할때 사용하는 명령어이다.

# 사용방법
```
hadoop distcp hdfs://nn1/job/logs hdfs://nn1/job/others hdfs://nn2/job/
```
`nn2` hdfs에 `nn1`의 두 디렉토리를 복제한다. 공백으로 구분해서 입력 경로를 여러 개를 지정할 수 있고, 마지막 인자에 출력 경로를 지정하면 된다.

```
hadoop distcp -D dfs.replication=2 -m 32 hdfs://nn1/job/logs hdfs://nn1/job/others hdfs://nn2/job/
```
`-D` 옵션으로 hadoop 프로그램에 일반적인 옵션을 지정할 수 있고, `-m` 옵셥으로 map 수를 지정하여 최대 동시 복제 수를 설정한다.


# 참고
[DistCp, Hadoop docs](https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html)
