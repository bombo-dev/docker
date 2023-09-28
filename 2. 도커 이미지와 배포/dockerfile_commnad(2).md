# Dockerfile 명령어

|     명령어     |                       설명                        |
| :------------: | :-----------------------------------------------: |
|    **FROM**    |                    기본 이미지                    |
|    **RUN**     |                  쉘 명령어 실행                   |
|    **CMD**     | 컨테이너 기본 실행 명령어(ENTRYPOINT 인자로 사용) |
|   **EXPOSE**   |                오픈되는 포트 정보                 |
|    **ENV**     |                   환경변수 설정                   |
|    **ADD**     |     파일 또는 디렉토리 추가. URL/ZIP 사용가능     |
|    **COPY**    |              파일 또는 디렉토리 추가              |
| **ENTRYPOINT** |             컨테이너 기본 실행 명령어             |
|   **VOLUME**   |              외부 마운트 포인트 생성              |
|    **USER**    |      RUN, CMD, ENTRYPOINT를 실행하는 사용자       |
|  **WORKDIR**   |                작업 디렉토리 설정                 |
|    **AGES**    |              빌드타임 환경변수 설정               |
|   **LABEL**    |                 key-value 데이터                  |
|  **ONBUILD**   |  다른 빌드의 베이스로 사용될 떄 사용하는 명령어   |

## 자주 사용되는 명령어 정리

참고) 대괄호는 생략가능을 의미한다.

#### FROM

```zsh
FROM [--platform=<platform>] <image>[:<tag>] [AS <name>]

# FROM ubunut:latest
# FROM node:12
```

- 베이스 이미지를 지정하는 명령어

#### COPY

```zsh
COPY [--chown=<user>:<group>] <src>... <dest>

# COPY index.html /var/www/html/
# COPY ./app /usr/src/app
```

- 파일 또는 디렉터리를 추가한다. 즉, source 의 데이터를 destination에 옮긴다.

#### RUN

```zsh
RUN <command>

# RUN apt-get update
```

- 쉘 명령어를 실행하는 명령어이다.

#### EXPOSE

```zsh
WORKDIR /path/to/workdir

# EXPOSE 8000
```

- 컨테이너에서 사용하는 포트 정보이다.

#### CMD

```zsh
CMD ["executable", "param1", "param2"]
CMD command param1 param2

# CMD ["node", "app.js"]
# CMD node app.js
```

- 컨테이너 생성 시 실행할 명령어이다.

보통 위에 있는 명령어로도 대부분의 문제를 해결 할 수 있다고 한다.
더 많은 정보는 https://docs.docker.com/engine/reference/builder/ 에서 확인이 가능하다.
