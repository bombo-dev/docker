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

## docker-compose 문법

#### Version

```zsh
version: '3'
```

- docker-compose.yml 파일의 명세버전이다.
- docker-compose.yml 버전에 따라 지원하는 도커 엔진 버전도 다르다.

#### services:

```zsh
services:
  postgres:
  django:
```

- 실행할 컨테이너를 정의한다. 위는 사실상 다음과 같다. 
`docker run --name=django django`, `docker run --name=postgres postgres`

#### image
```zsh
services:
  django:
    image: django-sample
```

- 컨테이너에 사용할 이미지 이름과 태그이다. 
- 만약 태그를 생략한다면 latest 이미지를 받아오며, 이미지가 없다면 자동으로 pull 한다.

#### ports
```zsh
services:
  django:
    ports:
      - "8080:80"
```

- 컨테이너와 연결할 포트들을 정의하는 부분이다.

#### environment

```zsh
services:
  mysql:
    environment:
      - MYSQL_ROOT_PASSWORD: password
```

- 컨테이너에서 사용할 환경변수들을 명시 할 수 있다.

#### volumes

```zsh
services:
  django:
    volumes:
      - ./app:/app
```

- 마운트하려는 디렉터리들이다. 

#### restart

```zsh
services:
  django:
    restart: always
```

- 재시작 정책을 정의 할 수 있다. 재시작 옵션은 여러가지가 있다.

> 아래 옵션 조사해서 추가
1. no
2. always
3. on-failure
4. unless-stopped

#### build

```zsh
django:
  build:
    context: .
    dockerfile: ./compose/django/Dockerfile-dev
```

- 이미지를 자체 빌드 후에 사용한다.
- image 속성 대신 사용할 수 있다.
- 여기에는 사용할 별도의 도커 파일을 만들어주어야 한다.