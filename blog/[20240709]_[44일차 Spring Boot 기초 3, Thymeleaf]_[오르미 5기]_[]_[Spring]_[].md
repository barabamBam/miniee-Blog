# SpringBoot 외부 설정

- 외부설정?
    
    애플리케이션의 동작을 제어하기 위해 **외부에서 주입**되는 설정 값들
    
    애플리케이션의 코드를 변경하지 않고도 다양한 환경에 맞게 애플리케이션을 구성할 수 있음
    
    resource 패키지 내, 
    
    `application.properties` (.으로 단어들을 이음) 
    
    `application.yml` :으로 단어들을 이음 → **최근 선호됨! 가독성이 좋음!**
    
    ```java
    // application.properties
    
    server.port=8080
    spring.application.name=MyApp
    
    ------------------------------------------------
    
    // application.yml
    
    server:
      port: 8080
    
    spring:
      application:
        name: MyApp
    ```
    

## 외부 설정을 이용해 변수 주입해보기!

`@Value` 어노테이션을 사용하여 변수 주입이 가능

```java
@Component
public class MyComponent {
    @Value("${server.port}")
    private int port;

    @Value("${spring.application.name}")
    private String appName;

    // ...
}
```

## 프로필 별로 외부 설정 다르게 하기!

프로필이란? 
`application-{profile}.properties` / `application-{profile}.yml` 형식으로 지정 가능하며 항상 application.* (application.properties 우선 > application.yml ) 다음에 실행됨

**환경에 따라 다른 설정 값**을 적용 가능해짐 (해당 프로필이 활성화되었을 때만 적용되는 설정 값 정의 가능)

개발기, QA환경, 상용 등 실제 접속해야하는 서버와 DB 정보가 다를 경우 각각의 정보를 별도로 저장해서 사용함 

### 실행 방법 1

실행시킬 프로필을 `application.yml`에서 명시하여 실행

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/e8f11927-b70c-4524-9227-a3efac08e7aa/34b5d117-2c5d-4723-878f-84566bd6098e/Untitled.png)

```java
# application-dev.yml
server:
  port: 8080

spring:
  application:
    name: MyApp-Dev
```

```java
# application-prod.yml
server:
  port: 8081

spring:
  application:
    name: MyApp-Prod
```

```java
// 활성화할 프로필을 지정 
// 해당 코드에서는 dev를 지정하였으므로, dev 파일 설정 값이 적용됨
spring:
  profiles:
    active: dev
```

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/e8f11927-b70c-4524-9227-a3efac08e7aa/e930b041-6533-41f0-b4ea-daf78f00cbe4/Untitled.png)

### 실행 방법 2

터미널에서 프로필 별로 옵션을 주어 실행

```java
# dev 프로필로 실행
java -jar myapp.jar --spring.profiles.active=dev

# prod 프로필로 실행
java -jar myapp.jar --spring.profiles.active=prod
```

### 사용 예시

```java
@Configuration
public class Config {

	// @Profile 어노테이션을 사용하면 해당 프로필이 활성화된 경우에만 빈을 등록한다
	@Bean
	@Profile("dev")
	public void devPrint() {
		System.out.println("dev");
	}
	
	@Bean
	@Profile("prod")
	public void prodPrint() {
		System.out.println("prod");
	}
```

### 확인 방법

```java
@SpringBootApplication
public class MyApplication {
    @Value("${server.port}")
    private int port;

    @Value("${spring.application.name}")
    private String appName;

    @PostConstruct
    public void printConfig() {
        System.out.println("포트번호: " + port);
        System.out.println("애플리케이션 이름: " + appName);
    }

    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}
```

![결과 출력](https://prod-files-secure.s3.us-west-2.amazonaws.com/e8f11927-b70c-4524-9227-a3efac08e7aa/b33eb31c-5e4b-42b7-bde1-8c54d0d7c6d4/Untitled.png)

결과 출력

### @PostConstruct

- 스프링 빈의 초기화 과정에서 사용되는 어노테이션
- 이 어노테이션이 붙은 메서드는 스프링 컨테이너가 빈을 생성하고 의존성 주입을 **완료한 후**에 자동 호출됨
- 역할
    1. 빈 초기화 메서드 지정
        
        : 이 어노테이션을 메서드에 붙이면 해당 메서드는 빈의 초기화 메서드로 지정됨
        
        : 스프링 컨테이너는 빈 생성 후 이 어노테이션이 붙은 메서드를 자동 호출함 
        
    2. 의존성 주입 후 초기화 작업 수행
        
        : 이 어노테이션이 붙은 메서드는 의존성 주입이 완료된 후 호출됨
        
        : 해당 메서드에서는 의존성 주입된 객체를 사용하여 초기화 작업을 수행할 수 있음
        
    3. 초기 데이터 로딩 또는 설정
        
        : 이 어노테이션이 붙은 메서드에서는 초기 데이터를 로딩하거나 필요한 설정을 수행할 수 있음
        
        : 외부 파일에서 데이터를 읽어오거나 환경 변수 값을 설정하는 등의 작업 수행 가능
        

### IntelliJ에서 프로필 설정

1. 우측 상단에서 구성 편집 클릭

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/e8f11927-b70c-4524-9227-a3efac08e7aa/015d41bb-3731-471c-9f71-d0de1e6ad282/Untitled.png)

