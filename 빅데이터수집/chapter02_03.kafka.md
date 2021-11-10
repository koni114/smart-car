## 수집에 활용할 기술 - kafka
- message oriented middleware software 중 하나
- 대규모로 발생하는 작은 데이터들이 있는데, 수십, 수백, 수천건이 발생할 때, 이를 동기방식으로 처리하기에는 무리가 있어 비동기 방식으로 처리하게 됨
- kafka는 비동기 처리를 위해 queue 나, topic 을 두게 됨(버퍼 역할)
- 원천에서 발생한 데이터를 최종 목표 시스템에 저장해야 하는데, queue/topic 이 버퍼, 트랜잭션 처리를 해줌
- 대규모 메세지 데이터들을 안정적으로 처리하게 해주는 기술

### 주요 구성 요소
- Broker
  - 카프카의 서비스 인스턴스. 다수의 Broker를 클러스터로 구성하고 Topic이 생성되는 물리적 서버
- Topic
  - Broker에서 데이터의 발행/소비 처리를 위한 중간 저장소 
- Provider
  - Broker의 특정 Topic에 데이터를 전송하는 역할
  - 애플리케이션에서 카프카 라이브러리를 이용해 구현 
- Consumer
  - Broker의 특정 Topic에서 데이터를 수신하는 역할로서 애플리케이션에서 카프카 라이브러리를 이용해 구현 

![img](https://github.com/koni114/smart-car/blob/master/img/smart_car_18.png)

- Single Broker On Single Node 
- 카프카 아키텍처는 업무 도메인이 단순할 때 많이 사용. 

![img](https://github.com/koni114/smart-car/blob/master/img/smart_car_19.png)

- Multi Broker On Single Node
- 카프카 서버가 한대이므로, 대규모 발행 소비 요건을 커버하기는 어렵지만, 업무의 복잡한 요건을 수행할 때 사용

![img](https://github.com/koni114/smart-car/blob/master/img/smart_car_20.png)

- 가장 많이 사용되는 카프카 아키텍처
- 물리적인 노드가 2대가 있고, 안에 여러개의 broker 들을 만들어 둠
- broker 안에 여러 개의 Topic 들을 둘 수 있음. 
- Provider 가 전송한 메세지들이 주변의 다른 노드에 전송되어 복제하기도 함
- Consumer 입장에서는 여러 군데에서 빼오는 것이 좋기 때문에 Consumer도 여러 개를 구성함

### 카프카 활용 방안
![img](https://github.com/koni114/smart-car/blob/master/img/smart_car_21.png)

- 플럼에 저장한 데이터를 바로 하둡에 저장하는 것이 아니라, 카프카에 전송함
- 카프카에 저장된 데이터는 별도의 transaction 관리가 돼서 최종 하둡에 안정적으로 저장이 될 수 있도록 함
- 도중에 실패한 메세지는 롤백해 다시 데이터를 전송받기 등을 수행함 

![img](https://github.com/koni114/smart-car/blob/master/img/smart_car_22.png)

- 카프카가 없는 경우의 아키텍처에 해당함
- 만약 카프카가 없는 상태에서 HBase 에 장애가 발생하면 플럼에 쌓이는데, 플럼에 버퍼 기능이 있기는 하지만, size가 그렇게 크지 않음
- 이렇게 되면 플럼에 2차 장애가 발생할 수 있음. HBase와 Flume 사이에 카프카를 두어야 함 

![img](https://github.com/koni114/smart-car/blob/master/img/smart_car_23.png)

- Hbase 가 발생하였더라도 kafka에 2시간 동안 담아두고, 장애 복구가 되는 시점에 kafka에 적재된 데이터를 HBase에 저장
- kafka는 다양한 서비스에 활용됨. 특히 분산 임시 저장소가 필요할 때 많이 사용됨