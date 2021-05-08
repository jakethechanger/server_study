# 시스템 개발환경

1. 개발환경: Local PC (VS, MySql workbench, Git client)
2. 개발서버: Docker (Nginx, Node, Flask, MySQL)
3. 스테이징서버: Docker/Cloud (Ngnix, Node, Flask, MySQL)
4. 서비스 서버: Cloud (Nginx, Node, Flask, MySQL)


## Docker
- docker --version
- docker system info

## install centos image on docker
- docker pull centos
- docker image ls

### docker image commands
```
docker image <command> [image-name]

- docker image pull | push [image-name]
- docker image ls, docker image -al
- docker image inspect nginx
- docker search python
- docker image rm nginix #도커 이미지 삭제
- docker image prune  #사용하지 않는 모든 이미지 삭제
- docker rmi $(docker images -q)
- docker login #도커 허브 로그인
- docker logout

```


### install container (example)
- docker container run --name centos centos:latest /bin/echo 'Hello Docker!'

### container 실행
- docker ps -al
- docker ps
- docker ps -a

```
docker [container] <command> [options]

- docker container run -it --name "dev" centos /bin/bash
- docker start|stop dev
- docker attach deve #container들어가기
- docker rm <container-id>
- docker stop $(docker ps -aq) #모든 컨테이너 중지
- docker rm $(docker ps -aq) #모든 컨테이너 삭제
```

### Set up the CentOS Container: mydealdev (컨테이너 이름)
1. set the file sharing (preference) - 디렉토리 연결 (깃허브, 플라스크, 도커)
2. run by centos image
3. install base package
4. install nginx
5. install volta, node, pm2
6. install python3
7. commit this image
8. rerun with ports & volume


1. docker file sharing  -preference
원하는 프로젝트 폴더를 resources / file sharing에 연결

2. cento image 설정

#### centos 정보
```
mydealdev:port
Centos
Nginx: 80
volta
PM2 by volta
Node, NPM (Express): 7101
python3, pip (flask): 7201
mySQL 8
rsync, rcp setting: 22
```

#### 컨테이너 설치 및 접속
docker container run -it --name "mydealdev" --hostname mydealdev -v /Users/michaelflynn/Desktop/test/scd:/home/workspace -p 80:80 -p 7101:7101 -p 7201:7201 centos /bin/bash

docker start mydealdev
docker attach mydealdev

3. 기본 패키지 설치
- yum install epel-release -y
- yum install which -y
- yum install telnet -y
- yum install rsync -y
- yum install openssh-server openssh-clients -y


4. Install nginx
- yum 외부 저장소(repo) 추가

vi /etc/yum.repos.d/nginx.repo
```
[nginx]
name=nginx repo
baseurl=https://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=0
enabled=1
```

- yum install nginx -y
- nginx v nginx -s stop
- ps -ef |grep nginx #process 확인 
- 서비스 등록
systemctl start nginx
systemctl enable nginx
systemctl stop nginx

- local에서는 nginx로 바로 띄울수 있음

```
[root@mydealdev /]# nginx
[root@mydealdev /]# !ps
ps -ef | grep nginx
root       186     1  0 15:39 ?        00:00:00 nginx: master process nginx
nginx      187   186  0 15:39 ?        00:00:00 nginx: worker process
nginx      188   186  0 15:39 ?        00:00:00 nginx: worker process
root       190     1  0 15:39 pts/0    00:00:00 grep --color=auto nginx
```

- Check whether it works in the browser, localhost