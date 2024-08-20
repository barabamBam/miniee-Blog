모든 출처: 이스트소프트 백엔드 개발자 오르미 5기

# Thymeleaf 문법 정리하고 가기!

- **변수 표현식**

Controller에서 `model.addAttribute("키", "값")`으로 보낸 데이터

→ `${전달받을 데이터 명}` 으로 받음

```html
<p th:text="${name}"></p>
```

- **선택 변수 표현식**

Controller에서 모델 여러 개를 보냈을 경우

→ 선택할 객체를 `th:object="${객체명}"` 으로 받고 자식 노드에서 `*{필드명}` 으로 데이터를 사용할 수 있음

```html
<div th:object="${user}">
	<p th:text="*{name}"></p>
</div>
```

- **조건문**

받은 데이터가 boolean 형식일 때 사용

조건이 true일 경우 실행, `th:if`  / false일 경우 실행, `th:unless`

```html
<p th:if="${user.isAdmin}">관리자입니다.</p>
<p th:unless="${user.isAdmin}">관리자가 아닙니다.</p>
```

- **반복문**

컬렉션 혹은 배열을 하나씩 추출할 때, `th:each="객체 : ${컬렉션 명}"`

```html
<div th:each="user : ${users}">
	<p th:text="${user.name}"></p>
</div>
```

- **주석**

`<!--/* ... */-->`

- **속성 설정**

html의 태그에 여러 속성을 사용하는데 Controller에서 받아온 값을 사용해야 할 경우, `th:attr` 사용

```html
<button type="button" th:attr="onclick=|openModal('${comment.id}', '${comment.user_name}', '${comment.comment}' )|" class="bi bi-pencil"></button>
```

```html
<a th:href="@{/주소}"></a>
<image th:src="@{/이미지 경로}" />
<input type="text" th:value="${name}" />
```

- **폼 바인딩**

`th:object`, `th:field` 속성을 사용하여 폼과 객체를 바인딩하고 필드를 불러올 수 있음

```html
<form th:action="@{/주소}" method="post" th:object="${user}">
	<input type="text" th:field="*{name}" />
	<button type="submit">저장</button>
</form>
```

여기서 보내진 데이터는 Controller에서 `@ModelAttribute` 를 통해 객체에 바인딩을 쉽게 할 수 있음 (받을 데이터가 있는 메서드에서는 해당 어노테이션 사용하기!)

> 기본적으로 CRUD 모두 `Entity` 설계가 필요
>
>
> ```java
> import lombok.AllArgsConstructor;
> import lombok.Getter;
> import lombok.NoArgsConstructor;
> import lombok.Setter;
> 
> @Getter
> @Setter
> @NoArgsConstructor
> @AllArgsConstructor
> public class Post {
> 	// 엔티티에 필요한 필드
> 	private Long id;
>   private String title;
>   private String content;
>   private LocalDateTime createdAt;
> }
> ```
>

> `application-dev.yml` 작성
>
>
> ```java
> server:
>   port: 8081
> 
> spring:
>   application:
>     name: est-service-dev
> 
>   devtools:
>     restart:
>       enabled: true
> 
>   thymeleaf:
>     cache: false
>     // 이 경로 아래의 페이지와 매핑함
>     prefix: classpath:/templates/ 
>     // 매핑할 페이지의 종류
>     suffix: .html
>     mode: HTML
>     encoding: UTF-8
> ```
>

# CREATE / READ 기능 구현

