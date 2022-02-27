## 스마트카 로그 시뮬레이터
### 로그 시뮬레이터 설치
1. Server02 에 root 계정으로 로그인해 다음 명령을 실행
- `cd /home`  
  `mkdir /home/pilot-pjt`  
  `mkdir /home/pilot-pjt/working/car-batch-log`  
  `mkdir /home/pilot-pjt/working/driver-realtime-log`  
  `chmod 777 -R /home/pilot-pjt`  
- mkdir 명령에 옵션(-p)를 주면 작업 폴더를 한 번에 생성 가능
2. Java compile과 실행 환경을 1.7에서 1.8로 변경함  
  아래의 명령을 통해 /usr/bin 의 심볼릭 링크를 재설정  
  `rm /usr/bin/java`  
  `rm /usr/bin/javac`  
  `ln -s /usr/java/jdk1.8.0_181-cloudera/bin/javac /usr/bin/javac`    
  `ln -s /usr/java/jdk1.8.0_181-cloudera/bin/java /usr/bin/java`  
3. 자바 환경의 버전 정보인 "java version 1.8.0_181" 확인  
  `java -version`
4. 자바로 만들어진 스마트카 로그 시뮬레이터 프로그램을 Server02에 업로드함  
  로그 시뮬레이터인 bigdata, smartcar.loggen-1.0.jar 파일을 FTP 클라이언트를 이용해 다음 URL로 업로드함
  - FileZila FTP 클라이언트 실행
  - Server02에 SFTP 접속
  - 호스트: server02.hadoop.com/home/pilot-pjt/working
  - 사용자명: root
  - 비밀번호: adminuser
  - 포트: 22
  - C://예제소스/bigdata2nd-master/CH02/bigdata.smartcar.loggen-1.0.jar 파일을 /home/pilot-pjt/working에 업로드  
5. 업로드한 스마트카 로그 시뮬레이터 실행
- 두 개의 메인 자바 프로그램이 있음. 
  - 스마트카 운전자의 운행 정보를 실시간으로 발생시키는 DriverLogMain.java 
  - 스마트카의 상태 정보를 주기적으로 발생시키는 CarLoginMain.java 
- DriverLogMain.java 실행  
  - 실시간으로 발생하는 데이터 
  - PuTTY 프로그램 실행
  - Server02에 root 계정으로 SSH 접속
  - `cd /home/pilot-pjt/working`
  - `java -cp bigdata.smartcar.loggen-1.0.jar com.wikibook.bigdata.smartcar.loggen.DriverLogMain 202160101 10`
6. Server02에 대한 SSH 세션을 하나 더 열어 로그 시뮬레이터가 정상적으로 작동하는지 확인
- `cd /home/pilot-pjt/working/driver-realtime-log`
- `tail -f SmartCarDriverInfo.log` 
- 로그가 실시간으로 생성되고 있는 것을 확인 가능
7. 두 개의 프로그램을 Ctrl + C 키로 각각 종료  
  아래와 같은 명령어로 시뮬레이터의 pid를 찾아 강제로 종료  
  `ps -ef | grep smartcar.log`  
  `kill -9 [pid]`
8. 두 번째 시뮬레이터 CarLogMain.java 실행  
  `java -cp bigdata.smartcar.loggen-1.0 jar com.wikibook.bigdata.smartcar.loggen.CarLogMain 202160101 10`
9. Server02에 SSH 세션을 하나 더 열어 로그 시뮬레이터 정상 확인  
  `cd /home/pilot-pjt/working/SmartCar`  
  `tail -f SmartCarStatusInfo_20160101.txt`  
  - 자동차의 상태 정보를 24시간동안 실행된 로그를 시뮬레이션 해주는 것  
    이 로그 생성 jar 는 24시간에 대한 로그만 생성하고 끝남   
    하루 당 약 10MB 정도 저장
10. 
  - 앞에서 실행한 `CarLogMain.java` 프로그램을 Ctrl+C 키로 종료하거나 시뮬레이터의 pid를 찾아 강제로 종료
11. 스마트카 로그 시뮬레이터 실행시, 별도 옵션 적용 가능  
  - 첫 번째 매개변수는 실행 날짜. 두 번째 매개변수는 스마트카 대수 매개변수  
  - 두 번째 옵션인 스마트카 대수는 전체 성능에 영향을 미칠 수 있으니, 파일럿 PC의 성능에 맞춤  

### 파일럿 환경 관리 - 파일럿 환경 시작 및 종료
1. virtualBox 실행하면 가상 머신 관리자 창이 활성화됨
2. Server01, Server02 를 순서대로 실행
3. 2개의 가상 머신 창이 활성화되고 CentOS 구동
4. PuTTY로 Server01에 SSH 접속해 CM이 구동 완료되었는지 확인  
  `service cloudera-scm-server status`  
5. cloudera-scm-server is running 이라는 메세지가 출력되면 다음 단계 진행
6. 크롬 브라우저를 실행하고 CM 관리자 화면에 접속  
   http://server01.hadoop.com:7180
7. CM의 홈 화면 왼쪽 메뉴 그룹 가운데 [Cluster1] 의 콤보박스를 선택하고 [재시작] 메뉴 클릭   
   Cluster1에 설치돼 있는 HDFS, 주키퍼, 카프카, YARN 등의 소프트웨어들은 서로 의존관계를 맺고 있음  
