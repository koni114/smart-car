# 클러스터 환경 구성
## 하둡, 주키퍼 기본구성
- Cloudera Manager 를 활용하여 하둡과 주키퍼 설치해보기

### Cloudera 에서 하둡, 주키퍼 기본 구성하기
- Select Repository
  - CDH 버전을 구성하는 곳
  - 우리는 CDH 6.3.2-1 version 을 선택
  - Additional Parcels 에서 Parcels 는 cloudera software package 단위임
  - accululo, kafka, spark, sqoop, mkl 은 일단 없음으로 setting 
  - 다음 버튼을 누르면 CDH parcel 이 자동 설치됨
- JDK 설치 옵션 창에서는 Oracle Java SE Development Kit(JDK) 설치에 동의
- Java 암호화 정책 설치 동의는 check 안하여도 됨
- 클러스터 내 SSH 접속 정보 입력
  - root 선택
  - server01, server02, server03 사이에서 SSH 통신을 하는데, 이 때 root 계정으로 하며, 암호는 adminuser로 입력
- CM 에이전트 및 기본 패키지 설치 
- CM Parcel 설치 및 완료(Install Parcels)
  - clpudera 서버에서 패키지를 인터넷 망을 통해 다운 받는 단계
  - server01, server02에 패키지 미리 셋팅
- Inspect Cluster
  - 클러스터의 네트워크 상태, 호스트 상태 등을 점검함  
  - `I understand the risks, let me continue with cluster setup` 선택
- Select Services
  - 사용자 지정 서비스(Custom Services) 선택 
  - HDFS, YARN, Zookeeper
  - HDFS 위에 YARN 이 깔리는데, 서버들의 자원을 관리해주는 resource management
  - 메모리 자원, 필요한 hadoop eco-system 등을 yarn 위에서 작동하게끔 하는 관리 시스템
- Assign Roles
  - 앞에서 선택한 서비스 설치 위치 지정 
  - 어떤 서버에 어떤 컴포넌트를 선택할 것인지 지정
  - HDFS
    - NameNode: Server01 선택
    - Secondary NameNode: Server01 선택
    - Balancer: Server01 선택
    - HttpFS: 미설치
    - NFS Gateway: 미설치
    - DataNode: Server02, Server03 선택
    - Cloudera Management Service 의 설치 위치를 모두 Server01 로 함
  - Cloudera Management Service
    - Cloudera Management Service 의 설치 위치를 모두 Server01로 선택
    - Activity Monitor 는 선택하지 않음 
    - Telometory Publisher 는 선택하지 않음
    - 고사양인 경우, Server03 으로 선택
  - YARN(MR2 Included)
    - ResourceManager: Server01 선택
    - JobHistory Server: Server01 선택
    - NodeManager: "DataNode로 저장" 선택  
  - ZooKeeper
    - Server02 선택
- 데이터베이스 설정
  - 내장된 데이터베이스 사용
  - PostgreSQL 선택 후 테스트 해서 문제 없는지 확인  
- First Run Command(설치 실행)
  - 지금까지 setting 한 환경 설정 및 HDFS, YARN, Zookeeper 가 설치됨
  - 설치 완료 메세지가 뜸
- CM 메인 화면에 HDFS, YARN, Zookeeper 가 설치되어 있음을 확인  
- Server01, 02, 03 의 자원 현황 모니터링 화면 볼 수 있음
- 서버 상태가 준비상태가 되지 않는 이상 문제가 되지는 않음
- 만약 Pilot project 를 진행하면서 자원 부족 현상이 발생하는 경우에는 cloudera service 는 중지해주는 것이 좋음. 그 이유는 해당 서비스는 단순히 모니터링을 위해 제공되는 서비스이기 때문

## DataNode 추가 및 환경설정
### DataNode 추가
- DataNode 추가 및 환경설정 해보기
- 우리는 server01 에도 dataNode 를 추가할 것임  
  이렇게 되면 computing power 가 더 늘어날 것임
- 현재 Pilot project 에서는 dataNode 2대만 사용할 것임

### 고사양(server03) 환경에서 DataNode 추가하기
- CM 홈에서 [HDFS] 메뉴 우측의 콤보박스를 클릭한 후 [역할 인스턴스 추가(Add Role Instance)] 메뉴 클릭
- 초기에 cloudera manager 에서 설정했던 정보들을 확인 가능
- server01 를 check 하고 확인 눌러주면 됨

### 기타 환경 설정
- 복제 계수(replicaSet) 변경 : 3개에서 2개로 변경  
  저사양 환경에서는 1개로 변경
- 고사양 server 기준으로 01, 02, 03이 있고, 해당 서버에 데이터 노드가 있음
- 사용자가 하둡에 200MB 데이터를 저장한다고 한다면 128MB 가 server01에 저장되며, 나머지 72MB는 server03에 저장됨
- 복제 계수는 s1 에 있는 128MB 데이터를 다른 서버에 저장하고, 72MB 데이터도 마찬가지로 다른 서버에 저장됨
- 하둡은 기본적으로 3 replicaSet 임
- pilot project 에서는 1개만 setting 하기로 함
- `HDFS` -> `configuration` -> `dfs.replication` 검색 후 1로 변경
- 복제의 이유는 1. 특정 서버에 장애가 나더라도 복제해둔 다른 데이터를 통해 안정적으로 서비스가 가능함 2. 적은 여러개의 데이터를 여러개의 서버에서 병렬로 읽어들이면 더 빠르기 때문 
- HDFS 접근 권한 해제
  - 하둡의 자체적인 access 제어
  - apache range 등을 통해 접근 제어 가능함 
  - HDFS 권한 검사: 해제  
    `HDFS` -> `configuration` -> `dfs.permissions` 검색 후 check 해제 후 저장
