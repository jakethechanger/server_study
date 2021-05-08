# 시스템 개발환경


1. 개발환경: Local PC (VS, MySql workbench, Git client)
2. 개발서버: Docker (Nginx, Node, Flask, MySQL)
3. 스테이징서버: Docker/Cloud (Ngnix, Node, Flask, MySQL)
4. 서비스 서버: Cloud (Nginx, Node, Flask, MySQL)

## 시스템 개발환경 : 개발서버

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
#컨테이너가 돌고 있는지 확인
root       186     1  0 15:39 ?        00:00:00 nginx: master process nginx
nginx      187   186  0 15:39 ?        00:00:00 nginx: worker process
nginx      188   186  0 15:39 ?        00:00:00 nginx: worker process
root       190     1  0 15:39 pts/0    00:00:00 grep --color=auto nginx
```

- Check whether it works in the browser, localhost

- nginx -s stop

5. Install volta
curl https://get.volta.sh | bash
// It should restart (exit->start->attach) to use volta

6. Install node
volta install node

7. Install pm2
volta install pm2

8. Install python3
yum install python3 -y

node -v
npm -v
pip3 --version


### 도커 이미지 생성

*** Do this before commit
```
v. nginx.conf - local.conf(local.mydeal.com)
v. .bashrc - ll, TZ, nginx, cd
v. /etc/hosts & rysnc
```

Nginx 설정
nginx
cd /etc/nginx
ls -al
vi nginx.conf
(그런데 nginx.conf를 바로 건드리지 않고 conf.d/local.conf를 수정해서 사용하는것이 낫다.)

//Change root path for Docker server - 도커는 로컬 서버이기때문에! Treat like server
/home/workspace/www/mydealdev
도커안의 센토스안에 폴더와 연결... 로컬 머신의 폴더와 연결이 아님. 그런데 이렇게 하면 docker share folder와 연결됨
Oh!!!!!! 여기서 연결함
```
docker container run -it --name "mydealdev" --hostname mydealdev -v /Users/michaelflynn/Desktop/test/scd:/home/workspace -p 80:80 -p 7101:7101 -p 7201:7201 centos /bin/bash
```


```
 For more information on configuration, see:
#   * Official English Documentation: http://nginx.org/en/docs/
#   * Official Russian Documentation: http://nginx.org/ru/docs/

user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;

# Load dynamic modules. See /usr/share/doc/nginx/README.dynamic.
include /usr/share/nginx/modules/*.conf;

events {
    worker_connections 1024;
}

http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   65;
    types_hash_max_size 2048;

    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;

    # Load modular configuration files from the /etc/nginx/conf.d directory.
    # See http://nginx.org/en/docs/ngx_core_module.html#include
    # for more information.
    include /etc/nginx/conf.d/*.conf;

    server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  _;
        #root         /usr/share/nginx/html;
        root         /home/workspace/www/mydealdev
        ************ root 변경 (서버의 개발 디렉토리) 이렇게 하면 docker share folder랑 연결된다. 

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        location / {
        }

        error_page 404 /404.html;
            location = /40x.html {
        }

        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }
    }

# Settings for a TLS enabled server.
#
#    server {
#        listen       443 ssl http2 default_server;
#        listen       [::]:443 ssl http2 default_server;
#        server_name  _;
#        root         /usr/share/nginx/html;
#
#        ssl_certificate "/etc/pki/nginx/server.crt";
ssl_certificate_key "/etc/pki/nginx/private/server.key";
#        ssl_session_cache shared:SSL:1m;
#        ssl_session_timeout  10m;
#        ssl_ciphers PROFILE=SYSTEM;
#        ssl_prefer_server_ciphers on;
#
#        # Load configuration files for the default server block.
#        include /etc/nginx/default.d/*.conf;
#
#        location / {
#        }
#
#        error_page 404 /404.html;
#            location = /40x.html {
#        }
#
#        error_page 500 502 503 504 /50x.html;
#            location = /50x.html {
#        }
#    }

}
```

add index.html file in the root server folder for testing

nginx -s reload
index.html 파일이 정상적으로 호스팅되면 성공




그런데 보통 nginx.conf를 바로 건드리지 않고 conf.d/local.conf를 수정해서 사용한다.


```
    server {
        listen       80;
        server_name local.cotnals.com;
        <!-- 이렇게 로컬 호스트를 따로 설정해도 됨 -->
        <!-- localhost -->
 
        location / {
            root /home/workspace/www/mydealdev;
            index index.html
            try_files $uri /index.html
    }
    }
```

그런데 local.cotnals.com은 없는 호스트거나 다른 사람 소유일수 있기때문에 로컬PC의 호스팅을 수정해줘야 함
그래서 로컬 PC(도커PC 아님)

vi /etc/hosts

```
# Host Database
#
# localhost is used to configure the loopback interface
# when the system is booting.  Do not change this entry.
##
127.0.0.1       localhost
127.0.0.1       local.cotnals.com 
<!-- 이렇게 넣어주면 됨 -->
255.255.255.255 broadcasthost
::1             localhost
# Added by Docker Desktop
# To allow the same kube context to work on the host and the container:
127.0.0.1 kubernetes.docker.internal
# End of section
```

브라우저에 주소를 입력하면 /etc/hosts로 먼저가서 찾고 없으면 dns로 간다.

그래서 브라우저에 도메인 입력하면 도메인을  conf파일의 server_name과 비교해서 localhost는 nginx.conf가 처리하고 local.cotnals.com은 local.conf가 처리한다.


### docker exit하면 docker start, docker attach를 다시 해줘야 하는 번거로움
그래서 ctrl p q로 빠져나오면 attach만 다시 하면된다


### .bashrc v / .bash_profile
ssh를 사용해서 로그인 하면 .bash_profile을 사용하기때문에 그안의 셋팅, 예를들어 alias가 사용 가능
그러나 docker는 attach해서 사용하는것이기 때문에 .bash_profile이 아닌 .bashrc를 사용한다.


### TZ 설정
rm /etc/localtime
ls -s
/usr/share/zoneinfo/Asia/Seoul
/etc/localtime
.bashrc
export TZ = "Asia/Seoul


### ssh keygen
- create in root in Docker Server
ssh-keygen i rsa
ls -al .ssh
 public key를 서버에 넣어주면 ssh를 통해 실제 서버에 연결 가능

ssh-copy-id -i .ssh/id_rsa.pub root@<ip-address> -p <port-number>


### rsync
create check.txt
vi check.txt
This is Server! # 서버에 접속했으면 확인하기위해 적는 메시지

rsync at server
systemctl start rsyncd.service //d = daemon
systemctl enable rsyncd.service //enable 서버가 시작되면 자동시작

rsync at client(docker)
rsync -avz -e 'ssh -p 50000' <host-ip-address>:/root/check.txt
//avz 압축해서 데이터를 서로 주고 받아라는 의미


## Commit Image
- docker commit mydealdev mydealdev img

#### Test image
-docker create -it --name "imgtest" -v </Users/jade/workspace/sfd:/home/worksapce> -p 80:80 mydealdevimg //run이랑 똑같음, 테스트용 이미지, nginx만 테스트, 받는 사람은 Volume을 연결해야한다.
docker start imgtest
docker attach imgtest

#### Save
docker save -o mydealdevimg.tar mydealdevimg
//o output

#### Load
docker load -i mydealdevimg.tar
자기 도커에 이미지 생성
