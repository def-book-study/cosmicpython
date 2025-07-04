# Chapter 10 - 커맨드와 커맨드 핸들러

## 10.1 커맨드와 이벤트
* **이벤트는 어떤 일이 일어났다는 사실이라고 표현한다.**
* **사건이 발생했다는 정보를 모든 핸들러에게 전달하고 핸들러들은 그 이벤트에 반응하여 작동할지 안할지 규칙에 의해 정해진다.**
* **커맨드는 해석 자체가 명령이다. 무엇을 해야 한다. 라는 의미이다.**
* **이벤트가 능동적이라면 커맨드는 수동적이다.**
* **이벤트가 발생하면 그에 맞게 특정 핸들러 혹은 여러개의 핸들러가 작동 되지만 커맨드는 특정 핸들러만 작동된다.**

## 이벤트가 능동적이라면 커맨드는 수동적이다?
* **일반적인 시스템 흐름에서는 그렇게 보일수 있지만 소프트웨어 설계 관점에서는 반대라고 한다.**
* **1. 커맨드는 행동의 의지가 담긴 명령, 내가 원하는 행동을 시스템에 강제하는 능동적 요청**
* **2. 이벤트는 일이 일어 났다는 사실만 알릴뿐 어떠한 행동도 하지 않음. 그저 핸들러에게 사건 발생 했으니 필요하면 반응해라라는 수동적인 입장**
* **소프트웨어 설계 관점에서는 행동을 강제하는 주체는 능동적으로 보고 단순히 상황을 알리는 부분에 대해서는 수동적으로 해석한다고 한다.**

## 소프트웨어 설계 관점이란 무엇일까?
* **소프트웨어 설계에서는 시스템의 행위와 상태를 명확히 구분한다.**
* **커맨드는 행위를 유발시키는 의도적인 요청임 시스템은 이 요청에 의해 반드시 어떤 동작을 수행할 수 밖에 없음**
* **이벤트는 상태의 변화가 일어 났음을 알림 허나 시스템이 반드시 반응을 해야하는것은 아님**
* **처리 보장 관점**
    * **커맨드는 반드시 처리되어야 하는 요청**
        * **명확한 성공, 실패를 반환, 실패시 재시도, 롤백 등의 처리 보장을 필요로 함**
    * **이벤트는 반드시 처리를 보장하는 것은 아님
    * **이벤트에 대해 핸들러는 무시 할 수도 있고 여러 핸들러가 반응 할 수도 있음**

## 10.4 동기적으로 오류 복구하기
* **비즈니스 프로세스의 시작과 끝의 경계를 trasaction 경계와 최대한 정확히 일치시킨다.**
* **시스템에서 처리 되기전 로깅을 통해 언제 시작되었는지, 어떤 데이터를 가지고 있는지 등을 남겨둔다.**
* **이를 통해 어떤 오류가 발생할지 모르지만 로깅된 데이터를 가지고 안전하게 재시도 처리를 할 수가 있다.**


## 10.5 마치며
* **커맨드와 이벤트 분리의 트레이드 오프**
  * **결국 복잡한 비즈니스 로직을 어떻게 다룰 것인지에 대한 고민이 담긴 내용 인것 같다.**
  * **기존의 복잡한 시스템을 리팩토링을 해야하는데 어는 부분을 커맨드, 이벤트로 분리를 해야 하는지**
  * **어떤 부분은 분리를 안해도 되는것인지 이 경계부터 명확하게 설정을 해야하고**
  * **어떤부분은 커맨드로 처리 할것인지 어떤부분은 이벤트로 처리 할 것인지도 나누기가 모호하다.**
  * **과도하게 분리 했을 경우 주요 로직들이 여기저기 흩여져 있어 복잡도가 증가하고 데이터의 일관성이나 모니터링 등 작업해야 할것들이 오히려 증가할 수 있다.**
  * **그 기준을 어떻게 잡아야 할까?**
      * **일반적으로 트랜잭션 단위로 분리하는 것이 적절해 보인다.**
          * **왜냐하면 데이터의 일관성을 적절히 유지 하기 위함**
      * **트랜잭션의 범위가 클 경우는?**
          * **작업단위를 분리해서 이를 이벤트로 적절히 분리를 하면 된다.**
      * **그 작업이 트랜잭션을 나눌수 없고 순차적으로 처리가 되어야 하는 작업이라면?**
          * **작업단위를 분리해서 이를 커맨드로 적절히 분리하여 순차적으로 처리 되도록 하면 된다.**
      * **주의할점은 작업단위별로 적절한 모니터링, 로깅, 검증, 롤백 처리가 이루어지도록 작업을 추가적으로 해놓아야 한다.**

