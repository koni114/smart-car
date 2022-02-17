## 빅데이터 파일럿 프로젝트 PC 환경 구성
- 빅데이터 개발 환경 구성와 서버 환경 구성이 진행

### 빅데이터 개발 환경 구성
- 자바 설치: version 1.8
- 이클립스 설치
- 버추얼 박스 설치: 5.2
- 가상 머신에 CentOS 설치
- 기타 도구 설치: Putty, filezila, chrome, 예제 코드
  - chrome : hadoop eco system을 설치하고 Web에서 수행을 시키려고 할 때, 반드시 chrome 필요
- 소스코드 다운로드: https://github.com/wikibook/bigdata2nd/archive/master.zip

### 빅데이터 서버 환경 구성
- 개인 PC 위의 리눅스 가상 머신 3대
- 클라우데라 매니저 설치
- 빅데이터 에코시스템 설치: Hadoop, Zookeeper 등 기본 구성

## 1. VM 통합 환경 구성
### 1.1 virtualBox network 구성
- virtualBox를 실행하고 [파일] -> [환경설정] -> [네트워크]를 선택
- 네트워크 환경 설정에서 [새 Nat 네트워크 추가] 버튼을 클릭하고, 추가된 [NatNetwork]를 더블클릭 해 NAT 네트워크 정보가 기본값으로 설정돼 있는지 확인

### 1.2 가상 서버 이미지 다운로드
- Pilot Project VM.zip 파일을 다운로드  
  18GB로 10 ~ 20 분 정도의 시간이 소요됨
- Server01, Server02 folder 의 가상서버 파일들을 확인(.vbox)
- 해당 .vbox file 을 import 시키기

### 1.3 파일럿 PC의 호스트 파일 수정하기
- 파일럿 PC 운영체제의 host file 수정
- terminal 창에 `sudo nano /private/etc/hosts` 입력
- `Command + O` 를 통해 저장, `Command + X` 를 통해 편집 종료
- 아래 3개의 내용 입력 
- host file의 다음 내용을 추가함. 가상 머신에 설치한 3대의 리눅스 서버의 IP/도메인명 정보
  - 192.168.56.101 server01.hadoop.com
  - 192.168.56.102 server02.hadoop.com
  - 192.168.56.103 server03.hadoop.com  
- PC에 설치돼 있는 보안 프로그램이 hosts 파일의 수정을 막는 경우가 있음  
  이 경우 보안 프로그램을 강제 종료하고 host 파일을 수정하면 됨

### 1.4 클라우데라 매니저 접속하기
- 크롬 브라우저를 통해 CM이 제대로 설치 및 구동됐는지 확인을 위해 로그인
- CM 서버가 기동하는 데 2~3분의 시간이 걸림. 접속이 되지 않더라도 아래 URL 로 여러 번 접속을 시도해 보자
- 접속정보
  - URL: http://server01.hadoop.com:7180
  - 사용자 이름 : admin
  - 암호 : admin

### 1.5 클러스터 설치
- 클라우데라 매니저를 통해 hadoop eco-system 을 설치하고 관리해 볼 것임
- cloudera express 버전 사용. enterise 는 보안 관련된 기능들이 있음
- 클러스터 이름 지정 --> Cluster 1 그대로 사용
- 만약 내가 직접 클라우데라 매니저를 설치하였다면, [새 호스트]를 선택하고, 서버 2대만 사용하여 pilot을 진행할 것이기 때문에, server01, server02 를 선택하여 진행했어야 함
  등록되었을 경우, [현재 관리되는 호스트]에 보임
- 실습 진행은 [현재 관리되는 호스트] -> server1, server2를 선택 

## 참고. 리눅스 가상 머신 환경 구성
- 위의 통합 환경 구성의 환경이 만들어지는 과정이므로, 참고만 하기

### 네트워크 설정


## 용어 정리
- hosts
  - 운영 체제가 호스트 이름을 IP 주소에 매핑할 때 사용하는 컴퓨터 파일 
- DHCP
  - Dynamic Host Configuration Protocol 의 약자 
  - 호스트의 IP 주소와 각종 TCP/IP 프로토콜의 기본 설정을 클라이언트에게 자동적으로 제공해주는 프로토콜
- Mount
  - 어떠한 것을 Avaliable 한 상태로 준비하는 것을 말함
  - ex) 하드 디스크 상의 데이터를 RAM으로 올리는 것
  - ex) Linux 의 Mount 명령어도 있음. --> 하드디스크나 어떤 특정한 장치를 인식하게 하는 것
- ssh
  - Secure Shell Protocol, 네트워크 프로토콜 중 하나
  - 컴퓨터와 컴퓨터가 인터넷과 같은 Public Network를 통해 서로 통신을 할 때 보안적으로 안전하게 통하기 위해 사용하는 프로토콜
- Telnet
  - 원격 접속 서비스로서 특정 사용자가 네트워크를 통해 다른 컴퓨터에 연결하여 컴퓨터에서 제공하는 서비스 
  - 보안문제로 사용률이 감소하고 원격 제어를 위해 SSH 로 대체되는 추세