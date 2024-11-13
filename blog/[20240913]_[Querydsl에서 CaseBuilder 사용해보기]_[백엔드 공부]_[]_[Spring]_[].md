## CaseBuilder()

- Querydsl에서 `CASE` 구문을 구성할 때 사용하는 빌더 클래스
- 일반적으로 조건에 따라 다른 값을 반환하거나, 특정 조건이 참인지 거짓인지에 따라 결과를 다르게 설정할 때 사용
- Querydsl에서 동적 쿼리 및 조건별 결과를 다루는 데 강력한 도구로, 복잡한 비즈니스 로직을 데이터베이스 쿼리로 표현할 때 매우 유용함

### 주요 메서드

- **`when(조건)`**: 조건을 설정합니다. 이 조건이 참일 때 반환할 값을 지정합니다.
- **`then(결과값)`**: `when` 메서드에서 설정한 조건이 참일 때 반환할 값을 설정합니다.
- **`otherwise(결과값)`**: 모든 조건이 거짓일 때 반환할 기본 값을 설정합니다.
- **`add(CaseBuilder caseBuilder)`**: 추가적인 `CaseBuilder`를 추가합니다.

### 사용 예제

```java
import com.querydsl.core.types.dsl.CaseBuilder;
import com.querydsl.core.types.dsl.BooleanExpression;
import com.querydsl.core.types.dsl.Expressions;
import com.querydsl.core.types.OrderSpecifier;

// 게시글에서 조건에 따라 점수를 계산
OrderSpecifier<Integer> scoreOrder = new CaseBuilder()
    .when(post.keyword.eq("example")).then(1)
    .when(post.language.eq("Java")).then(2)
    .otherwise(0)
    .desc(); // 점수에 따라 내림차순 정렬

```