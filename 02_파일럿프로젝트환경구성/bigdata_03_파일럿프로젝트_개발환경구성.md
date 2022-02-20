## 빅데이터 파일럿 프로젝트 PC 환경 구성
- 빅데이터 개발 환경 구성와 서버 환경 구성이 진행

### 빅데이터 개발 환경 구성 - 개인 PC
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

### 빅데이터 파일럿 프로젝트 PC 환경 구성
- 자바 설치
  - java 1.8 version 이상 설치 
  - 자바 다운로드 페이지: https://www.oracle.com/downloads/index.html 
  - URL 이동 -> Java 에 [Java for(jdk) developer] 클릭 -> [Java SE 8uxxx] 버전 설치  
    OS 버전에 맞는 jdk 설치
  - 오라클 계정이 없는 경우, 가입 후 jdk 설치
- Virtual Box 설치
- Putty, FileZila, chrome 설치 필요 
- 실습 예제 및 소스코드 다운로드    
  https://github.com/wikibook/bigdata2nd/archive/master.zip  

## 1. VM 통합 환경 구성
### 1.1 virtualBox network 구성(MacOS 기준)
- virtualBox를 실행하고 왼쪽 탭에 [도구] -> [환경설정(P)] -> [네트워크]를 선택
- 네트워크 환경 설정에서 우클릭 -> [NAT 네트워크 추가] 버튼을 클릭하고, 추가된 [NatNetwork]를 더블클릭 해 NAT 네트워크 정보가 기본값으로 설정돼 있는지 확인
  - 네트워크이름 : `NatNetwork`
  - 네트워크 CIDR: `10.0.2.0/24`
  - 네트워크 옵션: DHCP 지원 체크 
- [파일] -> [호스트 네트워크 관리자] -> [만들기] 를 통해 호스트 네트워크 생성  
  - 어댑터 설정
    - IPv4 주소 : `192.168.56.1`
    - IPv4 서브넷 마스크 : `255.255.255.0`
  - DHCP 서버
    - 서버 주소 : `192.168.56.100`
    - 서버 마스크 : `255.255.255.0`
    - 최저 주소 한계 : `192.168.56.101`
    - 최고 주소 한계 : `192.168.56.254`

### 1.2 가상 서버 이미지 다운로드
- `Pilot Project VM.zip` 파일을 다운로드  
  18GB로 10 ~ 20 분 정도의 시간이 소요됨
- Server01, Server02 folder 의 가상 서버 파일들을 확인(.vbox)
- 해당 .vbox file 을 import 시키기  
  [가져오기] -> Server01, Server02 .vbox 파일을 가져오면 됨

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
- 클라우데라 매니저를 통해 hadoop eco-system 을 설치하고 관리해 볼 것임
- cloudera express 버전 사용. enterise 는 보안 관련된 기능들이 있음
- 클러스터 이름 지정 --> Cluster 1 그대로 사용
- 만약 내가 직접 클라우데라 매니저를 설치하였다면, [새 호스트]를 선택하고, 서버 2대만 사용하여 pilot을 진행할 것이기 때문에, server01, server02 를 선택하여 진행했어야 함
  등록되었을 경우, [현재 관리되는 호스트]에 보임
- 실습 진행은 [현재 관리되는 호스트] -> server1, server2를 선택 

## 참고. 리눅스 가상 머신 환경 구성
- 위의 통합 환경 구성의 환경이 만들어지는 과정이므로, 참고만 하기

### 네트워크 설정
- 도구 -> 환경설정 -> 네트워크 -> 네트워크 추가(아이콘 클릭)
  - 네트워크 이름 : `NatNetwork`
  - 네트워크 CIDAR : `10.0.2.0/24` 
  - 네트워크 옵션 : DHCP 지원 체크
