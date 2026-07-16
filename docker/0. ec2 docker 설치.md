# EC2에서 docker 사용하기
터미널을 이용해 ec2 public ip로 ssh 접속해 설치 진행

## docker 설치
### 1. ubuntu 패키지 목록 업데이트
apt : 우분투 패키지 관리자
```bash
sudo apt update
```

### 2. docker 설치 및 확인
apt를 이용해 docker 설치
```bash
# 설치
sudo apt install docker.io -y
# 설치 확인
docker --version
# 서비스 상태 확인
sudo systemctl status docker
```

## docker로 이미지 다운로드
### 1. 이미지 다운로드
- pull  :  이미지를 다운로드하는 것일 뿐 컨테이너가 실행되지 않음
```bash
# 이미지 다운로드
sudo docker pull nginx

# 이미지 다운로드 확인
sudo docker images
```

### 2. 컨테이너 실행
ec2(ubuntu)가 nginx를 실행하는게 아니라 docker가 컨테이너를 만들어서 nginx를 실행하는 것.
보안그룹에 ssh port만 뚫려있으면, 로컬에서 http로 연결되지 않음.

- p : host port :container port를 설정
```bash
# 실행
sudo docker run -d --name web -p 80:80 nginx

# nginx 실행 확인
curl localhost
```

```bash
             내 pc

        http://EC2_Public_IP
                  │
                  │ 80
                  ▼
        AWS Security Group
          HTTP(80) 허용 ✅
                  │
                  ▼
        EC2 (Ubuntu)
                  │
                  │ Host Port 80
                  ▼
        Docker Engine (dockerd)
                  │
                  │ -p 80:80
                  ▼
        nginx Container
                  │
                  ▼
      Welcome to nginx!
```

### 3. 프로세스 확인 
컨테이너로 nginx가 실행됐지만, 컨테이너 안에 커널이 있는 가상머신과 다르게 커널이 없고 host에서 프로세스가 실행됨.
```bash
ps -ef | grep ngin
```

### 4. 컨테이너 내부 접속
- exec : 실행 중인 컨테이너 안에서 명령어를 실행
- bash : 컨테이너 안에서 실행할 프로그램
- i : interactive로 표준 입력을 유지. 즉 키보드 입력을 계속 할 수 있게 해줌
- t :tty로 터미널 환경을 만들어줌.
```bash
sudo docker exec -it web bash
```