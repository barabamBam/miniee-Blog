모든 출처: 이스트소프트 백엔드 개발자 오르미 5기

- 목차

# 연산자

## 이항 연산자

- 산술 연산자: 덧셈 `+`, 뺄셈 `-`, 곱셈 `*`, 나눗셈 `/`, 나머지 `%`
    - int 보다 작은 크기의 피연산자들(byte, char)은 int 타입으로 변환 후 계산
    - 더 큰 타입의 피연산자가 존재 시, 모든 타입을 더 큰 타입에 맞춰 형변환 후 계산 실행
    - 실수타입과 정수타입을 연산 시, 전체 실수 타입으로 바꾸어 계산
    - 정수 타입끼리 계산하여 실수 타입으로 저장을 원할 시, 명시적 형변환 필요
- 문자열 연결 연산자 `+`
- 비교 연산자: `<, >, ≤, ≥, ==, ≠` boolean 타입으로 반환
    - 더 큰 타입의 피연산자가 존재 시, 모든 타입을 더 큰 타입에 맞춰 형변환 후 계산 실행
    - 실수 타입은 float 타입으로 바꾸어 계산하기! (float는 근사값으로 계산하기 떄문)
    - String 타입에 이용 ❌ → String은 힙 영역에 있는 주소 값을 이용해 비교하기 때문
- 논리 연산자: boolean 타입으로 반환, 조건식 연결에 사용
    - AND `&&` x, y 둘 다 = true → true
    - OR `||` x, y 둘 중 하나 = true → true
    - XOR(배타적 논리합) `^` 둘이 서로 다른 값 → true
    - NOT(논리 부정) `!` 값을 반대로
- 비트 연산자: 이진수와 같은 숫자 & int 타입에서 사용
    - 비트 논리 연산자: AND(논리곱)`&`, OR(논리합)`|`, XOR`^`, NOT`~`  ⇒ 설명 위와 같음
    - 비트 이동 연산자(시프트 연산자): 오른쪽 숫자만큼 왼쪽으로 이동(빈자리 0)`<<`, 오른쪽 숫자만큼 오른쪽으로 이동(빈자리 최상위 부호 키트와 동일)`>>`, 오른쪽 숫자만큼 왼쪽으로 이동(빈자리 0)`>>>`

```java
// 산술 연산자 예제

int number1 = 10;
int number2 = 3;

int sum = number1 + number2;
int difference = number1 - number2;
int product = number1 * number2;
int quotient = number1 / number2;
int remainder = number1 % number2;

System.out.println("덧셈: " + sum);
System.out.println("뺄셈: " + difference);
System.out.println("곱셈: " + product);
System.out.println("나눗셈: " + quotient);
System.out.println("나머지: " + remainder);

--------------------------------

// 문자열 연결 연산자 예제

// 문자열 "Hello"와 123이 먼저 연산되어 "Hello123"이 되고,
// 이것을 다시 456과 연산하여 "Hello123456"이 됩니다.
System.out.println("Hello" + 123 + 456);  // Hello123456

// 숫자 123과 456이 먼저 연산되어 579가 되고,
// 이것을 문자열 "Hello"와 연산하여 "579Hello"가 됩니다.
System.out.println(123 + 456 + "Hello");  // 579Hello

--------------------------------

// 비교 연산자 예제

int a = 10;
int b = 15;

System.out.println(a > b); // false
System.out.println(a >= b); // false
System.out.println(a < b); // true
System.out.println(a <= b); // true
System.out.println(a == b); // false
System.out.println(a != b); // true

--------------------------------

// 논리 연산자 예제
 
boolean x = true, y = false, z = false;
if (x && !y) { // t && t
  System.out.println("조건 1 충족");
}
if (!y ^ z) { // t ^ f
  System.out.println("조건 2 충족");
}
if (x || y || z) { // t || f || f
  System.out.println("적어도 하나는 참");
}
if (!x && !y && !z) { // f && t && t
  System.out.println("모두 거짓");
}
  
  // 결과: 
  // 조건 1 충족
  // 조건 2 충족
  // 적어도 하나는 참
--------------------------------

// 비트 연산자 예제

0b01111 & 0b11110 == 01110 // 논리곱 (&)
0b01111 & 0b11110 == 11111 // 논리합 (|)
0b01111 & 0b11110 == 10001 // 배타적 논리합 (^)
~0b01111  == 10000 // 논리 부정 (~)

int num = -8; // 11111000
num << 2 // 11100000 = -32
num >> 2 // 11111110 = -2
num >>> 2 // 00111110 = 62
```

## 삼항 연산자

- 세 개의 피연산자가 있는 연산자
- `조건식 ? true 결과 : false 결과` → 결과로는 연산식도 가능

```java
// 양수, 음수, 0 판독하기 예제
int x = 10;
String res = (x > 0) ? "x는 양수입니다." : (x < 0) ? "x는 음수입니다." : "x는 0입니다.";
System.out.println(res);
```

# 조건문

## if문

- 특정 조건 만족 시 코드를 실행하는 코드

```java
if (조건1) {
    // 조건1이 참일 때 실행되는 코드
} else if (조건2) {
    // 조건1이 거짓이고 조건2가 참일 때 실행되는 코드
} else {
    // 모든 조건이 거짓일 때 실행되는 코드
}
```

- 조건식에는 산술, 비교, 논리연산자 등을 이용할 수 있음

```java
// 가장 큰 수 출력하기 예제

int a = 10;
int b = 20;
int c = 30;

if (a > b && a > c) System.out.println(a);
else if (b > a && b > c) System.out.println(b);
else if (c > a && c > b) System.out.println(c);

```

## switch문

- 다중 분기 처리를 위해 사용
- 특정 변수의 값을 확인 후, 값에 맞춰 코드 블록 실행
- 각 코드 블록에 `break;` 필수!! → 없으면 그 분기 이후 코드 다 실행됨!

```java
switch (변수) {
    case 값1:
        // 변수가 값1과 일치하는 경우 실행할 코드
        break;
    case 값2:
        // 변수가 값2와 일치하는 경우 실행할 코드
        break;
    case 값3:
        // 변수가 값3과 일치하는 경우 실행할 코드
        break;
    default:
        // 변수와 일치하는 어떤 case 문도 없는 경우 실행할 코드
}
```

```java
// 점수에 맞는 성적 출력하기 예제

int score = 57;
  switch (score / 10) {
    case 10, 9:
      System.out.println("A");
      break;
    case 8:
      System.out.println("B");
      break;
    case 7:
      System.out.println("C");
      break;
    case 6:
      System.out.println("D");
      break;
    default:
      System.out.println("F");
      break;
  }
```