- [파일] -> [호스트 네트워크 관리자] -> [만들기] 를 통해 호스트 네트워크 생성  
  - 어댑터 설정
    - IPv4 주소 : `192.168.56.1`
    - IPv4 서브넷 마스크 : `255.255.255.0`
  - DHCP 서버
    - 서버 주소 : `192.168.56.100`
    - 서버 마스크 : `255.255.255.0`
    - 최저 주소 한계 : `192.168.56.101`
    - 최고 주소 한계 : `192.168.56.254`

### 가상머신 Server01 생성
- CentOS 6.x 설치 파일을 다운로드  
  http://ftp.daumkakao.com/centos/6/isos/x86_64/  
 (** 현재 deprecate 됨)  
- 다운로드 파일: `CentOS-6.10-x86_64-LiveDVD.iso`
- 아래의 공식 CentOS URL에서도 CentOS 6.x 의 ISO 파일을 다운로드 가능  
  http://isoredirect.centos.org/centos/6/isos/x86_64
- VirtualBox 에서 첫 번쨰 리눅스 가상 머신을 `Server01` 이라는 이름으로 생성  
  종류와 버전 정보는 다음과 같이 입력  
  이름 : `Server01`
  종류 : Linux
  버전 : Other Linux(64bit)
- 가상 머신의 메모리 크기를 2048MB로 설정하고, [다음] 버튼을 클릭  
  메모리의 크기를 할당할 때는 뒤에서 소개할 표 2.5의 virtualbox 가상 머신 메모리 할당을 참고해서 각자 환경에 맞는 메모리 크기로 재설정  
- 가상 머신의 하드 드라이브 설정  
  하드 드라이브의 동적 할당 크기는 30~40GB 정도로 설정  
  하드디스크 : `지금 새 가상 하드 드라이브 만들기` 선택  
  하드디스크 파일 종류 : `VDI(VirtualBox 디스크 이미지)`  
  물리적 하드 드라이브에 저장 : `동적 할당`  
  파일 위치 및 크기 : 30GB ~ 40GB
- Server01 네트워크 설정
  - 네트워크 어댑터 1 --> `NAT` 선택하고, 고급을 눌러 어댑터 종류와 MAC 주소가 자동으로 할당되었는지 확인 
  - 네트워크 어댑터 2 --> `호스트 전용 어댑터`, 무작위 모드를 `모두 허용`으로 선택
- Server01 디스크 설정 
  - 저장소에서 [비어 있음] 미디어를 선택하고, [드라이브 선택] 버튼을 클릭  
  - [가상 광학 디스크 파일 선택] 메뉴를 선택 한 후, 다운로드 했던 `CentOS-6.10-x86_64-LiveDVD.iso` 선택
- Server01 가상 머신 시작
  - CentOS 설치 화면이 나타나고, 차례차례 따라가면서 install 수행해 주면 됨
- 가상 머신은 OS나 local PC 의 특성을 많이 타기 때문에 다양한 문제는 구글을 통해서 해결하자

### CentOS 설치 및 설정 - 01
- Install 선택
- GUI 설치 화면에서 [next] 선택
- 언어 설정은 [English] 후 [next]
- 설치와 관련된 장치의 종류로 [기본 저장 장치(Basic Storage Devices)] 를 선택 후 다음 버튼 클릭  
  저장 장치 경고 메세지가 나타나면 모든 데이터 삭제
- 호스트 입력 창이 나타나면 `server01.hadoop.com` 입력 후 다음 버튼 클릭
- Time-Zone은 Asia/Seoul 선택 후 왼쪽 하단에 UTC 체크박스 선택
- Root 암호 입력 창이 나타나는데, admin/admin 으로 설정(기억하기)
- 설치 종류를 묻는 창에서는 "모든 공간 사용(Use All Space)" 을 선택하고 다음 버튼 누르기  
  디스크 설정 기록 Alert 창이 나타나면 "Write changes to disk" 버튼을 눌러 다음 단계로 진행
- Server01 의 가상머신을 [닫기] -> [전원 끄기]를 선택해 종료
- 버추얼 박스의 [설정] -> [저장소]로 이동해서 아래 순서에 따라 마운트했던 리눅스 ISO 파일을 언마운트함   

