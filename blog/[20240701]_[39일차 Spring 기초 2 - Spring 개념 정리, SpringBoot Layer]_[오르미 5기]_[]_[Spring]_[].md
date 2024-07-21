모든 출처: 이스트소프트 백엔드 개발자 오르미 5기

# PSA

- Portable Service Abstraction 이동식 서비스 추상화
- Spring Framework에서 제공하는 추상화(하위 시스템의 공통점을 뽑아내서 분리시키는 것) 개념으로, 서비스 객체를 구현할 때 특정 기술에 종속되지 않도록 도와주는 방법
- 서비스 객체의 코드를 변경하지 않고도 다양한 기술을 적용할 수 있어(기술 종속성 제거) 유연성과 유지보수성 향상 → DIP 개념
- 장점: 유연성, 유지보수성, 테스트 용이성
- 단점: 복잡성 증가, 성능 오버헤드

```java
           +---------------------+
           |    EmailService     |
           +---------------------+
                      |
                      | uses
                      ▼
           +---------------------+
           |    EmailSender      |
           |    (Interface)      |
           +---------------------+
                     ▲ ▲
                     | |
        implements   | |   implements
                     | |
           +-------------+-------------+
           |                           |
+---------------------+       +---------------------+
| SmtpEmailSender     |       | AwsSesEmailSender   |
+---------------------+       +---------------------+
```

# AOP

- Aspect-Oriented Programming 관점지향 프로그래밍
- 공통으로 적용되야하는 로직(로깅, 보안, 트랜잭션 등)을 구현할 때 사용
- 개발자는 각 비지니스 로직을 개발하는데 집중 가능
- **Aspect**: 공통 관심사를 모듈화한 클래스
- **Join** **Point**: 애스펙트를 적용할 수 있는 위치( 메서드 호출, 필드 접근 등 )
- **Pointcut**: 조인 포인트를 선택하는 표현식
- **Advice**: 특정 조인 포인트에서 실행되는 코드
- **Weaving**: 애스펙트를 대상 객체에 적용하는 과정

# MVC

- Model-View-Controller 패턴 → 각각은 컴포넌트로 개발, Layer (계층)이라고 함
- 웹 애플리케이션 개발에 널리 사용되는 아키텍처 패턴
- **Model**: 사용자의 정보를 저장하는 데이터 객채, 비즈니스 로직과 데이터 표현 담당, 데이터 상태 관리 및 조작 기능 제공
- **View**: 사용자에게 정보 표시, 모델의 데이터를 사용하여 사용자 인터페이스 표시, 사용자와의 상호작용 처리
- **Controller**: 모델과 뷰 사이의 상호작용 제어, 사용자의 입력을 받아 모델을 업데이트 하고 업데이트 된 모델을 뷰에 전달, 사용자의 요청 해석 및 동작 수행
- 장점: 코드의 재사용성과 모듈화, 유지보수성 향상, 협업과 병렬 개발 용이, 테스트 용이성, 유연성과 확장성
- 단점: 복잡성 증가, 개발 시간 증가, 과도한 추상화, 학습 곡선

```java
// Model
// Lombok 제공 어노테이션
@Getter
@Setter
@AllArgsConstructor
class User {
    private String name;
    private int age;
}

// View
class UserView {
    public void printUserDetails(User user) {
        System.out.println("User Details:");
        System.out.println("Name: " + user.getName());
        System.out.println("Age: " + user.getAge());
    }
}

// Controller
@AllArgsConstructor
class UserController {
    private User model;
    private UserView view;

    public void updateView() {
        view.printUserDetails(model);
    }

    public void setUserName(String name) {
        model.setName(name);
    }

    public void setUserAge(int age) {
        model.setAge(age);
    }
}

// Main
public class MVCExample {
    public static void main(String[] args) {
        User model = new User("John", 25);
        UserView view = new UserView();
        UserController controller = new UserController(model, view);

        controller.updateView();

        controller.setUserName("Alice");
        controller.setUserAge(30);

        controller.updateView();
    }
}
```

⇒ main에서 model, view 객체를 만들어 controller 객체에 넣어주고,

controller 객체에서 데이터를 매개변수로 입력받아 model에 저장한 후,

model에 저장된 데이터를 이용하여 view에서 출력

# SpringBoot Layer

## 표현 계층 Presentation Layer

- 사용자 인터페이스와 상호작용 담당, 사용자의 요청을 받아 처리 후 결과 반환
- UI 구성 요소, 컨트롤러, 뷰 템플릿 등을 포함
- 사용자 입력의 유효성 검사 및 적절한 응답 생성
- 다른 계층과의 통신 담당