- HDFS 블록 크기 변경
  - HDFS 블록 크기 변경: 128 -> 64  
  - `HDFS` -> `configuration` ->  `dfs.blocksize`  검색 후 64로 변경 후 저장
- YARN 스케줄러와 리소스메니저의 메모리 설정
  - YARN 스케줄러 메모리 변경: 1 -> 1.5  
    `YARN` -> `configuration` -> `yarn.scheduler.maximum-allocation-mb` 검색 후 1.5 로 늘려 저장
  - 리소스메니저 메모리 변경: 1 -> 5  
    `YARN` -> `configuration` -> `yarn.nodemanager.resource.memory-mb` 후 5로 변경
- YARN Scheduler 의 defualt는 fair-Scheduler
  - pilot 환경에서는 자원이 부족하여 사용 불가능
  - 일을 공평하게 시키다가 경합이 발생하여 overhead 가 크게 발생하여 행이 걸림
  - 우리는 FIFO 로 변경하자  
    `org.apache.hadoop.yarn.server.resoucemanager.scheduler.fifo.FifoScheduler` 로 변경  
    `YARN` -> `configuration` -> `Scheduler class` 검색 후 
    일이 들어온 순서대로 처리하도록 변경(First-In-First-Out)
- 위의 환경 구성 설정을 하게되면 CM 홈의 HDFS 아이콘 옆에 서버 배포 활성화 버튼이 활성화 됨

### 복제 계수(replica set)으로 분석 성능 향상
- 하둡에 이미 저장돼 있는 특정 파일에 대해 복제 계수를 강제로 증가시킬 수 있음
- 그러면 여러 데이터노드에 복제 파일이 분산 저장되어 분석 작업 시 로컬리티와 병렬성을 높여 성능을 극대화할 수 있음
- 예를 들어, 다음 명령을 통해 /users/hadoop 하위 경로의 모든 파일의 복제계수를 10으로 조정 가능
- `hadoop fs -setrep 10 -R /user/hadoop/`

### Putty로 서버 환경 접속 
- MacOS 기준 [터미널] - [새로운 원격 연결]
- 192.168.56.101, 192.168.56.102 접속 후, root/adminuser 로 접속

### 2.5 HDFS/주키퍼 명령을 이용한 설치 확인
- 예제소스 및 download 파일을 설치  
  https://github.com/wikibook/bigdata2nd  
  우측 중앙의 [Clone and download] 버튼 클릭 후 [Download ZIP] 클릭
- local PC 의 특정 directory 에 bigdata2nd-master 라는 디렉토리를 위치시키고, /CH02/Sample.txt 를 Server02의 /home/bigdata/ 경로에 업로드

#### HDFS 명령어
- Server02 에 SSH 접속을 하고 업로드한 샘플 파일이 있는 위치로 이동하여 HDFS의 put 명령을 실행
- `cd /home/bigdata`
- `hdfs dfs -put Sample.txt /tmp`   
  Sample.txt 파일이 HDFS의 /tmp 디렉토리로 저장됨
- HDFS에 저장한 파일 확인  
  `hdfs dfs -ls /tmp`
- 앞서 /tmp 디렉터리에 저장한 "Sample.txt" 파일의 목록이 조회됨
- HDFS 저장한 파일 내용 보기  
  `hdfs dfs -cat /tmp/Sample.txt`
- HDFS에 저장한 파일 상태 확인  
  `hdfs dfs -stat '%b %o %r %u %n' /tmp/Sample.txt`  
  파일 크기(%b), 파일 블록 크기(%o), 복제 수(%r), 소유자명(%u), 파일명(%n) 정보를 보여줌
- HDFS 저장한 파일의 이름 바꾸기  
  `hdfs dfs -mv /tmp/Sample.txt /tmp/Sample2.txt`  
  기존 파일명인 'Sample.txt'를 'Sample2.txt'  
- HDFS의 파일 시스템 상태 검사  
  `hdfs fsck /`  
  전체 크기, 디렉터리 수, 파일 수, 노드 수 등 파일 시스템의 전체 상태를 보여줌  
  `hdfs dfsadmin -report`  
  하둡 파일시스템의 기본 정보 및 통계를 보여줌  
- HDFS에 저장된 파일을 로컬 파일시스템으로 가져오기    
  `hdfs dfs -get /tmp/Sample2.txt`  
  그럼 로컬의 /home/pilot-pjt/ 디렉터리에 Sample2.txt 파일이 생성  
- HDFS의 저장한 파일 삭제(휴지통)  
  `hdfs dfs -rm /tmp/Sample2.txt`  
  삭제 명령 실행시, 휴지통에 임시 삭제되며, 복구가 가능  
  휴지통으로 임시 삭제된 파일은 특정 시간(24시간, CDH 기준)이 지나면 자동으로 완전 삭제됨. 휴지통에 임시 삭제가 필요 없을 때는 -skipTrash 옵션 이용


## 용어
- 에이전트
  - 작업을 대신해주는 프로그램이라고 생각하면 됨
  - 인공지능 분야의 사람들은 에이전트를 분산 환경에서 작업을 수행하는 지적인 특성을 갖는 응용 프로그램이라고 함