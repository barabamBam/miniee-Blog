모든 출처: 이스트소프트 백엔드 개발자 오르미 5기


<aside>
🤔 Mybatis 🆚 JPA (ORM)

Mybatis: SQL Mapper → SQL의 결과를 Object화
JPA (ORM): 테이블(Entity) 자체를 Object화

</aside>

# MyBatis

## 사용 예제

- 종속성 추가: `MyBatis Framework`
- `application.properties`

    ```java
    spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
    spring.datasource.url=jdbc:mysql://localhost:3306/test_db
    spring.datasource.username=root
    spring.datasource.password=0000
    
    #MyBatis
    mybatis.type-aliases-package=com.example.springmybatis.domain
    mybatis.configuration.map-underscore-to-camel-case=true
    logging.level.com.example.springmybatis.repository.mybatis=trace
    ```

- `resource`에 `xml` 파일 만들 때,
  1. 패키지명은 아티팩트 별로 / 로 구분
  2. Mapper.xml 파일 만들기
  3. xml파일에 코드 삽입
    - `<mapper namespace=”repository의 파일 경로 + 맵핑시킬 Mapper 이름”>`
    - `<select id=”Mapper에 일치하는 메서드명” resultType=”타입” >`
    - `<delete / update id=””>`
    - `<insert id=”” (useGeneratedKeys=”자동 생성 기능 유무” keyProperty=”자동 생성 컬럼”)>`

      ```xml
      <?xml version="1.0" encoding="UTF-8"?>
      <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
              "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
      <mapper namespace="com.barabam.springjdbcstudy.repository.StudentMapper">
      
              <select id="findStudentsByName" resultType="Student">
              select name, age, address
              from students
              where name = #{name}
          </select>
      
          <select id="findAllBy" resultType="Student">
              select name, age, address
              from students
              <where>
                  <if test="name != null and name != ''">
                      and name like concat('%', #{name}, '%')
                  </if>
                  <if test="upperAge != null">
                      and age &lt;= #{upperAge}
                  </if>
              </where>
          </select>
      
          <insert id="insertStudent" useGeneratedKeys="true" keyProperty="id">
              insert into students (name, age, address)
              values (#{name}, #{age}, #{address})
          </insert>
      
          <update id="updateStudent">
              update students
              set name=#{updateParam.name},
                  age=#{updateParam.age},
                  address=#{updateParam.address}
              where id = #{id}
          </update>
      ```

- 맵핑 순서
  1. url 검색
  2. controller에서 일치하는 url 맵핑
  3. service
  4. repository → mapper
  5. mapper.xml
  6. Database 연결
  7. Domain 맞춰서 출력

# JPA

- ORM 기술의 표준으로 인터페이스 형태
- Hibernate - JPA의 구현체
- Spring data jpa - 편하게 사용할 수 있는 JPA 기술
  - 여러 메서드를 포함
  - 알아서 쿼리를 만들어 줌
  - 페이징 처리 기능을 가지고 있음
  - 스프링 데이터의 인터페이스인 `PagingAndSortingRepository` 를 상속받아 `JpaRepository<엔티티, Long >`인터페이스를 만들었음
  - 메서드: `save() / findById() / findAll() / deleteById()` 등

## ORM 기술

- ORM: Object Relational Mapping, 자바의 객체와 데이터베이스를 연결하는 프로그래밍 기법
  - 데이터베이스의 값을 마치 객체처럼 사용 가능
  - 객체와 데이터베이스를 연결해 자바 언어로만 데이터베이스를 다룰 수 있게 하는 도구
- 장점
  - SQL을 직접 작성하지 않고 사용하는 언어로 데이터베이스에 접근 가능
  - 객체지향적으로 코드를 작성할 수 있기 때문에 비즈니스 로직에만 집중 가능
  - 데이터베이스 시스템이 추상화 되어있기 때문에 데이터베이스 시스템에 대한 종속성이 줄어듦
  - 맵핑하는 정보가 명확하기 때문에 ERD에 대한 의존도를 낮출 수 있고 유지보수에 유리
- 단점
  - 프로젝트의 복잡성이 커질수록 사용 난이도도 올라감
  - 복잡하고 무거운 쿼리는 ORM으로 해결 불가능

## 사용 예제

- 종속성 추가: `Spring Data JPA`
- `application.properties`

    ```java
    spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
    spring.datasource.url=jdbc:mysql://localhost:3306/test_db
    spring.datasource.username=root
    spring.datasource.password=0000
    
    #JPA
    spring.jpa.show-sql=true
    spring.jpa.properties.hibernate.format_sql=true
    ```

- Domain

    ```java
    package com.barabam.springjpastudy.domain;
    
    import jakarta.persistence.Column;
    import jakarta.persistence.Entity;
    import jakarta.persistence.GeneratedValue;
    import jakarta.persistence.GenerationType;
    import jakarta.persistence.Id;
    import lombok.Getter;
    
    @Entity    // 클래스를 엔티티로 사용하기 위한 어노테이션 -> 클래스 이름과 동일한 테이블이 매핑됨 -> 이를 원치 않을 경우, (name="") 사용
    @Getter
    public class Students {
    
      @Id         // PK 설정
      @GeneratedValue(strategy = GenerationType.IDENTITY) // 숫자 자동 증가 기능
      private Long id;
    
    	// 테이블에 해당하는 열 속성이 있음을 나타내는 어노테이션 
      @Column private String name;
    
      @Column private int age;
    
      @Column private String address;
    }
    
    ```

- Controller

    ```java
    package com.example.springdatajpaexample.controller;
    
    import com.example.springdatajpaexample.domain.Students;
    import com.example.springdatajpaexample.service.StudentService;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.PathVariable;
    import org.springframework.web.bind.annotation.RestController;
    
    import java.util.List;
    
    @RestController
    public class StudentController {
        private final StudentService studentService;
    
        public StudentController(StudentService studentService) {
            this.studentService = studentService;
        }
    
        @GetMapping("/students")
        public List<Students> getAllStudents() {
            return studentService.selectAllStudents();
        }
    
        @GetMapping("/students/{name}")     // GET http://localhost:8080/students/이름
        public List<Students> getAllStudentsByName(@PathVariable String name) {
            return studentService.selectAllByName(name);
        }
    }
    ```

- Service

    ```java
    package com.example.springdatajpaexample.service;
    
    import com.example.springdatajpaexample.domain.Students;
    import com.example.springdatajpaexample.repository.StudentRepository;
    import org.springframework.stereotype.Service;
    
    import java.util.List;
    
    @Service
    public class StudentService {
        private final StudentRepository studentRepository;
    
        public StudentService(StudentRepository studentRepository) {
            this.studentRepository = studentRepository;
        }
    
        public List<Students> selectAllStudents() {
            return studentRepository.findAll();
        }
    
        public List<Students> selectAllByName(String name) {
            return studentRepository.findByName(name);
        }
    }
    ```

- Repository

    ```java
    package com.example.springdatajpaexample.repository;
    
    import com.example.springdatajpaexample.domain.Students;
    import org.springframework.data.jpa.repository.JpaRepository;
    import org.springframework.stereotype.Repository;
    
    import java.util.List;
    
    @Repository
    public interface StudentRepository extends JpaRepository<Students, Long> {
    
        List<Students> findByName(String name);
    }
    ```

- 맵핑 순서
  1. url 검색
  2. controller에서 일치하는 url 맵핑
  3. service
  4. repository extends JpaRepository
  5. Database 연결
  6. Domain 맞춰서 출력