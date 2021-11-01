## 파일럿 아키텍처(SoftWare)
![img](https://github.com/koni114/TIL/blob/master/smart-car/img/smart_car_3.png)

- 수십, 수백대의 하둡 클러스터 노드가 필요할 수 있음

### 소프트웨어 아키텍처 -> 수집 레이어

![img](https://github.com/koni114/TIL/blob/master/smart-car/img/smart_car_4.png)

- 원천 데이터의 유형에 따라 플럼을 통해 수집하고 적재하는 방식이 달라짐
- 스마트카 상태 정보(100MB 이상의 Batch data)데이터는 바로 hadoop 으로 적재
- 운전자의 운행 데이터(event)는 카프카와 storm/에스퍼를 통해 수집하고 HBase와 redis 에 적재함
- 실시간 데이터의 버퍼링과 transaction 을 위해 카프카가 활용됨

### 소프트웨어 아키텍처 -> 적재 레이어

![img](https://github.com/koni114/TIL/blob/master/smart-car/img/smart_car_5.png)

- 수집 영역인 플럼과 스톰이 수집한 데이터를 Hadoop, HBase, redis 에 저장
- 대용량 batch data 는 hadoop, 실시간 성 메세지 데이터는 HBase 와 redis 에 저장함
- hadoop 과 HBase/redis 로 나눠진 데이터는 처리/탐색 영역에서 데이터 웨어하우스를 만드는데 활용됨
- 적재 영역을 크게 2가지 part 로 나누어서 진행함  
  batch/real-time 데이터 처리로 나눠서 진행
- 실시간 데이터 처리에 있어서는 스톰과 에스퍼를 결합하여 event processing data 처리를 진행할 것임

### 소프트웨어 아키텍처 -> 처리/탐색 레이어

![img](https://github.com/koni114/TIL/blob/master/smart-car/img/smart_car_6.png)

- Hadoop, HBase, Redis 에 저장된 데이터를 통해 DW 와 Data Mart를 편성할 것임
- DW 와 Mart 편성하는 작업은 자동화 해야하는데, 이때 Hue, Hive, Spark, Oozie 를 활용하게 됨
- 분석 주제 영역들이 만들어지게 되고, 주제마다 분석으로 활용됨

### 소프트웨어 아키텍처 -> 분석/응용 영역
![img](https://github.com/koni114/TIL/blob/master/smart-car/img/smart_car_7.png)

- 다양한 분석 도구를 통해 EDA, AL/ML 분석 진행
- 머하웃/SparkML는 직접 Hadoop에 분산 클러스터를 만들어서 진행함
- Impala/제플린 분석 도구들은 In-memory 기반에서 빠르게 진행됨

## 파일럿 아키텍처(HW)
- 하드웨어 아키텍처 -> 3V 관점
- 서버, 네트워크, 디스크, RAM 등을 3V 관점에서 접근하여 설계해야함
- Volume, Velocity, Variety 관점에서 설계해야 함
- 고사양 PC는 가상 서버 3대로 진행

### 하드웨어 아키텍처 > 파일럿 PC 구성도

![img](https://github.com/koni114/TIL/blob/master/smart-car/img/smart_car_8.png)

![img](https://github.com/koni114/TIL/blob/master/smart-car/img/smart_car_9.png)

- CM 을 통해 각각 서버마다 hadoop ecosystem 들을 설치
- BigData 시스템이 가상 분산 노드로 가상환경으로 만들어지면 PC 개발 도구환경에서 각각의 서버에 접속해 BigData / AI 작업을 진행
