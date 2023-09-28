# docker_image

## 도커의 이미지란

도커는 레이어드 파일 시스템 기반이다. AUFS, BTRFS, Overlayfs 등이 있다.
이미지는 프로세스가 실행되는 파일되는 집합(환경) 이다. 프로세스는 환경이 변할 수 있고, 이 환경을 저장해서 새로운 이미지를 만드는 것이다.

도커 이미지는 두 가지로 분류된다.

1. **Only Read**
2. **Writable**

이렇게 두 가지이다.

### rootfs / Base Image

Base Image는 읽기 전용에 해당하는 이미지이다.
ex) docker pull ubuntu 를 통해서 ubuntu 이미지를 pull 받았다면 해당 ubuntu 이미지를 변경 할 순 없지만, 새로운 것들에 대해서 추가, 수정, 삭제는 가능하다.

위와 같은 상황을 실습으로 알아보자.

### Base Image에 새로운 것을 추가

```zsh
docker run -it --name git ubuntu bash # git 이라는 이름으로 ubuntu를 설치하고, bash 명령어를 실행한다.

root@xxx:/# git # git을 사용하기 위해 명령어를 입력해보자.
bash: git: command not found # git이 없다는 것을 볼 수 있다.
```

이제 ubuntu에 git을 설치해보자.

```zsh
root@xxx:/# apt-get update
root@xxx:/# apt-get install -y git
root@xxx:/# git --version

git version 2.34.1
```

정상적으로 깃이 설치된 것을 볼 수 있다. 우리는 기존의 Base Image에서 새로운 환경을 추가했다.
docker는 이렇게 변한 상태를 commit 즉, 형상관리가 가능하다.

현재 ubuntu 컨테이너가 올라와 있는 상태에서 commit을 해보자.

```zsh
docker commit git ubuntu:git
# docker commit container tag
```

커밋을 완료하고 나면 다음과 같은 응답을 살펴 볼 수 있다.
`sha256:c584a519745febce4f91b22cd7d8918dd4eaa8bd0e2e1af62bf7ad964bd46c92`

이어서 해당 커밋이 image에 잘 반영이 되었는지 한 번 살펴보자. 이미지를 조회하는 명령어를 사용하면 된다.

```zsh
docker images | grep ubuntu

ubuntu                git       c584a519745f   About a minute ago   184MB
ubuntu                latest    6a47e077731f   6 weeks ago          69.2MB
ubuntu                20.04     15c9d636cadd   8 weeks ago          65.7MB
```

위와 같이 3가지 결과를 볼 수 있고 **git이라는 태그의 ubuntu 이미지가 새로 생긴 것**을 볼 수 있다.

이제 우리가 커스터마이징해서 생성했던 docker 이미지를 이용하여 컨테이너를 새로 열어보자.

```zsh
docker run -it --name git2 ubuntu:git bash

root@xxx:/# git --version

git version 2.34.1
```

우리가 커스터마이징했던 이미지에는 git이 이미 설치가 되어있기때문에 설치하지 않더라도 git이 설치되어 있는 것을 볼 수 있다.

도커는 이처럼 새로운 상태를 이미지로 저장하는 기능을 가지고 있다. 도커가 새로운 상태를 이미지로 저장하는 과정을 이미지로 살펴보면 아래와 같다.

![Image](https://github.com/bombo-dev/docker/blob/main/2.%20%EB%8F%84%EC%BB%A4%20%EC%9D%B4%EB%AF%B8%EC%A7%80%EC%99%80%20%EB%B0%B0%ED%8F%AC/docker_custom_state.png)

### Dokcer Image 명명 규칙

```zsh
bombo/ubuntu:git01 .

# [namespace]/[image_name]:[Tag] [build_context]
```

예시를 한 번 살펴보자.

```zsh
REPOSITORY            TAG       IMAGE ID       CREATED          SIZE
jxlwqq/http-echo      latest    d7659fff7a4e   16 months ago    16.7MB
ubuntu                git       c584a519745f   18 minutes ago   184MB
```

REPOSITORY에 두 가지 케이스가 보인다. `/` 가 있느냐 없느냐의 차이이다.
`/` 가 없는 것들은 공식적으로 지원하는 이름이다. 그 외에 것들은 다 개인의 namespace를 명시한 것을 살펴 볼 수 있다.

### Docker Image 만들기

위에서 살펴봤던 명명 규칙을 바탕으로 Docker Image를 한 번 만들어보자.

```zsh
docker build -t bombo/ubuntu:git01 .
```

docker-compose 에서는 봤었지만, docker에서는 보지 못했던 build 명령어이다.
여기서 의문이 들 것이다.

> 이전에 commit 명령어로 docker image를 생성하지 않았었나요?

- 맞다. commit 명령어도 동일하게 docker image를 생성하는 명령어이다.
  하지만 docker build는 dockerfile을 사용하여 이전에 손 수 해주어야 했던 일을 file에 명시를 해주면 바로 만들어 줄 수 있고 **손 쉽게 배포도 가능**하다.

이제 dockerfile을 만들어서 build를 수행해보자.

```zsh
mkdir dockerfile_folder
cd dockerfile_folder
```

dockerfile을 폴더를 만들고 해당 폴더에 Dockerfile 을 만든다.
dockerfile에는 다음과 같은 명령어를 입력한다.

```zsh
FROM ubuntu:latest

RUN apt-get update
RUN apt-get install -y git
```

dockerfile이 생성되었다면 해당 폴더에서 위에서 적었던 명령어를 사용해보자. `docker build -t bombo/ubuntu:git01 .`
뭔가 로그를 살펴보면 우리가 이전에 명시했던 명령들이 실행되는 것을 볼 수 있다.

이어서 제대로 생성되었는지 image를 한 번 확인해보자.

```zsh
docker images | grep ubuntu

bombo/ubuntu          git01     a3b671cbebfc   31 seconds ago   184MB
```

위 처럼 우리가 build 한 이미지가 생성 된 것을 볼 수 있다.
그리고 해당 이미지를 실행하고 `git --version`` 명령어를 입력하면 이전과 동일하게 git이 설치되어있음을 확인 할 수 있다.

#### .dockerignore

추가적으로 docker image를 만들면서 해당 빌드 컨텍스트에서 image로 말고 싶지 않은 파일이 있을 수 있다.
git에서는 `.gitignore` 라는 이름으로 제공을 해주는 것처럼 docker 또한 `.dockerignore` 를 제공해주고 있다.
이를 활용했을 때의 장점은 다음과 같다.

1. **.git이나 민감한 정보를 제외가 가능**
2. **.git이나 에셋 디렉터리들을 제외하니 빌드 속도가 개선**

주의 할 점은 **이미지 빌드 시에 사용해야 하는 파일은 제외시키면 안된다!**

#### 번외

한 번에 성공하는 빌드는 없다.
TDD를 하듯이 해당 빌드가 성공 할 때까지 계속해서 수행하고, 리팩토링을 통한 최적화된 이미지를 생성하는 것이 중요하다.