```java
// MVC 의 C
@Controller 
public class UserController {
    @GetMapping("/users")
    public String listUsers(Model model) {
        List<User> users = userService.getAllUsers();
        model.addAttribute("users", users);
        return "user-list";
    }
}
```

## 서비스 계층 Service Layer

- 비즈니스 로직 구현, 표현 계층과 데이터 접근 계층 사이의 중간 계층 역할
- 트랜잭션 관리, 보안, 비즈니스 규칙 등을 처리
- 도메인 모델과 밀접하게 연관됨
- 표현 계층과 데이터 접근 계층을 분리하여 애플리케이션의 유지 보수성을 향상시켜줌

```java
// MVC 의 M
@Service
public class UserService {
    private final UserRepository userRepository;

    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    public List<User> getAllUsers() {
        return userRepository.findAll();
    }
}
```

## 데이터 접근 계층 Data Access Layer

- 데이터 저장소(데이터베이스, 외부 API 등)와의 상호작용 담당
- 데이터의 조회, 저장, 수정, 삭제 등의 작업 수행
- ORM(Object-Relational Mapping) 프레임워크(JPA 등)나 데이터 액세스 기술 활용
- 데이터 액세스 로직을 캡슐화하여 다른 계층으로부터 분리

```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    // 데이터 액세스 메서드 정의
}
```

## 계층의 필요성

- 관심사 별로 분리하여 코드의 가독성과 유지보수성 향상
- 테스트 용이성, 확장성이 높아짐
- 계층 간의 의존성은 주로 인터페이스를 통해 이루어짐 → 약결합 유지

```java
   +-----------------------+
   |   Presentation Layer  |
   |     (Controllers)     |
   +-----------------------+
             |
             | 요청/응답 -> Service 호출을 통해 비즈니스 로직 실행
             |
   +-----------------------+
   |     Service Layer     |
   |   (Business Logic)    |
   +-----------------------+
             |
             | 데이터 액세스 -> 데이터 조회 및 변경
             |
   +-----------------------+
   |   Data Access Layer   |
   |    (Repositories)     |
   +-----------------------+
             |
             | 데이터베이스 연동
             |
   +-----------------------+
   |       Database        |
   +-----------------------+
```

## Spring MVC와 비교하기!

- 🆚 **Presentation Layer**
  - MVC에서 표현 계층에 해당하는 부분은 컨트롤러와 뷰 템플릿
  - 컨트롤러는 사용자의 요청을 받아 처리 및 적절한 모델과 뷰 반환
  - 뷰 템플릿은 컨트롤러에서 전달받은 모델 데이터를 사용하여 사용자 인터페이스를 렌더링
  - Spring MVC의 `DispatcherServlet`은 표현 계층의 중심 역할, 요청을 적절한 컨트롤러에게 전달하고 응답 처리
- 🆚 **Service Layer**
  - MVC에서 서비스 계층에 해당하는 부분은 `@Service` 서비스 클래스
  - 서비스 클래스는 비즈니스 로직을 구현하고, 트랜잭션 관리, 보안 등의 기능을 처리함
  - 컨트롤러는 서비스 클래스를 호출하여 필요한 비즈니스 로직을 실행하고 결과를 받아옴
  - Spring MVC에서는 서비스 클래스를 Bean으로 등록하고, 컨트롤러에 주입하여 사용
- 🆚 **Data Access Layer**
  - MVC에서 데이터 접근 계층에 해당하는 부분은 `@Repository` 레포지토리
  - 레포지토리는 데이터베이스나 외부 저장소와의 상호작용을 담당하며, 데이터의 조회/저장/수정/삭제 등 작업 수행
  - Spring MVC에서는 주로 Spring Data JPA, MyBatis등의 프레임 워크를 사용하여 레포지토리 구현
  - 서비스 클래스는 레포지토리를 사용하여 데이터 액세스 작업을 수행

<aside>
📝 정리

- Presentation Layer
  Spring MVC의 **컨트롤러**와 뷰 템플릿이 담당

- Service Layer
  Spring MVC의 **서비스 클래스**가 담당, 컨트롤러를 통해 비즈니스 로직을 실행 및 결과를 받아오고 레포지토리를 통해 데이터 액세스 작업 수행

- Data Access Layer
  Spring MVC의 **레포지토리**가 담당, 데이터베이스나 외부 저장소와의 상호작용 처리

- Spring MVC는 Presentation Layer, Service Layer, Data Access Layer의 역할을 명확히 구분하고 있으며, 각 계층 간의 의존성을 관리하는 데 도움을 줌
- 컨트롤러 `@Controller` / 서비스 `@Service` / 레포지토리 `@Repository` 어노테이션을 사용하여 MVC에서 해당 계층의 역할을 나타냄

</aside>