## 수집에 활용할 기술 - 플럼
### 플럼 소개
- 원천에 있는 데이터들은 다양한 유형의 데이터(File, DB, socket, API 등)를 수집할 때, 프로토콜, 메세지 포멧, 발생 주기, 데이터 크기를 고려해야 하는데 이를 지원하는 것을 Flume 임
- 하둡에 적재하는 기능도 있음

### 플럼 주요 요소
- Source
  - 다양한 원천 시스템의 데이터를 수집하기 위해 Avro, Thrift, JMS, Spool Dir, Kafka 등 여러 주요 컴포넌트를 제공하며, 수집한 데이터를 Channel로 전달 
- Sink
  - 수집한 데이터를 Channel로 부터 전달받아 최종 목적지에 저장하기 위한 기능으로 HDFS, Hive, Logger, Avro, ElasticSerach, Thrift 등을 제공 
- Channel
  - Source와 Sink를 연결하며, 데이터를 버퍼링하는 컴포넌트로 메모리, 파일, 데이터베이스를 채널의 저장소로 활용 
- Interceptor
  - Source와 Channel 사이에서 데이터 필터링 및 가공하는 컴포넌트로서 Timestamp, Host, Regex Filtering 등을 기본 제공하며, 필요 시 사용자 정의 Interceptor를 추가 
- Agent
  - Source -> (Interceptor) -> Channel -> Sink 컴포넌트 순으로 구성된 작업 단위로 독립된 인스턴스로 생성  
- 원천에서 데이터를 수집하는 컴포넌트가 Source이고, 하둡에 적재하는 컴포넌트가 Sink
- Sink 컴포넌트는 주로 하둡 컴포넌트를 이용할 것이고, Source 컴포넌트는 File 컴포넌트를 사용
- Channel 는 Source 와 Sink 사이에 연결하는 역할 수행
- Agent는 모든 것들을 총칭해서 Agent 라고 함. 즉 Source, Sink, Channel, Interceptor 를 총칭해서 agent 라고 함
- 유사 프로젝트는 Flunted, Scribe, logstash, Chukwa, NiFi, Embulk 등이 있음

### 플럼 아키텍처

![img](https://github.com/koni114/smart-car/blob/master/img/smart_car_12_1.png)

- Flume 은 Source와 Sink로 구성되며, 이를 연결하는 channel로 구성
- 아주 기본적인 Flume 의 데이터 파이프라인

![img](https://github.com/koni114/smart-car/blob/master/img/smart_car_13.png)

- 플럼을 Source, Channel, Sink을 어떻게 구성하느냐에 따라서 아키텍처가 바뀜
- Sink를 여러개 구성하여 병렬로 구성하기도 함
- 복잡한 데이터 파이프라인을 만들 수 있는 특징도 가지고 있음
- Interceptor 를 통해 중간 과정에서 데이터 가공을 진행하며, 여러개의 Sink를 통해 여러 저장소에 저장 할 수 있음. 예를 들어 같은 데이터를 hadoop/RDMS에 저장 가능
- 이러한 전체를 하나의 에이전트라고 함
- 하나의 에이전트 안에 여러개의 파이프라인 구성 가능

![img](https://github.com/koni114/smart-car/blob/master/img/smart_car_14.png)

- 병렬로 구성한 아키텍처. 데이터 유형별로 나눠서 처리하게끔 하는 경우

![img](https://github.com/koni114/smart-car/blob/master/img/smart_car_15.png)

- Tier 1에서 대규모 데이터를 처리할 수 있는 구조로 만들고, Tier 2 에서는 비즈니스적으로 복합적인 처리가 필요할 때 가져갈 수 있는 아키텍처

### 플럼 활용 방안
![img](https://github.com/koni114/smart-car/blob/master/img/smart_car_16.png)

- 플럼 활용 방안1
  - CarLogMain.java 시뮬레이터는 3초 간격으로 데이터가 발생하는데, 이러한 데이터를 1일 단위로 파일로 적재한 후, 이러한 파일을 Flume 을 통해 하둡에 적재할 때 활용 

![img](https://github.com/koni114/smart-car/blob/master/img/smart_car_17.png)
- 플럼 활용 방안2
- 발생한 순간 바로 수집.(실시간 분석 활용)

## 용어 정리
- 인스턴스
  - 객체 지향 프로그래밍(OOP)에서 인스턴스(instance)는 해당 클래스의 구조로 컴퓨터 저장공간에서 할당된 실체를 의미
  - 여기서 클래스는 속성과 행위로 구성된 일종의 설계도 
- Agent
  - 특정 목적에 대해 사용자를 대신하여 작업을 수행하는 자율적 프로세스
  - 독자적으로 존재하지 않고 어떤 환경(운영체제, 네트워크)의 일부이거나 그 안에서 동작하는 시스템
  - 지식 기반(knowledge base)과 추론 기능을 가지며, 자원 또는 다른 에이전트와의 정보 교환과 통신을 통해 문제를 해결
  - Agent는 스스로 환경의 변화를 인지하고, 그에 대응하는 행동을 취하며, 경험을 바탕으로 학습하는 기능을 가짐 
- Agent vs daemon
  - 사용자 입력 여부에 의해 구분할 수 있는데, daemon은 사용자의 입력 없이 백그라운드에서 작업을 수행하며 에이전트는 사용자의 입력이나 사용자가 주어진 목적을 달성하기 위해 수행 