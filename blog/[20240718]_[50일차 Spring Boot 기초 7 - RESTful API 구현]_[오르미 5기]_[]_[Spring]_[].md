모든 출처: 이스트소프트 백엔드 개발자 오르미 5기

# ResponseEntity

- HTTP 응답을 나타내는 객체
- 응답 본문 `.body()`, 상태코드 `.status()`, 헤더 `.headers()`를 포함하여 완전한 HTTP 응답을 구성할 수 있게 도와줌
  - body - 객체, 문자열, 파일 등 다양한 형태로 제공 가능
  - status - `200, 201` → status에 감싸서 보냄 / `ok, noContent` → 자체로 메서드
  - headers - Content-Type, Cache-Control 등

# 게시판 구현 예시

- BoardPost

    ```java
    package net.chimaek.day0717;
    
    import java.time.LocalDateTime;
    import java.util.ArrayList;
    import java.util.List;
    
    import lombok.AllArgsConstructor;
    import lombok.Getter;
    import lombok.NoArgsConstructor;
    import lombok.Setter;
    
    @Getter
    @Setter
    @NoArgsConstructor
    @AllArgsConstructor
    public class BoardPost {
    
    	private Long id;
    	private String title;
    	private String content;
    	private String author;
    	private LocalDateTime createdAt;
    	private LocalDateTime updatedAt;
    	private List<Comment> comments = new ArrayList<>();
    
    	public void addComment(Comment comment) {
    		comments.add(comment);
    		comment.setBoardPost(this);
    	}
    
    	public void removeComment(Comment comment) {
    		comments.remove(comment);
    		comment.setBoardPost(null);
    	}
    }
    
    ```

- BoardPostDTO

    ```java
    package net.chimaek.day0717;
    
    import java.time.LocalDateTime;
    import java.util.List;
    
    import lombok.AllArgsConstructor;
    import lombok.Getter;
    import lombok.NoArgsConstructor;
    import lombok.Setter;
    
    @NoArgsConstructor
    @AllArgsConstructor
    @Setter
    @Getter
    public class BoardPostDTO {
    	private Long id;
    	private String title;
    	private String content;
    	private String author;
    	private LocalDateTime createdAt;
    	private LocalDateTime updatedAt;
    	private List<CommentDTO> comments;
    }
    ```

- BoardPostController

    ```java
    package net.chimaek.day0717;
    
    import java.util.List;
    
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.http.ResponseEntity;
    import org.springframework.web.bind.annotation.DeleteMapping;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.PathVariable;
    import org.springframework.web.bind.annotation.PostMapping;
    import org.springframework.web.bind.annotation.PutMapping;
    import org.springframework.web.bind.annotation.RequestBody;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.RestController;
    
    @RestController
    @RequestMapping("/api/board-post")
    public class BoardPostController {
    
    	private final BoardPostService boardPostService;
    
    	@Autowired
    	public BoardPostController(BoardPostService boardPostService) {
    		this.boardPostService = boardPostService;
    	}
    
    	@PostMapping
    	public ResponseEntity<BoardPostDTO> createBoardPost(@RequestBody BoardPostDTO boardPostDTO) {
    		BoardPostDTO createdBoardPostDTO = boardPostService.createBoardPost(boardPostDTO);
    		return ResponseEntity.ok(createdBoardPostDTO);
    	}
    
    	@GetMapping
    	public ResponseEntity<List<BoardPostDTO>> getAllBoardPosts() {
    		List<BoardPostDTO> boardPostDTOs = boardPostService.getAllBoardPosts();
    		return ResponseEntity.ok(boardPostDTOs);
    	}
    
    	@GetMapping("/{id}")
    	public ResponseEntity<BoardPostDTO> getBoardPostById(@PathVariable("id") Long id) {
    		BoardPostDTO boardPostDTO = boardPostService.getBoardPostDtoById(id);
    		return ResponseEntity.ok(boardPostDTO);
    	}
    
    	@DeleteMapping("/{id}")
    	public ResponseEntity<Void> deleteBoardPost(@PathVariable("id") Long id) {
    		boardPostService.deleteBoardPost(id);
    		return ResponseEntity.noContent().build();
    	}
    
    	@PutMapping("/{id}")
    	public ResponseEntity<BoardPostDTO> updateBoardPost(@PathVariable("id") Long id, @RequestBody BoardPostDTO updateboardPostDTO) {
    		BoardPostDTO updatedBoardPostDto = boardPostService.updateBoardPost(id, updateboardPostDTO);
    		return ResponseEntity.ok(updatedBoardPostDto);
    	}
    
    	@PostMapping("/{postId}/comments")
    	public ResponseEntity<CommentDTO> createComment(@PathVariable("postId") Long postId, @RequestBody CommentDTO createCommentDTO) {
    		CommentDTO createdCommentDto = boardPostService.createComment(postId, createCommentDTO);
    		return ResponseEntity.ok(createdCommentDto);
    	}
    
    	@DeleteMapping("{postId}/comments/{commentId}")
    	public ResponseEntity<Void> deleteComment(
    		@PathVariable("postId") Long postId,
    		@PathVariable("commentId") Long commentId)
    	{
    		boardPostService.deleteComment(postId, commentId);
    		return ResponseEntity.noContent().build();
    	}
    }
    
    ```

