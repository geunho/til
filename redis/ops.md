---
author: "Kim, Geunho"
date: 2020-06-04
title: "Redis - 조회 명령어 정리"
---

redis-cli를 이용해서 간단히 조회할 때 사용할만한 redis 명령어 몇 가지를 정리한다.

# get
`string` 형식으로 저장된 값 하나를 조회한다.

```
get my-key
"my value blah blah"
```

# hget
`hash`로 저장된 값의 필드 하나를 조회한다.
```
# my-hkey로 저장된 hash의 my-field에 대한 값을 조회
hget my-hkey my-field
"my field value blah blah"
```

# lrange
`list`로 저장된 목록을 조회한다.
```
# 전체 목록 조회
lrange my-lkey 0 -1

# 일부 목록 조회, 시작 인덱스와 끝 인덱스를 지정
# 끝 인덱스는 결과에 포함됨
lrange my-lkey 1 4
```

# smembers
`set`으로 저장된 모든 값을 조회한다.
```
smembers my-skey
1) "first"
2) "second"
3) "third"
```

# exists
어떤 값들은 특정한 형식으로 저장되어 있어서 아래와 같은 오류를 뿜기도 한다.
```
(error) -WRONGTYPE Operation against a key holding the wrong kind of value
```
키가 없나? 하고 처음에는 잘 못 생각했는데, `exists` 명령어를 이용하면 키의 유무를 파악할 수 있다.
```
exists my-key
# 있음
(integer) 1 

# 없음 
(integer) 0
```

# type
만약 `exists` 명령어로 키가 있음을 알게 되었는데,
지금까지 조회 명령어를 보면 알 수 있듯이 값이 어떤 형식으로 저장 되었는가에 따라 사용하는 명령어가 다르다.

이때 `type` 명령어로 어떤 형식인지 확인할 수 있다.
```
type my-key
"string" # get
"list" # lrange
"set" # SMEMBERS
"hash"
```

# dump
`type`으로 형식을 조회했는데 redis 내장 형식이 아니라면 어떻게 조회할까?
이때는 `dump` 명령어로 redis 형식의 직렬화한 결과를 출력할 수 있다.
```
dump my-key
"\x05\x05\x01*\rblahblah..."
```
