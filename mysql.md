# Install mySQL
- brew install mysql
- mysql.server start
- mysql -uroot -p
- mysql.server stop 


brew update를 통해 최신 버전으로 업데이트 해준다.

brew search mysql을 통해 설치할 MySQL 버전을 확인해준다.

brew install mysql을 통해 원하는 버전의 MySQL을 설치해준다.(이 경우 최신버전)

MySQL 설정
mysql.server start 명령어로 MySQL 서버를 실행시킨다.

mysql_secure_installation 명령어로 MySQL 설정으로 넘어간다.

Would you like to setup VALIDATE PASSWORD component?(비밀번호 가이드 설정에 대한 질문)
yes: 복잡한 비밀번호
no: 쉬운 비밀번호

Remove anonymous users? (Press y|Y for Yes. any other key for No)(사용자 설정에 관한 질문)
yes: 접속시 -u 옵션필요
no: 접속시 -u 옵션 불필요

Disallow root login remotely? (Press y|Y for Yes, any other key for No)(다른 IP에서 root 아이디로 원격접속을 설정)
yes: 원격접속 불가능
no: 원격접속 가능

Remove test database and access to it? (Press y|Y for Yes, any other key for No)(테스트 데이터베이스 설정)
yes: 테스트 데이터베이스 제거
no: 테스트 데이터베이스 유지

Reload privilege tables now? (Press y|Y for Yes, any other key for No)(변경된 권한을 테이블에 적용)
yes: 적용
no: 미적용

mysql -uroot -p 명령어로 비밀번호 입력 후 접속


로그아웃 시: exit 또는 quit
MySQL 서버 종료: mysql.server stop

MySQL 삭제
아래의 명령어들을 입력해준다.

sudo rm -rf /usr/local/var/mysql
sudo rm -rf /usr/local/bin/mysql*
sudo rm -rf /usr/local/Cellar/mysql

## mysql 실행

```
mysql.server start
mysql -uroot -p
mysql.server stop
```


# Senior coding SQL

docker search mysql
docker pull mysql
docker run -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=r! --name mysql mysql
docker start mysql //attach가 아닌 exec으로 들어간다.
docker exec -it mysql bash


## Create Test Data
- Install MySQL Workbench
- Create Database (testdb)
- Create Tables (Dept, Emp)
- Insert Test Data

### Create Database
User, Authority는 따로 만들어줘도 됨
- create database dooodb;
- show databases;
- use dooodb;

// db 삭제
- drop database dooodb;

### Create User
- create user <user-name>@'<host(IP address)>' identified by '<password>';
- create user dooo@'%' identified by '1234';
- select * FROM user;
- select host, user FROM user;
```
| %         | root             |
| localhost | mysql.infoschema |
| localhost | mysql.session    |
| localhost | mysql.sys        |
| localhost | root
```


- mysql -u dooo -p

//mySQL client에서 Docker mysql을 사용하고 싶으면 port를 다르게 하거나 로컬 sql을 중지

### User privileges
- grant all privileges on *.* to '<usern-name>'@'<host>';
grant all privileges on dooodb.* to 'dooo'@'%';

- revoke all privileges on dooodb.* from 'dooo'@'%';

- flush privileges;

### Check privileges
- show grants for '<user-name>'@'<host>';
show grants for dooo;


### Delete user
- drop user dooo@'%';

### find current user 
select current_user();

### see timezone
show variables like '%time_zime%';
set time_zone = 'Asia/Seoul';

select unix_timestamp();
select from_unixtime(1620538451);

### show processlist;


### Create table
```
create table Test(
    id tinyint unsigned not null auto_increment,
    name char(5) not null,
    PRIMARY key(id));
```

If want to see the structure of Table
-SHOW CREATE TABLE <table-name>;


### truncate table <table-name>;

### create new table copied by current table
CREATE TABLE t_Test like Test;
INSERT INTO t_Test SELECT * FROM Test;
