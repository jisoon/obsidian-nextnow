---
tags:
  - oop
  - Java
  - 가이드
  - 설계
created: 2024-11-05 09:13
modified: 2024-11-05 09:13
related: 
updated: 2024-11-05 09:24
---

# 객체지향 원칙: "작업을 요청하라 (Tell, Don't Ask)"
## 1. 개념 이해하기

### 기본 원칙
"객체에게 데이터를 달라고 하지 말고, 작업을 해달라고 요청하세요"

### 실생활 비유
레스토랑에서 주문하는 상황을 생각해봅시다:

**나쁜 예 (데이터 요구):**
```java
// 손님이 주방에 직접 가서:
- 냉장고를 열어봄 (데이터 조회)
- 재료가 있는지 확인 (상태 확인)
- 요리사에게 어떻게 요리할지 지시 (데이터 기반 판단)
```

**좋은 예 (작업 요청):**
```java
손님 -> 웨이터: "파스타 하나 주문할게요"
웨이터 -> 주방: "파스타 주문 들어왔습니다"
주방 -> 요리사: "파스타 준비해주세요"
```

## 2. 코드로 보는 예시

### 나쁜 예제 (데이터를 요구하는 방식)
```java
// 계좌 이체 상황
public class BankService {
    public void transfer(Account from, Account to, int amount) {
        // 계좌의 데이터를 직접 확인하고 수정
        if (from.getBalance() >= amount) {           // 데이터를 달라고 요구
            from.setBalance(from.getBalance() - amount); // 데이터를 직접 수정
            to.setBalance(to.getBalance() + amount);     // 데이터를 직접 수정
        }
    }
}
```

### 좋은 예제 (작업을 요청하는 방식)
```java
// 계좌 이체 상황
public class BankService {
    public void transfer(Account from, Account to, int amount) {
        // 계좌에게 작업을 요청
        from.withdraw(amount);    // "출금해주세요"
        to.deposit(amount);       // "입금해주세요"
    }
}
```

## 3. 실제 업무에서의 적용

### 쇼핑몰 주문 처리 예시

#### 나쁜 예제:
```java
@Service
public class OrderService {
    public void processOrder(Long cartId) {
        ShoppingCart cart = cartRepository.findById(cartId).orElseThrow();
        
        // 장바구니의 데이터를 하나씩 조회
        List<Item> items = cart.getItems();
        double total = 0;
        
        // 직접 계산하고 판단
        for (Item item : items) {
            if (item.getStock() < item.getQuantity()) {
                throw new Exception("재고 부족");
            }
            total += item.getPrice() * item.getQuantity();
        }
        
        // 직접 상태 변경
        cart.setTotal(total);
        cart.setStatus("COMPLETED");
    }
}
```

#### 좋은 예제:
```java
@Service
public class OrderService {
    public void processOrder(Long cartId) {
        ShoppingCart cart = cartRepository.findById(cartId).orElseThrow();
        
        // 장바구니에게 작업을 요청
        Order order = cart.checkout();  // "체크아웃 해주세요"
        
        orderRepository.save(order);
    }
}
```

## 4. 장점

1. **코드 유지보수가 쉬워집니다**
   - 내부 구현이 변경되어도 외부 코드는 변경할 필요가 없음
   - 관련 로직이 한 곳에 모여 있어 수정이 용이

2. **버그 발생 가능성이 줄어듭니다**
   - 데이터 접근이 제한되어 잘못된 조작 방지
   - 비즈니스 로직이 한 곳에서 관리됨

3. **코드 재사용성이 높아집니다**
   - 동일한 작업을 여러 곳에서 중복 구현할 필요가 없음
   - 일관된 방식으로 작업 처리 가능

## 5. 적용 가이드

### 기본 규칙
1. get으로 시작하는 메서드 호출이 많다면 다시 한번 생각해보기
2. 객체의 상태를 직접 변경하는 코드가 있다면 객체에게 작업을 요청하도록 변경
3. 여러 데이터를 조합해서 판단하는 코드는 객체 내부로 이동

### 실제 적용 단계
1. 데이터를 가져와서 판단/계산하는 코드를 찾습니다
2. 해당 로직을 데이터를 가진 객체의 메서드로 이동합니다
3. 외부에서는 해당 메서드를 호출하는 방식으로 변경합니다

```java
// Before
if (user.getPoint() >= item.getPrice()) {
    user.setPoint(user.getPoint() - item.getPrice());
}

// After
user.purchaseItem(item);  // "아이템을 구매해주세요"
```

## 6. 예외 상황

모든 상황에서 무조건 이 원칙을 적용할 필요는 없습니다:

1. **단순 데이터 전달 객체(DTO)**
   - 단순히 데이터 전달이 목적인 경우
   - getter/setter 사용이 일반적

2. **UI 표시 목적**
   - 화면에 데이터를 표시하기 위한 경우
   - 데이터 조회는 허용

3. **아주 단순한 로직**
   - 과도한 캡슐화가 오히려 복잡도를 높이는 경우

## 7. 정리

이 원칙의 핵심은 "객체에게 믿고 맡기기"입니다.
- 객체가 자신의 데이터를 스스로 관리하도록
- 필요한 작업은 요청만 하고
- 어떻게 처리할지는 객체가 알아서 하도록

이렇게 하면 더 유지보수하기 쉽고, 버그도 적고, 재사용하기 좋은 코드를 만들 수 있습니다.
