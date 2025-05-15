# Chapter 06 - 작업 단위 패턴

## 작업단위(Unit of Work)
* **원자적 연산**
    * **트랜잭션 관리를 객체지향적으로 추상화한 개념**

## 콘텍스트 관리자
* **진입점(__enter__)**
* **종료지점(__exit__)**
```
class UnitOfWork:
    def __enter__(self):
        # 세션 시작 및 리포지토리 초기화
        self.session = self.session_factory()
        self.products = ProductRepository(self.session)
        self.orders = OrderRepository(self.session)
        return self
        
    def __exit__(self, exc_type, exc_val, exc_tb):
        # 예외 발생 시 롤백, 정상 종료 시 커밋
        self.session.close()
        if is_success:
            self.rollback()
        else:
            self.commit()
```

## 6.3.2 자신이 만든것이 아니면 모킹하지 말라
* **내가 컨트롤 할 수 없는 코드에 대해서는 모킹하면 안된다.**
* **외부에 의존하는 코드의 경우 변경사항을 모킹에서 적절한 대응이 어렵고 테스트 코드가 깨질 가능성이 높다.**
* **그래 추상화된 코드를 사용해서 의존성을 낮춰야 한다.**
* **포트 어댑터 패턴을 적용 시켜보면 좋을것으로 보인다.**


## 트랜잭션과 같은 더 불확실한 데이터베이스 동작을 실제 데이터베이스에 대해 테스트할 가치가 있다
* **트랜잭션과 같은 처리는 모킹으로는 테스트 하기가 어렵기 때문에 실제 데이터 베이스를 사용해서 테스트하는것이 나을수 있다.**
* **데이터베이스 마다 처리되는 방식이 다를수 있고 이를 모킹을 통해 구현하기에는 복잡도가 높고 데이터 베이스의 변경이 있을경우 변경사항에 대해 반영하기가 까다롭다.**