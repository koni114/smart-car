# 클러스터 환경 구성
## 빅데이터 클러스터 구성 1 - 클라우데라 매니져 설치
- 클라우데라 매니저는 하둡을 포함하여 여러 프레임워크 등을 설치, 관리, 모니터링 할 수 있는 기능을 제공

### CM의 주요 기능 설명
- 프로비저닝 : 하둡 에코시스템을 설치, 삭제, 수정 관리
- 매니지먼트 : 설치한 에코시스템의 설정 변경 및 최적화 지원 
- 모니터링 : 하드웨어 리소스 및 설치 컴포넌트의 상태 모니터링/대시보드

### Cloudera Manager 설치 방법
- SSH를 통해 Server01에 root 또는 root 권한을 가진 계정으로 접속
- CM 6.3.1 의 설치 정보가 있는 레포파일을 다운로드 한 후 수정/적용하고 JDK 1.8까지 설치
~~~shell
$ cd /root
$ wget https://archive.cloudera.com/cm6/6.3.1/redhat6/yum/cloudera-manager.repo
~~~
- 다운로드한 cloudera-manager.repo 파일을 yum의 리포지토리 경로로 이동
~~~shell
$ mv /root/cloudera-manager.repo /etc/yum.repos.d/
~~~
- CM 데몬과 서버를 설치. 그 전에 JDK 1.8을 설치  
  yum으로 JDK1.8 설치. 설치 중 표시되는 질문에는 모두 yes를 선택
~~~shell
$ yum install oracle-j2sdk1.8
~~~
- JDK 설치가 완료되면 이제 CM을 설치함
~~~shell
$ yum install cloudera-manager-daemons cloudera-manager-server
~~~
- CM 데몬과 서버를 설치하는 중에 표시되는 질문에는 모두 yes를 선택  
  설치가 완료되기까지는 네트워크 상황에 따라 10분 ~ 30여 분 정도 걸림
- CM 내부에서 사용하는 DB를 설치. 다음 명령으로 PostgreSQL을 설치하고 서비스 시작
~~~shell
$ yum install cloudera-manager-server-db-2
$ service cloudera-scm-server-db start
~~~
- 서비스 기동이 완료되면 PostgreSQL 서버의 원격 접근 제한을 다음 절차를 통해 해제  
  vi 명령으로 PostgreSQL의 `pg_hba.conf` 파일을 열어 다음의 정보를 수정 
~~~shell
$ vi /var/lib/cloudera-scm-server-db/data/pg_hba.conf
~~~
- 먼저 81번째 줄(마지막 3번째 줄)에서 "reject"를 "md5"로 변경  
  - 변경 전: `host all cloudera-scm,scm 0.0.0.0/0 reject`
  - 변경 후: `host all cloudera-scm,scm 0.0.0.0/0 md5`
- 두 번째로 마지막 줄에 아래 내용을 추가해 PostgreSQL에 모든 접근을 허용
~~~shell
host all all 0.0.0.0/0 trust
~~~
- 변경된 설정을 반영하기 위해 DB를 재기동함
~~~shell
$ service cloudera-scm-server-db restart
~~~
- 아래 명령을 통해 CM 서버를 시작하고 상태를 확인해본다. 개인의 파일럿 PC 환경에 따라 기동 시간이 오래 소요될 수 있음
~~~shell
$ service cloudera-scm-server start
$ service cloudera-scm-server status
~~~
- `cloudera-scm-server is running...`  메세지가 표시되면 정상적으로 구동된 것임
- 하둡 배포판
  - 오픈소스로 공개되어 있는 하둡을 기반으로 특정 기업 또는 조직이 커스터마이징해서 만들어낸 독자적인 하둡 패키지를 의미
  - 전 세계적으로 가장 유명한 배포판은 클라우데라사의 CDH와 호튼웍스사의 HDP 가 있음
  - 이 책의 파일럿 프로젝트에서는 CM을 이용해 하둡을 설치하므로 CDH 배포판에 포한된 하둡을 설치 및 이용 


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
- 복제의 이유
  - 특정 서버에 장애가 나더라도 복제해둔 다른 데이터를 통해 안정적으로 서비스가 가능함  
  - 적은 여러개의 데이터를 여러개의 서버에서 병렬로 읽어들이면 더 빠르기 때문 
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
  주로 봐야할 것은 Missing block 이 있는지, Corrupt block 이 있는지 확인
  `hdfs dfsadmin -report`     
  하둡 파일시스템의 기본 정보 및 통계를 보여줌  
