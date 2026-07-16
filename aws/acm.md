# ACM 파헤치기

## ACM은 왜 필요한가?
https를 사용하기 위해서는 ssl 인증서가 필요함.
각 서버에서 ssl 인증서를 가지게 되면, 서버마다 ssl 인증서를 설치 및 갱신을 해야되는 불편함이 생긴다. 

```bash
사용자 브라우저
        │
   HTTPS (443)
        │
        ▼
      ALB
 (SSL 인증서 보유)
        │
   SSL Termination
        │
        ▼
    HTTP (80)
        │
        ▼
      EC2
        │
        ▼
Docker Engine
        │
        ▼
nginx (80)
```