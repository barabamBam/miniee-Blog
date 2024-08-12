모든 출처: 이스트소프트 백엔드 개발자 오르미 5기

# JDBC

- Java DataBase Connectivity의 약자
- Java Web Application에서 DataBase에 있는 데이터를 접근하기 위해서 사용하며, JDBC가 Java Application과 데이터베이스를 연결해줌
- JDBC API는 자바언어로 데이터베이스를 다르기 위한 라이브러리로 사용하기 위해서는 Driver Manager 필요

## Java에서 MySQL 다루기

> 데이터베이스를 사용하기 전 MySQL Connector/J 다운로드하기!
다운로드 받은 .jar 파일을 lib 폴더를 생성하여 해당 위치에 넣고 라이브러리에 추가하여 사용한다!
>

### SELECT

```java
// Plain Java JDBC Code - 조회 기능

import java.sql.*;

public class PlainJdbcExample {
    static final String DB_URL = "jdbc:mysql://localhost:3306/test_db";
    static final String USER = "root";   // DB username
    static final String PASS = "0000";   // DB Password
    static final String QUERY = "SELECT * FROM students";   // 조회 

    public static void main(String[] args) {
        // Open & get a connection
        try (Connection conn = DriverManager.getConnection(DB_URL, USER, PASS);
             Statement stmt = conn.createStatement();
             ResultSet rs = stmt.executeQuery(QUERY)) {

            // Extract data from result set after execute query
            while (rs.next()) {
                // Retrieve by column name
                System.out.print("ID: " + rs.getInt("id"));
                System.out.print(", name: " + rs.getString("name"));
                System.out.print(", Age: " + rs.getInt("age"));
                System.out.println(", address: " + rs.getString("address"));
            }
        } catch (SQLException e) {
            System.out.println(e.getErrorCode());
            System.out.println(e.getMessage());
        }
    }
}
```

### INSERT

```java
// 데이터 INSERT 기능 
import java.sql.*;

public class InsertPlainJdbcExample {
    static final String DB_URL = "jdbc:mysql://localhost:3306/test_db";
    static final String USER = "root";
    static final String PASS = "0000";
    static final String QUERY = "INSERT INTO students (name, age, address) VALUES (?, ?, ?)";

    public static void main(String[] args) {
        String insertName = "장이수";
        int insertAge = 40;
        String insertAddress = "서울특별시";
        
        // Open & get a connection
        try (Connection conn = DriverManager.getConnection(DB_URL, USER, PASS);
             PreparedStatement pstmt = conn.prepareStatement(QUERY)) {
            pstmt.setString(1, insertName);
            pstmt.setInt(2, insertAge);
            pstmt.setString(3, insertAddress);
            
            int rowsAffected = pstmt.executeUpdate();
            System.out.println(rowsAffected + " row(s) updated.");

        } catch (SQLException e) {
            System.out.println(e.getErrorCode());
            System.out.println(e.getMessage());
        }
    }
}

```

### UPDATE

```java
// UPDATE 기능 
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.SQLException;

public class UpdatePlainJdbcExample {
    static final String DB_URL = "jdbc:mysql://localhost:3306/test_db";
    static final String USER = "root";
    static final String PASS = "0000";
    static final String QUERY = "UPDATE students SET name = ?, age = ? WHERE id = ?";

    public static void main(String[] args) {
        int updatedId = 2;
        String updatedName = "한빛2";
        int updatedAge = 35;

        // Open & get a connection
        try (Connection conn = DriverManager.getConnection(DB_URL, USER, PASS);
             PreparedStatement pstmt = conn.prepareStatement(QUERY)) {
            pstmt.setString(1, updatedName);
            pstmt.setInt(2, updatedAge);
            pstmt.setInt(3, updatedId);
            
            int rowsAffected = pstmt.executeUpdate();
            System.out.println(rowsAffected + " row(s) updated.");

        } catch (SQLException e) {
            System.out.println(e.getErrorCode());
            System.out.println(e.getMessage());
        }
    }
}

```

### DELETE

```java
// DELETE 기능
import java.sql.*;

public class DeletePlainJdbcExample {
    static final String DB_URL = "jdbc:mysql://localhost:3306/test_db";
    static final String USER = "root";
    static final String PASS = "0000";
    static final String QUERY = "DELETE FROM students WHERE id = ?";

    public static void main(String[] args) {
        int deletedId = 1;
        // Open & get a connection
        try (Connection conn = DriverManager.getConnection(DB_URL, USER, PASS);
             PreparedStatement pstmt = conn.prepareStatement(QUERY)) {

            pstmt.setInt(1, deletedId);
            int rowsAffected = pstmt.executeUpdate();
            System.out.println(rowsAffected + " row(s) deleted.");

        } catch (SQLException e) {
            System.out.println(e.getErrorCode());
            System.out.println(e.getMessage());
        }
    }
}

```

