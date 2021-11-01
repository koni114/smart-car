## smart car 파일럿 프로젝트 개요
- 파일럿 프로젝트에는 POC, PoV, BMT 가 있음
- 이번 파일럿 프로젝트는 PoC 와 PoV 를 진행

### 프로젝트 목표 시스템
![img](https://github.com/koni114/TIL/blob/master/smart-car/img/smart_car_1.png)

- 프로젝트 목표는 다음의 아키텍처를 이해하는 것
- 프로젝트 환경
  - CPU : i5 이상
  - 메모리 : 16GB 이상
  - 디스크 : 120GB 이상 여유공간
  - 디스크는 SSD 권장
  - CPU 가상화를 지원해야 하며, VitualBox를 설치, 서버를 여러대 설치하여 프로젝트 환경을 지원
  - 인터넷이 연결되어 있어야 함

### 프로젝트 개요
- 준비 -> 빅데이터 이해 -> 환경구성 -> 수집 -> 적재 ->  처리/탐색 -> 분석/응용 단계로 진행

#### 환경 구성
- PC 위에 VirtualBox 를 구성하고, 3대의 서버를 구성할 예정
- 그 위에 분산환경을 구성하고 BigData/AI 환경 구성

#### 수집
- 데이터는 Smart Car bigdata를 수집함
- smart car 를 시뮬레이션 하기 위한 시뮬레이터를 사용할 것인데, 시뮬레이터에는 real-time을 발생시키는 데이터와 batch data 를 발생시킴
- batch-data 는 smart car 의 상태정보, real-time 은 운전자의 event data 발생

#### 적재
- batch data --> hadoop 에 저장
- real time data --> Hbase 에 저장

#### 처리/탐색
- data lake 를 구성할 것임
- 주제 영역 별 mart를 생성

#### 분석/응용
- Model 생성 및 API 를 통한 결과 전송