1. 활성화된 프로파일에 원하는 파일명 작성 후 적용버튼 클릭 및 실행

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/e8f11927-b70c-4524-9227-a3efac08e7aa/0307233b-1708-4eab-9349-a3e80c0c7bc3/Untitled.png)

# Thymeleaf

## Thymeleaf란?

- Java 기반의 **서버 사이드 템플릿 엔진** 중 하나
    - **템플릿 엔진**
    : 웹 애플리케이션에서 **동적인 웹 페이지를 생성**하기 위해 사용되는 도구, 미리 정의된 템플릿에 데이터를 삽입하여 최종 HTML 페이지를 생성해줌
    - **서버 사이드 템플릿**
    : **서버 쪽에서 템플릿 구성**을 하는 방식, 서버에서 DB 또는 저장영역에서 가져온 데이터를 정의된 템플릿에 넣어 HTML을 그려서 클라이언트에게 전달해줌                              ex) JSP, Thymeleaf, FreeMarker 등
    - 서버 사이드 템플릿의 흐름
        1. 클라이언트(브라우저)에서 사용자가 어떤 요청을 하면 서버에 보냄
        2. 서버는 요청을 받아 로직을 수행함
        3. 로직 수행으로 이루어진 결과 데이터를 미리 정의된 템플릿에 넣음
        4. 서버에서 데이터가 들어가 HTML을 그림 → 렌더링
        5. HTML을 클라이언트에 전달함

## 특징

- 자연스러운 템플릿: Thymeleaf 템플릿은 유효한 HTML 구조를 가지므로 웹 브라우저에서 직접 열어볼 수 있음
- 강력한 표현식 언어: Thymeleaf는 다양한 표현식을 지원하여 데이터를 처리하고 조건문, 반복문 등을 사용할 수 있음
- 스프링 통합: Thymeleaf는 스프링 프레임워크와 잘 통합되어 스프링 MVC 패턴에서 손쉽게 사용 가능함

### 장점

- HTML과 유사한 구조로 템플릿을 작성할 수 있어 학습 곡선이 낮음
- 템플릿이 유효한 HTML이므로 디자이너와 개발자 간의 협업이 용이해짐
- 스프링과의 긴밀한 통합으로 개발 생산성을 높일 수 있음

### 단점

- 복잡한 로직을 템플릿에 포함시키면 가독성이 떨어짐
- 다른 템플릿 엔진에 비해 상대적으로 성능이 느릴 수 있음

### 흐름

```java
   +----------------------+
   |      웹 브라우저      |
   +----------------------+
              |
              | HTTP 요청
              v
   +----------------------+
   |   DispatcherServlet  |
   +----------------------+
              | 
              | 요청 전달
              v
   +----------------------+
   |       컨트롤러        |
   +----------------------+
              | 
              | 요청 처리 후 필요한 데이터를 담은 모델 생성
              v
   +----------------------+
   |         모델          | <- 비지니스 로직을 처리
   +----------------------+
              | 
              | DispatcherServlet이 모델과 뷰 이름 전달
              v
   +----------------------+
   |       뷰 리졸버       |
   +----------------------+
              | 
              | 해당하는 템플릿에 뷰 전달
              v
   +----------------------+
   |       Thymeleaf      |
   |       템플릿 엔진     |
   +----------------------+
              | 
              | 전달받은 모델 데이터를 템플릿에 삽입하여 HTML 생성
              v
   +----------------------+
   |      웹 브라우저      |
   +----------------------+
```

## SpringBoot에서 사용해보자!

