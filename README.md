# stock-sample  jpa+redis
재고시스템 기반 동시성이슈 해결방법 제시

mac기반 
작업환경 세팅

docker 설치
brew install docker 
brew link docker
docker version
윈도우 사용자 분들은 현재 셋팅 상태에서 구동시키시면 됩니다.

mysql 설치 및 실행
docker pull mysql
docker run -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=1234 --name mysql mysql 
docker ps

docker: no matching manifest for linux/arm64/v8 in the manifest list entries. 오류가 발생하시는분은
docker pull --platform linux/x86_64 mysql

my sql 데이터베이스 생성
docker exec -it mysql bash
mysql -u root -p
create database stock_example;
use stock_example;

==============================================================================


