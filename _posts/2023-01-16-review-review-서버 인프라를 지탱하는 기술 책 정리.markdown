---  
layout: post  
title: "[Review] 서버/인프라를 지탱하는 기술 책 정리"  
subtitle: "서버/인프라를 지탱하는 기술 책 정리"  
categories: review
tags: review book 
comments: true
header-img: img/review/sustains-server-infra.png

---
> `서버/인프라를 지탱하는 기술` 책을 보고 정리한 글입니다.

## 서버/인프라를 지탱하는 기술

책의 내용을 요약 및 정리합니다.

### CHAPTER 01 서버/인프라 구축 입문

* 다중화의 본질
    1. 장애를 상정한다.
    2. 장애에 대비해서 예비 운용장비를 준비한다.
    3. 장애가 발생했을 때 예비 운용장비로 교체할 수 있는 운용체제를 정비한다.

* Dns 라운드로빈
    * 서버의 수만큼 글로벌 주소가 필요
    * 균등하게 분산되는 것은 아님

      모마일 사이트 등에서 문제가 되는 경우가 있다. 휴대전화로부터의 접속은 캐리어
      게이트웨이라고 하는 프록시 서버를 경유한다. 프록시 서버에서는 이름변환 결과가
      일정시간동안 캐싱되므로 같은 프록시 서버를 경유하는 접속은 항상 같은 서버로 전달된다.
      따라서 특정 서버에만 처리가 집중할 가능성이 있다. DNS TTL을 짧게 설정할 수있지만
      반드시 TTL에 따라 캐시를 해제하는 것은 아니므로 주의할 필요가 있다.

      서버가 다운돼도 감지하지 못한다.

    * 확장 되었을 경우
        * 한 서버가 다운되었을 때 어떤 서버가 VIP를 인계할지 미정
        * 장애극복하는 타이밍에 따라서는 두 대의 서버가 같은 IP주소를 지닐 가능성이 있음
        * 한번 정지한 서버를 복귀시키기가 곤란

* 로드밸런서
    * 로드밸런서의 동작
      로드밸런서는 서비스용 글로벌 주소를 가진 가상적인 서버로서 동작한다.
      그리하여 클라이언트로부터 전송되어 온 요청을 실제 웹 서버로 중계함으로써 마치 자신이 웹 서버인 것처럼 작동한다.
    * 로드밸런서의 기능
      로드밸런서는 여러 대의 리얼서버 중에 한 대를 선택해서 처리를 중계한다. 이때, 헬스체크가
      실패하는 서버는 선택되지 않고 반드시 헬스체크가 성공한 서버를 선택한다.
      따라서 특정 서버 한 대가 정지해 있더라도 정상적으로 가동하고 있는 서버가 있는 한 서비스가 정지하지 않는다.
    * 로드밸런서의 도입장벽
      고가의 장비라는 이미지나 제대로 운용할 수 있을까 걱정과 같은 불안감이 도입장벽이 된다.

    * NAT 구성
      클라이언트 -> L4 -> 웹서버
      수신지 주소를 변경해서 전송
      클라이언트 <- L4 <- 웹서버
      송신지 주소를 되돌려서 전송

    * DSR 구성
      위 상황에서 주소를 변경하지 않고 그대로 전송
      클라이언트 -> L4 -> 웹서버
      웹서버 -> 클라이언트

      메일 서버인 경우 NAT는 불가

* 라우터 및 로드밸런서의 다중화
    * 각 로드밸런서끼리의 체크 VRRP 프로토콜 방식

### CHAPTER 02 한 단계 높은 서버/인프라 구축

