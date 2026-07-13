# 1. EC2 AMI 지정
- os : Canonical, Ubuntu, 26.04, amd64
- type : t3.micro

## 2. 키페어 설정
ec2 접속할 때 보안을 위해 비밀번호 대신 ssh 키 인증을 이용해 로그인
신규로 생성 시 다운로드 받은 .pem 파일은 분실하면 안됨. (재발급 불가)

```bash
             Key Pair

       ┌────────────────┐
       │                │
       │  Public Key    │  ← AWS EC2에 저장 (자물쇠)
       │  (공개키)        │
       │                │
       └────────────────┘
                ^
                |
                |  (SSH + 개인키)
                |
       ┌────────────────┐
       │                │
       │  Private Key   │  ← 내가 보관 (.pem 파일) -> (열쇠)
       │  (개인키)        │
       │                │
       └────────────────┘
# 사용자의 개인키와 ec2가 가지고 있는 공개키와 비교한 뒤 일치하면 접속
```

키 페어는 두 개가 한 쌍
공개키 : ec2 생성할 때 서버에 등록
개인키 : 내 pc에만 있음
```bash
Private Key (개인키)
        +
Public Key (공개키)
```

### 2-1. 키페어 생성
RSA로 생성. 가장 호환성이 좋으며 우분투, mac, 리눅스 모두 문제 없음.
RSA : 암호화 알고리즘
- type : RSA

### 2-2. 키페어에 권한 변경
파일 소유자에 한해 읽기만 가능하고, 다른 사람은 접근 불가하게 권한 변경

* chmod : chanmge mode로 파일의 권한을 변경
```bash
chmod 400 docker-study-key.pem

# 파일 권한 구조 (read,write,x실행불가)
# 파일 소유자만 read 권한이 있고, 나머지는 없음.
  4   0   0
- r-- --- ---
│ │   │   │
│ │   │   └── Others (다른 사용자)
│ │   └────── Group (같은 그룹)
│ └────────── Owner (파일 소유자)
└──────────── 파일 종류
```

## 3. 네트워크 설정

### 3-1. VPC, 서브넷
생성한 vpc 선택, default 서브넷으로 둠

### 3.2. auto-assign public ip
- ip : 네트워크에서 컴퓨터를 찾기 위한 주소
- public ip : 인터넷에서 접근 가능한 ip 주소 (인터넷에서 ec2를 찾아가는 주소)
              public ip가 있으면 전세계 어디서든 인터넷을 통해 이 주소로 ec2에 접근할 수 있음 -> 즉 외부 접속
              서버 재시작하면 ip 변경 -> EIP 사용하면 해결
- private ip : aws 내부 서버끼리 통신하는 주소
```bash
# 내 pc가 인터넷을 이용해 ec2를 찾아가기 위해서는 주소(public ip)가 필요
  내 pc
    |
    |
  인터넷
    |
    |
  Public IP
   13.xxx.xxx.xxx
    |
    |
  EC2 서버
    |
  Private IP
   10.x.x.x
    |
   RDS 서버
```

### 3-3. security group
ec2 앞에 붙이는 방화벽으로, ec2로 들어오고 나가는 네트워크 트래픽을 제어하는 가상 방화벽
```bash
  내 pc
    |
    | SSH 요청 (22번 포트)
    ↓
Security Group
    |
    | "22번 포트 허용되어 있나?"
    ↓
EC2 접속
```
상태저장(sateful) 특징이 있기 때문에 한번 허용된 연결에 대해서는 응답 트래픽 허용을 따로 하지 않아도 됨.
ec2 -> 내 pc로 나가는 outbound를 따로 설정하지 않아도 됨.

## 4. ec2 접속
pem키가 있는 디렉토리 위치로 가서 ssh를 이용해 ec2에 접속
```bash
ssh -i key-name.pem ubuntu@public.ip
```