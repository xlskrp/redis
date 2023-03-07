# ✏️ redis study

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
* 읽기 성능 지원을 위한 복제 지원(Replication, 마스터-슬레이브 구조 사용)
* 쓰기 성능 지원을 위한 샤딩 지원(Sharding, 데이터 세트 분할 분산 저장)   


redis 의 영속성
===============
* RDB(Snapshotting) 방식
   +순간적으로 메모리에 있는 내용을 DISK에 전체를 옮겨 담는 방식
* AOF (Append On File) 방식
   +Redis의 모든 write/update 연산 자체를 모두 log 파일에 기록하는 형태
