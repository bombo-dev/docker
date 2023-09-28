# nodejs 로 웹 애플리케이션을 만드는 이미지 만들기

해당 예제에서는 fastify 라는 라이브러리를 사용한다. [fastify](https://fastify.dev/docs/latest/Guides/Getting-Started)
위 사이트에 접속하게 되면 fastify를 시작하기 위한 과정들이 담겨있다.

```zsh
npm init

npm -i fastify --save
```

npm은 node.js 가 설치가 되어있어야 사용이 가능하다.
모듈들이 정상적으로 설치가 되었다면 이어서 해당 패키지에 app.js를 만들고 다음과 같은 코드를 작성해준다.

```js
// Require the framework and instantiate it

const fastify = require('fastify')({
  logger: true,
});

// Declare a route
fastify.get('/', function (request, reply) {
  reply.send({ hello: 'world' });
});

// Run the server!
fastify.listen({ port: 3000 }, function (err, address) {
  if (err) {
    fastify.log.error(err);
    process.exit(1);
  }
  // Server is now listening on ${address}
});
```

이제 실행해보자.

```zsh
node app.js

{"level":30,"time":1695888455342,"pid":13593,"hostname":"munjong-un-ui-MacBookAir.local","msg":"Server listening at http://[::1]:3000"}
{"level":30,"time":1695888455342,"pid":13593,"hostname":"munjong-un-ui-MacBookAir.local","msg":"Server listening at http://127.0.0.1:3000"}
```

다음과 같이 서버가 정상적으로 올라간 것을 볼 수 있다. 실제로 웹에 `localhost:3000` 을 입력하면 다음과 같은 화면을 만날 수 있다.

```json
{
  "hello": "world"
}
```

위에 코드에서 수정해주어야 할 부분이 있다. 현재는 localhost에서 접속해서 수정 할 부분이 없지만 docker에서 돌아가야한다면 조금 다르다.

```js
fastify.listen({ port: 3000, host: '0.0.0.0' }, function (err, address) {
  if (err) {
    fastify.log.error(err);
    process.exit(1);
  }
  // Server is now listening on ${address}
});
```

다음처럼 host를 추가를 해주자.
이제 빌드를 수행하기 위한 Dockerfile을 한 번 만들어보자.

동일한 folder에 Dockerfile을 만들자.
그리고 다음 내용을 작성해주자.

```zsh
# 1. node 설치
FROM      ubuntu:22.04 # 22.04 버전의 ubuntu를 설치를 한다.
RUN       apt-get update # apt-get 을 업데이트 해준다.
RUN       DEBIAN_FRONTEND=noninteractive apt-get install -y curl
RUN       curl -sL https://deb.nodesource.com/setup_16.x | bash -
RUN       DEBIAN_FRONTEND=noninteractive apt-get install -y nodejs

# 2. 소스 복사
COPY      . /usr/src/app # 해당 폴더에 소스 복사

# 3. Nodejs 패키지 설치
WORKDIR   /usr/src/app # 해당 폴더로 이동
RUN       npm install # npm install 수행

# 4. WEB 서버 실행 (Listen 포트 정의)
EXPOSE 3000 ## 컨테이너 포트
CMD node app.js # 실행 할 명령어를 지시자를 포함해서 명시
```

현재 상태로 build를 하게 되면 포함되지 말아야 할 node_modules 폴더까지 같이 빌드가 되어버린다.
이를 해결하기 위해서, .dockerignore 파일을 만들자.

`node_modules/*` 다음을 작성해주어 포함되지 않도록 한다.
이제 이미지를 만들어보자

```zsh
docker build -t web .
```

이제 이전에 켜두었던 서버는 끄고 docker에서 서버를 띄워보자. 다음 명령어를 실행한다.
```zsh
docker run -p 3000:3000 web
```

정상적으로 수행되는 것을 볼 수 있다.

여기서 Dockerfile 을 조금 더 개선해보자. 현재 Dockerfile을 보면 ubuntu curl 설치, nodejs 설치까지 많은 부분을 할애하고 있다.
하지만, 누군가가 이미 위의 작업을 해두었다면 이를 더 빠르고 편리하게 작성 할 수 있다.

Dockerfile을 다음과 같이 수정해보자.
```zsh
# 1. node 설치
FROM      node:12

# 2. 소스 복사
COPY      . /usr/src/app

# 3. Nodejs 패키지 설치
WORKDIR   /usr/src/app
RUN       npm install

# 4. WEB 서버 실행 (Listen 포트 정의)
EXPOSE 3000
CMD node app.js
```

이제 도커 이미지 빌드를 최적화해보자. 도커는 동일한 이미지를 만들 때에는 cache를 사용해서 빠르게 만들 수 있다.
하지만, 이미지를 빌드하고자 하는 파일에서 뭔가 바뀌게 되면 cache와 일치하지 않아 새로 만들어주어야 한다.
예를 들어서, app.js 에서 json으로 보내주는 부분을 다음과 같이 바꾼다고 하자.

```js
//reply.send({ hello: 'world' }); 

//reply.send({ hello: 'world!' }); 

reply.send({ hello: 'world~' }); 
```

별 다르게 크게 바뀐 것은 없어 보이는데, 캐시와 일치하지 않아 매번 새로 설치하는 것을 볼 수 있다.
그리고 캐시가 일치하지 않다면 일치하지 않은 부분부터 아래 명령어에도 캐시를 적용하지 않는다.

가장 많이 느려지는 부분은 `npm install` 이 부분인데, 이를 개선하기 위해서 패키지를 우선 설치하도록 DockerFile을 변경해보자.

Dockerfile을 다음과 같이 수정해보자.
```zsh
# 1. node 설치
FROM      node:12

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
```

이제 소스코드가 변경되더라도 소스코드를 복사하기 이전에 package는 캐시가 적용되어 매우 빠르게 이미지가 생성되는 모습을 볼 수 있다.
생성된 이미지의 용량을 살펴보자.

```zsh
REPOSITORY            TAG       IMAGE ID       CREATED             SIZE
web                   latest    d31584b3fd8d   11 minutes ago      877MB
```
size가 무려 877MB 이다. 많은 것들이 설치되어있기 때문으로 보이는데 이전에 배웠던 alpine을 이미지로 사용하면 용량을 많이 낮출 수 있다.
alpine은 필수적인 요소만 남기고 부가적인 것들은 전부다 빠진 것이다. Dockerfile을 다음과 같이 수정하자.

```zsh
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
```

이제 다시 이미지의 용량을 확인해보자.

```zsh
REPOSITORY            TAG       IMAGE ID       CREATED             SIZE
web                   latest    78c2bfd1fe1d   4 seconds ago       103MB
```

무려 103MB로 줄어들은 모습을 확인 할 수 있다. 용량이 넉넉하지 않다면 되도록 alpine을 사용하도록 하자.