- Controller

    ```java
    @Controller
    @RequestMapping("/posts")     // 모든 메서드에 걸릴 매핑 주소
    public class PostController {
    	// 필드 (리스트, 아이디 등)
    	private List<Post> posts = new ArrayList<>();
    	private Long nextId = 1L; // 아이디 자동 증가 기능 구현 용
    	
    	
    	// READ 기능
    	@GetMapping
    	public String list(Model model) {
    			// 웹페이지에 보낼 데이터를 model에 담아 전송
    			// 따옴표 안에 이름으로 html에서 매핑되며, 뒤에가 실제 매핑할 데이터 입력
    			model.addAttribute("posts", posts);
    			
    			return "post/list" // 일치하는 이름의 html에 데이터 전송 -> templates 패키지 아래 post 패키지 아래 list.html과 매핑됨
    	}
    	
    	// CREATE 기능 -> 폼 생성
    	@GetMapping("/new")
    	public String newPostForm(Model model) {
    			// 웹페이지에 보낼 데이터를 model에 담아 전송
    			// 따옴표 안에 이름으로 html에서 매핑되며, 뒤에 빈 객체를 전송
    			model.addAttribute("post", new Post());
    			
    			return "post/form" // templates 패키지 아래 post 패키지 아래 form.html과 매핑됨
    	}
    	
    	// CREATE 기능 -> 실제 데이터 저장
    	// 위에서 전송한 빈 객체에 데이터를 담아 Post 메서드로 보내오면 해당 데이터를 저장
      @PostMapping
      public String savePost(@ModelAttribute Post post) {
          post.setId(nextId++); // 자동 증가
          post.setCreatedAt(LocalDateTime.now()); // 현재 시간 저장
          posts.add(post); // 데이터 리스트에 저장
          return "redirect:/posts";  // 리다이렉트로 페이지 불러오기
      }
    
    	// READ 기능 -> 데이터 상세 보기
    	// 매개변수에 있는 어노테이션을 통해 주소에 적혀있는 데이터 값을 받아옴(이 데이터는 pk 지향)
    	@GetMapping("/{id}")
    	public String detail(@PathVariable("id") Long id, Model model) {
    			// 리스트에서 찾고자하는 데이터를 찾는 로직 -> 처음 한 개만 찾고 없으면 에러코드를 던짐
    	    Post post = posts.stream()
    	            .filter(p -> p.getId().equals(id))
    	            .findFirst()
    	            .orElseThrow(() -> new IllegalArgumentException("해당 게시글이 존재하지 않습니다. id: " + id));
    	    // 찾았으면 모델에 담아서 보내줌
    	    model.addAttribute("post", post);
    	    return "post/detail";  // // templates 패키지 아래 post 패키지 아래 detail.html과 매핑됨
    	}
    }
    ```

- CREATE 확인 용 HTML (`list.html`)
    - 여러 데이터를 each문으로 하나씩 출력 → Controller에서 보내온 데이터가 뒤에 `${posts}` , 이 리스트에서 객체를 하나씩 받는 게 `post`
    - 각 객체의 필드에 접근할 때는, `${post.필드명}`

    ```html
    <!DOCTYPE html>
    <html xmlns:th="http://www.thymeleaf.org">
    <head>
        <title>게시판</title>
        <link rel="stylesheet" th:href="@{/css/style.css}" />
    </head>
    <body>
        <h1>게시글 목록</h1>
        <table>
            <thead>
                <tr>
                    <th>번호</th>
                    <th>제목</th>
                    <th>작성일</th>
                </tr>
            </thead>
            <tbody>
    					  <tr th:each="post : ${posts}">
    					    <td th:text="${post.id}"></td>
    					    <td>
    						    <!-- 상세보기로 넘어가기 위한 코드 -->
    					      <a th:href="@{/posts/{id}(id=${post.id})}" th:text="${post.title}"></a>
    					    </td>
    					    <td th:text="${post.createdAt}"></td>
    					  </tr>
            </tbody>
        </table>
        <a th:href="@{/posts/new}">새 글 작성</a>
    </body>
    </html>
    ```

- 사용자에게서 데이터를 받을 페이지 (`form.html`)
    - 데이터를 전송할 페이지 `th:action=”@{/페이지}”` 로 매핑
    - 아까 데이터를 @PostMapping 하여 받기로 했으니까 `method=”post”`

    ```html
    <!DOCTYPE html>
    <html xmlns:th="http://www.thymeleaf.org">
    <head>
      <title>새 글 작성</title>
      <link rel="stylesheet" th:href="@{/css/style.css}" />
    </head>
    <body>
    <h1>새 글 작성</h1>
    <form th:action="@{/posts}" method="post">
      <div>
        <label for="title">제목</label>
        <input type="text" id="title" name="title" />
      </div>
      <div>
        <label for="content">내용</label>
        <textarea id="content" name="content"></textarea>
      </div>
      <button type="submit">저장</button>
    </form>
    </body>
    </html>
    ```

- READ 기능 → 상세 보기 페이지(`detail.html`)

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>게시글 상세보기</title>
    <link rel="stylesheet" th:href="@{/css/style.css}" />
</head>
<body>
    <h1>게시글 상세보기</h1>
    <div class="post-detail">
        <h2 th:text="${post.title}"></h2>
        <p class="post-meta">
            작성일: <span th:text="${post.createAt}"></span>
        </p>
        <div class="post-content" th:text="${post.content}"></div>
        <a th:href="@{/posts}">목록으로</a>
    </div>
</body>
</html>
```