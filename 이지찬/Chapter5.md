# Chapter 05 - 높은 기어비와 낮은기어비의 TDD

## 높은 기어비
* **빠른 속도의 개발**
* **테스트 보다는 구현에 초점**
* **MVP 개발 단계에 적합**

## 낮은 기어비
* **신중한 개발 속도**
* **코드 품질과 안정성에 집중**
* **리팩토링, 테스트, 문서화**
* **성능 최적화, 버그 수정에 중점**


## 기어비와 TDD와의 관계
* **TDD는 낮은 기어비 개발 방식의 대표적인 방식중 하나**

## 5.2 도메인 계층 테스트를 서비스 계층으로 옮겨야 하는가?
* **도메인 계층 테스트의 문제점**
    * **구현 세부사항에 대한 테스트이므로 테스트가 깨질가능성이 높음**
    * **구현 방식이 변경 되었을 경우 테스트도 같이 수정을 해야함**

```python
def test_prefers_current_stock_batches_to_shipments():
    in_stock_batch = Batch("in-stock-batch", "RETRO-CLOCK", 100, eta=None)
    shipment_batch = Batch("shipment-batch", "RETRO-CLOCK", 100, eta=tomorrow)
    line = OrderLine("oref", "RETRO-CLOCK", 10)

    allocate(line, [in_stock_batch, shipment_batch])  # 직접 함수 호출 하여 사용

    assert in_stock_batch.available_quantity == 90
    assert shipment_batch.available_quantity == 100

def test_prefers_warehouse_batches_to_shipments():
    in_stock_batch = Batch("in-stock-batch", "RETRO-CLOCK", 100, eta=None)
    shipment_batch = Batch("shipment-batch", "RETRO-CLOCK", 100, eta=tomorrow)
    repo = FakeRepository([in_stock_batch, shipment_batch])  # Fake 사용
    session = FakeSession()  # Fake 사용

    line = OrderLine("oref", "RETRO-CLOCK", 10)
    services.allocate(line, repo, session)  # 추상화된 메서드 호출

    assert in_stock_batch.available_quantity == 90
    assert shipment_batch.available_quantity == 100
```
## 둘의 차이점
* **첫번째의 경우 함수를 직접 호출 해서 사용하고있고 두번째는 추상화된 서비스의 메서드를 호출 하고 있다.**
* **만약 내부 구현이 직접적인 DB 쿼리를 한다고 했을때 DB를 RDS에서 NoSQL로 변경 했을경우 매개변수로 넘기는 객체의 타입이나 구조 같은것들이 변경되었을 가능성이 높을수 있다.**
* **추상화가 잘 되어 있을 경우 이런 변경에도 적절히 대응 할 수가 있다는 차이가 있다.**
* **왜냐하면 2번째 테스트 코드에서는 Fake를 사용하여 추상화된 객체를 사용하고 있기 때문에 테스트 코드를 따로 변경 하지 않아도 통과될 가능성이 높다.**
* **여기서 어차피 메서드 변경에 대해서 Fake 내부 구현도 변경해야 할텐데 똑같은거 아니냐 라는 생각이 들었는데 만약 저 메서드를 여러곳에서 사용하고 있었을경우 Fake 구현만 변경하면 동일한 메서드를 사용하고 있는 테스트 코드에 대해서 변경할 부분이 적어진다.**

## 5.3 어떤 종류의 테스트를 작성할지 결정하는 방법
* **결국 도메인 테스트와 API 테스트 두가지 모두 장단점이 있지만 적절한 트레이드 오프가 이루어져야 한다.**
* **그러면 자주 변경되는 코드와 잘 변경이 안되는 코드로 구분해서 나누어야 하는데 결국 "도메인의 핵심 규칙"과 직접적으로 밀접한 코드들은 도메인 테스트로 커버를 해야 할것 같고 그 외에 코드들은 API 테스트 형태로 테스트를 하는것이 가장 적절 해보인다.**

## 5.6 E2E 테스트에 도달할 때까지 계속 개선하기, 5.7 마치며
* **직접적인 쿼리, ORM 호출하여 테스트 하는 방식에서 단위테스트 > 통합 테스트 > E2E 테스트 형태로 개선되어가며 테스트 피라미드 형태로 테스트가 유지 되게끔 하는것이 목표**
* **이때 여러개의 단위 테스트 적절한 수의 통합테스트 적은수의 E2E 테스트 형태로 유지**
* **결국 단단한 서비스를 만들기 위해서는 피라미트 테스트 형태로 내부 구현체들에 대해서 적절한 추상화를 통해 테스트 커버리지를 높이고 안정적으로 유지하는데 목표를 잡아야 한다.**


### 서비스 계층을 도메인 객체가 아니라 원시 타입을 바탕으로 기술하라.
* **예를들어 매개변수로 도메인 객체 타입을 받는다 라고 가정하면 결국 도메인 객체에 의존한다 라고 볼수 있기에 원시타입인 str, int 같은 타입을 사용한다면 유연 하게 사용할수가 있다.**

### 타입힌트
* **Protocol**
    * **클래스가 다르지만 동일 메서드를 가지고 있다면 동일한 타입을 사용할 수 있다.**
* **TypeVar**
    * **특정 변수에 여러가지 복합적인 타입의 데이터가 있을경우 주로 사용**

```python
# 모든 타입 허용
T = TypeVar('T')
def identity(x: T) -> T:
    return x

# 숫자 타입만 허용
Number = TypeVar('Number', int, float)
def add(x: Number, y: Number) -> Number:
    return x + y

identity("hello")  # 가능: T는 str로 결정됨
identity(123)      # 가능: T는 int로 결정됨

add(1, 2)          # 가능: Number는 int로 결정됨
add(1.5, 2.5)      # 가능: Number는 float로 결정됨
add(1, 2.5)        # 오류: Number가 int와 float 둘 다로 결정될 수 없음
add("hello", "world")  # 오류: Number는 str이 될 수 없음
```

* **https://meticulousdev.tistory.com/entry/PYTHON-TypeVar-%EA%B7%B8%EB%A6%AC%EA%B3%A0-Generic**

* **TypeIs 라는게 있다.**
    * **python 3.13 버전에서 추가 되었다고 한다.**
    * **이전 버전은 typing_extensions 패키지를 통해서 사용이 가능 하다고 한다.**