# AWS-Docker
- [주의](#주의)
- [서버 접속](#서버-접속)
- [리눅스](#리눅스)

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

## 리눅스
- 모든 것은 파일이라는 철학을 따릅니다. `하드웨어 포함`
- 파일 네임스페이스
    - `/media/floofy/dave.jpg`, "/" - root directory
### 쉘
사용자와 컴퓨터, 하드웨어 또는 운영체제간 인턴페이스입니다.
- 사용자의 명령을 해석해서, 커널에 명령을 요청합니다.
- 종류
    - Bourne-Again Shell (bash) : GNU 프로젝트 일환으로 개발.
    - Bourne Shell (sh)
    - C Shell (csh)
    - Korn Shell (ksh) : 유닉스에서 가장 많이 사용됩니다. 

### 명령어
- 리눅스 명령어는 결국 쉘이 적용하는 명령어입니다.
- 리눅스 기본 쉘이 bash이므로, bash에서 제공하는 기본 명령어라고 볼 수 있습니다.
- 일반적으로는 root로의 직접적 접속을 하지 않으며, root 권한이 필요한 상태에선 `sudo`를 사용합니다. `sudo apt-get update` <br />
`whami` : 로그인 사용자 ID 조회 <br />

`ssh -i awstest.pem ubuntu@13.213.23.234` : pem 사용하여 접속 (걍 푸티로 하셈) <br />
`sudo apt-get update` : 패키지 업데이트 <br />
`ls -al` : 숨긴 파일 포함하여 조회 <br />
- 소유자 - 소유자 그룹 - 기타
- `rwx rw- r-x` = `765`
- d : 폴더 <br />
    
`chmod` : 권한 변경
- `chmod 400 security.pem`
- `chmod -R 777 directory` : `-R`로 directory의 하부 파일을 포함하여 부여합니다.
`rm` : 파일 및 폴더 삭제
- 주로 사용하는 명령어 형태 : `rm -r 디렉토리명`
- `r` : 옵션 하위 디렉토리를 포함한 모든 파일 삭제
- `f` : 강제로 삭제

### Standard Stream
표준 입출력으로 command로 실행되는 프로세스는 세 가지 스트림을 가집니다.
- Standard Input Stream - stdin
- Standard Output Stream - stdout
- Standart Error Stream - stderr <br />
모든 스트림은 일반적인 plain text로 console에 출력하게 되어있습니다.
