## chapter01 빅데이터 이해하기
### 빅데이터 구현기술
![img](https://github.com/koni114/smart-car/blob/master/img/smart_car_24.png)

#### 수집
- 조직의 내.외부의 다양한 시스템으로부터 데이터 수집
- 외부에 있는 데이터, 특히 블로그, 뉴스 등의 데이터는 크롤링을 통한 데이터 수집이 선택적으로 적용됨
- 실시간 수집의 경우에는 CEP(Complex Event Proessing), ESP(Event Streaming Processing) 등의 기술이 사용됨
- Volume, Variety, Velocity 관점에서 중요함

#### 적재
- 빅데이터의 적재 기술은 크게 4가지 
  - 대용량 파일 전체를 영구적으로 저장하기 위한 HDFS
  - 대규모 messaging data 전체를 영구 저장하기 위한 NoSQL
  - 대규모 messaging data를 일부만 임시 저장하기 위한 In-memory DB 
  - 대규모 messaging data 전체를 버퍼링하기 위한 MOM(Message Oriented Middleware)
- 빅데이터 적재 기술은 수집된 데이터에 따라 적재적소에 저장해야함
- 실시간 & 대량으로 발생하는 작은 message 데이터를 HDFS에 저장하게 되면, file이 기하급수적으로 늘어나 관리 노드와 병렬 처리의 효율성이 떨어짐
- 이를 보완하기 위해 데이터의 성격에 따라서 In-memory, MOM 등으로 선택적으로 사용해야 함

#### 처리/탐색
- 대용량 데이터에 저장되어 있는 데이터를 처리/탐색하여 분석에 활용할 데이터를 정형화/정규화 하는 기술
- Hue, Hive, Spark 등 

#### 분석/응용
- 대규모 데이터를 기반으로 패턴을 찾고 insight를 추출해내는 과정

### 빅데이터 보안
- 파일럿 프로젝트에서는 보안 기술을 특별히 다루지는 않음
- 하지만, 데이터 보안과 접근 제어 보안은 일반적인 시스템과는 달리 분산환경의 빅데이터 시스템에 특징이 있음


#### 빅데이터 보안 - 데이터 보안
- 개인정보 비식별화
  - 개인의 사생활 정보를 보호
- 개인정보 비식별화는 2가지 이슈가 존재
  - 개인정보 재식별화 문제: 주변의 다른 데이터와 결합했을 때, 식별력이 높아지는 것을 말함
  - 개인정보 비식별화로 인한 마케팅 사용의 어려움 존재  
    - 개인 식별 문제는 대체키를 활용해 다른 시스템과 결합하여 사용

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



### 용어 정리
- LDAP(Lightweight Directory Access Protocol)
  - 네트워크 상에서 조직이나, 조직 내 파일/개인정보/디바이스 정보 등을 찾아볼 수 있도록 하는 프로토콜 
  - 디렉터리 서비스를 제공하는 것이 주요 기능
  - 디렉터리 안에는 저장/공유할 무엇이든 넣을 수 있고 삽입/수정보다는 검색에 특화된 계층적 구조로 이루어져 있음

 