## Spring에서 MySQL 다루기

> 프로젝트 시작 시 JDBC Driver, MySQL Driver 체크해서 시작하기!
>
>
> ```sql
> // application.properties 파일 
> spring.datasource.driver-class.name=com.mysql.cj.jdbc.Driver
> spring.datasource.url=jdbc:mysql://localhost:3306/test_db
> spring.datasource.username=root
> spring.datasource.password=0000
> ```
>
>  중복코드 비중이 높은 로직들을 **JdbcTemplate**을 이용하면 코드가 줄어듦
>

```sql
// 1.
// JDBC API 직접사용하는 것 보다 중복되는 부분이 많이 제거됨
// JdbcTemplate도 JDBC와 마찬가지로 데이터를 다루는(저장/조회/수정/삭제) 것을 도와주는 API
// JdbcTemplate은 SQL Mapper의 일종 - SQL을 직접 작성하고 Object의 필드를 매핑하여 데이터를 객체화
@Repository
public class StudentJdbcTemplateRepository implements StudentRepository {
    private JdbcTemplate jdbcTemplate;

    public StudentJdbcTemplateRepository(JdbcTemplate jdbcTemplate) {
        this.jdbcTemplate = jdbcTemplate;
    }
}
```

```sql
// 2.
// JdbcTemplate 에서 ? 을 이용하여 데이터 처리를 했던 방식에서 개선
// ? 대신에 :변수명 을 이용하여 처리함으로써 파라미터의 순서에 강제를 받지 않음.
// 파라미터에 이름을 지어서 쿼리를 실행할 수 있음.
@Repository("studentNamedParameterJdbcRepository")
public class StudentNamedParameterJdbcRepository implements StudentRepository {

    private final NamedParameterJdbcTemplate jdbcTemplate;

    public StudentNamedParameterJdbcRepository(NamedParameterJdbcTemplate namedParameterJdbcTemplate) {
        this.jdbcTemplate = namedParameterJdbcTemplate;
    }
}
```

## SELECT

```sql
@Override
public List<Student> findAll() {
    List<Student> list = jdbcTemplate.query("SELECT * FROM students", new StudentRowMapper());

    return jdbcTemplate.query(
            "SELECT * FROM students",
            (rs, rowNum) ->
                    new Student(
                            rs.getLong("id"),
                            rs.getString("name"),
                            rs.getInt("age"),
                            rs.getString("address")
                    )
    );
}

@Override
public List<Student> findStudents(String name) {
    return jdbcTemplate.query(
            "select * from students where name like ?",
            new Object[]{"%" + name + "%"},
            (rs, rowNum) ->
                    new Student(
                            rs.getLong("id"),
                            rs.getString("name"),
                            rs.getInt("age"),
                            rs.getString("address")
                    )
    );
}

@Override
public Optional<Student> selectStudentById(Integer studentId) {
    return jdbcTemplate.queryForObject(
            "select * from students where id = ?",
            new Object[]{studentId},
            (rs, rowNum) ->
                    Optional.of(new Student(
                            rs.getLong("id"),
                            rs.getString("name"),
                            rs.getInt("age"),
                            rs.getString("address")
                    ))
    );
}
```

```sql
@Override
public List<Student> findStudents(String name) {
    MapSqlParameterSource mapSqlParameterSource = new MapSqlParameterSource().addValue("name", name);
    return jdbcTemplate.query("SELECT * FROM students WHERE name = :name",
            mapSqlParameterSource,
            (rs, rowNum) -> new Student(rs.getLong("id"), rs.getString("name"),
                    rs.getInt("age"), rs.getString("address")));
}

@Override
public Optional<Student> selectStudentById(Integer studentId) {
    return jdbcTemplate.queryForObject(
            "select * from students where id = :id",
            new MapSqlParameterSource("id", studentId),
            (rs, rowNum) ->
                    Optional.of(new Student(
                            rs.getLong("id"),
                            rs.getString("name"),
                            rs.getInt("age"),
                            rs.getString("address")
                    ))
    );
}
```

⇒ `MapSqlParameterSource` 이용 전 = 데이터를 `?`  형태로 받아옴 → Object 형식으로 데이터 전달

⇒ `MapSqlParameterSource` 이용 시 = 데이터를 `:column` 형태로 받아옴 → `MapSqlParameterSource` 에 데이터를 저장하여 전달

## INSERT

```sql
@Override
public int insertStudent(Student student) {
    return jdbcTemplate.update(
            "INSERT INTO students (name, age, address) VALUES(?, ?, ?)",
            student.getName(), student.getAge(), student.getAddress());
}

@Override
public int insertStudent(Map<String, Object> params) {
    String name = (String) params.get("name");
    int age = (int) params.get("age");
    String address = (String) params.get("address");

    return jdbcTemplate.update(
            "INSERT INTO students (name, age, address) VALUES (?,?,?)", name, age, address);
}
```