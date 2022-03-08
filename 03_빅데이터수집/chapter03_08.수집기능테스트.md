## chapter03-08 수집 기능 테스트
- 먼저 server02에 ssh 접속을 하고 `bigdata.smartcar.loggen-1.0.jar` 가 위치한 곳으로 이동
~~~shell
$ cd /home/pilot-pjt/working
~~~
- 다음 명령으로 2개의 스마트카 로그 시뮬레이터를 백그라운드 방식으로 실행한다
~~~shell
$ java -cp bigdata.smartcar.loggen-1.0.jar com.wikibook.bigdata.smartcar.loggen.CarLogMain 2016010 3 &
$ java -cp bigdata.smartcar.loggen-1.0.jar com.wikibook.bigdata.smartcar.loggen.DriverLogMain 2016010 3 &
~~~
- 2016년 1월 1일에 3대의 스마트카에 대한 상태 정보와 운전자의 운행 정보가 생성되기 시작함
- 정상적으로 시뮬레이터가 작동되고 있는지 아래의 내용으로 확인
  - `/home/pilot-pjt/working/SmartCar` 경로에 `SmartCarStatusInfo_20160101.txt` 파일이 생성됐는지 확인
  - `SmartCarStatusInfo_20160101.txt` 파일의 내용을 확인해 보면 3개의 스마트카 상태 정보가 기록된 것을 볼 수 있음
~~~shell
$ cd /home/pilot-pjt/working/SmartCar
$ vi SmartCarStatusInfo_20160101.txt
~~~
- `/home/pilot-pjt/working/driver-realtime-log` 경로에 SmartCarDriverInfo.log 파일이 생성됐는지 확인
- `tail -f SmartCarDriverInfo.log` 명령을 통해 3대의 스마트카 운전자의 운행 정보가 실시간으로 발생하는 것을 볼수 있음
~~~shell
$ cd /home/pilot-pjt/working/driver-realtime-log
$ tail -f SmartCarDriverInfo.log
~~~
- 마지막으로 `/home/pilot-pjt/working/SmartCar` 경로에 만들어진 `SmartCarStatusInfo_20160101.txt` 파일을 플럼 SmartCarInfo 에이전트의 SpoolDir 경로로 옮긴다
~~~shell
$ mv /home/pilot-pjt/working/SmartCar/SmartCar/SmartCarStatusInfo_20160101.txt /home/pilot-pjt/working/car-batch-log
~~~

### 플럼 에이전트 작동
- CM 에서 flume -> 재시작 수행

### Kafka Consumer 작동
~~~shell
$ kafka-console-consumer --bootstrap-server server02.hadoop.com:9092 --topic SmartCar-Topic --partition 0
~~~

### 수집 기능 점검
- 스마트카의 상태 정보 로그 파일이 플럼의 표준 출력 로그로 전송됐는지 리눅스 `tail` 명령어를 통해 확인
~~~shell
$ tail -f /var/log/flume-ng/flume-cmf-flume-AGENT-server02.hadoop.com.log
~~~
- 스마트카 운전자의 실시간 운전 정보인 DriverCarInfo가 정상적으로 수집되는지 확인  
  앞서 실행했던 Kafka의 Consumer 콘솔창을 확인해보자. 창을 닫았다면 Server02에 SSH에 접속. 아래의 명령어를 다시 실행
~~~shell
$ kafka-console-consumer --bootstrap-server server02.hadoop.com:9092 --topic SmartCar-Topic --partition 0
~~~
- 백그라운드로 실행했던 로그 시뮬레이터를 모두 종료
~~~shell
$ ps -ef | grep smartcar.log
$ kill -9 [pid]
~~~

### 파일럿 환경의 로그 확인 
- 빅데이터 시스템에서는 에코시스템들의 로그를 확인하는 것이 중요. 많은 소프트웨어가 설치되고 서로간의 의존성이 커서 다양한 문제점들을 로그를 통해 확인해야 하기 때문이다. 파일럿 환경의 로그를 점검하기 위해서는 아래의 경로를 참고한다
  - Hadoop 에코시스템 서버들의 로그 위치: /var/log/디렉터리(cloudera, Hadoop, Oozie 등)
  - Redis 서버 로그 위치: /var/log/redis_6379.log
  - Storm 서버 로그 위치: /home/pilot-pjt/storm/logs/
  - Zeppelin 서버 로그 위치: /home/pilot-pjt/zeppelin-0.8.2-bin-all/logs

### 파일럿 환경에서 HDFS 문제 발생
- 개인의 파일럿 프로젝트 환경은 가상머신으로 구성돼 있어 비정상적인 종료가 자주 발생할 수 밖에 없음
- 이때 HDFS상에 CORRUPT BLOCKS/FILES 같은 문제가 발생하거나 Safe 모드로 전환되어 빠져 나오지 못하는 경우가 자주 발생
- 만약 파일럿 환경의 일부 기능 또는 설치 중에 문제가 발생했다면 HDFS의 파일/블록 깨짐 또는 Safe 모드 전환 여부를 체크해야 함
  - HDFS 파일 시스템 검사: $ hdfs fsck /
  - HDFS에 Safe 모드 발생 후 빠져나오지 못할 경우  
    Safe 모드 강제 해제 : `hdfs dfsadmin -safemode leave`
  - HDFS에 CORRUPT BLOCKS/FILES 등이 발생해 복구가 불가능 할 경우  
    손상된 파일 강제 삭제: `$ hdfs fsck / -delete`
    손상된 파일을 /lost + found 디렉터리로 이동: `$ hdfs fsck / -move`
