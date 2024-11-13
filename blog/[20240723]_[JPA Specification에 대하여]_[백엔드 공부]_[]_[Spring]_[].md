## JPA Specification

- `extends JpaSpecificationExecutor<Object>`
- JPA에서 제공하는 기능 중 하나
- 동적으로 쿼리 생성하는 방법 제공
- 자바 어플리케이션에서 관계형 데이터베이스와 상호 작용을 도와주는 API

### 구성

- `Root<T> root` 쿼리의 루트 엔티티, 쿼리하고자 하는 엔티티의 속성에 접근하기 위해 사용
- `CriteriaQuery<?> query` 구성된 쿼리 자체, 쿼리의 구조와 반환 타입을 정의하며 최종적으로 생성되는 쿼리의 구조를 결정, `toPredicate()` 를 통해 특정 조건을 생성하는 것이 주 목적임
- `CriteriaBuilder cb` 쿼리를 구성하는 데 사용되는 다양한 조건과 표현식 생성, 쿼리의 조건과 함수를 생성하는데 사용됨

    ```java
    // 매개변수 root.get("속성"), expression
    cb.equal()
    cb.like()
    cb.greaterThan()
    cb.between()
    .
    .
    .
    ```


### 장점

- 동적 쿼리를 도메인 객체 그대로 사용해 편리함
- 데이터 저장소를 변경해도 코드 변경이 없어도 되도록 추상화되어 있음
- 스프링 데이터 JPA JpaRepository 인터페이스에 포함되어 있음

### 단점

- 내부 조인만 가능, 외부 조인 불가능
- 중첩 제약 조건 불가능
- 매칭 조건이 매우 단순한 경우만 가능