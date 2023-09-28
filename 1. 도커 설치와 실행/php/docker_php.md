# php 컨테이너를 설치해보자.

```zsh
$ docker run --rm -v $(pwd)/hello.php:/app/hello.php php:7 php /app/hello.php
```

처음에 시도한 명령어는 다음과 같다.
`docker run --rm -d -v $(pwd)/hello.php:/app/hello.php php:7 php /app/hello.php`

이전에 만들어두었던 nginx 처럼 백그라운드에서 실행되도록 하였는데, 콘솔에 연결해두었던 info 정보가 출력되지 않았다.
`docker ps` 로 확인을 해봤을 때, 컨테이너가 종료된 것으로 보아 정상적으로 수행을 하고 종료된 것으로 보였다.

백그라운드에서 돌아가서 CLI 를 보여주지 않고 `-d` 명령어를 제거해봤는데 정상적으로 출력되었다.
여기서 중요한 사실을 알 수 있다. CLI 명령어를 입력했을 때 **백그라운드로 돌아가게 한다면 따로 log로 확인하지 않는 이상 쉘에서 확인 할 수 없다.**