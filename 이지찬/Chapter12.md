# Chapter 12 - 명령-질의 책임 분리(CQRS)

### 읽기 일관성을 정말로 달성할 수 있을까?
* **완전 실시간으로 바로바로 처리 하는 서비스가 아닌 이상 해결 할 수 없는 문제인것 같음**
    * **완전 실시간 서비스라 하더라도 네트워크 지연 시간 등 여러문제로 반영하기 힘듬**
* **읽기 측면에서 성능과 일관성을 바꿔도 좋다. 라는 말이 데이터가 업데이트 될때마다 바로바로 반영해서 성능이 떨어지는 점을 어쩔수 없이 안고 가거나 캐싱처리를 통해서 일관성을 떨어지지만 데이터 서빙속도를 빠르게 한다 이 차이점을 이야기 하는것 같다.**

## 12.3 Post/리디렉션/Get과 CQS
* **명령-질의 분리(CQS) 기법**
    * **만약 사용자가 Post 요청후에 그 페이지에서 바로 결과를 보여줬다고 가정 했을때 만약 새로고침을 눌렀을때 Post에 중복 요청을 보낼수 있다.**
    * **이런 문제를 방지하기 위해서 Post 요청이 성공했을 경우 Get(상세조회) 요청을 리다이렉션 처리를 할 경우 새로고침을 해도 Get 요청이 한번 더 발생하게 되어 문제를 해결 할 수 있다.**

## 12.7 읽기 연산에 최적화되지 않은 도메인 모델
* **도메인 모델은 기본적으로 비즈니스 로직과 규칙을 표현하는것에 초점이 맞춰져 있다. 그래서 읽기 연산과는 전혀 관련이 없다.**
* **그래서 보통 쓰기 연산과 관련이 깊게 되어 있다.**
    * **생성, 수정, 삭제 처리 등**

### 읽기 연산에 최적화된 도메인 모델이라는게 있을까?
* **있을수가 없다.**


## 12.10 이제는 상어를 완전히 뛰어 넘을 때이다
* **제목 자체가 무슨 말인지 잘 이해가 안간다.**
* **상어가 읽기, 쓰기 방식이 결합된것을 말하는 거라면 CQRS방식을 적용하면 이를 뛰어 넘는다를 말하고자 하는것 같다.**
* **아무튼 읽기와 쓰기를 분리하면 쓰기 연산을 하더라도 읽는 것에는 영향이 없기 때문에 읽기 전용의 서비스는 수평적으로 확장이 가능하다**
    * **단일 모델에서는 쓰기 연산중 락(교착상태)가 발생할 경우 읽기 성능에도 영향을 준다**
    * **읽기 연산 처리용 모델과 쓰기 연산 처리용 모델을 따로 분리**
    * **쓰기 연산이 락이 걸리든 에러가 발생하든 읽기 연산에는 영향을 끼치지 않는다.**
    * **쓰기 연산을 통해 업데이트된 데이터는 주기적으로 읽기 모델에 업데이트 시켜 준다.**

### 여기서 읽기 모델에 대해 SQL 질의를 직접 던지는 것과 Redis같은 인메모리 DB 사용시 성능차이가 얼마나 날까? 라는 궁금증이 생긴다.
* **작은 단위의 데이터에 대한 조회가 많은 상황일 경우 Redis가 유리하며**
    * **물론 메모리가 빵빵하다면 대용량 데이터도 거뜬하게 조회 할것이다.**
* **복잡한 조회 조건이나 대용량 데이터 조회시 SQL 질의를 직접하는것이 더 유리 하다**
    