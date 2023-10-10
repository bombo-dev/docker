# run

컨테이너를 실행하는 명령어이다. 기본적인 구조는 다음과 같다.

```zsh
docker run [OPTIONS] IMAGE[:TAG|@DIGEST] [COMMAND] [ARG...]
```

### OPTIONS에서도 중요한 옵션들

|     옵션      |                          설명                          |
| :-----------: | :----------------------------------------------------: |
|    **-d**     |             detached mode(백그라운드 모드)             |
|    **-p**     |            호스트와 컨테이너의 포트를 연결             |
|    **-v**     |          호스트와 컨테이너의 디렉토리를 연결           |
|    **-e**     |         컨테이너 내에서 사용할 환경변수를 설정         |
|  **--name**   |                   컨테이너 이름 설정                   |
|   **--rm**    |           프로세스 종료시 컨테이너 자동 제거           |
|    **-it**    | -i와 -t를 동시에 사용한 것으로 터미널 입력을 위한 옵션 |
| **--network** |                     네트워크 연결                      |

## ubuntu 컨테이너 만들기 실습

```zsh
docker run ubuntu:20.04
```

위 명령어를 입력하면 ubuntu:20.04 버전이 없다면 이미지를 설치하고 실행이 되었다가 바로 종료가 될 것이고, 있다면 잠깐의 대기시간 이후 바로 종료가 될것이다.

```zsh
docker rmi ubuntu:20.04 # 설치되어 있는 ubuntu:20.04 이미지를 제거
```

이는 컨테이너의 동작원리에 근거해서 정상적으로 동작하고 있는 것이다.

### docker run 동작원리

run 명령어를 사용하면 사용 할 이미지가 저장되어 있는지 확인하고 없다면 **다운로드(pull)** 한 후 컨테이너를 **생성(create)** 하고 **시작(start)** 한다.

컨테이너는 정상적으로 실행됐지만 명령어를 전달하지 않았기 때문에 컨테이너는 생성되자마자 종료가 되는 것이다. 컨테이너는 프로세스이다. 즉, 실행중인 프로세스가 없으니 컨테이너가 종료 된 것이다.

### 컨테이너 내부로 들어가기

```zsh
docker run --rm -it ubuntu:20.04 /bin/sh
```

터미널 입력을 위해 `-it` 옵션을 추가하고 컨테이너 내부인 `/bin/sh`로 들어간다. 이때, 프로세스가 종료되면 컨테이너가 자동으로 삭제되도록 `--rm` 옵션도 추가해주었다.
만약, `--rm` 옵션이 없다면 컨테이너가 종료되더라도 삭제되지 않고 남아 있어서 수동으로 삭제를 해주어야 한다.

기본적으로 `--rm` 옵션이 없다면 컨테이너에서 빠져나왔을 때 중지 상태가 되어서 남아있다. 이를 방지해주는 것이다.

해당 명령어를 실행하고 난 결과를 확인해보자.

```zsh
ls
bin dev home media opt root sbin ...

cat /etc/issue
Ubuntu 20.04.6 LTS \n \l
```

현재 ubuntu 20.04의 리눅스에서 터미널 명령어를 수행하고 있는 것이다. 이를 위해 우리가 이전에 수행 할 때 `-it` 옵션을 추가 한 것이다.

## CentOS 실행하기

```zsh
docker run --rm -it centos:8 /bin/sh
```

```zsh
sh-4.4# ls
bin  etc   lib	  lost+found  mnt  proc  run   srv  tmp  var
dev  home  lib64  media       opt  root  sbin  sys  usr

sh-4.4# cat /etc/issue
\S
Kernel \r on an \m
```

우분투와 패키지가 다르게 뜨는 것을 볼 수 있고, CentOS는 커널위에서 동작함을 확인 할 수 있다.

