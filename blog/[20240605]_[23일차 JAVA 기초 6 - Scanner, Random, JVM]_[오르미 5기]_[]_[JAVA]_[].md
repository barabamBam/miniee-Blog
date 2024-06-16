모든 출처: 이스트소프트 백엔드 개발자 오르미 5기

# 라이브러리

- 미리 작성된 코드의 모음 → 개발자가 모든 기능을 직접 구현하지 않아도 사용할 수 있도록 자바가 **미리** 만들어둔 것
- 클래스, 인터페이스, 메서드 등으로 구성
- 예약어 `import`를 이용하여 필요한 클래스를 불러옴

## Scanner

- 사용자에게서 **입력**을 받아오기 위한 클래스
- `import java.util.Scanner`를 통해 불러올 수 있음
- main 메소드 내에서 `Scanner 변수명 = new Scanner(System.in)`을 통해 객체 생성 → `변수명.next타입()`으로 읽어옴
- nextInt() / nextDouble() 등 → **원하는 타입**의 정수 혹은 실수를 입력받음
- nextLine() → 개행(엔터) 전까지의 **문자열**을 읽어들임
    - nextInt() 등의 코드 뒤에 오는 경우 개행 문자를 문자열로 인식할 수 있기 때문에 미리 nextLine()을 이용해 개행문자를 제거해준다!
- Scanner를 다 사용한 후에는 `close()` 를 통해 자원을 해제한다. → 메모리 누수 방지 및 프로그램 성능 향상!

```java
// 영화 티켓 예매 시스템 예제
// Scanner 객체 생성
Scanner scanner = new Scanner(System.in);

// String은 nextLine()으로, int는 nextInt()를 통해 입력받음
System.out.println("당신이 보고싶은 영화의 제목을 입력하세요!");
String movie = scanner.nextLine();
System.out.println("관람하실 인원을 입력하세요.");
int people = scanner.nextInt();
scanner.nextLine();
System.out.println("예매자의 성함을 입력하세요.");
String name = scanner.nextLine();
System.out.println("에매자의 전화번호를 입력하세요.");
String phone = scanner.nextLine();

System.out.println("***** 영화 티켓 예매 정보 *****");
System.out.println("영화 제목: " + movie);
System.out.println("관람 인원: " + people + "명");
System.out.println("예매자 이름: " + name);
System.out.println("예매자 전화번호: " + phone);
System.out.println("총 결제 금액: " + (10000 * people) + "원");

// 다 썼으면 자원 해제!
scanner.close();

```

- `InputMismatchException` 잘못된 타입의 데이터 입력

```java
// Scanner 객체 생성
Scanner scanner = new Scanner(System.in);
int num = scanner.nextLine(); // 디버깅 단계에서 이미 오류
double num2 = scanner.nextInt(); // InputMismatchException 
```

<aside>
💡 스트림 Stream
자바에서 사용자의 입력 및 출력하는 과정을 하나의 흐름으로 보고, 이를 스트림이라고 함
I/O : `System.in`  표준 입력 input / `System.out`  표준 출력 output

</aside>

## Random

- **랜덤으로 숫자**를 추출하기 위한 클래스
- `import java.util.Random` 을 통해 불러올 수 있음
- main 메소드 내에서 `Random 변수명 = new Random()` 을 통해 객체 생성
- `nextInt() / nextLong() / nextFloat() / nextDouble() / nextBoolean()` → 여러 타입의 난수 생성 가능
- 괄호 안에 숫자가 있으면 **0 ~ 해당 숫자 - 1** 까지의 랜덤 숫자 반환

```java
// 숫자 맞추기 게임 예제
// 컴퓨터에서 추출한 숫자를 맞추는 게임 -> 난수 생성(1~100까지 난수를 추출하려면 (100-1)까지의 숫자를 랜덤으로 발생시키고 +1 해야함
Random random = new Random();
int targetNum = random.nextInt(100) + 1;
int num = 0;

// Scanner 객체 생성
Scanner input = new Scanner(System.in);
System.out.println("1~100 사이의 숫자를 맞춰보세요!");
while (true) {
	// 사용자가 맞춰야하니까 입력받음
  num = input.nextInt();
  // 판별하기
  if (num == targetNum) {
    System.out.println("정답입니다!");
    break;
  } else if (num > targetNum) {
    System.out.println("더 낮게 불러봐요!");
  } else {
    System.out.println("더 높게 불러봐요!");
  }
}
// 게임이 끝났으니 자원 해제!
input.close();
```

# JVM (Java Virtual Machine)

