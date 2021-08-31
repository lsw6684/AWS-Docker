# AWS-Docker
- [주의](#주의)
- [서버 접속](#서버-접속)
- [리눅스](#리눅스)
- [Ubuntu](#ubuntu)

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