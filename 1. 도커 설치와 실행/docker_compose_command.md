# docker-compose 명령어

#### up

```zsh
docker-compose up
docker-compose up -d # docker run -d 옵션과 동일하다.
docker-compose up --force-recreate # 컨테이너를 새로 만든다.
docker-compose up --build # 도커 이미지를 다시 빌드한다. 단, build로 선언했을 때만 가능하다. 
```

- docker-compose.yml 에 정의된 컨테이너를 실행하는 명령어

#### start

```zsh
docker-compose start
docker-compose start wordpress # wordpress 컨테이너만 재개한다.
```

- 멈춘 컨테이너를 재개하는 명령어이다.

#### restart

```zsh
docker-compose start
docker-compose restart wordpress # wordpress 컨테이너만 재시작한다.
```

- 컨테이너를 재시작하는 명령어이다.

#### stop

```zsh
docker-compose stop
docker-compose stop wordpress # wordpress 컨테이너만 멈춘다.
```

- 컨테이너를 중지시키는 명령어이다.

#### down

```zsh
docker-compose down
```

- 컨테이너를 종료하고 삭제하는 명령어이다. `docker rm`과 동일하다.

#### logs

```zsh
docker-compose logs
docker-compose logs -f # docker logs -f container 와 동일하다.
```

- 컨테이너의 로그를 확인하는 명령어이다.

#### ps

```zsh
docker-compose ps
```

- 컨테이너의 목록을 확인하는 명령어이다.

#### exec

```zsh
docker-compose exec contanier command
# docker-compose exec wordpress bash
```

- 실행 중인 컨테이너에서 명령어를 실행하는 명령어이다.

#### build

```zsh
docker-compose build
docker-compose build wordpress # wordpress 컨테이너만 build 한다.
```

- 컨테이너 build 부분에 정의된 내용대로 빌드한다. 단, build로 선언된 컨테이너만 빌드가 된다.