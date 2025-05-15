# Chapter 04 - API와 서비스 계층

## 서비스
* **도메인 서비스**
    * **도메인 모델에 속하지만, 엔티티, ValueObject에 속하지 않는 로직을 부르는 이름**
    * **비즈니스의 규칙이나 어떤 연산처리를 하는 로직들**

* **서비스 계층**
    * **외부 요청으로부터 필요한 작업들을 실행을 시켜주는 처리(오케스트레이션)**


## DIP(의존성 역전 원칙)
```python
async def allocate(
        line: model.OrderLine,
        repo: repository.AbstractRepository,
        session,
) -> str:
    batches = await repo.list()
    if not is_valid_sku(line.sku, batches):
        raise InvalidSku(f'Invalid sku {line.sku}')
    batchref = model.allocate(line, batches)
    await session.commit()

    return batchref

```


* **allocate 함수는 repository라는 추상 인터페이스를 매개변수로 받는다.**
* **repo.list() 함수가 어떻게 구현되어 있는지는 모르지만 이 함수를 호출하면 조회된 데이터를 얻을수 있다.**
* **어떤 DataBase를 사용해도 상관이 없다.**