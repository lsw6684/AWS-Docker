# AWS-Docker
- [주의](#주의)
- [서버 접속](#서버-접속)
- [리눅스](#리눅스)
- [Ubuntu](#ubuntu)
- [VIM](#vim)
- [Docker](#docker)
    - [Docker Engine](#docker-engine)
    - [Docker image](#docker-image)
    - [Docker Internals](#docker-internals)
    - [LXC](#lxc)
- [Docker on Ubuntu](#docker-on-ubuntu)
- [FTP Solution](#ftp-solution)
- [docker와 alpine](#docker와-alpine)
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

### redirection
표준 스트림의 흐름을 바꿀 수 있습니다.
- `>, <`
    - 주로 명령어 표준 출력을 화면이 아닌 파일에 쓸 때 사용합니다.
    - `ls > files.txt` : ls로 출력되는 표준 출력 스트림의 방향을 files.txt로 바꿔줍니다. 즉, 출력 결과가 files.txt에 저장됩니다. **원래 내용이 있다면 덮어씌움.**
    - `head < files.txt` : files.txt의 파일 내용이 head라는 파일의 입력으로 삽입됩니다. **최대 상위 10줄만 가능.**
- `>>, <<`
    - `ls >> files.txt` : 기존 files.txt파일 끝에, ls 출력 결과를 추가합니다.

### Pipe
한 프로세스의 출력 스트림을 또다른 프로세스의 입력 스트림으로 사용할 때 쓰입니다. **stdout | stdin**
- `|`

### grep 명령어
현재 리눅스에 실행되는 프로그램 중에 특정 프로그램을 검색하여 확인합니다.
- `grep [-option] [pattern] [file or directory name]`
- \<option>
    - -i : 영문 대소문자 구별 x.
    - -v : pattern을 포함하지 않는 라인 출력.
    - -n : 출력 각 행의 선두에 번호 추가.
    - -l : 파일 명만 출력.
    - -c : 패턴과 일치하는 라인의 개수만 출력.
    - -r : 하위 디렉토리까지 검색.
### foreground process & bacnkground process
- foreground process : 쉘에서 해당 프로세스 실행을 명령 후, 해당 프로세스 종료까지 다른 입력을 하지 못합니다.
    - [CTRL] + C 로 프로세스 작업 취소 가능합니다. **(예상보다 오래 걸릴 때 사용)**
- background process : 사용자 입력과 상관없이 실행되는 프로세스입니다.
    - 시간이 오래 걸리는 것을 실행할 때 background process를 사용하여 다른 작업도 병행이 가능하도록 합니다.
    - 쉘에서 해당 프로세스 실행 시, 맨 뒤에 `&`을 붙입니다.
    - 사용 예
        ```
        # find / -name '*.py' > list.txt &
        [1] 57
        1은 작업 번호, 57은 pid(process ID)를 의미합니다.
        ```
- ps 명령어
    - `ps [option(s)]`
        - -a : 시스템을 사용하는 모든 사용자의 프로세스 출력. **(보통 aux와 같이 u, x 옵션과 함께 사용)**
        - -u : 프로세스 소유자에 대한 상세 정보 출력.
        - -l : 프로세스 관련 상세 정보 출력.
        - -x : 터미널에 로그인한 후 실행한 프로세스가 아닌 프로세스들도 출력.
            > 주로 데몬 프로세스까지 확인하기 위해 사용.
        - -e : 해당 프로세스와 관련된 환경 변수 정보도 함께 출력.
        - -f : 프로세스 간 관계 정보도 출력.
- kill 명령어
    - `kill % [작업 번호]`
    - `kill PID`
    - `kill -9 57` : 강제 종료.

### 주 사용 프로세스 명령어
- ps aux | grep [프로세스명] : 프로세스가 실행 중인지를 확인 후, 관련 프로세스에 대한 정보 출력
- kill -9 [PID] : 해당 프로세스 강제 종료.
- 명령 & : 터미널에서 다른 작업을 해야 하거나, 프로세스 실행에 시간이 오래 걸릴 경우 background로 실행
- [CTRL] + c : 프로세스 종료(실행 취소).

### 하드 링크와 소프트 링크
- cp 명령어
    - `cp A B` : A파일을 B파일로 복사.
    - `cp -rf * [폴더명]` : 하위 디렉토리 포함하여 복사.
- 하드 링크 : 독립적으로 저장되어 삭제엔 dependency가 없지만, 한쪽이 수정되면 다른 한쪽도 같이 됩니다.
    - `ln [A] [B]`
- 소프트(심볼릭) 링크 : Windows OS의 바로가기 아이콘과 동일합니다.
    - **ls -al 로 소프트 링크 확인 가능 :**  `A.txt -> B.txt`
    - `ln -s [A.txt] [B.txt]`

## Ubuntu
다양한 배포판 중 하나로 데비안 배포판 기반입니다.
- `sudo apt-get update` : ubuntu 패키지 인덱스 정보 업데이트.
- `sudo apt-get upgrade` : 설치된 ubuntu 패키지 업그레이드. **(함부로 하지 말기)**
- `sudo apt-get install [패키지명]` : 패키지 설치.
- `sudo apt-get remove [패키지명]` : 패키지 삭제(설정파일 제외).
- `sudo apt-get --purge remove [패키지명]` : 패키지 삭제(설정파일 포함).

## VIM
Vi improved.
- Vi : 전통적 유닉스 에디터, Visual Editor.
- Vim : Vi에 자동화, 시각화를 추가한 프로그램입니다.
- Vim 이외에 이맥스 (Emacs, GNU 프로젝트 설립자 리차드 스톨만이 개발) 라는 유명 에디터도 있습니다.

### vim 네 가지 모드
- 일반(명령) 모드 : 처음 vim을 실행했을 때 또는 입력모드/명령 모드에서 ESC 입력 시 - 찾기, 커서 이동 등.
- 명령(명령행) 모드 : 일반 모드에서 `:` 입력 시 - 저장, 파일읽기, vim 설정 등
- 입력(편집) 모드 : 일반 모드에서 a, i, o 입력 시 - 내용 입력
- visual 모드 : 일반 모드에서 v입력 시 - 블럭 복사/붙이기

## Docker
### Docker Engine
- Docker는 서버/클라이언트 구조로 이루어집니다.
    - 서버는 docker daemon process 형태로 동작합니다.
        - 데몬, 실행 중인 프로그램으로 이해(계속 떠있다고 생각).
    - Docker daemon process에 요청하기 위해, 프로세스 간 통신 기법이 필요하며, Docker는 이를 위해 **Rest API**를 사용합니다.
    - Docker command는 일종의 클라이언트입니다.
        - Docker command를 내리면, 내부적으로 Rest API를 사용해서 docker daemon process를 호출합니다.

### Docker image
- Docker 컨테이너를 생성 목적의 명령들을 가진 템플릿입니다.
- 여러 이미지들을 **layer**로 쌓아서, 원하는 형태의 이미지를 만드는 것이 일반적입니다.
    - ex) ubuntu 이미지에, apache 웹서버 이미지를 얹어서, 웹서버 이미지를 만듭니다.
### Docker container
- docker image가 리눅스 컨테이너 형태로 실행한 상태(instance)를 의미합니다.
- docker daemon에 있는 
### Docker Internals
docker는 리눅스 컨테이너부터 시작된 기술로 리눅스 커널에 [LXC](#lxc)기술을 사용하여 개별적 시스템이 동작하도록 합니다.

### LXC
Linux Containers로 단일 컴퓨팅 시스템에 설치된 리눅스 운영체제 상에서 다른 영역과 완전히 분리된 별도의 리눅스 시스템을 운영할 수 있는 리눅스 커널 기술입니다.
> 다른 가상 시스템의 경우, 하드웨어 레벨에서 별도의 컴퓨터인 것처럼 분리해서 사용할 수 있는 기술이 있습니다. 이를 운영체제 레벨에서 사용하는 기술이라 생각하면 됩니다.

> 초기 docker는 LXC 기술을 기반으로 구현되었으나 최근에는 별도 컨테이너 기술을 구현하여 사용합니다.

## Docker on Ubuntu
1. 최신 패키지 리스트 업데이트.
    - `sudo apt update`
2. docker를 위한 https 관련 패키지 설치.
    - `sudo apt install apt-transport-https ca-certificates curl software-properites-common`
3. docker repository 접근을 위한 GPG key 설정.
    - `curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -`
4. docker repository 등록.
    - `sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"`
5. 등록한 docker repository까지 포함하여 최신 패키지 리스트 업데이트.
    - `sudo apt update`
6. docker 설치.
    - `sudo apt install docker-ce`
7. docker 실행 중임을 확인.
    - `sudo systemctl status docker`

### sudo 명령어 없이 docker 명령어 사용
1. 현 사용자(ubuntu) ID를 docker group에 포함.
    - `sudo usermod -aG docker ${USER}`
2. 터미널 재접속.
3. 현 ID가 docker group에 포함되어 있는지 확인. (docker가 리스트에 나오면 OK)
    - `id -nG`

### docker-compose install
1. release page에서 버전 확인 후 설치.
    - `sudo curl -L "https://github.com/docker/compose/releases/download/1.28.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose`
2. 실행 권한 주기
    - `sudo chmod +x /usr/local/bin/docker-compose`
3. 다음 명령 실행 시 버전 확인이 가능하면, 성공
    - `docker-compose --version`

### Docker image 주요 명령
- default formation on CLI
    - `docker 명령 옵션 선택자(이미지ID/컨테이너 등)`

- doker는 image와 container 명령이 각각 별도로 존재하기 때문에 image or container를 생략 가능.
    -   ```
        docker image 명령 옵션 ...
        docker container 명령 옵션 ...
        -----------------------------
        docker 명령 옵션
        ```
- 도커 로그인&로그아웃
    - `docker login`
    - `docker logout`
- 다운 받을 이미지 검색
    - `docker search ubuntu`, docker search 검색어
    - `docker search --limit=5 ubuntu`, 5개 제한.
- 이미지 다운
    - `docker pull ubuntu`
    - `docker pull ubuntu:20.10`, 특정 버전 다운로드
    - `docker images`, 보유 이미지 조회
    - `docker image ls -q`, IMAGE ID만 출력.
- 이미지 삭제
    - 이미지ID가 겹치지 않는다면 일부만 작성해도 OK
    - `docker rmi 이미지ID or Repository명:TAG명`, rmi 명령
    - `docker image rm 이미지ID or Repository명:TAG명`

- 컨테이너 생성
    - 각 이미지는 컨테이너로 만들어야 실행 가능합니다.
    - 이미지와 컨테이너는 각각 관리해야 합니다.
    - 컨테이너 생성시, docker 프로그램에서 이름이 자동 부여됩니다.
    - `docker create ubuntu`
    - `docker create --name myUbuntu ubuntu`, myUbuntu로 이름 지정.
- 현재 실행 중인 컨테이너 확인
    - `docker ps`
    - `docker ps -a`, 전체 컨테이너 확인
    - `docker ps -a -q`, 아이디만 확인
- 컨테이너 삭제
    - 컨테이너ID 혹은 이름으로 삭제합니다. (일부 입력 가능)
    - `docker rm`
- 컨테이너 실행
    - `docker start 컨테이너이름`
    - ### docker run, 해당 이미지를 생성 하면서 실행합니다.
        - `-i`, 컨테이너 입력(STDIN)을 열어 놓는 옵션.
        - `-t`, tty(teletypewriter)는 리눅스(유닉스 계열)에서는 콘솔 또는 터미널을 의미합니다.
        - `docker run -it ubuntu`, ubuntu 패키지로 진입합니다.
        - `--name`, 컨테이너 이름을 설정하는 옵션.
        - `-d`, 컨테이너를 백그라운드에서 실행합니다.
        - `--rm`, 컨테이너 종료 시 컨테이너를 자동으로 삭제합니다.
        - `-p`, 호스트와 컨테이너 포트를 연결합니다. (NAPT, Network Address Port Translaction을 이용하여 port를 docker 컨테이너의 특정 Private IP 포트로 변환)
        - `-v`, 호스트와 컨테이너 디렉토리를 연결합니다. (컨테이너 종료 시 데이터는 사라지기 때문에 호스트 PC상에 저장.)
- 실행 중인 컨테이너 종료/중지
    - `docker stop myubuntu`, 종료
    - `docker pause`, 중지
- 웹서버 docker run 옵션 테스트
    - 웹서버는 크게 2가지가 사용됩니다.
        - apache
        - nginx (엔진엑스)
- apache 웹서버 공식 docker
    - `docker search httpd`, 일반적으로 각 docker마다 공식 이름이 프로그램명과 동일하지만, apache는 `httpd`를 사용합니다.
    - `docker search httpd --limit-5`, 최대 5개 출력
    - `docker run -d --name apacheweb httpd`, 이름 설정 후, 백그라운드로 실행
    - `docker run -d -p 9999:80 --name apacheweb2 httpd`, 호스트 pc의 9999포트로 접속하여 apacheweb2의 컨테이너 내부 ip의 80번 포트로 포워딩 합니다.
- docker가 사용하고 있는 저장매체 현황 확인
    - `docker system df`
- 실행 중인 컨테이너 사용 리소스 확인
    - `docker container stats`

## FTP Solution
FileZilla 사용, 로컬에 존재하는 파일을 서버로 업로드합니다.
- 호스트(H) - 탄력적 IP
- 키 파일(K) - .pem 파일.
- 테스트, `-v` : ;호스트와 컨테이너 디렉토리를 연결합니다. 
    ```
    docker run -d -p 9999:80 -v /home/ubuntu/2021_DEV_HTML:/usr/local/apache2/htdocs --name apacheweb2 httpd
    ```

## docker와 alpine
- docker 이미지는 여러 개의 이미지가 계층(layer)으로 쌓인 형태로 작성 됩니다.
    - e.g.) C 라이브러리 이미지를 쌓고, bash와 같은 shell 프로그램 이미지를 쌓고, 응용 프로그램 이미지를 쌓는 방식.
    - 통상 리눅스 사용 시, 다양한 기능을 가진 ubuntu 등의 리눅스 패키지를 사용하지만, docker 컨테이너의 경우 특정 응용 프로그램 실행을 목적으로 하는 경우가 많기 때문에, 다양한 기능을 모두 포함할 필요가 없습니다.**(동일한 기능을 한다면, 도커 이미지/컨테이너 사이즈가 작으면 작을 수록 좋습니다.)**
- 대부분의 docker 이미지에 가장 기본이 되는 이미지는 ubuntu가 아닌, **alpine**인 경우가 많습니다.