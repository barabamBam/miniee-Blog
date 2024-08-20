모든 출처: 이스트소프트 백엔드 개발자 오르미 5기

# DELETE 기능 구현

- Controller

```java
@PostMapping("/{id}/delete")
public String delete(@PathVariable("id") Long id) {
	// 일치하는 아이디의 데이터가 있으면 삭제
	posts.removeIf(post -> post.getId() == id);
	// 삭제 후 리다이렉트
	return "redirect:/posts";
}
```

- `detail.html` 페이지에 삭제 버튼 추가

```html
<div class="post-actions">
    <a th:href="@{/posts}">목록으로</a>
    <form th:action="@{/posts/{id}/delete(id=${post.id})}" method="post" class="delete-form">
        <button type="submit">삭제</button>
    </form>
</div>
```

# UPDATE 기능 구현

- Controller

```java
@GetMapping("/{id}/edit")
public String editForm(@PathVariable("id") Long id, Model model) {
		// 일치하는 아이디의 데이터가 있으면 불러오기
    Post post = posts.stream()
            .filter(p -> p.getId() == id)
            .findFirst()
            .orElseThrow(() -> new IllegalArgumentException("해당 게시글이 존재하지 않습니다. id: " + id));
    // 불러온 데이터 모델에 담아 전송
    model.addAttribute("post", post);
    return "post/edit";
}

@PostMapping("/{id}/edit")
public String edit(@PathVariable("id") Long id, @ModelAttribute Post updatedPost) {
    // 일치하는 아이디의 데이터가 있으면 불러오기
    Post post = posts.stream()
            .filter(p -> p.getId() == id)
            .findFirst()
            .orElseThrow(() -> new IllegalArgumentException("해당 게시글이 존재하지 않습니다. id: " + id));
    // 해당 아이디로 검색한 데이터를 수정된 데이터로 다시 세팅
    post.setTitle(updatedPost.getTitle());
    post.setContent(updatedPost.getContent());
    // 상세보기 페이지로 다시 리다이렉트
    return "redirect:/posts/{id}";
}
```

- 수정 용 페이지 작성 (`edit.html` )

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>게시글 수정</title>
    <link rel="stylesheet" th:href="@{/css/style.css}" />
</head>
<body>
    <h1>게시글 수정</h1>
    <form th:action="@{/posts/{id}/edit(id=${post.id})}" method="post">
        <div>
            <label for="title">제목</label>
            <input type="text" id="title" name="title" th:value="${post.title}" />
        </div>
        <div>
            <label for="content">내용</label>
            <textarea id="content" name="content" th:text="${post.content}"></textarea>
        </div>
        <button type="submit">저장</button>
    </form>
</body>
</html>
```

- `detail.html` → 수정 버튼 추가

```html
  <div class="post-actions">
    <a th:href="@{/posts}">목록으로</a>
    <a th:href="@{/posts/{id}/edit(id=${post.id})}" class="edit-btn">수정</a>
    <form th:action="@{/posts/{id}/delete(id=${post.id})}" method="post" class="delete-form">
      <button type="submit">삭제</button>
    </form>
  </div>
```

# API란?

**API (Application Programming Interface)**

애플리케이션 간의 통신을 위한 규약 또는 인터페이스 → 한 애플리케이션에서 API를 통해 다른 애플리케이션의 기능이나 데이터에 접근 가능하게 도와줌

# RESTful API

**REST (Representational State Transfer)**

아키텍처 스타일을 따르는 API로, HTTP 프로토콜을 기반으로 리소스에 대한 CRUD 작업을 수행할 수 있음

## 특징

1. 리소스 중심의 URL 설계 가능
2. HTTP 메서드를 활용한 리소스 조작 가능
3. 무상태(Stateless) 통신
4. JSON / XML 형식의 데이터 교환

## RESTful API 설계 방법

### 1. 리소스 식별

- API에서 다룰 리소스를 명확하게 식별함
- 리소스는 URL을 통해 표현됨
- `/users`, `/products`, `/orders`

### 2. HTTP 메서드 활용

- HTTP 메서드를 사용하여 리소스에 대한 작업 수행
- `GET`(리소스 조회) / `POST`(새로운 리소스 생성) / `PUT` ,`PATCH`(기존 리소스 수정) / `DELETE`(리소스 삭제)

### 3. 적절한 HTTP 상태 코드 사용

- API 응답 시 적절한 HTTP 상태 코드 반환으로 요청 처리 결과 명확히 전달
- 2xx: 성공
- 3xx: 리다이렉트
- 4xx: 클라이언트 오류
- 5xx: 서버 오류

### 4. 버전 관리

- API의 버전을 관리하여 이전 버전과의 호환성을 유지
- 버전 정보를 URL에 포함시키는 방법이 일반적임
- `/v1/users`, `/v2/products`

### 5. 에러 처리

- API 요청 처리 중 발생하는 오류를 적절히 처리하고 클라이언트에게 명확한 에러 메세지 제공
- 에러 응답에는 에러 코드와 상세 설명 포함

### 6. 보안(Spring Security)

- API 접근에 대한 인증 및 권한 체크 수행
- 민감한 데이터는 암호화하여 전송
- API 키, OAuth, JWT 등의 인증 방식을 활용 가능

## 예시

```java
@RestController
@RequestMapping("/api/users")
public class UserController {

    @GetMapping
    public List<User> getAllUsers() {
        // 전체 사용자 목록 조회
    }

    @PostMapping
    public User createUser(@RequestBody User user) {
        // 새로운 사용자 생성
    }

    @GetMapping("/{id}")
    public User getUserById(@PathVariable("id") Long id) {
        // 특정 사용자 조회
    }

    @PutMapping("/{id}")
    public User updateUser(@PathVariable("id") Long id, @RequestBody User updateUser) {
        // 특정 사용자 정보 수정
    }

    @DeleteMapping("/{id}")
    public void deleteUser(@PathVariable("id") Long id) {
        // 특정 사용자 삭제
    }
}
```