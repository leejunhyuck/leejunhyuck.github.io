---  
layout: post  
title: "[DB] Master Slave 로의 Application 설정 변경"  
subtitle: "Master Slave 로의 Application 설정 변경"  
categories: dev
tags: database replication
comments: true

---
> `Master Slave` 구성된 DB에서 질의 Application에서의 설정

## Master Slave 구성된 DB에서 질의 Application에서의 설정

* 목적  
  DB의 부하를 줄이기 위해서 Write, Read의  Master Slave 구성된 DB에서 분리하기 위함

* 상세 설계
    * 정보  
      현재 구성된 DB 구조는 Master(Write), Slave(Read) 2개로 구성되어 있음

    * 요구사항  
      Write(Create, Update, Delete)일 경우 Master로 사용  
      Read(Selete)일 경우 Slave로 사용

    * 구현 방법  
          Transactional 어노테이션의 ReadOnly 옵션 값 (true), (false)으로 설정  
          AOP를 사용한 DataSource를 Service Method단에서 사용자 지정 설정

          Transactional
           구성 해야되는 부분은 똑같다.
           Propagation을 잘 고려하여야 한다.
           @Transactional 어노테이션을 모든 곳에 추가하여야한다.

          DataSource 지정
           구성 해야되는 부분은 똑같다.

    * 참고사항
      #### Replication 의 동작 방법
      __Master Slave로 나뉘어져 있는 DB에 데이터를 동기화가 어떻게 되는 것인가??__
            
          Master는 바이너리 로그라는 파일에 DB에서 발생한 변경내역을 계속해서 저장한다.  
          Slave는 I/O Thread를 통하여 Master에 요청을하고,  
          Master는 Binlog dump를 통하여 해당 내용을 전송하여 받은 다음,  
          Slave는 전송받은 내용을 Relay Log를 만들고, 변경사항을 계속해서 동기화 한다.  
          만약 Master, Slave 1:1 구성일 경우
          (대체로 Replication을 구성한다면 Master(1), Slave(3)의 셋트로 구성하는 것이 일반적이다) 
          Slave가 죽는 경우가 발생하여 연결이 되지 않을 수 있다. 
          그런 경우, Multi Master(2대의 Master)로 구성을 고려 해 볼 수 있을 것 같다.


각각의 Master/Slave 구성 설정완료 후, 커넥션의 갯수가 증가하는 것을 아래 커맨드로 알 수 있다.

```roomsql
  show status where variable_name in (
  
    'max_used_connections',
    
    'aborted_clients',
    
    'aborted_connects',
    
    'threads_connected',
    
    'connections'
  
  );
```




---