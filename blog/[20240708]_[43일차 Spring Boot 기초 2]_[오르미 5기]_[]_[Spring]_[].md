# @SpringBootApplication

Sprin`SpringBootApplication` 어노테이션 

= Spring Boot 애플리케이션의 시작점 표시 

= `@Configuration + @EnableAutoConfiguration + @ ComponentScan`

---

→   `@Configuration` - 해당 클래스를 Spring의 설정 클래스로 지정

`@EnableAutoConfiguration` - Spring Boot의 자동 구성 기능을 활성화

`@ComponentScan` - 컴포넌트 스캔을 수행하여 `@Component`, `@Controller`, `@Service`, `@Repository` 등의 어노테이션이 붙은 클래스를 자동으로 빈으로 등록

<aside>
💡 작동 순서

`@SpringBootApplication` → `@Configuration` → `@EnableAutoConfiguration` → `@ComponentScan` → Spring Container 

</aside>

## **@ComponentScan** 주의점

@SpringBootApplication 어노테이션이 붙어있는 클래스의 위치 기준으로 **지정된 패키지와 그 하위 패키지**에서 `@Component`, `@Controller`, `@Service`, `@Repository` 등의 어노테이션이 붙은 클래스를 자동으로 탐색하여 빈으로 등록함❗

→ 즉, 다른 패키지에 있는 컴포넌트를 스캔하기 위해서는 

`@SpringBootApplication(scanBasePackages = “탐색할 패키지”)` 사용

```java
@SpringBootApplication
@ComponentScan(basePackages = "com.example.main")

com.example
├── main  ◄─── @ComponentScan
│   └── Application.java
│
├── service  ✗ (스캔되지 않음)
│   └── UserService.java
│
└── util  ✗ (스캔되지 않음)
    └── Helper.java
    
----------------------------------------------------

@SpringBootApplication(scanBasePackages = "com.example.main")

com.example  ◄─── @ComponentScan
├── main
│   └── Application.java
│
├── service  ✓ (스캔됨)
│   └── UserService.java
│
└── util  ✓ (스캔됨)
    └── Helper.java
```

# Spring DI (의존성 주입) 종류

## 1. Setter 주입 방식

- setter 메서드를 만들고 `@Autowired` 어노테이션을 붙여 의존성을 주입받음
    
    ```java
    @Service
    public class MyService {
        private MyDependency myDependency;
    
        @Autowired
        public void setMyDependency(MyDependency myDependency) {
            this.myDependency = myDependency;
        }
    
        // ...
    }
    ```
    
- 장점
    - 의존성이 선택적일 때 사용 가능
    - 의존성을 나중에 변경할 수 있는 유연성 제공
- 단점
    - 객체 생성 이후에 의존성 주입이 이루어지기 때문에 의존성이 주입되지 않은 상태로 객체가 사용될 수도 있음
    - 메서드가 public이라 캡슐화가 약화됨

## 2. field 주입 방식

- 클래스의 필드에  `@Autowired` 어노테이션을 붙여 의존성을 주입받음
    
    ```java
    @Service
    public class MyService {
        @Autowired
        private MyDependency myDependency;
    
        // ...
    }
    ```
    
- 장점
    - 간결한 코드
    - 의존성 주입을 위한 별도의 코드 불필요
- 단점
    - 의존성이 명시적으로 표현되지 않아 코드의 가독성이 떨어짐
    - 의존성 변경이 어려워 유연성이 낮음

## 3. 생성자 주입 방식 (가장 선호됨!)

- 생성자를 만들고 생성자의 매개변수를 통해 의존성 주입받음
    
    ```java
    @Service
    public class MyService {
        private final MyDependency myDependency;
    
        @Autowired
        public MyService(MyDependency myDependency) {
            this.myDependency = myDependency;
        }
    
        // ...
    }
    ```
    