- HDFS에 저장된 파일을 로컬 파일시스템으로 가져오기    
  `hdfs dfs -get /tmp/Sample2.txt`  
  그럼 로컬의 `/home/pilot-pjt/` 디렉터리에 Sample2.txt 파일이 생성  
- HDFS의 저장한 파일 삭제(휴지통)  
  `hdfs dfs -rm /tmp/Sample2.txt`  
  삭제 명령 실행시, 휴지통에 임시 삭제되며, 복구가 가능  
  휴지통으로 임시 삭제된 파일은 특정 시간(24시간, CDH 기준)이 지나면 자동으로 완전 삭제됨. 휴지통에 임시 삭제가 필요 없을 때는 -skipTrash 옵션 이용

#### HDFS 파일의 비정상 상태
- HDFS 점검 명령을 실행할 때 하둡의 파일시스템에 문제가 발생할 경우 "CORRUPT FILES", "MISSING BLOCKS", "MISSING SIZE", "CORRUPT BLOCKS" 등의 항목에 숫자가 표기됨
- 이 같은 상태가 지속되면 하둡은 물론 HBase, Hive 등에 부분적인 장애가 발생할 수 있음
- 강제 셧다운이 빈번하고 리소스가 부족한 테스트 환경에서 자주 발생할 수 있는 현상임
- 원래 HDFS는 비정상적인 파일 블록을 발견할 경우 다른 노드에 복구하려고 시도함
- 다음과 같이 사용자가 직접 삭제/이동 명령으로 조치할 수 있음
- 안전 모드 상태로 전환됐다면 강제로 안전 모드를 해제한다  
  `hdfs dfsadmin -safemode leave`
- 손상된 파일들(missing block, corrupt block)이 발생한다면, 특정 서버에 파일이 깨졌을 경우, 자동으로 복제를 해서 복구를 함
- 둘 다 복구가 불가한 경우는 손상된 파일을 삭제할 수 있음  
  손상된 파일을 강제로 삭제하는 명령어는 다음과 같음  
  `hdfs fsck -delete`
- 복구 중 일수도 있으므로, 바로 삭제하는 것은 위험할 수도 있으므로, 손상된 파일을 /lost + found 디렉터리로 옮길 수도 있음  
`hdfs fsck / -move`

### 주키퍼 명령어 사용해보기
- PuTTY 프로그램 실행
- Server2에 root 계정으로 SSH 접속
- zookeeper-client 실행
- Server2에 root 계정으로 로그인한 후 다음 명령 실행  
  `zookeeper-client`   
- 주키퍼 Z노트 등록/조회/삭제
  - 주키퍼의 Z노트 생성과 조회, 삭제가 정상적으로 수행되는지 확인 
  - `create /pilot-pjt bigdata`
  - `ls /`
  - `get /pilot-pjt`
  - `delete /pilot-pjt`



## 용어
- 에이전트
  - 작업을 대신해주는 프로그램이라고 생각하면 됨
  - 인공지능 분야의 사람들은 에이전트를 분산 환경에서 작업을 수행하는 지적인 특성을 갖는 응용 프로그램이라고 함
- 프로비저닝  
  - IT 인프라를 설정하는 프로세스
  - 어떤 종류의 서비스든 사용자의 요구에 맞게 시스템 자체를 제공하는 것
- namenode
  - hdfs 에서의 mater 역할. hdfs에 있는 데이터를 datanode에 분산시키고 관리하는 기능 담당
- hadoop balancer
  - 블록을 재분재하기 위해 사용률이 높은 데이터노드의 블록을 사용률이 낮은 데이터노드로 옮기는 하둡 데몬
  - 각 데이터노드의 사용률 / 클러스터의 사용률을 비교하여 지정된 임계치 비율 이내일 때를 균형 상태라고 함
  - 시간이 지날수록, data-node 마다의 데이터 불균형이 발생할 수 있음
  - 불균형 상태의 클러스터는 map-reduce의 locality에 영향을 받게되며, 자주 사용되는 데이터노드에 큰 부하를 주게됨
- HttpFS
  - HDFSProxy를 대체하기 위해 클라우데라에서 만들었으며, HTTP REST API로 구현되어 손쉽게 HDFS로 접근 할 수 있음 
- zookeeper 
  - 분산 애플리케이션을 위한 코디네이션 시스템. 분산 애플리케이션이 안정적인 서비스를 할 수 있도록 분산되어 있는 각 애플리케이션의 정보를 중앙에 집중하고 구성 관리, 그룹 관리 네이밍, 동기화 등의 서비스 제공 