도커는 **다양한 리눅스 배포판을 실행** 할 수 있다. 그러나 공통점은 모두 동일한 커널을 사용한다는 것이다. 만약, Ubuntu 혹은 CentOS에 포함된 다양한 기능들이 필요없다면 `Alpine`이라는 초소형 이미지(5MB) 를 사용 할 수 도 있다.

## 웹 애플리케이션 실행

웹 애플리케이션을 실행해보는 예제를 실행해보자.

```zsh
docker run --rm -p 5678:5678 hashicorp/http-echo -text="hello world"

> curl localhost:5678
hello world
```

container의 5678 포트를 host의 5678 포트와 연결했다. 이때, 다음과 같이 host의 다른 포트에 또 해당 이미지를 연결 할 수 있다.

```zsh
docker run --rm -p 5679:5678 hashicorp/http-echo -text="hello"

> curl localhost:5679
hello
> curl localhost:5678
hello world
```

두 개의 컨테이너가 host의 포트와 연결되어 수행 되는 모습을 볼 수 있다.

### 문제 발생

> The requested image's platform (linux/amd64) does not match the detected host platform (linux/arm64/v8) and no specific platform was requested

다음과 같은 에러가 발생했다. 아무래도 arm64에 지원하지 않는 이미지 파일인가보다. 이럴 때에는 이미지 파일과 플랫폼을 일치시켜주면 된다는 말이 있었다. 다음과 같이 명령어를 수정해주었다.

```zsh
docker run --rm -p 5678:5678 --platform linux/amd64 hashicorp/http-echo -text="hello world"
```

이전과 같은 오류가 사라졌지만, 다음과 같은 예외를 또 다시 볼 수 있었다.

> runtime: failed to create new OS thread (have 2 already; errno=22)
> fatal error: newosproc

해당 문제를 해결하기 위한 방법을 찾지 못했는데 동일한 문제를 커뮤니티에서 확인 할 수 있었다.
해당 이미지를 사용하지 않고 다른 이미지를 사용하라는 내용이었다.

이미지 명령어를 다음과 같이 수정해주었다.

```zsh
docker run --rm -p 8080:8080 jxlwqq/http-echo --text="hello"
```

~수행 후에 curl을 사용해봤지만 묵묵부담이었다.~

- 동일한 쉘에서 수행해서 생긴 문제였다. 다른 쉘을 키고 실행하면 정상적으로 curl을 사용 할 수 있다.

웹 사이트에 아예 localhost:8080 을 입력하니 정상적으로 hello를 출력하는 것을 확인 할 수 있었다. 위 예제와 동일하게 같은 이미지를 host의 다른 포트에 연결해보자.

```zsh
docker run --rm -p 5678:8080 jxlwqq/http-echo --text="hello world"
```

다른 쉘에서 다음과 같은 명령어를 호출해봤다.

```zsh
curl localhost:5678
```

hello world가 출력되는 것을 확인 할 수 있었다.

## 레디스 설치

인 메모리 데이터베이스인 Redis를 설치해보자.

```zsh
docker run --rm -p 1234:6379 redis

telnet localhost 1234

set hello world
+OK
get hello
$5
world

quit
```

redis를 정상적으로 실행 할 수 있는 것으로 보인다. 만약 하나의 서버에서 애플리케이션에 대해서 각기 다르게 redis를 사용해야 할 때, docker를 사용한다면 보다 빠르게 다른 포트로 redis를 수행 할 수 있을 것이다.

## MYSQL 설치

강의에서는 5.7 버전을 설치해주어서 8.0 버전으로 진행을 해주었다. 이에 따라 명령어도 달라 질 것이다.

```zsh
docker run --rm -p 3306:3306 --name=docker_mysql8 -d mysql

docker exec -it docker_mysql8 mysql -uroot -p
```

다음과 같은 명령어를 입력하면 패스워드를 입력하라고 한다. 하지만 우리는 루트 패스워드의 비밀번호를 알지 못한다. docker에서 root의 패스워드를 찾아 볼 수 있다.

