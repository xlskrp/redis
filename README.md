# ✏️ redis study(1) - redis 란?

redis 란?
=========
> Key-Value 구조의 오픈소스 인메모리 데이터 저장소   
   
         
   
redis 의 특징
=============
* Key-Value 구조 이기 때문에 쿼리 필요 X
* 영속성을 지원(Disk 에 데이터 저장, Disk 에 저장된 데이터를 읽어 메모리에 로딩)
* 데이터를 메모리에 로딩 시켜놓고 사용하기 때문에 일반적인 다른 데이터베이스보다 처리속도가 빠름
* String, list, hash, set, sorted set 등 다양한 자료 구조 지원
* 싱글 쓰레드 
* 읽기 성능 지원을 위한 복제 지원(Replication, Master-Slave(Replica) 구조 사용)
* 쓰기 성능 지원을 위한 샤딩 지원(Sharding, 데이터 세트 분할 분산 저장)   



redis 의 영속성
===============
* RDB(Snapshotting) 방식
   + 순간적으로 메모리에 있는 내용을 Disk에 전체를 옮겨 담는 방식
* AOF (Append On File) 방식
   + Redis의 모든 write/update 연산 자체를 모두 log 파일에 기록하는 형태



redis 역할 및 구성
==================
> redis 는 기본적으로 Master, Slave(Replica), Sentinel 로 역할이 나누어져있으며, 세개의 역할을 상황에 맞게 구성하여 사용 
## 1. Master-Slave(Replica)    
     
<img src="https://user-images.githubusercontent.com/108176836/223335455-7761befa-1704-47b5-b109-2e84df2752cf.png" width="200px" height="350px"></img><br/>

   * Master는 기본적으로 모든 명령에 대해 실시간으로 데이터를 처리하며(Read/Write), Slave(Replica)는 Master를 바라보며 실시간으로 마스터에서 처리한 데이터를 복제
   * Slave(Replica)에 사용자는 읽기 권한만 존재(오로지 Master 에 의해서만 데이터가 쓰여짐)
   * Master-Slave(Replica) 구조는 Master 한대에 Slave(Replica)를 다수 구성
   * 그러나 Master 에 장애가 발생하였을 경우, FailOver 되어 Slave(Replica) 가 Master 로 승격되는 것은 아니며 수동으로 승격을 시켜주어야함
   * 즉, Master가 장애가 나면 데이터에 대해 쓰기가 불가능하므로 서비스가 중단됨(물론, 슬레이브는 살아있으니 장애 전 데이터에 대해서는 읽기는 가능) 
## 2. Master-Slave(Replica)-Sentinel

<img src="https://user-images.githubusercontent.com/108176836/223337472-48f0aa37-a71d-49f5-a1c4-fd6d86be9619.png" width="400px" height="350px"></img><br/>

   * Master-Slave(Replica) 구조를 보완하기 위해 만들어짐
   * 기존의 Master-Slave(Replica) 구조에서 Sentinel 이라는 역할을 추가함으로 이를 보완
   * Sentinel 은 지속적으로 Master와 Slave(Replica) 를 모니터링하며 Master 에 장애가 발생하였을 경우, Slave(Replica)를 Master로 자동 승격시켜주는 FailOver 역할을 담당
   * Sentinel 은 최소 한대가 필요하지만 주로 3대를 권장하며, 홀수개를 구성해야함
## 3. Cluster

<img src="https://user-images.githubusercontent.com/108176836/223338032-fa7a5222-724e-4988-b60c-3f25e24ad250.png" width="500px" height="350px"></img><br/>

   * Cluster란 여러 대의 서버를 하나로 묶어서 1개의 시스템처럼 동작하게 하는 것
   * 빅데이터를 처리하는 곳이 많아지는 만큼 성능 지연에 대한 문제를 해결하기위해 서버의 자원을 업그레이드하는 Scale-up 방식, 별도의 서버를 추가하는 Scale-out 방식을 사용하나 Scale-up 방식은 조건이 제한적일수 있으므로 Scale-out 방식을 주로 사용하는데, Scale-out 방식을 사용하여 여러 대의 서버를 사용할 경우 여러 대의 서버에 데이터를 분산하여 처리하게 하도록 해주어야함
   * redis 는 안그래도 싱글 쓰레드이기 때문에 key 가 많아 질수록 서비스에 성능에 문제를 야기 할 가능성이 높아, 이와 같은 문제를 해결하기 위해 데이터를 물리적으로 여러 파티션에 나누어 저장할 수있는 수평적 파티셔닝(Horizontal Partitioning) 또는 샤딩(Sharding) 이라고 불리는 기능을 제공
   * 이와 같이 데이터 분산 처리를 위한 샤딩과 안정성 확보를 위한 복제 시스템을 함께 사용하는 것을 Redis 클러스터(Shared-Replication) 라고 함
   * 기본적으로 Master, Slave 서버만으로 구성되며 Sentinel 서버는 필요 X (Sentinel이 없어도 FailOver 가능)
   * 기본적으로 16384개의 슬롯을 가지며 3대의 Redis 서버가 구축되어 있는 환경에서 첫 번째 서버에는 0-5460, 두 번째 서버에는 5461-10922, 세번째는 10923-16384 슬롯 정보가 분산되도록 사용




참고
====
   * https://devlog-wjdrbs96.tistory.com/374
   * https://wildeveloperetrain.tistory.com/21
   * https://steady-coding.tistory.com/586
   * https://dataonair.or.kr/db-tech-reference/d-guide/data-practical/?mod=document&uid=393
   * https://cla9.tistory.com/102
   * https://velog.io/@limsubin/Redis%EB%A5%BC-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90
   * https://redis.io/
