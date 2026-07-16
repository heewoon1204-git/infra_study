# 네트워크 기본 지식

### IP, Port
서버에서는 여러 프로그램이 동시에 실행될 수 있음.
컴퓨터는 port번호를 이용해 프로그램을 구분하게됨.

- ip : 아파트 주소
- port : 집 호수
```bash
            EC2 (ip)

        ┌──────────────────────┐
22  --->│ SSH 서버 (sshd)       │
80  --->│ HTTP                 │
443 --->│ HTTPS                │
5432 -->│ PostgreSQL           │
3306 -->│ MySQL                │
        └──────────────────────┘
```

|   포트 | 서비스        |
| ---: | ---------- |
|   22 | SSH        |
|   80 | HTTP       |
|  443 | HTTPS      |
| 3306 | MySQL      |
| 5432 | PostgreSQL |
| 6379 | Redis      |
| 9092 | Kafka      |
| 9090 | Prometheus |

### http, https 차이
https : ssl/tls 암호화를 사용하는 http
        즉 https를 사용하려면 ssl 인증서가 필요함.

ssl인증서는 aws acm할 때 자세하게 다룰 예정
```bash
  브라우저(HTTPS)
      │
 TLS Handshake
      │
  인증서 검사
      │
  암호화 완료
      │
 HTTP 요청 시작
```