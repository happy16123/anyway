# anyway
공부하면서 정리한 사소한 기록들

# 
```bash
# docker 설치
apt-get update

apt-get install \
	apt-transport-https \
	ca-certificates \
	curl \
	gnupg-agent \
	software-properties-common

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable"

apt-get update

apt-cache madison docker-ce (두번째 문자열이 버젼임)
apt-get install docker-ce=<VERSION_STRING> docker-ce-cli=<VERSION_STRING> containerd.io

# docker compose 설치
curl -L https://github.com/docker/compose/releases/download/1.27.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose
```

```bash
# mysql pull 받기
docker pull mysql:latest

# docker-compose 백그라운드 실행
docker-compose up -d

docker exec -it (컨테이너 이름) /bin/bash
# 이후 mysql 명령어 실행
```

```bash
# nignx /etc/nginx
# sites-available에 파일생성
server {
 
    root ../../../dist;
 
    index index.html;
 
    server_name -IP 혹은 Domain-;
 
    location / {
        try_files $uri $uri/ /index.html;
    }
}

# image 접근
server{
        listen 8888;
        root /;
        server_name j3a402.p.ssafy.io;

        location /upload/.*(png|jpg|jpeg|gif|ico|swf)$ {
                alias /upload;
        }
}

# 파일 설정 후 하는 작업
ln -s /etc/nginx/sites-available/[config file name] /etc/nginx/sites-enabled/
```

```bash
# docker-compose.yml
version: "3"

services:
        db:
                image: mysql:latest
                container_name: mysqldb
                ports:
                        - 3306:3306
                environment:
                        - MYSQL_ROOT_PASSWORD="비밀번호"
                volumes:
                        - /home/ubuntu/mysql:/var/lib/mysql
                command:
                        - --character-set-server=utf8mb4
                        - --collation-server=utf8mb4_unicode_ci

services:
         jenkins:
                image: jenkins/jenkins:lts
                container_name: jenkins
                restart: always
                user: root
                volumes:
                        - /home/ubuntu/jenkins:/var/jenkins_home
                        - /var/run/docker.sock:/var/run/docker.sock
                ports:
                        - 8080:8080
                        - 50000:50000
                environment:
                        TZ: "Asis/Seoul"
```

```bash
# jenkins 최초 접근 시 비밀번호 입력
cat /var/jenkins_home/secrets/initialAdminPassword

# Maven Build
# 각 프로젝트 별 빌드 후 배포 예정
# 테스트 없이 빌드
clean package -Dmaven.test.skip=true

# docker not found 오류 발생 시(jenkins 내에서 도커 설치하는듯?)
docker exec -it jenkins_id /bin/bash
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh

# docker image none 삭제
docker rmi $(docker images -f "dangling=true" -q)

# test라는 이름의 컨테이너 중지 및 컨테이너 삭제, 이미지까지 삭제
docker stop test || true && docker rm test || true && docker rmi test || true

# discovery라는 이미지를 test라는 이름으로 8761 포트로 실행
docker run -d -p 8761:8761 --name test discovery

# Dockerfile을 기준으로 discovery라는 이미지 이름으로 빌드
docker build -f Dockerfile -t discovery .
```

```bash
# Dockerfile 예시
FROM openjdk:8-jdk-alpine

VOLUME /a402

# git pull 받은 최상위 루트에서 시작되는듯
COPY backend/Discovery/target/*.jar discovery.jar

ENTRYPOINT ["java", "-jar", "discovery.jar"]
```
