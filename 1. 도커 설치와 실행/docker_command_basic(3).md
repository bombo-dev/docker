## 도커 기본 명령어

### ps (process list)
```zsh
docker ps # 실행중인 컨테이너 목록을 확인한다.

docker ps -a #중지 한 것을 포함한 컨테이너 목록을 확인한다.
```
- 도커에 있는 컨테이너 목록을 확인하는 명렁어

### stop
```zsh
docker stop [OPTIONS] CONTAINER [CONTAINER...] # 실행중인 컨테이너를 중지하는 명령어
# docker stop docker_mysql8 docker_wordpress
# dokcer stop 05f7c846ad6e(Container ID)
```
- 실행중인 컨테이너를 하나 또는 여러 개 중지

### rm
```zsh
docker rm [OPTIONS] CONTAINER [CONTAINER...]
# docker rm docker_mysql8
```
- 종료된 컨테이너를 완전히 제거하는 명령어

### logs
```zsh
docker logs [OPTIONS] CONTAINER
# docker logs docker_mysql8
```
- 컨테이너가 정상적으로 동작하는지 확인 할 수 있는 로그 확인
- 기본 옵션
  - `-f` : 로그를 켜놓은 상태에서 업데이트 된 로그를 지속적으로 확인 할 수 있다.
  - `--tail` : --tail [숫자] 발생한 로그에서 아래에서 숫자 줄 만큼 보여주는 옵션이다.

### images
```zsh
docker images [OPTIONS] [REPOSITORY[:TAG]]
```
- 도커가 다운로드 한 이미지를 조회하는 명령어

### pull
```zsh
docker pull [OPTIONS] NAME[:TAG|@DIGEST]

# docker pull ubuntu:18.04
```
- 이미지를 다운로드하는 명령어
- run 명령어를 사용하더라도 기본적으로 설치가 되기 때문에 이런 명령어도 있구나 하자.

### rmi
```zsh
docker rmi [OPTIONS] IMAGE [IMAGE...]
```
- 이미지를 삭제하는 명령어이다. 단, 주의할 점이 있다.
- images 명령어로 조회했던 모든 image 들을 삭제 할 수 있지만, **컨테이너가 실행중인 이미지들은 삭제되지 않는다.**

### network create
```zsh
docker network create [OPTIONS] NETWORK

# docker network create app-network
```
- 컨테이너끼리 통신할 네트워크를 만드는 명령어

### network connect
```zsh
docker network connect [OPTIONS] NETWORK CONTAINER

# docker network connect app-network docker_mysql
```
- 기존에 생성된 컨테이너에 네트워크를 추가하는 명령어

### volume mount (-v)

```zsh
docker stop docker_mysql
docker rm docker_mysql

docker run --rm -p 3306:3306 -e MYSQL_ALLOW_EMPTY_PASSWORD=true --name=docker_mysql8 -d mysql
```

가상의 환경에 있는 데이터도 컨테이너를 제거하면 컨테이너에 있던 데이터가 전부 다 날라가는 현상이 발생한다.
기존에 해당 데이터베이스를 바라보던 다른 애플리케이션에서 장애가 발생하게 되는 것이다.

이러한 문제를 해결하기 위해서 저런 데이터들을 mount 시킬 수 있다.
```zsh
docker run --rm -p 3306:3306 -e MYSQL_ALLOW_EMPTY_PASSWORD=true --name=docker_mysql8 -d -v /Users/munjong-un/Documents/study/docker/mysql:/var/lib/mysql mysql
```

이전 처럼 데이터베이스를 만들고 삭제한 이후에 다시 위 명령어를 실행하면 이전에 만들어두었던 데이터베이스가 남아있는 것을 확인 할 수 있다.