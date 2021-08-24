# AWS-Docker
- [주의](#주의)
- [서버 접속](#서버-접속)

### EC2
Elastic Compute Cloud, 한 대의 컴퓨터를 임대합니다.

## 주의
- 리전 및 영역 : 아시아 태평양 (서울)ap-northeast-2 (권장)
- 인스턴스 생성 후, 탄력적 IP 설정 안 하면 과금.

## 서버 접속
### PuTTYgen을 사용하여 프라이빗 키 변환
윈도우는 pem파일을 처리 하지 못하기 때문에 .ppk 파일로 변환해야 합니다.
- PuTTY
    ```java
    // Session
    Host Name (or IP address)   : ubuntu@13.125.187.129
    Port                        : 22
    Connection type             : SSH

    // Connection/SSH/Auth
    Private key file for authentication : .ppk 위치
    ```