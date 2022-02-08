# 연구실 Interview 과제 (2022-02-08~ 수정필요)

---

#### 네트워크 TCP/IP Socket 통신 구현
TCP 기반 멀티 클라이언트가 연결되어 채팅을 중계하는 서버 프로그램을 작성하시오.

* Client
- 연결 성공 시, “ftp>” 프롬프트가 뜨면 명령어 입력
- 아래 명령어들이 동작하도록 구현해야 함
 
![comment_role](https://user-images.githubusercontent.com/84845045/152977496-7ab86298-7d5d-445e-826b-c7b36c27ee11.png)
 
- 클라이언트는 사용자가 입력한 명령어를 서버로 보내 해당 명령어를 처리하도록 함
1. 사용자 입력 명령어를 서버로 전송
2. 서버는 전송 받은 명령어를 처리하여 사용자(클라이언트)에게 전송
3. 전송이 끝나면 서버는 클라이언트에게 ‘처리 완료’를 알림

 
* Server
단계별로 서버 구현
1단계(서버와 클라이언트 1 대 1 통신 방식으로 구현)
- 1 대 1 통신
- 명령어(help, get, put, cd, ls, lcd, quit)를 모두 구현
- 제어포트와 데이터 전송 포트는 별도의 번호를 사용할 것.(Well-known 포트 제외)

2단계(‘Select’를 사용하여 다중 클라이언트 처리(멀티플렉싱))
- 1 대 N 통신
- select 문을 사용하여(멀티플렉싱 방식) 구현
- Select를 사용 시 최대 클라이언트 수는 4개로 고정

3단계(멀티 프로세스 방식 서버 구현)
- 1 대 N 통신
- 제어 포트로부터 클라이너트 접속 요청을 받으면 해당 클라이언트를 처리할 프로세스를 새로 생성하여 명령어 처리
- 서버는 프로그램 데몬(daemon) 형태로 동작 되도록 처리하며 서버의 모든 처리기록 및 클라이언트와의 통신 기록은 로그파일에 별도 저장해야 함

4단계(멀티 쓰레드 방식 서버 구현)
- 1 대 N 통신
- 멀티 프로세싱 서버에서는 한 클라잉언트와의 연결을 자식 프로세스를 생성하여 처리했으나 이 단계에서는 한 프로세스 내에 스레드를 생성하여 처리
- 실행결과는 3단계와 동일하지만 내부 구현은 다중 스레드 방식으로 구현


4 단계 추가 설명
* 클라이언트로부터 연결 요청이 오면 해당 요청을 맡아 처리할 새 스레드를 생성
-> 로그인 처리 완료 후 클라이언트로부터 명령어를 수신
-> 이후 처리는 3단계와 동일
* 스레드로 구현할 경우, 스레드 간 문맥교환은 OS가 해주지만 문맥교환에 따른 스레드 상태정보는 별도로 저장해 둬야만 해당 스레드가 다시 실행상태가 되었을 때 자신이 저장해 놓은 상태정보를 가져와서 다시 처리를 진행할 수 있음
* 그렇게 하지 않으면 다른 스레드들이 실행될 경우 자신의 상태정보가 깨져 처리가 어려울 수 있음. 따라서 스레드(클라이언트)마다 자신의 클라이언트 ID 및 현재 자신이 동작하는 디렉토리 정보(CWD) 등과 같은 상태정보를 저장해 놓을 자료구조가 필요함

FTP – 파일 송수신 처리
* 명령 송수신(제어) 채널과 데이터 송수신 채널을 분리 구현(PASS 방식)
* 데이터 송수신 순서:
1. 클라이언트 측에서 데이터 채널 생성
2. 서버와 미리 약속해둔 데이터 채널 포트로 클라이언트 측에서 연결요청
3. 데이터 채널 연결 설정이 완료되면 파일 데이터 송수신 진행
4. 데이터 송수신 완료 후 데이터 채널 연결 종료

실행
* Ctrl + alt + t를 눌러 터미널창 하나를 활성화하여 명령어를 입력하여 실행
* 활성화된 터미널에서 cd 명령어로 서버와 클라이언트 실행파일이 존재하는 디렉토리로 이동
ex) cd network_homework/2018_FTP/

* 서버 및 클라이언트 실행
ex) ./server 50000
./client