- 예를 들어 HDFS 를 실행하려면 주키퍼가 먼저 실행되고 있어야 하고, YARN을 사용하려면 HDFS가 먼저 실행되고 있어야 함
- Cluster1의 하위 소프트웨어들을 각각 재시작할 때는 이러한 의존관계를 잘 고려해야 함
- Cluster1 전체를 재시작하면 CM이 알아서 의존성에 따라 모든 소프트웨어들을 재기동하게 됨

### 파일럿 환경 종료 순서
1. 개발 환경 시작 역순으로 하면 됨. 먼저 CM의 홈에서 Cluster1을 중지시키고, 클라우데라 관리 서비스도 중지시킴
2. Server01, Server02, Server03 서버에 각각 PuTTY로 접속해서 시스템 종료 명령을 실행  
  `halt` --> 시스템 강제 종료 명령어 
  halt 명령으로 셧다운이 안되는 경우 3번으로 넘어감
3. 버추얼 박스의 가상 머신 목록에서 Server01, Server02, Server03 을 모두 전원을 끄고 종료
4. 버추얼 박스 관리자를 닫으면 개발 환경이 종료
- 다음과 같이 종료 process를 지키지 않으면, FILE CORRUPT 가 발생할 수 있음

### 빅데이터 실환경 이해
- 빅데이터의 실제 하드웨어와 소프트웨어의 아키텍처 구성이 어떻게 되는지 알아보자
- 15대 정도로 구성된 소규모 BigData 구성도 아키텍처 확인

![img](https://github.com/koni114/smart-car/blob/master/img/smart_car_10_1.png)

- 15대로 구성된 소규모 빅데이터 실환경
- 3대로 구성된 하둡 클러스터의 관리 노드(NameNode, HBase Master.. 등이 2중 3중화 되어 있음)
- 각 물리 서버 한대씩 Name Node(Active, standby) 가 있음
- Ambari mgmt 가 Cloudera 와 동일한 역할을 수행함
- 7대의 서버로 구성된 DataNode를 위해 있는 것. 여기에 BigData 가 저장
  DataNode(HDFS) 관리를 위해 Node Manager가 있는 것임
- 2대의 Nutch 와 Solr 를 통해 외부 분산 크롤링을 배치  
  pilot project 에서는 크롤링을 수행하지는 않음  
  크롤링된 데이터를 저장하고 인덱싱하기 위해 Nutch와 Solr를 배치함


![img](https://github.com/koni114/smart-car/blob/master/img/smart_car_10.png)

- 위의 15대로 구성된 소프트웨어 아키텍처는 위와 같은 H/W 아키텍처로 구성될 수 있음
- 하나의 렉 서버 안에 서버들을 모두 다 꽂아놓고, x86 장비 server 10대 배치
- 통신을 위한 네트워크 스위치 10G 배치(Public/Private)
- 필요하다면 렉을 확장할 수 있음
- 3대의 렉까지 확장할 수 있으며, 개발환경을 가상환경으로 구성해 놓기도 함
- 수집/크롤링 시스템은 물리적 네트워크 존이 다른데 있음
- 기존에 있던 ETL Tool과 연결도 되며, Web/WAS 처럼 업무 시스템과도 연결됨


#### 중간급 규모의 아키텍처
![img](https://github.com/koni114/smart-car/blob/master/img/smart_car_11.png)

- Data Node가 빅데이터 시스템의 규모를 나타낸다라고 볼 수 있음
- 20대의 DataNode가 있음(물리적 서버)
- Tez인거보니 Ambari 계열로 보임
- 수집영역은 랜딩 영역 또는 엣지영역으로 부르기도 함  
- Queue 노드 1번부터 3번까지 3대의 서버로 구성
- 네트워크는 10G망으로 구성된 것이 중요한데, 그 이유는 노드들간의 데이터의 네트워크가 많이 일어나기 때문

#### Rack 구성
![img](https://github.com/koni114/smart-car/blob/master/img/smart_car_12.png)

- rack을 확장할 때마다, 3V(Variety, Velocity, Volume)을 보장
- bigdata ecosystem은 rack-balancing이 자동으로 이루어지게끔 되어 있음
- <b>하나의 랙이 추가될 때마다 순간적으로 data spread 되는 overhead는 발생하나, 하둡의 가장 큰 장점이 노드 확장성임!</b>
- 빅데이터 시스템의 아키텍처가 정해진 것은 없음. 그 이유는 빅데이터 시스템만 구축되는 경우는 없기 때문


## 용어
- 심볼릭 링크
  - 리눅스에서 단순히 원본파일을 가리키도록 링크만 연결 시켜둔 것으로 위에서 설명한 윈도우에서의 바로가기파일이라고 생각하면 됨 
- 아파치 너치(apache nutch)
  - 웹 사이트 크롤링을 위한 오픈 소스 웹 크롤러 소프트웨어 프로젝트
  - 검색시 애플리케이션 페이지의 rank를 높이고 필요에 따라 커스터마이징할 수 있는 자체 검색 엔진을 제공 
- 스키마 
  - 테이블의 정보를 구성하는 추상적인 개념