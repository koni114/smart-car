# bigdata 이해하기
- RDBMS 와 BigData 를 비교해서는 안됨
- 두 개는 상호 보완적인 역할을 수행함. 

## 빅데이터 프로젝트
### 플랫폼 구축형 프로젝트
- 전형적인 빅데이터 SI 구축형 사업
- 빅데이터의 하드웨어와 소프트웨어 설치 및 구성 
- 수집 -> 적재 -> 처리 -> 탐색 -> 분석의 기능을 구현
- 3 ~ 6개월 정도로 추진
- 플랫폼 파트(설치/구성), 전처리 파트 등

### 빅데이터 분석 프로젝트
- 빅데이터 플랫폼 구축 완료후 수행
- 빅데이터 탐색으로 데이터의 이해가 높아질때 시작
- 조직의 가치사슬 중, 대규모 분석이 필요한 시점에 추진
- 1 ~ 3개월 일정으로 추진
- 분석주제영역 - 마케팅/고객, 상품/서비스 개발, 리스크 관리 등

### 빅데이터 운영 프로젝트
- 구축 완료된 플랫폼을 중장기적으로 유지 관리
- 대규모 하드웨어/소프트웨어 운영 비용 높음
- 빅데이터 분야별 전문가 그룹이 확보 되어야 함
- 빅데이터 거버넌스 체계를 수립 해야 함
  - 전사 시스템이기 때문
  - 필요한 프로세스를 표준화해야 함 

### 빅데이터 구현기술
- 수집, 적재, 처리/탐색, 분석/응용으로 구성

![img](https://github.com/koni114/smart-car/blob/master/img/smart_car_24.png)

#### 수집
- Crawling, FTP, Open API, RSS, Log Aggregation, DB Aggregation, Streaming
- Volume, Variety, Velocity 측면 중요
- 분산 기능의 선형적 확장이 필요
- 수집기는 원천 시스템의 인터페이스 유형, 데이터베이스 파일, 메세지 등과 연결되어 정형/비정형 데이터를 대량으로 수집
- 수집/처리는 대용량 파일 수집과 실시간 streaming 형 수집으로 나눌 수 있음
- 실시간 수집의 경우에는 CEP(Complex Event Processing), ESP(Event Streaming Processing) 가 필요
- 대용량 데이터 및 대규모 메세지 수집
- 정형/반정형/비정형 데이터 수집. ex) Log, RSS, XML, 파일, DB, HTML, 음성, 사진, 동영상 등

#### 적재
- Distributed File, NoSQL, Memory Cached, Message Queue
- Volume, Variety, Velocity, Veracity 중요
- 분산 스토리지에 임시/영구 저장함
- 영구 저장하기 위한 HDFS 가 있음, 대규모 메세징 데이터를 영구 저장하기 위한 NoSQL(MongoDB, ..)  
- 대규모 메세징 데이터를 일부만 임시 저장하기 위한 In-memory cache 인 Redis, RAMCache 등이 있음
- 대규모 메세징 데이터를 버퍼링하기 위한 Kafka..등이 있음
- 수집할 데이터의 성격에 따라서 선택적인 아키텍처가 필요
- 실시간 및 대량으로 발생하는 작은 메세지 같은 경우 HDFS에 저장하게 된다면 File 개수가 기하급수적으로 늘어나 관리 노드와 병렬 처리의 효율이 떨어지게 됨
- 빅데이터가 적재될 시, 추가적인 전처리 작업이 필요하게 됨. 비정형 데이터를 정형 데이터로 가공하거나, 비식별 처리가 추가됨
- 이러한 작업은 HDFS에 적재 후 후처리로 사용되기도 함
- 프로젝트에서는 Hadoop, HBase, redis(분산-캐시 저장소), kafka(메시징 저장소)를 사용

#### 처리/탐색
- Structured Processing, Unstructured Processing, Workflow, Scheduler
- SQL Like, Distributed Programming, Exploration, Visualization
- 대용량 저장소에 적재된 데이터를 분석에 활용하기 위해 정형화 등을 수행
- 데이터를 통해 가치를 발굴하기 위해서는 데이터를 이해하는 것이 선행되어야 하며, 데이터를 지속적으로 관찰해야 함
- 탐색적 분석에는 SQL on Hadoop 이 사용됨. 데이터를 탐색/선택/변환하여 사용됨
- 정기적으로 발생하는 처리/과정은 워크플로우로 처리하여 자동화함
- 워크플로우 작업이 끝나면 데이터셋 등은 데이터 마트로 옮겨짐
- 워크플로우 자동화를 위해 Oozie 사용

#### 분석
- Data Mining, Machine Learning, Analysis Visualization

#### 응용
- Data Export/Import
- Reporting
- Business Visualization

### 빅데이터 보안 - 접근제어 보안
- ID/Password 를 통한 인증 관리와 인증된 계정별 권한 관리가 별도로 필요함
- Hadoop은 인증관리와 권한관리가 다소 취약함
- 하둡 내에 존재하는 개인정보, 거래정보, 행동이력 등을 다양한 사용자와 시스템들에게 제공하기 위해서 3rd-party 제품인 접근 제어 기술을 적용

![img](https://github.com/koni114/smart-car/blob/master/img/smart_car_25.png)
- Apache KNOX
  - 네트워크 방화벽 구간인 DMZ에 설치를 함
  - 클라이언트와 하둡 에코시스템이 직접 접근하지 못하도록 하고, apache KNOX 를 통해 접근할 수 있도록 함
  - LDAP과 KDC에서 계정과 권한 정보를 받아 client와 Hadoop ecosystem의 중간다리 역할을 수행

![img](https://github.com/koni114/smart-car/blob/master/img/smart_car_26.png)
- Sentry
  - 중앙에 sentry 서버를 두게됨
  - 계정과 권한을 관리하는 Policy MetaStore에서 계정과 권한 관리 수행
  - Impala, Hive, hadoop name-node와 같은 주변 노드 시스템들에게 centry-agent를 설치하여 중앙의 sentry 서버의 계정 정보와 권한 관리 정보를 활용할 수 있게끔 하는 아키텍처를 구성함

![img](https://github.com/koni114/smart-car/blob/master/img/smart_car_27.png)
- ranger
  - 중앙에 apache ranger를 두고, 계정 및 권한 관리를 수행하는 policy database 가 있음 
  - hadoop-ecosystem 에 좀 더 깊숙이 관여하는 ranger plugin 있음
  - sentry와 유사

![img](https://github.com/koni114/smart-car/blob/master/img/smart_car_28.png)
- Kerberos
  - key distribution center라고 하는 시스템으로 불리기도 함
  - key distribution center에는 인증 서버와 티켓 발행 서버가 있는데, 하둡 클라이언트는 KDC 에서 티켓을 획득하고 이를 기반으로 하둡 파일 시스템에 접근하게 되는 개념

