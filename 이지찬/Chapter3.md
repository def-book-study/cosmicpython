# Chapter 03 - 결합과 추상화

## 좋은 추상화를 만드는 요소
* **단일 책임 원칙**: 각각 목적에 맞는 책임만을 가지도록 구현
* **낮은 결합도**: 서비스내에서 직접 객체 생성이 아닌 외부에서 주입해주어 결합도를 낮출수 있음
* **일관된 추상화**: 서비스내에 저수준의 구현이 섞여 있으면 안된다.
....

## 추상화로부터 얻으려는 것
* **코드 재사용**
* **유지보수**
* **확장성**
* **테스트 용이**
* **복잡성 관리**
* **팀원과의 협업의 용이**
* **재사용성**
....

## 무엇을 원하는가, 원하는바를 어떻게 달성할지를 분리한다.
* **무엇**
    * 의도: 해당 기능이 어떠한 목적을 가지고 있는지 정의
* **어떻게**
    * 목적을 달성하기 위한 내부 구현

```python
from abc import ABC, abstractmethod

from bcd_main_server.apps.market.models import MarketProduct


class MarketProductEntity:
    def __init__(self, id: int, market_product_code: str):
        self.id = id
        self.market_product_code = market_product_code
    
    def to_dict(self) -> dict:
        return {
            "id": self.id,
            "market_product_code": self.market_product_code
        }

# 무엇에 대한 정의
class MarketProductInterface(ABC):
    @abstractmethod
    def get_market_product(self, id: int) -> MarketProductEntity | None:
        pass


class MarketProductRepositoryInterface(ABC):
    @abstractmethod
    def find_by_id(self, id: int) -> MarketProductEntity | None:
        pass

# 어떻게에 대한 정의
class MarketProductRepository(MarketProductRepositoryInterface):
    def find_by_id(self, id: int) -> MarketProductEntity | None:
        try:
            model_instance = MarketProduct.objects.get(id=id)
        except MarketProduct.DoesNotExist:
            return None

        return MarketProductEntity(
            id=model_instance.id,
            market_product_code=model_instance.market_product_code,
        )


class MarketProductService(MarketProductInterface):
    def __init__(self, repository: MarketProductRepositoryInterface):
        self.repository = repository

    def get_market_product(self, id: int) -> MarketProductEntity | None:
        return self.repository.find_by_id(id=id)


repository = MarketProductRepository()
service = MarketProductService(repository)
market_product = service.get_market_product(id=10267453)
print(market_product.to_dict())

```

## mock.patch 를 쓰지 않는 이유
* **패치를 사용 할 경우 각 데코레이터에 하드코딩으로 모듈 경로를 주입하기 떄문에 관리 포인트 발생**
    * **테스트 케이스가 늘어나면 늘어 날수록 변경에 대한 리소스가 늘어남**
* **Mock 객체의 경우도 스펙의 변경이나 코드 변경에 의해 테스트가 실패할 가능성이 높음**

## Fake를 이용한 edge-to-edge 테스트
* **구현체가 변경 되어도 인터페이스의 변경이 없을 경우 테스트는 깨지지 않는다.**
* **Fake 객체는 실제 구현체와 유사하게 작동되도록 작성 되기 때문에 실제 환경과 유사한 테스트가 가능하다.**


## 요점
* **다양한 책임과 목적이 다른 코드는 분리**
* **적절한 추상화로 결합도를 낮춰 변경에 유연한 코드를 작성**
* **테스트는 Mock보다 Fake를 활용해 현실적이고 유지보수하기 쉬운 테스트 코드를 작성**



참고
## 행동 기반 테스트
* **Mock 객체를 통해 함수의 매개변수, 예상 결과에 대한 검증**

## 상태 기반 테스트
* **테스트 더블**
    * **외부 의존성을 대체하는 객체들을 총칭**
    * **사용 목적**
        * **외부 의존성 제거: DB, API ...**
    * **종류**
        * **더미**
            * **깡통: 아무것도 하지 않고 단순 매개변수 채우기 위한 용도**
        * **스텁**
        * **스파이**
            * **호출된 메서드, 매개변수, 호출 회수등을 기록**
        * **페이크**
            * **실제구현체와 유사하게 구현**
            * **메모리 내에서 동작**