- BoardPostService

    ```java
    package net.chimaek.day0717;
    
    import java.time.LocalDateTime;
    import java.util.ArrayList;
    import java.util.List;
    import java.util.stream.Collectors;
    
    import org.springframework.stereotype.Service;
    
    import lombok.extern.slf4j.Slf4j;
    
    @Service
    @Slf4j
    public class BoardPostService {
    	List<BoardPost> boardPosts = new ArrayList<>();
    	private Long nextPostId = 1L;
    	private Long nextCommentId = 1L;
    
    	@LogExecutionTime
    	public BoardPostDTO createBoardPost(BoardPostDTO boardPostDTO) {
    		BoardPost boardPost = convertToBoardPostEntity(boardPostDTO);
    		boardPost.setId(nextPostId++);
    		boardPost.setCreatedAt(LocalDateTime.now());
    		boardPosts.add(boardPost);
    		return convertToBoardPostDTO(boardPost);
    	}
    
    	public List<BoardPostDTO> getAllBoardPosts() {
    		return boardPosts.stream()
    			.map(this::convertToBoardPostDTO)
    			.collect(Collectors.toList());
    	}
    
    	public BoardPostDTO getBoardPostDtoById(Long id) {
    		log.info("룰루랄라 아이디 찾는 중~~~~");
    		return boardPosts.stream()
    			.filter(post -> post.getId().equals(id))
    			.map(this::convertToBoardPostDTO)
    			.findFirst()
    			.orElseThrow(() -> new IllegalArgumentException("해당하는 아이디의 글을 찾을 수 없습니다."));
    
    	}
    
    	public void deleteBoardPost(Long id) {
    		BoardPost boardPost = findBoardPostById(id);
    		boardPosts.remove(boardPost);
    		log.info("룰루 삭제 성공~~~");
    	}
    
    	private BoardPost findBoardPostById(Long id) {
    		return boardPosts.stream()
    			.filter(post -> post.getId().equals(id))
    			.findFirst()
    			.orElseThrow(() -> new IllegalArgumentException("해당하는 아이디의 글을 찾을 수 없습니다."));
    	}
    
    	public BoardPostDTO updateBoardPost(Long id, BoardPostDTO updateboardPostDTO) {
    		BoardPost boardPost = findBoardPostById(id);
    		boardPost.setTitle(updateboardPostDTO.getTitle());
    		boardPost.setContent(updateboardPostDTO.getContent());
    		boardPost.setUpdatedAt(LocalDateTime.now());
    		return convertToBoardPostDTO(boardPost);
    	}
    
    	private static BoardPost convertToBoardPostEntity(BoardPostDTO boardPostDTO) {
    		BoardPost boardPost = new BoardPost();
    
    		boardPost.setTitle(boardPostDTO.getTitle());
    		boardPost.setContent(boardPostDTO.getContent());
    		boardPost.setAuthor(boardPostDTO.getAuthor());
    
    		if(boardPostDTO.getComments() != null) {
    			boardPostDTO.getComments().forEach(commentDTO -> {
    				Comment comment = convertToCommentEntity(commentDTO);
    				comment.setBoardPost(boardPost);
    				boardPost.addComment(comment);
    			});
    		}
    		return boardPost;
    	}
    
    	private static Comment convertToCommentEntity(CommentDTO commentDTO) {
    		Comment comment = new Comment();
    		comment.setId(commentDTO.getId());
    		comment.setContent(commentDTO.getContent());
    		comment.setAuthor(commentDTO.getAuthor());
    		return comment;
    	}
    
    	private BoardPostDTO convertToBoardPostDTO(BoardPost boardPost) {
    		BoardPostDTO boardPostDTO = new BoardPostDTO();
    		boardPostDTO.setId(boardPost.getId());
    		boardPostDTO.setTitle(boardPost.getTitle());
    		boardPostDTO.setContent(boardPost.getContent());
    		boardPostDTO.setAuthor(boardPost.getAuthor());
    		boardPostDTO.setCreatedAt(boardPost.getCreatedAt());
    		boardPostDTO.setUpdatedAt(boardPost.getUpdatedAt());
    
    		if(boardPost.getComments() != null) {
    			boardPostDTO.setComments(
    				boardPost.getComments()
    					.stream()
    					.map(BoardPostService::convertToCommentDTO)
    					.collect(Collectors.toList()));
    		}
    		return boardPostDTO;
    	}
    
    	private static CommentDTO convertToCommentDTO(Comment comment) {
    		CommentDTO commentDTO = new CommentDTO();
    		commentDTO.setId(comment.getId());
    		commentDTO.setContent(comment.getContent());
    		commentDTO.setAuthor(comment.getAuthor());
    		commentDTO.setCreatedAt(comment.getCreatedAt());
    		return commentDTO;
    	}
    
    	public CommentDTO createComment(Long postId, CommentDTO createCommentDTO) {
    		BoardPost boardPost = findBoardPostById(postId);
    		Comment comment = convertToCommentEntity(createCommentDTO);
    		comment.setId(nextCommentId++);
    		comment.setCreatedAt(LocalDateTime.now());
    		boardPost.addComment(comment);
    		return convertToCommentDTO(comment);
    	}
    
    	public void deleteComment(Long postId, Long commentId) {
    		BoardPost boardPost = findBoardPostById(postId);
    		Comment comment = findCommentById(commentId, boardPost);
    		boardPost.removeComment(comment);
    	}
    
    	private static Comment findCommentById(Long commentId, BoardPost boardPost) {
    		return boardPost.getComments().stream()
    			.filter(comment -> comment.getId().equals(commentId))
    			.findFirst()
    			.orElseThrow(() -> new IllegalArgumentException("댓글을 찾을 수 없습니다."));
    	}
    }
    
    ```

- Comment

    ```java
    package net.chimaek.day0717;
    
    import java.time.LocalDateTime;
    
    import lombok.AllArgsConstructor;
    import lombok.Getter;
    import lombok.NoArgsConstructor;
    import lombok.Setter;
    
    @NoArgsConstructor
    @AllArgsConstructor
    @Setter
    @Getter
    public class Comment {
    	private Long id;
    	private String content;
    	private String author;
    	private LocalDateTime createdAt;
    	private BoardPost boardPost;
    }
    
    ```

- CommentDTO

    ```java
    package net.chimaek.day0717;
    
    import java.time.LocalDateTime;
    
    import lombok.NoArgsConstructor;
    import lombok.Setter;
    import lombok.AllArgsConstructor;
    import lombok.Getter;
    
    @NoArgsConstructor
    @AllArgsConstructor
    @Setter
    @Getter
    public class CommentDTO {
    	private Long id;
    	private String content;
    	private String author;
    	private LocalDateTime createdAt;
    }
    
    ```