1. application-dev.yml 파일 작성
    
    ```yaml
    server:
      port: 8081
    
    spring:
      application:
        name: MyApp-Dev
    
      devtools:
        restart:
          enabled: true # 자동 재시작 활성화
    
      thymeleaf:
        cache: false # 캐시를 사용하지 않음 -> 수정 시 바로 반영됨
        prefix: classpath:/templates/ # 템플릿 파일 위치
        suffix: .html # 템플릿 파일 확장자
        mode: HTML # HTML5 호환
        encoding: UTF-8 # 인코딩 설정
    ```
    
2. resource → templates → index.html 파일 생성
    
    ![resource → templates → index.html 파일 생성](https://prod-files-secure.s3.us-west-2.amazonaws.com/e8f11927-b70c-4524-9227-a3efac08e7aa/293c3bf0-715e-4efa-a3a5-d9257ab164eb/Untitled.png)
    
    resource → templates → index.html 파일 생성
    
3. html 파일 작성 → 이때 다음과 같은 코드를 작성하여 타임리프 템플릿 사용 명시해주기
    
    ```html
    <html xmlns:th="http://www.thymeleaf.org">
    ```
    
4. `(Thymeleaf)Controller` 생성
    
    ```java
    import org.springframework.stereotype.Controller;
    import org.springframework.ui.Model;
    import org.springframework.web.bind.annotation.GetMapping;
    
    @Controller
    public class ThymeLeafController {
    
        @GetMapping("/index") // url 주소
        public String index(Model model) {
            
            return "index"; // 작성한 html 파일 이름
    
        }
    }
    ```
    
5. 스프링 실행 후 [`http://localhost:8080/index`](http://localhost:8080/index) 입력

### 변수 표현식

`${변수명}` 구문을 사용하여 모델에서 전달된 데이터를 HTML에 삽입함

모델에서 데이터를 전달할 때는 `model.addAtrribute(”변수명=키”, “데이터=값”);` 

```html
<!-- model에서 받아온 데이터가 name에 들어가고 메세지가 대체됨 -->
<p th:text="${name}">메세지가 표시됩니다.</p> 

<!-- 다른 문자와 함께 전달받은 데이터를 노출시키고 싶을 경우 -->
<p th:text="'name: ' + ${name} + '입니다.'">메세지가 표시됩니다.</p> 
```

```java
@Controller
public class ThymeLeafController {
    @GetMapping("/index")
    public String index(Model model) {
        model.addAttribute("name", "thymeleaf"); // 모델에 데이터 추가

        return "index";

    }
}
```

![결과](https://prod-files-secure.s3.us-west-2.amazonaws.com/e8f11927-b70c-4524-9227-a3efac08e7aa/408fc944-7cf9-4d3f-b40c-a0a2b73da424/Untitled.png)

결과

# @Controller

Spring MVC에서 사용되는 어노테이션으로써 클래스가 웹 요청을 처리하는 컨트롤러 역할을 한다는 것을 나타내줌

- 주요 역할
    1. 요청 매핑
        
        : `@RequestMapping`, `@GetMapping`, `@PostMapping` 등의 어노테이션을 사용하여 특정 URL 패턴에 대한 요청을 처리하는 메서드 정의
        
        : 요청 URL, HTTP 메서드, 파라미터 등을 기반으로 적절한 핸들러 메서드 실행
        
        <aside>
        🤔 `@RequestMapping`, `@GetMapping`, `@PostMapping` 뭐가 달라?
        
        `@RequestMapping` 클래스 레벨과 메서드 레벨에서 사용 가능 /  HTTP 메서드와 URL 경로, 요청 매개변수, 헤더 등을 기반으로 요청 매핑 가능
        
        ```java
        @RequestMapping(value = "/hello", method = RequestMethod.GET)
        //@RequestMapping(value = "/hello", method = RequestMethod.POST) 도 가능
        //@RequestMapping(value = "/hello", method = RequestMethod.PUT) 도 가능
        //@RequestMapping(value = "/hello", method = RequestMethod.DELETE) 도 가능
        ```
        
        `@GetMapping` 메서드 레벨에서만 가능, GET 요청에 대한 매핑만  가능
        
        ```java
        @GetMapping("/users/{id}")
        public User getUser(@PathVariable Long id) {
           // id에 해당하는 User 객체 반환 로직
           return user;
        }
        ```
        
        `@PostMapping` 메서드 레벨에서만 가능, POST 요청에 대해 매핑되기 때문에 RequestBody의 값을 읽을 수 있음
        
        ```java
        @PostMapping("/user")
        public User createUser(@RequestBody User user) {
           // User 객체 생성 로직
           return user;
        }
        ```
        
        즉, @RequestMapping은 모든 HTTP 메서드에 특화되어 있고
        @GetMapping은 GET 메서드에, @PostMapping은 POST 메서드에 특화되어 있음
        
        </aside>
        
    2. 데이터 준비 및 모델 생성
        
        : 컨트롤러는 서비스 계층을 호출하여 필요한 데이터를 가져오고, 이를 모델에 담아 뷰에 전달함
        
        : Model 객체를 사용하여 뷰에서 사용할 데이터를 저장하고 전달함
        
    3. 뷰 선택 및 렌더링
        
        : 컨트롤러는 처리 결과에 따라 적절한 뷰를 선택하고, 모델 데이터를 전달함
        
        : 뷰는 전달받은 모델 데이터를 사용하여 최종 HTML을 생성하고, 클라이언트에게 응답을 반환함
        
        ```java
           +-----------------------+
           |      사용자 요청       |
           +-----------------------+
                       |
                       ▼
           +-----------------------+
           |      @Controller      |
           +-----------------------+
                       |
                       ▼
           +-----------------------+
           |    서비스 계층 호출    |
           +-----------------------+
                       |
                       ▼
           +-----------------------+
           |    모델 데이터 준비    |
           +-----------------------+
                       |
                       ▼
           +-----------------------+
           |      뷰 이름 반환      |
           +-----------------------+
                       |
                       ▼
           +-----------------------+
           |      ViewResolver     |
           +-----------------------+
                       |
                       ▼
           +-----------------------+
           |    Thymeleaf 템플릿    |
           +-----------------------+
                       |
                       ▼
           +-----------------------+
           |       HTML 생성       |
           +-----------------------+
                       |
                       ▼
           +-----------------------+
           |     HTTP 응답 반환     |
           +-----------------------+
        ```
        

## 잠깐! HTTP 메서드가 뭐야?

HTTP (Hypertext Transfer Protocol)는 클라이언트와 서버 간의 통신을 위한 프로토콜

HTTP 메서드는 클라이언트가 서버에게 요청의 목적이나 종류를 알리는 수단으로 사용됨

| 메서드 | 역할 | 데이터 전달 방법 | 예시 |
| --- | --- | --- | --- |
| GET | 서버에서 리소스 조회 시 사용 | 데이터를 쿼리 문자열로 전달 | GET /users?page=1&size=10 |
| POST | 서버에 새로운 리소스 생성 시 사용 | 데이터를 요청 본문(바디)에 포함하여 전달 | POST /users (요청 본문에 사용자 정보 포함) |
| PUT | 서버에 존재하는 리소스를 갱신할 때 사용 | 데이터를 쿼리문자열(인덱스) + 요청 본문(바디)에 포함하여 전달 | PUT /users/1 (요청 본문에 갱신할 사용자 정보 포함) |
| PATCH | 서버에 존재하는 리소스의 일부를 수정할 때 사용 | 데이터를 쿼리문자열(인덱스) + 요청 본문(바디)에 포함하여 전달 | PATCH /users/1 (요청 본문에 수정할 사용자 정보 포함) |
| DELETE | 서버에 존재하는 리소스를 삭제할 때 사용 | 데이터를 쿼리 문자열(인덱스)로 전달 | DELETE /users/1 |

<aside>
🤔 PUT 🆚 PATCH 뭐가 다를까?

**PUT**
: 요청 본문에 필요한 데이터를 일부 누락한 채로 전송하였을 경우, 누락된 데이터가 필드에 매핑되어 빈 값으로 수정됨

**PATCH**
: 데이터가 없으면 기존에 값 유지

</aside>

### HTTP status code

서버의 응답 결과를 나타내는 코드로, 100~500번대까지 존재함

- **100번대** = 처리중
- **200번대** = 성공
    - 200 OK: 요청이 성공적으로 처리됨
    - 201 Created: 새로운 리소스가 생성됨
- **300번대** = 리다이렉션
    - 301 Moved Permanently: 요청한 리소스가 영구적으로 이동됨
    - 302 Found: 요청한 리소스가 일시적으로 이동됨
- **400번대** = 클라이언트 오류
    - 400 Bad Request: 잘못된 요청
    - 401 Unauthorized: 인증되지 않은 사용자
    - 403 Forbidden: 접근 권한 없음
    - 404 Not Found: 요청한 리소스를 찾을 수 없음
- **500번대** = 서버 오류
    - 500 Internal Server Error: 서버 내부 오류 발생
    - 503 Server Unavailable: 서버가 일시적으로 요청을 처리할 수 없음
