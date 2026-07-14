# docekr 이슈 정리

## 1. 이미지 다운로드 시 에러 발생

### 장애 현상
nginx 이미지 다운로드 시 현재 로그인한 사용자(ubuntu)가 Docker 데몬에게 명령을 보낼 권한이 없다는 에러 발생
```bash
# 장애 로그
docker pull nginx Using default tag: latest permission denied while trying to connect to the docker API at unix:///var/run/docker.sock
```

### 장애 원인
docker 엔진에 명령을 보내야하는데 중간다리인 docker.sock 파일에 접근 권한이 없어서 발생

docker.sock : docker cli와 docker 엔진을 연결해주는 통로
              해당 파일의 권한은 srw-rw----

```bash
# docker 명령어 사용시 flow
ubuntu 사용자
      |
      | docker 명령어
      ↓
/var/run/docker.sock
      |
      ↓
Docker Daemon (dockerd)
      |
      ↓
Docker Hub
```

```bash
# 소켓 파일 권한 확인
ls -l /var/run/docker.sock

# 결과
권한        링크수  소유자   그룹    크기    날짜               파일명
srw-rw----  1    root  docker   0   Jul 14 02:38 /var/run/docker.sock

s(소켓) | rw-(소유자인 root는 read, write 가능) | rw-(그룹인 docker는 read, write 가능) ---(다른 사용자는 read, write, execute 전부 불가능)
```

### 장애 해결
소켓파일의 권한이 root 사용자, docker 그룹 기준으로 read, write만 가능하게 되어있기 때문에 root 권한 혹은 그룹의 권한이 필요함.

1. 임시로 sudo 붙여서 실행
임시로 root 권한자로 docker 명령어 실행
그렇게되면 소켓 파일을 실행할 수 있기 때문에 명령어 실행이 가능
```bash
sudo docker pull nginx
```

2. docker 그룹에 ubuntu 사용자 추가
ubuntu 사용자를 docker 그룹에 추가
- usermod : user modify로 사용자 정보 변경
- a : append로 기존 그룹을 유지한채로 추가
- G : secondary group 지정 (이 사용자를 어떤 그룹에 넣을지 지정)
```bash
# docker 그룹에 ubuntu 사용자 추가. -a 조건으로 ubuntu에 있는 기존 그룹은 유지
sudo usermod -aG docker ubuntu

# 그룹 확인
groups ubuntu
```

## 2. 로컬 pc에서 nginx 접속이 안될 때

### 장애 현상
command 창에서는 nginx가 curl localhost로 접속이 되는데 개인 pc에서 http로 접속하면 접속이 불가한 상황

### 장애 원인
ec2 보안그룹에 ssh 20번 port만 inboud 규칙이 만들어져 있어 http로 접속이 불가했음.

### 장애 해결
접속하고자 하는 nginx가 있는 서버의 보안 그룹에 http 접근이 가능하도록 80 port를 inbound에 추가