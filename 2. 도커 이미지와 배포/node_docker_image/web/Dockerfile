# 1. node 설치
FROM      node:12-alpine

# 2. 패키지를 우선 설치
COPY ./package* /usr/src/app/
WORKDIR /usr/src/app
RUN npm install

# 3. 소스 복사
COPY      . /usr/src/app

# 4. Nodejs 설치 된 패키지로 이동
WORKDIR   /usr/src/app

# 5. WEB 서버 실행 (Listen 포트 정의)
# 컨테이너 포트
EXPOSE 3000 
CMD node app.js