- 장점
    - **의존성이 명시적으로 표현**되기 때문에 코드의 가독성이 좋음
    - 의존성 필수 → 객체 생성 시점에 의존성이 주입되지 않으면 컴파일 에러 발생
    - **불변성 유지 가능** (필드에 `final` 로 선언되기 떄문)
    - **테스트가 용이**함, 단위 테스트 작성 시 의존성을 목(Mock) 객체로 대체 가능
        
        ```java
        public class UserServiceTest {
            @Test
            public void testGetUser() {
                // 목 객체 생성
                UserRepository mockUserRepository = mock(UserRepository.class);
                when(mockUserRepository.findById(1L)).thenReturn(new User());
                
                // 의존성 주입
                UserService userService = new UserService(mockUserRepository);
                
                // 테스트 수행
                User user = userService.getUser(1L);
                assertNotNull(user);
            }
        }
        ```
        
- 단점
    - 의존성이 많아지면 생성자의 매개변수가 많아져 코드가 복잡해짐
    

# DispatcherServlet

- Spring의 사용자 요청 처리 흐름
    
    ```java
       +-----------------------+
       |      HTTP 요청        |
       +-----------------------+
                 |
                 ▼
       +-----------------------+
       |   DispatcherServlet   |
       +-----------------------+
                 |
                 |----- 1. 요청 매핑
                 |
                 ▼
       +-----------------------+
       |    HandlerMapping     |
       +-----------------------+
                 |
                 |----- 2. 핸들러 선택
                 |
                 ▼
       +-----------------------+
       |  Controller(Handler)  |
       +-----------------------+
                 |
                 |----- 3. 요청 처리 (+ 비즈니스 로직 수행)
                 |
                 ▼
       +-----------------------+
       |        Service        |
       +-----------------------+
                 |
                 |----- 데이터 조회 및 저장
                 |
                 ▼
       +-----------------------+
       |      Repository       |
       +-----------------------+
                 |
                 |----- 데이터 조회 및 저장
                 |
                 ▼
       +-----------------------+
       |    ModelAndView       |
       +-----------------------+
                 |
                 |----- 4. 뷰 리졸버 선택
                 |
                 ▼
       +-----------------------+
       |     ViewResolver      |
       +-----------------------+
                 |
                 |----- 5. 뷰 렌더링
                 |
                 ▼
       +-----------------------+
       |        View           |
       +-----------------------+
                 |
                 ▼
       +-----------------------+
       |      HTTP 응답        |
       +-----------------------+
    ```
    
    1. 요청 매핑
        - `DispatcherServlet`이 들어오는 HTTP 요청을 받음
        - 요청 URL, HTTP 메서드 등을 기반으로 적절한 핸들러를 찾기 위해 `HandlerMapping`에게 요청 전달
    2. 핸들러 선택
        - `HandlerMapping`은 요청 정보를 기반으로 적절한 핸들러(컨트롤러)를 선택
        - 선택된 핸들러 정보가 `HandlerExecutionChain` 객체로 반환됨
    3. 요청 처리
        - 선택된 핸들러(컨트롤러)는 요청을 처리하고, 서비스 계층과 상호작용하여 필요한 비즈니스 로직을 수행
        - 서비스 계층은 리포지토리를 통해 데이터베이스와 상호작용하여 데이터 조회 및 저장
        - 컨트롤러는 처리 결과를 모델에 담아 `ModelAndView` 객체로 반환
        - `ModelAndView` = 뷰 이름 + 모델 데이터
    4. 뷰 리졸버 선택
        - `DispatcherServlet`은 `ModelAndView`에서 뷰 이름을 추출하고, 적절한 `ViewResolver`를 선택
        - `ViewResolver`는 뷰 이름을 기반으로 실제 뷰 객체를 결정
    5. 뷰 렌더링
        - 선택된 뷰는 모델 데이터를 이용하여 최종 결과를 렌더링
        - 렌더링된 결과는 HTTP 응답으로 클라이언트에게 전송됨
