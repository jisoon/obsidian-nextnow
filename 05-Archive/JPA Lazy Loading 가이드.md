---
tags:
  - JPA
  - 가이드
created: 2024-10-24 00:00
modified: 2024-10-24 00:00
related: 
updated: 2024-10-24 13:49
---
## 0. Index

- [1. Lazy Loading이란?](#1.%20Lazy%20Loading%EC%9D%B4%EB%9E%80?)
	- [1.1 Lazy Loading 예시](#1.1%20Lazy%20Loading%20%EC%98%88%EC%8B%9C)
- [2. LazyInitializationException 문제](#2.%20LazyInitializationException%20%EB%AC%B8%EC%A0%9C)
	- [2.1 발생 원인](#2.1%20%EB%B0%9C%EC%83%9D%20%EC%9B%90%EC%9D%B8)
	- [2.2 문제 발생 상황](#2.2%20%EB%AC%B8%EC%A0%9C%20%EB%B0%9C%EC%83%9D%20%EC%83%81%ED%99%A9)
- [3. 해결 방법들](#3.%20%ED%95%B4%EA%B2%B0%20%EB%B0%A9%EB%B2%95%EB%93%A4)
	- [3.1 Fetch Join (권장)](#3.1%20Fetch%20Join%20(%EA%B6%8C%EC%9E%A5))
	- [3.2 EntityGraph (권장)](#3.2%20EntityGraph%20(%EA%B6%8C%EC%9E%A5))
	- [3.3 DTO 직접 조회 (권장)](#3.3%20DTO%20%EC%A7%81%EC%A0%91%20%EC%A1%B0%ED%9A%8C%20(%EA%B6%8C%EC%9E%A5))
	- [3.4 @Transactional 사용 (비권장)](#3.4%20@Transactional%20%EC%82%AC%EC%9A%A9%20(%EB%B9%84%EA%B6%8C%EC%9E%A5))
- [4. 각 해결방법의 장단점](#4.%20%EA%B0%81%20%ED%95%B4%EA%B2%B0%EB%B0%A9%EB%B2%95%EC%9D%98%20%EC%9E%A5%EB%8B%A8%EC%A0%90)
	- [4.1 Fetch Join](#4.1%20Fetch%20Join)
	- [4.2 EntityGraph](#4.2%20EntityGraph)
	- [4.3 DTO 직접 조회](#4.3%20DTO%20%EC%A7%81%EC%A0%91%20%EC%A1%B0%ED%9A%8C)
	- [4.4 @Transactional (비권장)](#4.4%20@Transactional%20(%EB%B9%84%EA%B6%8C%EC%9E%A5))
- [5. 실전 사용 가이드](#5.%20%EC%8B%A4%EC%A0%84%20%EC%82%AC%EC%9A%A9%20%EA%B0%80%EC%9D%B4%EB%93%9C)
	- [5.1 상황별 권장 해결책](#5.1%20%EC%83%81%ED%99%A9%EB%B3%84%20%EA%B6%8C%EC%9E%A5%20%ED%95%B4%EA%B2%B0%EC%B1%85)
	- [5.2 성능 최적화 팁](#5.2%20%EC%84%B1%EB%8A%A5%20%EC%B5%9C%EC%A0%81%ED%99%94%20%ED%8C%81)
- [6. 정리](#6.%20%EC%A0%95%EB%A6%AC)
	- [6.1 기본 원칙](#6.1%20%EA%B8%B0%EB%B3%B8%20%EC%9B%90%EC%B9%99)
	- [6.2 해결방법 선택 기준](#6.2%20%ED%95%B4%EA%B2%B0%EB%B0%A9%EB%B2%95%20%EC%84%A0%ED%83%9D%20%EA%B8%B0%EC%A4%80)
	- [6.3 추가 고려사항](#6.3%20%EC%B6%94%EA%B0%80%20%EA%B3%A0%EB%A0%A4%EC%82%AC%ED%95%AD)


## 1. Lazy Loading이란?
- 연관된 엔티티를 실제로 사용할 때까지 데이터베이스 조회를 미루는 기능
- 불필요한 데이터베이스 조회를 줄여 성능을 향상시키는 것이 목적
- JPA의 기본적인 최적화 전략

### 1.1 Lazy Loading 예시
```java
@Entity
public class Order {
    @Id
    private Long id;
    
    @OneToMany(mappedBy = "order", fetch = FetchType.LAZY) // 기본값
    private List<OrderItem> items;
}

// 이때는 Order만 조회
Order order = orderRepository.findById(1L);

// 실제 items를 사용할 때 쿼리 실행
order.getItems().size(); 
```

## 2. LazyInitializationException 문제

### 2.1 발생 원인
1. 영속성 컨텍스트가 종료된 후에
2. Lazy Loading으로 설정된 엔티티에 접근할 때 발생

### 2.2 문제 발생 상황
```java
@Service
public class OrderService {
    @Transactional
    public Order getOrder(Long id) {
        return orderRepository.findById(id);
    }
}

@RestController
public class OrderController {
    public OrderDTO getOrder(Long id) {
        Order order = orderService.getOrder(id);
        // 예외 발생! 트랜잭션이 종료된 상태
        order.getItems().size(); 
    }
}
```

## 3. 해결 방법들

### 3.1 Fetch Join (권장)
- 가장 명시적이고 성능이 좋은 방법
```java
@Repository
public interface OrderRepository extends JpaRepository<Order, Long> {
    @Query("SELECT o FROM Order o JOIN FETCH o.items WHERE o.id = :id")
    Optional<Order> findOrderWithItems(@Param("id") Long id);
}
```

### 3.2 EntityGraph (권장)
- 재사용 가능하고 유연한 방법
```java
@Repository
public interface OrderRepository extends JpaRepository<Order, Long> {
    // 방법 1: 어노테이션으로 직접 정의
    @EntityGraph(attributePaths = {"items"})
    Optional<Order> findById(Long id);

    // 방법 2: 엔티티에 정의된 Named EntityGraph 사용
    @EntityGraph("Order.withItems")
    List<Order> findByCustomerId(Long customerId);
}

@Entity
@NamedEntityGraph(
    name = "Order.withItems",
    attributeNodes = @NamedAttributeNode("items")
)
public class Order {
    // ...
}
```

### 3.3 DTO 직접 조회 (권장)
- 필요한 데이터만 정확히 조회
```java
@Repository
public interface OrderRepository extends JpaRepository<Order, Long> {
    @Query("SELECT new com.example.dto.OrderDTO(o.id, o.number, " +
           "i.id, i.quantity) " +
           "FROM Order o JOIN o.items i WHERE o.id = :id")
    List<OrderDTO> findOrderDTO(@Param("id") Long id);
}
```

### 3.4 @Transactional 사용 (비권장)
- 간단하지만 여러 문제를 초래할 수 있음
```java
@Service
public class OrderService {
    @Transactional  // 이 방법은 권장되지 않음
    public Order getOrder(Long id) {
        Order order = orderRepository.findById(id);
        order.getItems().size(); // 동작하지만 비효율적
        return order;
    }
}
```

## 4. 각 해결방법의 장단점

### 4.1 Fetch Join
장점:
- 한 번의 쿼리로 필요한 모든 데이터 조회
- N+1 문제 해결
- 성능이 가장 우수

단점:
- 쿼리가 복잡해질 수 있음
- 페이징 처리시 메모리 이슈 가능성

### 4.2 EntityGraph
장점:
- 재사용 가능
- 동적으로 구성 가능
- 가독성이 좋음

단점:
- Fetch Join보다 약간의 성능 오버헤드
- 복잡한 조건을 표현하기 어려움

### 4.3 DTO 직접 조회
장점:
- 필요한 데이터만 정확히 선택 가능
- 성능 최적화가 용이
- 화면에 필요한 데이터만 전송

단점:
- DTO 클래스 추가 작성 필요
- 비즈니스 로직에서 엔티티 사용 불가

### 4.4 @Transactional (비권장)
장점:
- 구현이 단순
- 빠른 개발 가능

단점:
- 불필요한 데이터 로딩
- 데이터베이스 커넥션 보유 시간 증가
- 성능 이슈 발생 가능
- 비즈니스 로직 누수

## 5. 실전 사용 가이드

### 5.1 상황별 권장 해결책

1. **단순 1:1, 1:N 관계 조회**
   - EntityGraph 사용
   ```java
   @EntityGraph(attributePaths = {"items"})
   Optional<Order> findById(Long id);
   ```

2. **복잡한 조건이나 조인이 필요한 경우**
   - Fetch Join 사용
   ```java
   @Query("SELECT o FROM Order o JOIN FETCH o.items i " +
          "WHERE o.status = :status AND i.quantity > :quantity")
   List<Order> findOrdersWithItemsByCondition(
       @Param("status") OrderStatus status,
       @Param("quantity") int quantity);
   ```

3. **API 응답용 데이터 조회**
   - DTO 직접 조회
   ```java
   @Query("SELECT new com.example.dto.OrderSummaryDTO(" +
          "o.id, o.number, COUNT(i), SUM(i.quantity)) " +
          "FROM Order o LEFT JOIN o.items i GROUP BY o.id")
   List<OrderSummaryDTO> findOrderSummaries();
   ```

### 5.2 성능 최적화 팁

1. **N+1 문제 방지**
```java
// 나쁜 예
List<Order> orders = orderRepository.findAll();
orders.forEach(order -> order.getItems().size()); // N+1 문제 발생

// 좋은 예
List<Order> orders = orderRepository.findAllWithItems(); // Fetch Join 사용
```

2. **필요한 데이터만 조회**
```java
// 나쁜 예
@EntityGraph(attributePaths = {"items", "customer", "delivery"})
Optional<Order> findById(Long id); // 불필요한 데이터까지 모두 조회

// 좋은 예
@EntityGraph(attributePaths = {"items"})
Optional<Order> findById(Long id); // 필요한 연관관계만 조회
```

3. **페이징 처리시 주의사항**
```java
// 주의: 1:N 관계에서 Fetch Join + 페이징
@Query("SELECT o FROM Order o JOIN FETCH o.items")
Page<Order> findAll(Pageable pageable); // 메모리 문제 발생 가능

// 권장: ToOne 관계만 Fetch Join + 페이징
@Query("SELECT o FROM Order o JOIN FETCH o.customer")
Page<Order> findAllWithCustomer(Pageable pageable);
```

## 6. 정리

### 6.1 기본 원칙
1. 무조건 즉시 로딩(EAGER)은 피한다
2. 성능 최적화가 필요한 곳에 적절한 해결책을 적용
3. 비즈니스 로직에 맞는 조회 방식을 선택

### 6.2 해결방법 선택 기준
- Fetch Join: 복잡한 조회 조건, 높은 성능이 필요할 때
- EntityGraph: 간단한 연관관계 조회, 재사용성이 필요할 때
- DTO 조회: API 응답, 화면에 특화된 데이터가 필요할 때
- @Transactional: 프로토타입 또는 매우 간단한 기능 구현시

### 6.3 추가 고려사항
1. 항상 필요한 연관관계만 로딩
2. 페이징이 필요한 경우 주의
3. 성능 테스트를 통한 검증
4. 비즈니스 요구사항에 맞는 방식 선택
