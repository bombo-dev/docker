# nginx 컨테이너를 설치해보자.

```zsh
$ docker run -d --rm -p 50000:80 -v $(pwd)/index.html:/usr/share/nginx/html/index.html nginx
```
이후에 웹 브라우저에서 `localhost:50000` 을 호출하면 만들어두었던 index.html 이 호출된다.