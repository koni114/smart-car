## chapter03-06 플럼 기능 구현
## SmartCar 에이전트 생성
- Agent 이름: `SmartCar_Agent`

### 구성 파일(Agent 속성)
- source, channel, sink 컴포넌트 지정
  - `SmartCar_Agent.sources = SmartCarInfo_SpoolSource`
  - `SmartCar_Agent.channels = SmartCarInfo_Channel`
  - `SmartCar_Agent.sinks = SmartCarInfo_LoggerSink`
- source 상세정보 지정. 파일당 size는 1000으로 지정
  - `SmartCar_Agent.sources.SmartCarInfo_SpoolSource.type = spooldir`
  - `SmartCar_Agent.sources.SmartCarInfo_SpoolSource.batchSize =  1000`
- channel 사용시 임시 저장소(memory, file, DB) 중 memory 이용. capacity 지정 
  - `SmartCar_Agent.channels.SmartCarInfo_Channel.type=memory`
  - `SmartCar_Agent.channels.SmartCarInfo_Channel.capacity=100000`
  - `SmartCar_Agent.channels.SmartCarInfo_Channel.transactionCapacity=10000`
- sink. 수집한 데이터는 logger로 뿌려볼 것임
  - `SmartCar_Agent.sinks.SmartCarInfo_LoggerSink.type=logger`
- source -> channel -> sink 연결하기 위한 설정 
  - `SmartCar_Agent.sources.SmartCarInfo_SpoolSource.channels = SmartCarInfo_Channel`
  - `SmartCar_Agent.sinks.SmartCarInfo_LoggerSink.channel = SmartCarInfo_Channel`

### SmartCar 에이전트에 Interceptor 추가
- interceptor 추가. source에서 들어온 데이터를 channel로 넘기기전에 수정이나 클렌징 수행하기 위한 설정
- 설정 값의 위치는 크게 중요하지 않지만, 이해력을 높이기 위해 source 와 channel 사이에 위치
- garbage data 를 filter 하기 위해 filterInterceptor 사용 
  - `SmartCar_Agent.sources.SmartCarInfo_SpoolSource.interceptors = filterInterceptor`
- filterInterceptor 는 정규식 사용
  - `SmartCar_Agent.sources.SmartCarInfo_SpoolSource.interceptors.filterInterceptor.type = regex_filter`
- 정규식은 숫자 14자리. 14자리가 아닌 것들은 filter.(excludeEvents = false)
- `SmartCar_Agent.sources.SmartCarInfo_SpoolSource.interceptors.filterInterceptor.regex = ^\\d{14}`
- `SmartCar_Agent.sources.SmartCarInfo_SpoolSource.interceptors.filterInterceptor.excludeEvents = false`
- IOT, 다양한 시스템, 네트워크 정보들을 수집할 때, 간간히 header 정보가 들어올 수 있음  
  들어온 header 정보를 플럼에서 filter 해줌.

## DriverCarInfo 에이전트 생성
### 구성 파일(Agent 속성)
- `SmartCar_Agent.sources.DriverCarInfo_TailSource.type = exec`
- `SmartCar_Agent.sources.DriverCarInfo_TailSource.command = tail -f /home/pilot-pjt/working/driver-realtime-log/SmartCarDriverInfo.log`
- `SmartCar_Agent.sources.DriverCarInfo_TailSource.restart = true`
- `SmartCar_Agent.sources.DriverCarInfo_TailSource.batchSize = 1000`
- `SmartCar_Agent.sources.DriverCarInfo_TailSource.interceptors.filterInterceptor2.type = regex_filter`
- `SmartCar_Agent.sources.DriverCarInfo_TailSource.interceptors.filterInterceptor2.regex = ^\\d{14}`
- `SmartCar_Agent.sources.DriverCarInfo_TailSource.interceptors.filterInterceptor2.excludeEvents = false`
- kafka 설정
  - `SmartCar_Agent.sources.DriverCarInfo_KafkaSink.type = org.apache.flume.sink.kafka.KafkaSink`
  - `SmartCar_Agent.sources.DriverCarInfo_KafkaSink.topic = SmartCar-Topic`
  - `SmartCar_Agent.sources.DriverCarInfo_KafkaSink.brokerList = server02.hadoop.com:9092`
  - `SmartCar_Agent.sources.DriverCarInfo_KafkaSink.requiredAcks = 1`
  - `SmartCar_Agent.sources.DriverCarInfo_KafkaSink.batchsize = 1000`
- DirverCarInfo의 Channel 정보 지정
  - `SmartCar_Agent.channels.DirverCarInfo_Channel.type = memory`
  - `SmartCar_Agent.channels.DirverCarInfo_Channel.capacity = 100000`
  - `SmartCar_Agent.channels.DirverCarInfo_Channel.transactionCapacity = 10000`
- channel 연결
  - `SmartCar_Agent.sources.DirverCarInfo_TailSource.channels = SmartCarInfo_Channel`
  - `SmartCar_Agent.sinks.DirverCarInfo_KafakSink.channel = SmartCarInfo_Channel`