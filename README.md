# stock-sample  
재고시스템 기반 동시성이슈 해결방법 제시
JPA + Redis 

# mac기반 
작업환경 세팅

1. docker 설치
   brew install docker 
   brew link docker
   docker version

윈도우 사용자 분들은 현재 셋팅 상태에서 구동시키시면 됩니다.

2. mysql 설치 및 실행
   docker pull mysql
   docker run -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=1234 --name mysql mysql 
   docker ps

   docker: no matching manifest for linux/arm64/v8 in the manifest list entries. 오류가 발생하시는분은
   docker pull --platform linux/x86_64 mysql

3. my sql 데이터베이스 생성
   docker exec -it mysql bash
   mysql -u root -p
   create database stock_example;
   use stock_example;

# 레디스 설치 
 docker pull redis
 docker run --name myredis -d -p 6379:6379 redis

# 레이스 컨디션(Race Condition) 이란 ?
둘 이상의 스레드가 공유 데이터에 액세스할 수 있고 동시에 변경하려고 할 때 발생하는 문제

둘 이상의 스레드 : 요청
공유 데이터 : 재고 데이터
동시에 변경하려고 할 때 : 업데이트 할때
발생하는 문제 : 값이 정상적으로 바뀌지 않는 문제


# 해결방법
  하나의 스레드만 데이터에 액세스 할 수 있도록 한다.

# Mysql 을 활용한 다양한 방법
1. Pessimistic Lock
실제로 데이터에 Lock 을 걸어서 정합성을 맞추는 방법입니다. exclusive lock 을 걸게되며 다른 트랜잭션에서는 lock 이 해제되기전에 데이터를 가져갈 수 없게됩니다.
데드락이 걸릴 수 있기때문에 주의하여 사용하여야 합니다.

2. Optimistic Lock
실제로 Lock 을 이용하지 않고 버전을 이용함으로써 정합성을 맞추는 방법입니다. 먼저 데이터를 읽은 후에 update 를 수행할 때 현재 내가 읽은 버전이 맞는지 확인하며 업데이트 합니다. 내가 읽은 버전에서 수정사항이 생겼을 경우에는 application에서 다시 읽은후에 작업을 수행해야 합니다.

3. Named Lock
이름을 가진 metadata locking 입니다. 이름을 가진 lock 을 획득한 후 해제할때까지 다른 세션은 이 lock 을 획득할 수 없도록 합니다. 주의할점으로는 transaction 이 종료될 때 lock 이 자동으로 해제되지 않습니다. 별도의 명령어로 해제를 수행해주거나 선점시간이 끝나야 해제됩니다.

# 참고
https://dev.mysql.com/doc/refman/8.0/en/
https://dev.mysql.com/doc/refman/8.0/en/locking-functions.html
https://dev.mysql.com/doc/refman/8.0/en/metadata-locking.html

# Mysql vs Redis 비교 
 1. Mysql
    Mysql 을 사용하고 있다면 별도의 비용없이 사용가능하다.
    어느정도의 트래픽까지는 문제없이 활용이 가능하다.
    Redis 보다는 성능이 좋지않다.

2. Redis
  활용중인 Redis 가 없다면 별도의 구축비용과 인프라 관리비용이 발생한다.
  Mysql 보다 성능이 좋다.