### CentOS 설치 및 설정 - 02
- CentOS를 구동. VirtualBox의 [시작] 버튼 클릭
- 라이선스 동의 
- 사용자를 생성하는 창에서 이름, 성명, 암호, 암호 확인에 모두 bigdata라고 입력하고 [Forward] 버튼 클릭
- 시간 및 설정 창에서 [Synchronize date and time over the network] 체크 후 [Forward] 버튼 클릭
- kdump 설정 창이 마지막으로 나타나는데, [Enable kdump?] 체크를 반드시 해제하고 [Finish] 버튼 클릭
- bigdata/bigdata 로 계정 로그인 되는지 확인

### CentOS 환경 구성 - 네트워크 설정
- 네트워크 설정 같은 경우는 잘못 설정할 경우, 문제가 발생할 수 있으니 꼼꼼히 확인해야 함
- CentOS 데스크톱 상단의 [Applications] -> [System Tools] -> [Terminal] 을 차례로 선택하여 리눅스 터미널 실행  
  이번 설정을 통해 CentOS의 X-Window 부팅 해제. 앞으로 편의상 CentOS 터미널에서만 작업 진행함  
  따라서 아래 명령을 차례로 실행
~~~shell
$ su root # Password: adminuser
$ vi /etc/inittab
~~~
- 위의 `inittab` 파일을 vi 에디터로 열어서, 해당 위치의 숫자 '5' 를 '3'으로 수정한 후 저장  
  아래의 reboot 명령 실행
~~~shell
$ reboot
~~~
- reboot 명령 수행 후, X-window 화면이 나타나지 않고 콘솔에 패스워드 입력창이 바로 나타남  
  아래의 계정으로 로그인. 리눅스 계정은 특별한 경우를 제외하고는 모두 root 계정 사용할 것임
~~~shell
$ localhost login : root
$ Password : adminuser
~~~
- Server01 가상 머신에 고정 IP와 네트워크 설정을 해보자  
  vi 에디터로 /etc/sysconfig/network-scripts 경로에 ifcfg-eth0 파일을 생성
~~~shell
vi /etc/sysconfig/network-scripts/ifcfg-eth0
~~~
- Ethernet 설정을 작업함  
  DEVICE: `eth0`
  HWADDR : `08:00:27:04:A7:F3` --> Virtualbox 에서 네트워크 -> 어댑터2의 MAC주소를 다음과 같이 콜론을 통해 구분하여 입력!  
  TYPE:`Ethernet`    
  ONBOOT=`yes`  
  BOOTPROTO=`static`  
  IPADDR=`192.168.56.101`  
  NETMASK=`255.255.255.0`
  GATEWAY=`192.168.56.1`  
  NETWORK=`192.168.56.0`
