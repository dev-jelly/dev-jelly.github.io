---
title: "[Mysql] mysqldump에 조건을 걸 수 없을까?"
date: "2020-07-07T06:00:00.000Z"
template: "post"
draft: false
slug: "mysqldump에 조건을 걸 수 없을까?"
category: "DB"
tags:
  - "Mysql"
  - "mysqldump"
description: "[Mysql] mysqldump에 where 옵션을 추가할 수 있다."
socialImage: "/media/image-2.jpg"
---

## 짧은 글

### mysqldump에 where 옵션을 추가할 수 있다. 

아래와 같이 mysqldump에 옵션을 추가할 수 있다. `{}` 안에해당하는 내용은 대체하여 사용하시면 됩니다. (옵션에 대한 이해가 부족하다면 꼭 아래의 옵션 설명을 읽으시길 바랍니다.)

```shell
mysqldump --where "seq > {firstSeq} AND seq < {lastSeq}" -u {user_name} -p{P@ssw0rd} -h {remote_host} --database {database(schema)} --tables {table_name} --no-create-info --no-create-db --single-transaction > some_dump_file.sql
```

데이터 복구는 기존과 똑같은 방법으로 가능하다.

```shell
mysql -u {user_name} -p{P@ssw0rd} -h {remote_host} {database} < some_dump_file.sql
```



## 긴 글

### 시나리오

이번에 로그 DB와 운영 DB를 통합하는 작업을 진행하였다. 왜 이 작업을 진행했는 지는 나중에 다시 이야기 할 예정이다.

로그 DB를 운영 DB에 통합한 뒤 로그 DB를 삭제하는 작업이었는데, 이 DB통합 프로세스를 무중단으로 할 수 있는 시나리오를 생각한 뒤 다음과 같은 방식으로 진행하려고 했다. 현재 어트랙트는 실시간 데이터를 제공하고 있지 않아,  24시간안에 동기화만 완료되면 완벽히 무중단이 가능하다.

1. 로그 DB를 기반으로 운영 DB에 테이블을 생성한다. (데이터 미포함) 이때 Auto Increment 정보도 같이 가져간다.
2. 로그 서버를 운영 DB 정보로 바꾼다.
   - 실제로는 제품 API 서버에 이미 로그 서빙도 똑같이 구현 되어 있어서 DB정보와 DNS만 운영서버로 바꾸어 주었다.
   - 로그 서버 또한 제품 API 서버에 통합한 뒤 최종적으로 내릴 예정이었다.
3. DNS 정보가 변경되는 동안 로그 DB에 쌓인 데이터는 수동으로 인서트하여 싱크를 맞춘다. 
   - 수동으로 싱크 맞춘 데이터는 로그 DB에서 삭제한다.
4. 기존에 있는 로그 DB데이터 중 운영 최초의 seq 이전데이터만 다 넣는다.

위와 같은 방식으로 진행할 경우 정확하게 조건에 들어 맞는 무중단이 가능하며, 작업도 굉장히 편하고, PK 값이 오염되거나 중복될 일 같은 게 없었다. 문제는 4번에서 네트워크 문제로 데이터가 중간까지만 마이그레이션 되었다는 것이다. 다시 처음부터 마이그레이션을 할까 생각하다가 너무 시간이 오래걸리고, 이번에는 네트워크 유실이 발생하지 않을거라는 보장이 없기에 중간부터 마이그레이션을 할 수 없을까 찾아보다가 mysqldump에서도 where 조건을 사용할 수 있다는 걸 알았다.

### mysqldump 옵션 설명

```shell
mysqldump --where "seq > {firstSeq} AND seq < {lastSeq}" -u {user_name} -p{P@ssw0rd} -h {remote_host} --database {database(schema)} --tables {table_name} --no-create-info --no-create-db --single-transaction > some_dump_file.sql
```

--where 옵션은 이 포스트의 핵심이다. 위와 같이 일반 적인 SQL의 where 과 같다. 필요에 따라 `"seq > {firstSeq} limit 500"`  와 같은 식으로 리밋을 걸 수도 있다.

-u, -p, -h 는 접속에 관한 정보니 생략한다.

--database, --tables mysqldump의 경우 SQL을 통해 가져오는 게 아니기 때문에 명시적으로 두 옵션을 필수 적으로 사용해야한다. 여러분이 모든 테이블의 데이터를 조건을 걸어서 가져오는 걸 의도한 게 아니라면 말이다.

--no-create-info, --no-create-db mysqldump에서는 기본적으로 테이블 생성정보를 가져오고, **데이터를 복원 할때 이미 존재하는 테이블을 없앤 뒤** 복원하기 때문에 중간에 있는 데이터만 복구하고 싶다면 꼭 써줘야 하는 옵션이다.

--single-transaction InnoDB에서 테이블 락을 하고 싶지 않을 때 사용할 수 있는 옵션
