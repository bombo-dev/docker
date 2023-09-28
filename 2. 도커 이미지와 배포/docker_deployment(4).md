# 도커 이미지 배포
내 PC에 있는 도커 이미지를 다른 사람이 사용하게 하려면 어딘가에 배포를 해야한다.
이 때, 주로 도커 허브라는 곳을 이용을 하게 된다.
도커 허브 사이트는 hub.docker.com 이다.

이미지 저장소에 도커 이미지를 저장하는 예제를 살펴보자.

```zsh
docker login
docker push {ID}/example
docker pull {ID}/example
```

신기한 부분은 도커 허브를 유료를 사용을 해야 private 하게 올릴 수 있고, 무료 버전은 무조건 public 하게 공유해야 한다.