- 자바 실행 흐름: 사용자가 `.java` 파일 생성 → 자바 compiler 단계 실행(`javac 자바파일.java` ) → 컴파일러가 바이트코드 (`.class` )로 컴파일하여 파일 생성 → JVM 실행 → 바이트 코드를 한 줄씩 읽고 프로그램 실행(`java 파일명` )
- 이러한 특성때문에 자바를 컴파일 언어이면서 인터프리터(컴퓨터가 자신이 읽을 수 있는 코드로 변환된 언어를 해석하는 과정)의 특성을 지녔다고 봄
- JIT 컴파일러는 자주 실행되는 바이트 코드 부분을 네이티브 머신 코드로 변환하여 실행 속도를 높임
- 자바 바이트코드(.class 파일)을 OS에 특화된 코드로 변환하여 실행시켜줌 → 다양한 운영체제에서 동작할 수 있게 됨, JVM이 OS 위에 있기 때문에 운영체제에 독립적임
- JVM architecture

  ![가상머신의 구조도 (Kernel : 애플리케이션 프로그램이 하드웨어에 접근할 수 있도록 하는 중간다리)](https://prod-files-secure.s3.us-west-2.amazonaws.com/e8f11927-b70c-4524-9227-a3efac08e7aa/764187fd-69d9-4497-ba4e-846afe09dd6e/Untitled.png)

  가상머신의 구조도 (Kernel : 애플리케이션 프로그램이 하드웨어에 접근할 수 있도록 하는 중간다리)

  ![원본: https://gngsn.tistory.com/252](https://prod-files-secure.s3.us-west-2.amazonaws.com/e8f11927-b70c-4524-9227-a3efac08e7aa/515418c7-d10f-4584-a743-a7123dbe49b6/Untitled.png)

  원본: https://gngsn.tistory.com/252

    - **클래스 로더 시스템 Class Loader System** : 클래스 파일을 **메모리에 로드** (이 떄, 필요한 다른 클래스들도 함께 로드)
    - **실행 엔진 Execution Engine**: 로드된 바이트 코드(클래스 파일)를 실행 → **인터프리터**가 바이트 코드 한 줄 씩 읽고 실행 → **JIT**가 반복되는 코드를 기계어로 컴파일하여 직접 실행 → **가비지 컬렉터**가 생성된 객체들 중 사용하지 않는 객체의 자원을 해제
    - **런타임 데이터 영역 Runtime Data Area**: 프로그램 실행에 필요한 메모리 부분
        - **메서드 영역**: 클래스 정보, 상수, static 변수 등이 저장 → 다른 클래스에서도 사용 가능한 데이터 저장
        - **힙**: 객체와 배열이 생성되는 공간(변수가 참조하는 실제 데이터)
        - **스택**: 메서드 호출 시 지역 변수, 매개 변수, 리턴 값, 참조변수 등이 저장(변수는 스택, 변수는 값 혹은 주소를 저장하고 있음) → 해당 클래스에서만 사용 가능
        - **PC 레지스터**: 현재 실행 중인 명령의 주소 저장
        - **네이티브 메서드 스택**: 자바 외의 언어로 작성된 네이티브 코드 저장

# GC (Garbage Collector)

- 메모리누수(필요 이상의 메모리공간을 점유하는 현상)를 방지하기 위해 메모리를 할당하고 사용 후에 해제해주는 일련의 과정을 지원해줌
- 자바에서 굉장히 중요한 영역
- 프로그램 실행 중 생성된 객체는 힙 영역에 저장 → 더이상 참조되지 않는 객체 존재 시, 힙 영역에서 제거 → 메모리 확보

```java
// 가비지 컬렉터가 필요한 경우

public static void main(String[] args) {
    String str = "Hello, World!"; // 객체 생성 -> 힙 영역에 저장됨
    System.out.println(str);
    str = null; // 주소를 참조하고 있던 str 객체가 null을 참조하면서 더 이상 문자열을 참조하지 않게 되었기 때문에 문자열을 힙 영역에서 제거한다.
}
```

# 래퍼 클래스

- **기본 타입을 객체로** 다룰 수 있도록 해주는 클래스
- 일반적으로 `기본타입 → 앞글자만 대문자` (단, 축약형은 원래대로 `int → Integer` / `char → Character`)
- 기본형은 객체가 아니기 때문에 객체지향언어의 장점을 가질 수 없고(데이터와 메소드), 매개변수로 객체를 요구할 떄 사용할 수 없으며, null 타입을 가질 수 없다는 단점을 보완 가능
- 내용을 비교할 땐, `equals()` 메서드 이용
- 박싱 Boxing: 기본 타입 → 래퍼클래스로 변경
    - `변수타입.valueOf(데이터)` -128~127처럼 자주 사용하는 숫자 값 재사용
    - `new 변수타입(데이터)` 그 외 숫자 호출
- 언박싱 Unboxing: 래퍼클래스 → 기본 타입으로 변경
    - `.타입Value()`
- 오토박싱 & 오토 언박싱: 자바5부터 자동으로 형변환 처리

    ```java
    // 박싱
    Integer boxedI1 = Integer.valueOf(5);
    Integer boxedI2 = Integer.valueOf(5);
    Integer boxedNewI1 = new Integer(5);
    Integer boxedNewI2 = new Integer(5);
    
    System.out.println(boxedI1 == boxedI2);
    System.out.println(boxedNewI1 == boxedNewI2);
    System.out.println(boxedI1.equals(boxedI2));
    System.out.println(boxedI.equals(boxedNewI1));
    System.out.println(boxedNewI2.equals(boxedNewI1));
    
    // 언박싱
    Integer boxedI = Integer.valueOf(5);
    int unboxedI = boxedI.intValue();
    
    System.out.println(boxedI.equals(unboxedI));
    System.out.println(boxedI == unboxedI);
    ```

- 문자열 → 숫자 변환
    - `변수객체.parse변수타입()`: 원시타입으로 변환  like `Integer.parseInt()`
    - `변수객체.valueOf()`: 래퍼 클래스 변환  like `Integer.valueOf()`
- 숫자 → 문자열 변환
    - `변수객체.toString()`: null이라는 문자열은 NullPointException 발생
    - `String.valueOf()`: null이라는 문자열 변환 가능
    - `변수 + “”` (Casting 방식)