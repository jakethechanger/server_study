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

test