- IP와 넷마스크 등의 설정이 끝났으면 CentOS 리눅스가 기본값으로 설정한 네트워크 룰을 삭제해서 향후 발생할 수 있는 네트워크 충돌을 방지  
  아래의 vi 명령으로 관련 파일을 열고 자동으로 만들어진 네트워크 룰을 모두 주석(#) 또는 삭제 처리  
~~~shell
$ vi /etc/udev/rules.d/70-persistent-net.rules
~~~  
- 고정 IP인 `192.168.56.101` 을 할당받기 위해 Server01 가상 머신을 재시작해야함  
  재시작시, 반드시 VirtualBox 에서 재시작해야함
- 가상 머신을 다시 시작해서 root 계정으로 로그인하고 네트워크 서비스에 고정 IP가 인식되도록 다음 명령 실행  
~~~shell
$ service network restart
~~~
- 위의 명령 실행 후, 별다른 오류가 발생하지 않으면 고정 IP 설정이 성공적으로 된 것임  
  아래 명령을 통해 Server01의 고정 IP가 정확히 할당됐는지 확인
~~~shell
$ ifconfig eth0
~~~
- Server01의 network 설정이 끝났음. 이제는 putty 를 통해 server01 에 접속할 것임  
  이를 위해 Server01 에 `openssh` 설치
- 이제 SSH 접속을 위한 패키지를 설치하기 위해 아래의 `yum install` 과 커널 설정 명령을 차례로 실행  
  설치 도중 나오는 질문에는 모두 y 를 입력하고 엔터 키를 누르며 진행
~~~shell
$ yum install openssh*   # ssh와 관련된 것들은 모두 설치됨
$ service sshd restart
$ chkconfig sshd on      # booting 시 sshd 가 on 되도록 함 
$ reboot
~~~
- 리부팅이 완료되면 다음 명령으로 네트워크 설정을 다시 한 번 재시작
~~~shell
$ service network restart
~~~
- server01 의 SSH 접속이 잘 되면 이제 Server01의 호스트 정보를 수정  
  먼저 vi 명령을 통해 hosts 파일을 수정
~~~shell
$ vi /etc/hosts
~~~
- hosts 파일을 다음과 같이 수정
~~~
127.0.0.1 localhost server01
192.168.56.101 server01.hadoop.com server01
192.168.56.102 server02.hadoop.com server02
192.168.56.103 server03.hadoop.com server03
~~~
- HOSTNAME 에 `server01.hadoop.com`을 설정한다
~~~
$ vi /etc/sysconfig/network
~~~
~~~
NETWORKING=yes
NETWORKING IPV6=no
HOSTNAME=server01.hadoop.com
~~~

### CentOS 환경 구성 - 방화벽 및 커널 설정
- 다음과 같은 명령어들을 통해 방화벽 및 기타 커널 매개변수 설정이 필요
~~~shell
# 방화벽 설정을 disabled 해주어야 분산 환경에서 ssh 통신 가능
$ vi /etc/selinux/config      # config 파일에서 SELINUX 를  "SELINUX=disabled" 로 수정
$ service iptables stop       # iptables 중지 명령
$ chkconfig iptables off      # iptables 자동 시작 중지 명령
$ chkconfig ip6tables off     # ip6tables 자동 시작 중지 명령
# memory swap 은 보통 사용하지 않지만, 우리는 적극적으로 사용해야함.
# 그 이유는, 우리 local PC 환경의 Memory 가 보통 16G, 8G 일텐데, 물리적인 메모리가 한계가 있음
# 따라서 memory swap 을 사용하여 부족한 부분을 disk에 메모리 공간을 만들어서 사용
$ sysctl -w vm.swappiness=100 # vm swappiness 사용 제어 설정
$ vi /etc/sysctl.conf         # sysctl.conf 파일에서 "vm.swappiness=100" 설정을 추가
# 다소 큰 파일에 대한 사용을 할 때, 해당 옵션을 추가함
$ vi /etc/rc.local            # rc.local 파일에서 아래 명령어를 추가                     
echo never > /sys/kernel/mm/transparent_hugepage/enabled
echo never > /sys/kernel/mm/transparent_hugepage/defrag

# 아래 해당 옵션은 프로세스들이 뜰 때, 프로세스 개수들을 많이 늘려주는 명령어임

# limits.conf 파일에서 아래의 파일 디스크립터 설정을 추가
$ vi /etc/security/limits.conf 
root soft nofile 65536
root hard nofile 65536
* soft nofile 65536
* hard nofile 65536
root soft nproc 32768
root hard nproc 32768
* soft nproc 32768
* hard nproc 32768
~~~

### CentOS 환경 구성 - 가상 서버 복제
- 위에서 설정한 server01 서버를 복제하여 server02, server03 서버를 만들 것임
- 먼저 server02 를 만들기 위해 server01 가상머신 끄기
- server02 를 복제하기 위해 아래의 단계를 순차적으로 실행한다.   
  - Server01 에서 복제 버튼 클릭
  - 이름 항목에는 `Server02` 로 입력  
  - [모든 네트워크 카드의 MAC 주소 초기화]를 체크후 다음 버튼 클릭
  - 복제 방식 -> 완전한 복제 체크 후 복제 버튼 클릭
- Server02 가 복제되었으면, Server02를 선택하고 새로운 Server02 가상 머신을 시작  
  부팅이 완료되면, 아래의 계정으로 로그인. 로그인이 정상적으로 된다면 복제는 잘 된 것임
~~~shell
$ login : root
$ Password : adminuser
~~~
- Server02의 MAC 정보와 고정 IP 를 수정
~~~
$ vi /etc/sysconfig/network-scripts/ifcfg-eth0
~~~
- 아래 두 곳을 수정.  
  HWADDR, --> Server02 의 MAC 주소를 2자리씩 잘라서 입력
  IPADDR, --> `192.168.56.102`
- CentOS 리눅스가 자동으로 설정한 Server01의 네트워크 룰을 삭제해야 함  
  해당 파일을 열고 자동으로 만들어진 네트워크 룰을 주석(#) 또는 삭제 처리함  
~~~shell
$ vi /etc/udev/rules.d/70-persistent-net.rules
~~~
- 새로운 네트워크 정보를 virtualbox로 부터 할당받아야 하므로, Server02 를 종료하고 다시 시작
- 정상적으로 실행되면, Server02의 호스트 정보를 수정. 먼저 vi 명령을 통해 hosts 파일 수정
~~~shell
vi /etc/hosts
~~~
- Server01의 정보가 그대로 남아 있음. `server01` -> `server02` 로 수정. 소문자임을 주의!!!
- 아래 파일을 열어 Server02 의 호스트명을 `server02.hadoop.com` 으로 수정
~~~shell
$ vi /etc/sysconfig/network
~~~
- 네트워크 설정 정보를 재시작하고 운영체제를 리부트
~~~shell
$ service network restart
$ reboot
~~~
- Server02의 네트워크 설정이 정상적으로 반영됐는지 확인  
  새롭게 설정된 Server02 의 IP 와 HOSTNAME 을 다음 명령어로 하나씩 확인  
  고정 IP인 `192.168.56.102`와 호스트명인 `server02.hadoop.com` 이 조회되어야 함
~~~shell
$ ifconfig eth0
$ hostname
~~~
- ** 만약 server03 생성할 경우, 앞의 1~6단계를 아래의 내용에 주의하며 실행해 줌
  - Server01 가상 머신을 복제해서 Server03 가상 머신 생성
  - /etc/sysconfig/network-scripts/ifcfg-eth0 --> 파일의 IPADDR을 "192.158.56.103" 으로 수정
  - /etc/sysconfig/network-scripts/ifcfg-eth0 파일의 HWADDR을 복제한 Server03 가상 머신의 MAC 주소로 수정
  - /etc/udev/rules.d/70-persistent-net.rules 파일의 네트워크 룰 삭제 또는 주석(#) 처리
  - /etc/hosts 파일의 루프백 Alias 를 "server03" 으로 수정
  - /etc/sysconfig/network 파일의 HOSTNAME을 "server03.hadoop.com" 으로 수정 
- 가상 머신의 CPU와 메모리 리소스 설정을 파일럿 프로젝트에 맞게 최적홰해보자  
  메모리의 경우 아래를 참고하여 PC 환경에 맞게 변경  
  가상 서버를 종료하고  각 서버 [설정] -> [시스템] -> [마더보드] 에서 기본 메모리 설정에서 변경
- 저사양 파일럿 아키텍처 : CPU: 듀얼코어, 메모리:8GB  
  Server01 : 3584MB  
  Server02 : 3072MB  
  Server03 : N/A
- 고사양 파일럿 아키텍처 : CPU: i3 이상, 메모리: 16GB  
  Server01 : 5120MB  
  Server02 : 5120MB  
  Server03 : 3072MB
- 분산환경을 구성하는 방법은 여러가지 방법이 있을 수 있음  
  - 가상 머신 이용
  - 실제 물리적인 서버 여러 대를 네트워크 장치로 묶기
  - 상용 클라우드 서비스
  - 벤더사가 제공하는 샌드박스 VM 이용
- 이렇게 수작업으로 수백대의 x86 서버들이 대규모로 설치되고 10여 개 이상의 빅데이터 오픈소스 소프트웨어들이 복잡하게 설치되는데,  
  이렇게 수작업으로 수많은 서버/소프트웨어를 제어하는 데 한계가 발생함
- 이를 극복하기 위한 대안으로 자동화된 소프트웨어 설치/관리 작업툴이 오래전부터 사용됨  
  Puppet, Ansible, Chef, Ambari, Cloudera Manager, BigTop 등
- 호튼웍스의 Ambari, 클라우데라의 CM은 좀 더 상위 수준의 빅데이터 전용 설치/환경 구성 툴로서 빅데이터 소프트웨어들을 웹 GUI 환경에서 쉽게 활용할 수 있게 지원함
- 파일럿 프로젝트에서는 클라우데라의 CM을 이용


## 용어 정리
- NAT
  - 네트워크 주소 변환(Network Address translation, NAT)
  - IP 패킷의 TCP/UDP 포트 숫자와 소스 및 목적지의 IP 주소 등을 재기록하면서 라우터를 통해 네트워크 트래픽을 주고 받는 기술
  - NAT을 사용하는 이유는 대개 사설 네트워크에 속한 여러 개의 호스트가 하나의 공인 IP 주소를 사용하여 인터넷에 접속하기 위함
  - NAT은 IPv4 의 주소 부족 문제를 해결하기 위한 방법으로 고려되었으며, 주로 비공인(local) 네트워크 주소를 사용하는 망에서 외부의 공인망(public)과의 통신을 위해 네트워크 주소 변환
- CIDR(사이더)
  - Classless Inter-Domain Routing
  - 클래스 없는 도메인간 라우팅 기법 
  - CIDR가 나오면서 Class 체계보다 더 유연하게 IP 주소를 여러 네트워크 영역으로 나눌 수 있게 됨
  - Intra-Domain 과 같이 각 네트워크 대역을 구분 짓고 Inter-Domain과 같이 구분된 네트워크간 통신을 위한 주소 체계 
  - `192.168.10.0/24` 에서 `/24`가 사이더 표기법
  - 이 숫자는 비트 단위이며, 0~32까지 표현이 가능
  - 예를들어 `/24` 이면, IP 주소에서 앞에서부터 24비트 이후에 오는 4번째 옥텟(0 ~ 255)을 전부 사용 가능  
    즉, `143.7.65.203/24` 일때, 143.7.65.0 ~ 143.7.65.255 까지 사용이 가능하다는 의미
- hosts
  - 운영 체제가 호스트 이름을 IP 주소에 매핑할 때 사용하는 컴퓨터 파일 
- DHCP
  - Dynamic Host Configuration Protocol 의 약자 
  - 호스트의 IP 주소와 각종 TCP/IP 프로토콜의 기본 설정을 클라이언트에게 자동적으로 제공해주는 프로토콜
  - DHCP는 네트워크에 사용되는 IP주소를 DHCP 서버가 중앙집중식으로 관리하는 클라이언트/서버 모델을 사용
  - DHCP 지원 클라이언트는 네트워크 부팅과정에서 DHCP 서버에 IP 주소를 요청하고 이를 얻을 수 있음
  - 네트워크 안에 컴퓨터에 자동으로 네임 서버 주소, IP 주소, 게이트웨이 주소를 할당해 주는 것을 의미함
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
- hostname
  - 호스트명은 네트워크에 연결된 장치들에게 부여되는 고유한 이름 
  - 도메인 이름과 유사하지만 엄밀하게는 더 넓은 의미를 가지고 있음
  - 보통 사람이 이해할 수 있는 이름으로 지어지며, 흔히 IP 주소나 MAC 주소와 같은 기계적인 이름 대신 쓸 수 있음