```zsh
docker logs docker_mysql8 2>&1 | grep GENERATED

GENERATED ROOT PASSWORD: [패스워드]
```

다음과 같이 ROOT PASSWORD를 확인해 볼 수 있다. 해당 패스워드를 복사하고 다시 mysql에 접속해보자.

```zsh
docker exec -it docker_mysql8 mysql -uroot -p

Enter Password: 복사한 패스워드

mysql>
```

정상적으로 실행이 됐다. 이제 `exit` 를 눌러서 컨테이너를 종료시켜보자.
매번 위와 같이 ROOT PASSWORD를 찾는 과정은 너무나도 귀찮다. root 패스워드 없이 컨테이너가 생성 될 수 있도록 명령어를 바꿔보자.

```zsh
docker run --rm -p 3306:3306 -e MYSQL_ALLOW_EMPTY_PASSWORD=true --name=docker_mysql8 -d mysql

이 순서대로 진행해왔으면 다음과 같은 문제점에 봉착하게 될 것이다.
```

```zsh
docker: Error response from daemon: Conflict. The container name "/docker_mysql8" is already in use by container
"39c64bd70886d1b35f2a94058c77e3e98a9c4b3c7411c66ceaee484f094c3908".
You have to remove (or rename) that container to be able to reuse that name.
```

컨테이너가 이미 실행되고 있다는 것이다. 우리는 `--rm`` 옵션을 붙여두어서 프로세스가 종료 된 이후에는 컨테이너가 삭제 되기를 예상했는데 왜 삭제가 안된것일까? 이는 백그라운드에서 실행하기 위한 -d 옵션때문에 발생 한 것으로 보인다.

> 해당 질문은 강사님에게 질문을 드린 상태이다. 답변을 받게 되면 내용을 추가한다.
- 강사님의 답변이 아닌 AI의 답변이지만 내용은 다음과 같다. mysql을 백그라운드로 실행을 한 상태에서 mysql 콘솔을 실행하고 종료한 것은 단순히 프로세스를 종료한 것이 아니기때문에
계속해서 백그라운드에서 해당 프로세스는 실행 중이기 때문에 컨테이너가 삭제되지 않은 것이다.

다음의 명령어를 사용하여 기존의 존재하던 docker를 삭제해보자.

```zsh
docker rm -f [dockerId | dockerName]
# docker rm -f docker_mysql8
```

정상적으로 삭제가 되고 난 이후에 다음 명령어를 실행해보자.

```zsh
docker run --rm -p 3306:3306 -e MYSQL_ALLOW_EMPTY_PASSWORD=true --name=docker_mysql8 -d mysql
```

아까와 달리 오류가 발생하지 않고 정상적으로 수행이 되었다. 이전과 동일하게 명령어를 수행하여 해당 mysql을 실행해보자.

```zsh
docker exec -it docker_mysql8 mysql
```

패스워드를 입력하지 않아도 정상적으로 수행되는 것을 확인 할 수 있다.

## exec 명령어

run 명령어와 달리 실행중인 도커 컨테이너에 접속할 때 사용하며 컨테이너 안에 ssh server등을 설치하지 않고 exec 명령어로 접속 할 수 있다.

## WordPress를 만들기 위한 mysql db 생성

이전에 생성해둔 mysql에 접속해서 db를 생성해보자.

```zsh
create database wp CHARACTER SET utf8;

### database 5.7
# grant all privileges on wp.* to wp@'%' identified by 'wp';
# flush privileges;

### database 8.0 up
# create user 'wp'@'%' identified by 'wp';
# grant all privileges on wp.* to wp@'%';
# flush privileges;

mysql> show databases;
```

정상적으로 데이터베이스가 생성된 것을 볼 수 있다.

## WordPress 블로그 실행

실습 진행 중 Error establishing a database connection 오류 발생으로 이후에 해결되면 서술
