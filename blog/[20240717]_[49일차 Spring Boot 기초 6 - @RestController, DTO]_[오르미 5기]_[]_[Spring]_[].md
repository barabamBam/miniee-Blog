모든 출처: 이스트소프트 백엔드 개발자 오르미 5기

# RESTful API

- `@RestController` = `@Controller` + `@ResponseBody`
    - 해당 컨트롤러 클래스의 모든 메서드에 `@ResponseBody`가 자동 적용됨 → 메서드의 반환 값이 뷰 이름이 아닌 실제 데이터로 처리되어 HTTP 응답 본문에 직접 작성됨
- Controller는 주소 매핑과 결과 반환에 초점 / Service는 비즈니스 로직에 초점
- **GET**

  `@GetMapping` → 매개변수 없음, 리턴 값은 데이터 리스트
  `@GetMapping("/{id}")` → 매개변수 `@PathVariable("id") Long id`, 리턴 값은 해당 아이디를 가진 객체

- **POST**

  `@PostMapping` → 매개변수 `@RequestBody 객체`, 리턴 값 저장한 객체

- **PUT / PATCH**

  `@PutMapping("/{id}")` -> 매개변수 `(@PathVariable("id") Long id, @RequestBody 객체)`, 리턴 값은 해당 아이디를 가진 객체 수정 후 반환

- **DELETE**

  `@DeleteMapping("/{id}")` -> 매개변수 `@RequestBody 객체`, 리턴 값 없음


```java
@RestController
@RequestMapping("/api/members")
public class MemberController {
    private List<Member> members = new ArrayList<>();
    private long nextId = 1;

    // GET /api/members
    // 모든 사용자 목록 반환
    @GetMapping
    public List<Member> getAllMembers() {
        return members;
    }

    // POST /api/members
    // 새로운 사용자 생성 후 반환
    @PostMapping
    public Member createMember(@RequestBody Member member) {
        member.setId(nextId++);
        members.add(member);
        return member;
    }

    // GET /api/members/{id}
    // 특정 아이디에 해당하는 사용자를 반환
    @GetMapping("/{id}")
    public Member getMemberById(@PathVariable("id") Long id) {
        return members.stream()
            .filter(member -> member.getId()==id)
            .findFirst()
            .orElseThrow(() -> new IllegalArgumentException("멤버를 찾을 수 없습니다. Id: " + id));
    }

    // PUT /api/members/{id}
    // 특정 아이디에 해당하는 사용자의 정보를 업데이트 후 반환
    @PutMapping("/{id}")
    public Member updateMember(@PathVariable("id") Long id, @RequestBody Member updatedMember) {
        Member member = members.stream()
            .filter(m -> m.getId()==id)
            .findFirst()
            .orElseThrow(() -> new IllegalArgumentException("멤버를 찾을 수 없습니다. Id: " + id));

        member.setName(updatedMember.getName());
        member.setEmail(updatedMember.getEmail());
        return member;
    }

    // DELETE /api/members/{id}
    // 특정 아이디에 해당하는 사용자 삭제
    @DeleteMapping("/{id}")
    public void deleteMember(@PathVariable("id") Long id) {
        members.removeIf(member -> member.getId()==id);
    }
}
```

# DTO (Data Transfer Object)

- 계층 간 데이터 전송을 위한 객체
- 클라이언트와 서버 간 또는 서비스 레이어와 컨트롤러 레이어 간 데이터 전송에 사용됨
- 데이터 전송을 위한 순수한 데이터 객체이며, 로직 포함 ❌
- Getter, Setter, 생성자 등을 포함하고 직렬화 지원
- 데이터베이스의 엔티티와 1:1 매핑되지 않을 수 있으며, 필요한 데이터만 담아서 전송

```java
// DTO 예제
// 기본생성자로 생성 후 값을 유동적으로 변경 
public class DtoEx {
    private String name;
    private int age;

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setAge(int age) {
        this.age = age;
    }
}

```

## 🆚 DAO (Data Access Object)

- 데이터베이스에 접근하여 데이터를 조작하는 객체
- 데이터베이스와의 상호작용을 캡슐화하여 데이터 액세스 로직을 분리함
- 데이터베이스에 대한 CRUD 작업을 수행
- 데이터베이스와의 연결, 쿼리 실행, 결과 매핑 등의 작업을 처리

```java
// DAO 예제
public class TestDao {
public void add(TestDto dto) throws ClassNotFoundException, SQLException {
		private static final String DRIVER = "com.mysql.jdbc.Driver";
    private static final String URL = "jdbc:mysql://localhost:3306/dao_Db";
    private static final String USER = "root";
    private static final String PASSWORD = "1234";   
		
		String sql = "SELECT * FROM vouchers";

        try {
            con = DriverManager.getConnection(URL, USER, PASSWORD);
            stmt = con.createStatement();
            res = stmt.executeQuery(sql);
            while (res.next()) {
                System.out.println(res.getString("id") + " ");
                System.out.println(res.getString("value") + " ");
            }
        } catch (SQLException throwables) {
            throwables.printStackTrace();
        }

    preparedStatement.setString(1, dto.getName());
    preparedStatement.setInt(2, dto.getValue());
    preparedStatement.setString(3, dto.getData());
    preparedStatement.executeUpdate();
    preparedStatement.close();

    connection.close();

	}
}

```

## 🆚 VO (Value Object)

- 값 객체라고도 하며, 불변하고 `equals()`와 `hashCode()` 를 재정의한 객체임
- 주로 값 자체로 동등성을 판단하며, 식별자 개념이 없음
- 보통 Getter ⭕, Setter ❌
- 도메인 엔티티의 일부 또는 속성 그룹을 나타내는 데 사용됨

```java
// VO예제
// 값을 표현만 하기 때문에 setter()메소드는 사용하지 않는다.
public class VoEx {
    static class Money{
        private final int value;

        Money(int value) {
            this.value = value;
        }

        public int getValue() {
            return value;
        }
				
	// 사용자 생성 메소드이지만 setter형태의 메소드가 아니므로 무관함
        public String printMoney(){
            return this.value + "원";
        }
					
	// 두 객체의 모든 필드 값들이 모두 같으면 같은 객체이도록 만들기 위한 오버라이딩
		@Override
        public int hashCode() {
            return Objects.hash(value);
        }

        @Override
        public boolean equals(Object obj) {
            if(this == obj)
                return true;
            if(obj == null || getClass() != obj.getClass())
                return false;
            Money money = (Money) obj;
            if(value == money.value)
                return true;
            else
                return false;
        }
    }
}
```

## Entity와 DTO의 차이점

<aside>
🤔 Entity랑 DTO를 구분해서 사용하는 이유?

Entity랑 DTO는 구조는 비슷하나 Entity와 달리 DTO 내부에는 로직이 존재하지 않음
애초에 DTO를 사용하는 이유가 **Entity의 사용을 최소화**하기 위해서임 → Entity는 **실제 DB 테이블과 매핑이되는 클래스**이기 때문에 레포지토리 외 서비스나 컨트롤러에서는 DTO를 사용하는 것을 지향함

</aside>

---

### 📝 부록 - PathVariable와 RequestParam의 차이점

<aside>
🤔 `@PathVariable` 🆚 `@RequestParam`

`@PathVariable` - 하나의 검색 데이터를 이용할 때 사용
/api/books/1

`@RequestParam` - 여러 개의 검색 데이터를 이용할 때 사용
/api/books?title=okay
/api/books?id=1&title=okay

</aside>