* 리버스 프록시
    * 로드 밸런서와 웹 서버 사이에 리버스 프록시라고 하는
      역할의 서버를 넣음으로써 보다 유연하게 부하를 분산할 수 있게 된다.
      리버스 프록시는 아파치제 mod_proxy 나 mod_proxy_balancer를 내장함으로써
      구축할 수 있다.

    * 리버스 프록시를 이용하면 클라이언트로부터의 요청이 웹 서버로 전달되는 도중의
      처리에 끼어들어서 다양한 전후 처리를 시행할 수가 있게 된다.
      구체적인 장점은 다음과 같다.
        1. http 요청의 내용에 따라 시스템의 동작 제어(L7 스위치와 역할 비슷)
            * 클라이언트로부터 요구된 URL /image/logo.jpg 이면 이미지용 웹서버
            * 클라이언트로부터 요구된 URL /news이면 동적 컨텐츠를 생성하는 웹서버로
            * IP주소를 이용한 제어
                - 호스트로부터의 요청을 차단할 목적에도 이용할수 있다.
                - 관리자 전용 페이지 (특정 IP주소에서만 접속 가능하도록)
            * User-Agent에 의한 제어
                - 검색엔진 로봇 (로봇에게는 사용자명을 표시할 필요가 없어서 캐쉬서버로 이동 가능)
                - URL 다시쓰기 (레거시 시스템일 이용하는 경우) 경유해서 url 변경 후 전송
        2. 시스템 전체의 메모리 사용효율 향상
            * 정적컨텐츠만을 반환하는 웹 서버에 비해 동적 컨텐츠를 반환하슷 서버는  수 배에서 수십 배의 메모리를
              소비하는 것도 드물지 않다.
            * 서버를 분할하여 리버스 프록시를 사용하여 정적텐츠용, 동적컨텐츠용으로 분할한다.
            * 이 떄에는 리버스 프록시가 웹 서버인라는 특징을 살려서 (따로 서버를 별도로 준비하는 것이 아닌) 리버스 프록스
              자체에서 반환하도록 하는 것이 일반적이다.
        3. 웹 서버가 응답하는 데이터의 버퍼링 역할
            * http의 keep-alive 특정 클라이언트가 한 번에 다수의 컨텐츠를 동일한
              웹서버에서 얻고자 할 경우 (30개의 이미지 요청)
            * 요청을 받은 프로세스/쓰레드는 그 시점으로부터 일정 시간 동안 해당
              클라이언트로의 응답을 위해서 점유된다.
            * 클라이언트-> 프록시(keep-alive on) -> 웹서버 (off)
              일반적으로 리버스 프록시 역할을 하는 웹 서버는 프로세스당 메모리소비량이
              많지 않다.
        4. 아파치 모듈을 이용한 처리의 제어
            * http 요청의 전처리/후처리로 임의의 프로그램을 실행시킬수가 있다.
              (ex ssh 암호화)

* 캐시서버 도입
    * http는 state-less 하다.
    * if-modified-since 클라이언트로부터 송신된 갱신일시 수신
    * 로컬 문서의 날짜 비교
    * 클라이언트가 저장한 문서는 갱신되지 않았다고 판단
    * 그래서 클라이언트는 네트워크로부터 이미지 데이터를 다운로드하지 않아도 된다.
    * 서버는 문서를 클라이언트로 전송하지 않아도 된다.

* MySql 레플리케이션 (기본적으로 마스터 슬레이브간의 SQL문 전송으로 인한 동기화)
    * DB서버가 정지할경우
        1. DB서버의 프로세스가 비정상 종료함
        2. 디스크가 가득 참
        3. 디스크가 고장 남
        4. 서버 전원이 고장 남
    * 레플리케이션의 원리
        * I/O 쓰레드와 SQL 쓰레드
          슬레이브에서는 레플리케이션을 위하여 각 2개의 쓰레드가 동작하고 있다.
          I/O 쓰레드는 마스터에서 얻은 데이터를 릴레이 로그라고 하는 파일에 단지 기록한다.
          SQL 쓰레드는 릴레이 로그를 읽어서 오로지 실행만 한다.
          각각의 쓰레드의 일을 하나의 쓰레드에서 수행할 경우, 처리에 시간이 소요되면 복제를 하지 못하게 된다.
        * 바이너리 로그와 릴레이 로그
          마스터에는 바이너리 로그, 슬레이브에는 릴레이 로그라고하는 파일이 생성된다.
          바이너리 로그에는 데이터를 갱신하는 처리만이 기록되고 데이터를 참조하는 쿼리는 기록되지 않는다.
          릴레이 로그는 마스터로부터 수신된 바이너리 로그를 말한다.
          자동으로 삭제되므로 수동으로 삭제할 필요가 없다.
        * 포지션 정보
          슬레이브는 레플리케이션을 완료한 위지청보를 알고 있다. 종료한 시점부터 데이터 레플리케이션을 재개할 수 있다.

    * 레플리케이션의 조건
        * 마스터는 여러 슬레이브를 가질 수 있다.
        * 슬레이브는 마스터를 단 하나만 가질 수 있다.
        * 모든 마스터, 슬레이브에는 일련의 server-id를 지정해야 한다.
        * 마스터는 바이너리 로그를 출력해야 한다.

### CHAPTER 4 성능향상, 튜닝
* 병목 규명작업의 기본적인 흐름
  1. Load Average 확인(top 등)
  2. CPU, I/O 병목 원인 조사(sar 등)
    * CPU 부하가 높은 경우
        * 사용자 프로그램의 처리가 병목인지, 시스템 프로그램이 원인인지를 확인한다. (top, sar)
        * 또한 ps로 볼 수 있는 프로세스의 상태가 CPU 사용시간 등을 보면서 원인이 되고 있는 프로세스를 찾는다.
        * 프로세스를 찾은 후 보다 상세하게 조사할 경우는, strace로 추적하거나 oprofile로 프로파일링을 해서 병목지점을 좁혀간다.
    * I/O 부하가 높은 경우
        * 특정 프로세스가 극단적으로 메모리를 소비하고 있지 않은지를 ps로 확인할 수 있다.
        * 프로그램의 오류로 메모리를 지나치게 사용하고 있는 경우에는 프로그램을 개선한다.
        * 탑재된 메모리가 부족한 경우에는 메모리를 증설한다. 메모리를 증설할 수 없을 경우는 분산을 검토한다.
        
---