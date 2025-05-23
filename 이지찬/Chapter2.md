# Chapter 02 - 저장소 패턴

## 1. 저장소 패턴이 필요했던 이유

### 프로그램이 너무 복잡해지는 문제
* **섞여 있는 코드**: 비즈니스 로직과 DB 저장 로직이 뒤섞여 있어 복잡성 증가
* **바꾸기 어려운 구조**: DB 스펙 변경이나 DataBase 변경이 필요할경우 고쳐야하는 코드양이 많아진다. 전체 코드를 수정 해야 할 수도 있따.
* **테스트하기 어려움**: 실제 DB 로직이 강하게 결합되어 있다보니 테스트 하기가 까다롭다. 잘못된 테스트로 인해 이슈가 발생할 가능성이 높아진다.

### 문제 해결을 위한 아이디어의 등장
* **새로운 생각**: 2002년에 마틴 파울러라는 사람이 "우리 프로그램에 '창고 관리자'를 두면 어떨까?"라는 아이디어를 제안함
* **주요내용**: 사용자는 뒤에서 동작하는 내용이 어떻게 되어있든 알 필요가 없고 그저 저장소에 요청만 하면 된다. 

### 예시
> "도서관에 가서 책을 빌리고 싶다라고 가정 했을때 
> 모든 책의 위치를 외우고 직접 찾아다닐 필요 없이 그저 사서에게 'IT관련 책은 어디에 있나요?'라고 말하면 사서는 어디에 있다고 알려준다. 
> 사서가 어디서 어떻게 책을 찾는지는 알 필요가 없다. 프로그램에서 저장소는 바로 이 사서의 역할을 합니다."

### 왜 이런 방식을 사용할까?
* **간편한 테스트**: 실제 DB와 직접 연결이 되어 있지 않고 가짜 저장소를 만들어서 메모리상에 임시 데이터를 통해 테스트가 가능하다.
* **유연한 변경**: DB와 결합성이 낮기 때문에 변경이 용이함

## 2. 저장소 패턴이란 무엇인가?

### 예시
* **쇼핑몰**: 쇼핑몰에 가서 "260 사이즈 *** 운동화 있나요"라고 물으면, 직원은 해당 물품을 창고에서 가져와서 보여준다. 창고가 어떻게 구성되어 있는지 몰라도 상관이 없다.
* **음악 스트리밍 앱**: 노래를 검색하면 노래파일이 어디에 저장 되어있는지 몰라도 노래를 재생 시킬수 있다.

### 저장소 패턴의 기본 구조
* **사용하기 쉬운 창구**: "이 상품 찾아줘", "이 고객 정보 저장해줘" 같은 간단한 요청만 하면 된다.
* **숨겨진 복잡한 작업**: 데이터를 어떻게 변환하고, 어디에 저장하고, 어떻게 가져오는지는 저장소 내부에서 알아서 처리
* **실생활 비유**: 음식점에서 특정 메뉴를 주문 했을때 내가 요리하는 방법을 알지 않아도 주방장들이 요리를 해준다.

### 저장소는 컬렉션처럼 동작
* **컬렉션이란?**: 물건을 모아두는 상자나 서랍같은 것
* **사용법**: 물건을 넣고, 꺼내고, 찾고, 바꾸는 단순한 작업들
* **저장소도 비슷해요**: 데이터를 넣고, 꺼내고, 찾고, 바꾸는 단순한 방법을 제공합니다.

## 3. 저장소 패턴과 다른 방식의 차이

### 저장소 vs 직접 데이터베이스 접근
* **직접 접근**: 도서관에서 책 위치를 다 외우고 직접 찾아다니는 것과 같다.
* **저장소 사용**: 사서에게 책을 요청하고 받아오는 것과 같다.
* **차이점**: 저장소를 사용 하면 어느 도서관을 가도 사서에게 책을 찾아달라고 요청하면 책을 찾을수 있다. 직접 접근이라면 나는 모든 도서관의 책의 위치를 알고 있어야 한다.
