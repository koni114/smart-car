## chapter03-07 카프카 기능 구현
- kafka sink는 smart car에서 발생되는 운전자의 실시간 정보를 받아서 kafka sink를 통해서 kafka로 전송하는 agent였음.
- flume의 kafka sink가 바라보고 있는 kafka를 기능 구현 할 것임

### Kafka Producer / Consumer 사용
![img](https://github.com/koni114/smart-car/blob/master/img/smart_car_30.png)

- Kafka Broker 안에 SmartCar라는 Topic을 생성할 것임
- Producer가 kafka sink가 됨. smart-car에서 수집한 log data 들을 Producer(kafka sink)가 Broker에 쏴주면서 Topic에 저장을 하고, Topic을 바라보고 있는 Consumer(Console) 을 수신해서 뿌려줌
- Hello BigData 라는 문자열을 송신하면, 해당 문자열은 브로드캐스팅되어 consumer1, consumer2에 동일하게 전송받음

### Kafka 생성
~~~shell
$ kafka-topics --create --zookeeper server02.hadoop.com:2181 --replication-factor 1 --partitions 1 --topic SmartCar-Topic
~~~

- zookeeper, replication-factor, partition 지정
- Topic 명을 `SmartCar-Topic` 으로 지정
- 실행 후, `Created topic SmartCar-Topic` 라는 출력값이 보여야 함

### Kafka Producer 사용
~~~shell
$ kafka-console-producer --broker-list server02.hadoop.com:9092 --topic SmartCar-Topic
~~~
- 앞에 만들었던 Topic 명을 지정해줌

### kafka Consumer-1 사용
~~~shell
$ kafka-console-consumer --bootstrap-server server02.hadoop.com:9092 --topic SmartCar-Topic --partition 0 --from-beginning
~~~
- `from-beginning` -> 만약 받지 못한 데이터가 있으면, 처음부터 끝까지 다 받아서 출력하라는 뜻

### kafka Consumer-2 사용
~~~shell
$ kafka-console-consumer --bootstrap-server server02.hadoop.com:9092 --topic SmartCar-Topic --partition 0 --from-beginning
~~~