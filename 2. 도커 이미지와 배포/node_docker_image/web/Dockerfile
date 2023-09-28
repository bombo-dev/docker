# 1. node 설치
FROM      ubuntu:22.04
RUN       apt-get update
RUN       DEBIAN_FRONTEND=noninteractive apt-get install -y curl
RUN       curl -sL https://deb.nodesource.com/setup_16.x | bash -
RUN       DEBIAN_FRONTEND=noninteractive apt-get install -y nodejs

# 2. 소스 복사
COPY      . /usr/src/app

# 3. Nodejs 패키지 설치
WORKDIR   /usr/src/app
RUN       npm install

# 4. WEB 서버 실행 (Listen 포트 정의)
EXPOSE 3000
CMD node app.js