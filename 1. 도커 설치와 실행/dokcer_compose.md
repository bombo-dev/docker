# Docker Compose

기존의 docker run 같은 명령어나 옵션들을 사용을 할 때, 휴먼에러가 잦게 발생할 가능성이 있다.
docker-compose는 이러한 휴먼 에러를 줄여 줄 수 있는 기술이다.

만약 Docker Desktop 으로 도커를 설치했다면 docker-compose는 이미 설치가 되어있다.
설치가 잘 되어있는지를 확인해보자.

```zsh
docker-compose version
```

만약 리눅스에서 도커를 별도로 설치했다면 docker-compose도 별도로 설치를 해주어야 한다.
docker-compose 설치 명령어는 다음과 같다.

```zsh
sudo curl -L "https://github.com/docker/compose/releases/download/1.26.0/docker-compose-$(uname -s)

sudo chmod +x /usr/local/bin/docker-compose
```

## docker-compose을 이용한 mysql, wordpress 연결

docker-compose는 yml파일로 명시한다.

```zsh
version: '1'
services:
  db:
    image: mysql:8.0
    volumes:
      - ./mysql:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: wordpress
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress
  wordpress:
    image: wordpress:latest
    volumes:
      - ./wp:/var/www/html
    ports:
      - "8080:80"
    restart: always
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
```

이제 docker-compose로 실행해보자.

```zsh
docker-compose up
```

쉘에서 보기 편하게 log가 올라 올 것이다. 이제 입력했던 port 번호인 localhost:8080을 입력해주면 wordpress가 뜨는 것을 확인해 볼 수 있다.
