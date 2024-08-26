모든 출처: 이스트소프트 백엔드 개발자 오르미 5기

# AOP

# 기초 세팅부터 하고가자!

1. IntelliJ 오른쪽 상단 > 데이터베이스 > 데이터 소스 및 드라이버 연결 > MySQL 연결
2. [localhost](http://localhost) 마우스 우클릭 > 새로 작성 > 스키마 생성 (스키마 이름은 `.yml`이랑 맞춰서 생성)
3. application.yml
- application.yml

    ```java
    spring:
      application:
      name: Ormi-Spring
    
      datasource:
    	  // 데이터베이스 연결 URL, 생성한 스키마와 연결
        url: jdbc:mysql://localhost:3306/bookstore 
        // DB 사용자 정보
        username: root
        password: '0000'
        // MySQL 용 드라이버를 사용한다고 지정해줌
        driver-class-name: com.mysql.cj.jdbc.Driver  
        
      jpa:
        hibernate:
    	    // Hibernate의 DDL 자동 생성 옵션 -> update는 엔티티 클래스의 변경사항을 데이터베이스 스키마에 자동으로 반영함을 의미
          ddl-auto: update
        // SQL 쿼리를 콘솔에 출력할지 여부
        show-sql: true
        // 보기좋게 쿼리 포맷팅
        properties:
          hibernate:
            format_sql: true
            // 방언, SQL마다 쿼리가 다름 -> MySQL에 맞게 맞춰줌
            dialect: org.hibernate.dialect.MySQLDialect
    
    server:
      port: 8080
    
    // Hibernate가 생성하는 SQL 로그 레벨
    logging:
      level:
        org.hibernate.SQL: DEBUG
        // SQL 쿼리의 파라미터 값을 로그로 출력
        org.hibernate.type.descriptor.sql.BasicBinder: TRACE
    ```


# Entity

- 테이블과 매핑되는 클래스
- 클래스에 `@Entity`, `@Table(name= "table name")` 어노테이션 추가
- 필드마다 `@Column` 어노테이션 추가
- 기본키 - `@Id`, `@GeneratedValue(strate = GenerationType.IDENTITY)`  추가
- 예시

    ```java
    import jakarta.persistence.*;
    import lombok.Getter;
    import lombok.Setter;
    import lombok.NoArgsConstructor;
    import lombok.AllArgsConstructor;
    
    @Entity
    @Table(name = "books")
    @Getter 
    @Setter 
    @NoArgsConstructor 
    @AllArgsConstructor
    public class Book {
    
        @Id
        @GeneratedValue(strategy = GenerationType.IDENTITY)
        private Long id;
        // id: 책의 고유 식별자. 데이터베이스에서 자동으로 생성됩니다.
    
        @Column(nullable = false)
        private String title;
        // title: 책의 제목. null이 될 수 없습니다.
    
        @Column(nullable = false)
        private String author;
        // author: 책의 저자. null이 될 수 없습니다.
    
        @Column(nullable = false)
        private String isbn;
        // isbn: 국제 표준 도서 번호. 각 책의 고유 식별 번호입니다. null이 될 수 없습니다.
    
        @Column(nullable = false)
        private Double price;
        // price: 책의 가격. null이 될 수 없습니다.
    
        @Column
        private Integer stock;
        // stock: 재고 수량. null이 허용됩니다 (재고가 없을 수 있음).
    }
    ```


# DTO

- 컨트롤러와 서비스가 가능한 직접 테이블에 접근하지 않도록 구현
- 빌더 패턴 사용 권장 `@Builder`
  - 가독성
  - 유연성 - 필요한 필드만 선택적으로 설정 가능
  - 불변성 - 객체 생성 후 변경 불가능, 스레드 안정성 보장
  - 명명된  매개변수 - 매개변수 순서 기억 불필요
  - 중복 코드 감소 - 생성자 여러 개 안 만들어도 됨
  - 유효성 검사
- Entity → DTO / DTO → Entity 변환 로직 추가 (비즈니스 로직 아님)
- 예시

    ```java
    import lombok.Builder;
    import lombok.Getter;
    import lombok.NoArgsConstructor;
    import lombok.AllArgsConstructor;
    
    @Getter
    @NoArgsConstructor
    @AllArgsConstructor
    @Builder
    public class BookDTO {
    
        private Long id;
        private String title;
        private String author;
        private String isbn;
        private Double price;
        private Integer stock;
    
        // Entity를 DTO로 변환하는 정적 메서드
        // 필요없는 필드는 생략 가능
        public static BookDTO fromEntity(Book book) {
            return BookDTO.builder()
                    .id(book.getId())
                    .title(book.getTitle())
                    .author(book.getAuthor())
                    .isbn(book.getIsbn())
                    .price(book.getPrice())
                    .stock(book.getStock())
                    .build(); // 객체 생성 전 필요한 모든 필드가 설정되었는지 확인해줌
        }
    
        // DTO를 Entity로 변환하는 메서드
        public Book toEntity() {
            return new Book(
                    this.id,
                    this.title,
                    this.author,
                    this.isbn,
                    this.price,
                    this.stock
            );
        }
    }
    ```


# Controller

- 주소 매핑과 데이터 반환에 초점
- HTTP 메서드가 포함된 매핑 어노테이션 추가 (`GetMapping`, `PostMapping`, `PutMapping`, `PatchMapping`, `DeleteMapping`)
- 로직을 직접 작성하지 않고 서비스에게 요청 → 서비스 객체 생성 후, `@Autowired` 생성자로 주입 받기
- 각 메서드의 리턴타입은 `ResponseEntity<>`
- 리턴 값은 `ResponseEntity.메세지(반환 데이터)`
- 예시

    ```java
    import java.util.List;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.http.HttpStatus;
    import org.springframework.http.ResponseEntity;
    import org.springframework.web.bind.annotation.DeleteMapping;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.PathVariable;
    import org.springframework.web.bind.annotation.PostMapping;
    import org.springframework.web.bind.annotation.PutMapping;
    import org.springframework.web.bind.annotation.RequestBody;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.RequestParam;
    import org.springframework.web.bind.annotation.RestController;
    
    @RestController
    @RequestMapping("/api/books")
    public class BookController {
        private final BookService bookService;
    
        @Autowired
        public BookController(BookService bookService) {
            this.bookService = bookService;
        }
    
        // CREATE: 새 책 추가
        @PostMapping
        public ResponseEntity<BookDTO> createBook(@RequestBody BookDTO bookDTO) {
            BookDTO createdBook = bookService.createBook(bookDTO);
            // 201 Created 코드와 함께 생성된 책 정보 반환
            return new ResponseEntity<>(createdBook, HttpStatus.CREATED);
        }
    
        // READ: 모든 책 조회
        @GetMapping
        public ResponseEntity<List<BookDTO>> getAllBooks() {
            List<BookDTO> books = bookService.getAllBooks();
            return ResponseEntity.ok(books);
        }
    
        // READ: 특정 ID의 책 조회
        @GetMapping("/{id}")
        public ResponseEntity<BookDTO> getBookById(@PathVariable("id") Long id) {
    		    // 해당하는 책을 찾으면 200 ok 메세지, 없으면 404 NOT FOUND 메세지 반환
            return bookService.getBookById(id)
                .map(ResponseEntity::ok)
                .orElse(ResponseEntity.notFound().build());
        }
    
        // UPDATE: 책 정보 업데이트
        @PutMapping("/{id}")
        public ResponseEntity<BookDTO> updateBook(@PathVariable("id") Long id, @RequestBody BookDTO bookDTO) {
            return bookService.updateBook(id, bookDTO)
                .map(ResponseEntity::ok)
                .orElse(ResponseEntity.notFound().build());
        }
    
        // DELETE: 책 삭제
        @DeleteMapping("/{id}")
        public ResponseEntity<Void> deleteBook(@PathVariable("id") Long id) {
            boolean deleted = bookService.deleteBook(id);
            // 삭제 성공 시 204 NO CONTENT, 실패 시 404 NOT FOUND 반환
            return deleted ? ResponseEntity.noContent().build() : ResponseEntity.notFound().build();
        }
    
        // READ: ISBN으로 책 검색
        @GetMapping("/search/isbn")
        public ResponseEntity<BookDTO> searchBookByIsbn(@RequestParam String isbn) {
            return bookService.getBookByIsbn(isbn)
                .map(ResponseEntity::ok)
                .orElse(ResponseEntity.notFound().build());
        }
    
        // READ: 가격 범위 내의 책 검색
        @GetMapping("/price-range")
        public ResponseEntity<List<BookDTO>> getBooksByPriceRange(
            @RequestParam Double minPrice,
            @RequestParam Double maxPrice) {
            List<BookDTO> books = bookService.getBooksByPriceRange(minPrice, maxPrice);
            return ResponseEntity.ok(books);
        }
        // READ: 특정 재고 수량 이상의 책 검색
        @GetMapping("/in-stock")
        public ResponseEntity<List<BookDTO>> getBooksWithStockGreaterThan(@RequestParam Integer minStock) {
            List<BookDTO> books = bookService.getBooksWithStockGreaterThan(minStock);
            return ResponseEntity.ok(books);
        }
        // READ: 제목에 특정 키워드가 포함된 책 검색
        @GetMapping("/search/title")
        public ResponseEntity<List<BookDTO>> getBooksByTitleContaining(@RequestParam String keyword) {
            List<BookDTO> books = bookService.getBooksByTitleContaining(keyword);
            return ResponseEntity.ok(books);
        }
    }
    ```


# Service

- 실질적인 비즈니스 로직을 구현
- DB 데이터와 연결을 위해 Repository 주입 방식 선택 → `@Autowired` 생성자로 주입받음
- 각 메서드는 `@Transactional` 어노테이션이 달리며, READ 기능을 가진 메서드는 추가로 `(readOnly = true)` 를 적어야함 → 성능 최적화

## `@Transactional`

- 해당 메서드나 클래스에 트랜잭션 기능을 부여함
- 트랜잭션이란 ‘전체가 다 실행되거나 아니면 아예 실행이 안된다’는 원칙으로 수행되는 작업 단위를 의미
- **ACID** - 데이터베이스 트랜잭션이 안전하게 수행된다는 것을 보장하며 데이터베이스 시스템이 안정적이고 예측 가능하게 동작하도록 보장함
  1. **원자성** Atomicity - 트랜잭션이 선언된 부분의 모든 연산이 모두 실행되거나, 아니면 전혀 실행되지 않아야 함
  2. **일관성** Consistency - 트랜잭션이 실행을 성공적으로 완료하면 언제나 일관성 있는 데이터베이스(규칙 준수) 상태로 유지되어야 함
  3. **격리성** Isolation - 동시에 실행되는 트랜잭션들이 서로 영향을 미치지 않도록 격리된 상태여야 함
  4. **지속성** Durability - 트랜잭션이 성공적으로 완료되면, 그 결과가 영구적으로 반영되어야 함

  → 데이터 일관성을 유지시켜주며, 에러 발생 시 롤백이 가능하고, 동시성 제어가 가능해짐

- 동작 방식: 메서드 시작 시 트랜잭션 시작 → 메서드가 정상적으로 종료되면 트랜잭션 커밋 → 예외 발생 시 트랜잭션 롤백
- 비즈니스 로직에 트랜잭션 관리 코드를 섞지 않고, 어노테이션으로 간단히 트랜잭션을 적용할 수 있음

- 예시

    ```java
    import org.springframework.stereotype.Service;
    import org.springframework.transaction.annotation.Transactional;
    import org.springframework.beans.factory.annotation.Autowired;
    import java.util.List;
    import java.util.Optional;
    import java.util.stream.Collectors;
    
    @Service
    public class BookService {
    
        private final BookRepository bookRepository;
    	
    		@Autowired
        public BookService(BookRepository bookRepository) {
            this.bookRepository = bookRepository;
        }
    
    		// CREATE 기능을 수행하는 비즈니스 로직
    		// BookDTO 객체를 받아와 엔티티로 변환한 후 저장 후 다시 DTO 객체로 변환하여 반환해줌
        @Transactional
        public BookDTO createBook(BookDTO bookDTO) {
            Book book = bookDTO.toEntity();
            Book savedBook = bookRepository.save(book);
            return BookDTO.fromEntity(savedBook);
        }
    
    		// READ 기능을 수행하는 비즈니스 로직
    		// 데이터베이스에 저장되어 있는 모든 뎅티티 형식의 데이터들을 뽑아와 DTO 객체로 변환하여 반환해줌
        @Transactional(readOnly = true)
        public List<BookDTO> getAllBooks() {
            return bookRepository.findAll().stream()
                    .map(BookDTO::fromEntity)
                    .collect(Collectors.toList());
        }
        
    		// READ 기능을 수행하는 비즈니스 로직
    		// 데이터 베이스 내에 일치하는 아이디의 데이터가 있으면 엔티티로 뽑아와 DTO 객체로 변환 후 반환해줌
        @Transactional(readOnly = true)
        public Optional<BookDTO> getBookById(Long id) {
            return bookRepository.findById(id)
                    .map(BookDTO::fromEntity);
        }
    		
    		// UPDATE 기능을 수행하는 비즈니스 로직
    		// 데이터 베이스 내에 일치하는 아이디의 데이터가 있으면 엔티티로 뽑아서 수정을 위해 들어온 DTO 객체의 값으로 수정 후 저장 -> 저장된 엔티티 값을 다시 DTO 객체로 변환하여 반환해줌
        @Transactional
        public Optional<BookDTO> updateBook(Long id, BookDTO bookDTO) {
            return bookRepository.findById(id)
                    .map(existingBook -> {
                        existingBook.setTitle(bookDTO.getTitle());
                        existingBook.setAuthor(bookDTO.getAuthor());
                        existingBook.setIsbn(bookDTO.getIsbn());
                        existingBook.setPrice(bookDTO.getPrice());
                        existingBook.setStock(bookDTO.getStock());
                        return BookDTO.fromEntity(bookRepository.save(existingBook));
                    });
        }
    
    		// DELETE 기능을 수행하는 비즈니스 로직
    		// 데이터 베이스 내에 일치하는 아이디의 데이터가 있으면 엔티티로 뽑아와 삭제 후 true 반환, 없으면 false 반환
        @Transactional
        public boolean deleteBook(Long id) {
            return bookRepository.findById(id)
                    .map(book -> {
                        bookRepository.delete(book);
                        return true;
                    })
                    .orElse(false);
        }
    
    		// READ 기능을 수행하는 비즈니스 로직
    		// 데이터 베이스 내에 일치하는 isbn의 데이터가 있으면 엔티티로 뽑아와 DTO 객체로 변환 후 반환해줌
        @Transactional(readOnly = true)
        public Optional<BookDTO> getBookByIsbn(String isbn) {
            return bookRepository.findByIsbn(isbn)
                    .map(BookDTO::fromEntity);
        }
        
        // READ 기능을 수행하는 비즈니스 로직
        // 데이터 베이스 내에 일치하는 범위의 가격을 가진 데이터가 있으면 엔티티로 뽑아와 DTO 객체로 변환 후 반환해줌
        @Transactional(readOnly = true)
        public List<BookDTO> getBooksByPriceRange(Double minPrice, Double maxPrice) {
            return bookRepository.findByPriceBetween(minPrice, maxPrice).stream()
                    .map(BookDTO::fromEntity)
                    .collect(Collectors.toList());
        }
    		
    		// READ 기능을 수행하는 비즈니스 로직
    		// 데이터 베이스 내에 일치하는 재고량 이상의 데이터가 있으면 엔티티로 뽑아와 DTO 객체로 변환 후 반환해줌
        @Transactional(readOnly = true)
        public List<BookDTO> getBooksWithStockGreaterThan(Integer minStock) {
            return bookRepository.findByStockGreaterThanEqual(minStock).stream()
                    .map(BookDTO::fromEntity)
                    .collect(Collectors.toList());
        }
    		
    		// READ 기능을 수행하는 비즈니스 로직
    		// 데이터 베이스 내에 일치하는 타이틀 값을 포함한 데이터가 있으면 엔티티로 뽑아와 DTO 객체로 변환 후 반환해줌
        @Transactional(readOnly = true)
        public List<BookDTO> getBooksByTitleContaining(String keyword) {
            return bookRepository.findByTitleContaining(keyword).stream()
                    .map(BookDTO::fromEntity)
                    .collect(Collectors.toList());
        }
    }
    ```


<aside>
📝 Optional<T>
T 타입 객체를 포장하는 래퍼 클래스
null 값이 올 수 있는 값도 감싸주기 때문에 NullPointException을 방지해줌
단일 객체가 출력될 때 사용함
즉, 위의 코드에서는 READ 기능 수행 중 반환되는 객체가 없을 수도 있기 때문에 사용됨

</aside>

# Repository

- `extends JpaRepository<Entity, ID Type>` extends된 인터페이스 내의 기본적인 CRUD 메서드를 사용할 수 있게 됨
- IntelliJ 우측 상단 JPA 디자이너 (플러그인 설치) > Find Instance / Find Collection 등 이용하면 자동 작성 가능
- 예시

    ```java
    import org.springframework.data.jpa.repository.JpaRepository;
    import org.springframework.stereotype.Repository;
    import java.util.Optional;
    
    // JpaRepository<Book,Long>은 Book 엔티티에 대한 레포지토리이며, 엔티티의 ID 타입이 Long임을 명시함
    @Repository
    public interface BookRepository extends JpaRepository<Book,Long> {
        // ISBN으로 책 찾기
        Optional<Book> findByIsbn(String isbn);
    
        // 가격 범위로 책 목록 찾기
        List<Book> findByPriceBetween(Double minPrice, Double maxPrice);
    
        // 재고가 특정 수량 이상인 책 목록 찾기
        List<Book> findByStockGreaterThanEqual(Integer minStock);
    
        // 제목에 특정 키워드가 포함된 책 목록 찾기
        List<Book> findByTitleContaining(String keyword);
    }
    ```

  ## JPQL (Java Persistence Query Language)

  `@Query` 어노테이션을 사용하여 쿼리 정의

  `@Param` 어노테이션으로 메서드 파라미터와 쿼리의 파라미터를 매핑

    ```java
    import org.springframework.data.jpa.repository.JpaRepository;
    import org.springframework.data.jpa.repository.Query;
    import org.springframework.data.repository.query.Param;
    import org.springframework.stereotype.Repository;
    
    import java.util.List;
    import java.util.Optional;
    
    @Repository
    public interface BookRepository extends JpaRepository<Book, Long> {
    
        // ISBN으로 책 찾기
        @Query("SELECT b FROM Book b WHERE b.isbn = :isbn")
        Optional<Book> findByIsbn(@Param("isbn") String isbn);
    
        // 가격 범위로 책 목록 찾기
        @Query("SELECT b FROM Book b WHERE b.price BETWEEN :minPrice AND :maxPrice")
        List<Book> findByPriceBetween(@Param("minPrice") Double minPrice, @Param("maxPrice") Double maxPrice);
    
        // 재고가 특정 수량 이상인 책 목록 찾기
        @Query("SELECT b FROM Book b WHERE b.stock >= :minStock")
        List<Book> findByStockGreaterThanEqual(@Param("minStock") Integer minStock);
    
        // 제목에 특정 키워드가 포함된 책 목록 찾기
        @Query("SELECT b FROM Book b WHERE b.title LIKE %:keyword%")
        List<Book> findByTitleContaining(@Param("keyword") String keyword);
    
    }
    ```