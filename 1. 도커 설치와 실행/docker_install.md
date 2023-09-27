## 도커 설치
```zsh
curl -s https://get.docker.com/ | sudo sh
```
명령어를 입력하고 패스워드를 입력하면 리눅스 배포판에 따라 자동으로 최신버전의 도커를 설치한다.

#### CURL
- Client URL의 약자이다. 클라이언트에서 커맨드 라인이나 소스코드로 손 쉽게 웹 브라우저 처럼 활동할 수 있도록 해주는 기술이다. curl의 특징은 수 많은 프로토콜을 지원한다는 것이다.

ex) `DICT`, `FILE`, `FTP`, `FTPS`, `Gopher`, `HTTP`, `HTTPS`, `IMAP`, `IMAPS`, `LDAP`, `LDAPS`, `POP3`, `POP3S`, `RTMP`, `RTSP`, `SCP`, `SFTP`, `SMB`, `SMBS`, `SMTP`, `SMTPS`, `Telnet`, `TFTP`

**위 처럼 url을 가지고 할 수 있는 것들은 왠만하면 전부 다 할 수 있다.**
심지어는 SSL 또한 가능하다.

```zsh
sudo usermod -aG docker ubuntu
```
만약 ubuntu라면 위처럼 ubuntu 유저에 대한 권한을 추가한다.

docker for Mac / docker for Windows 라는 GUI를 사용하는 것이 일반적으로는 더 편하다.

### MacOS or Windows
도커는 기본적으로 linux를 지원하기 때문에 MacOS와 Windows에 설치되는 Docker는 가상머신에 설치된다.

#### MacOS
- xhyve를 사용

#### Windows
- Hyper-V 사용
- Windows Pro에서만 설치가 가능했으나 Windows WSL 2를 이용하여 Home 버전도 설치가 가능해졌다.
- 일반적으로 Windows 사용자는 VirtualBox에 ubuntu 리눅스를 설치하여 실습하는 것이 좋다.

## 설치 확인
```zsh
docker version

Server: Docker Desktop 4.20.1 (110738)
 Engine:
  Version:          24.0.2
  API version:      1.43 (minimum version 1.12)
  Go version:       go1.20.4
  Git commit:       659604f
  Built:            Thu May 25 21:50:59 2023
  OS/Arch:          linux/arm64
  Experimental:     false
 containerd:
  Version:          1.6.21
  GitCommit:        3dce8eb055cbb6872793272b4f20ed16117344f8
 runc:
  Version:          1.1.7
  GitCommit:        v1.1.7-0-g860f061
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
```
다음과 같은 결과가 나타나게 된다. 이 때, docker version 을 수행 할 때 다음과 같은 오류를 만날때가 있다.

> Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running? for mac
### 해결
- Docker가 프로세스 상에 띄워져있지 않기 떄문에 발생하는 문제이다. 결론적으로 도커 프로세스를 띄워주면 해결되는 문제이다. 해결방법은 운영체제에 따라 다르다.
#### MAC or Windows
- 맥 혹은 윈도우라면 설치해두었던 Docker Desktop을 실행해주자. 맥에서도 CLI로 도커를 실행하고자 찾는 사람이 있을 수 있는데 다음 링크를 확인하자
https://stackoverflow.com/questions/54437744/how-to-start-docker-from-command-line-in-mac

#### Linux
- Linux는 Systemctl을 사용하여 간단하게 해결이 가능하다.
```zsh
# docker 상태 확인
sudo systemctl status docker

# docker start
sudo systemctl start docker

# docker autoStart(부팅 시 자동 시작)
sudo systemctl enable docker
```

**systemctl** 은 서비스 유닛(.service)을 관리 및 제어하는 명령어이고 CentOS7 버전부터 사용이 가능한 명령어이다. 이전 버전에서는 Service 명령어로 사용이 가능하다.

## Client-Server 구조
- docker CLI는 도커 호스트에 명령을 전달하고 결과를 받아서 출력한다.
1. client에서 docker run xxx을 호출한다.
2. host(server)는 client에서 보낸 명령어를 docker daemon에서 실행한다.
3. client는 해당 명령어의 수행결과를 output으로 보낸다.