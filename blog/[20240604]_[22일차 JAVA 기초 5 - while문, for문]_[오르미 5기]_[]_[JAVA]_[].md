모든 출처: 이스트소프트 백엔드 개발자 오르미 5기

- 목차

# 반복문

## while문

- 조건이 참인 동안 코드 블록을 반복적으로 실행

```java
while (조건) {
    // 조건이 참일 때 실행되는 코드
}
```

- 조건을 true로 하고 무한반복으로 만든 뒤, 특정 조건에서 빠져나오도록 하는 코드를 만들 때 유용
- 반복문이 언제 끝날지 확실하지 않을 때 유용

```java
// num이 5의 배수면 출력하지 않고, 27의 배수가 되면 코드를 끝마치는 코드 예제

int num = 0;
while(true) {
	num++;
	if(num % 27 == 0) break; 
	// break를 이용하면 코드 반복을 끝내고 빠져나옴
	if(num % 5 == 0) continue;
	// continue를 이용하면 다음 코드를 실행하지 않고 코드 반복을 진행 
	System.out.println(num);
}
```

## for문

- 조건식이 참인 동안 코드를 반복적으로 실행
- 초기식에 변수 설정 → 해당 변수로 조건식 실행 → 참인 경우 코드 블록 실행 → 증감식 실행 → 조건에 대하여 거짓일 경우 반복문 종료

```java
for (초기식; 조건식; 증감식) {
    // 조건식이 참인 동안 실행되는 코드
}
```

![코드 실행 순서](https://prod-files-secure.s3.us-west-2.amazonaws.com/e8f11927-b70c-4524-9227-a3efac08e7aa/6f53e744-2a2a-4b47-8f7e-4b98b4e78305/Untitled.png)

코드 실행 순서

- 특정 횟수만큼 반복적으로 코드 실행 시 유용
- 이중 반복문 가능 (반복문 안에 반복문)

```java
// 19단부터 역순으로 구구단 출력 예제
for (int i = 19; i >= 1; i--) {
  for (int j = 19; j >= 1; j--) {
    System.out.println(i + "X" + j + "=" + (i * j));
  }
```

- 향상된 반복문: 기존의 for문 보다 **간결하고 가독성 좋게** 배열이나 컬렉션 요소를 순차적으로 처리하는 반복문, **인덱스 없이** 요소에 직접 접근!

```java
for (요소타입 변수명 : 배열 또는 컬렉션) {
    // 반복할 코드
}
```

```java
// 배열 예시 (합과 평균 구하기)
int[] scores = {1, 30, 20, 80, 15, 20, 30, 22};
int sum = 0;
for (int i : scores) {
  sum += i;
}
System.out.println("총합: " + sum + " 평균: " + sum / scores.length);

// 컬렉션 예시
List<String> fruits = Arrays.asList("apple", "banana", "orange");
for (String fruit : fruits) {
    System.out.println(fruit);
}

```

## break / continue

### break

- 반복문을 즉시 종료하고 빠져나옴

### continue

- 현재 진행 중인 코드 블록에서 `continue` 다음 코드 실행을 멈추고 다음 반복문을 실행

⇒ 각 예시는 위 while문 예시에서 ❗

---

# 피보나치 수열

피보나치 수열이란❓

첫번째 항과 두번째 항의 값이 1이고, 세번째 항부터는 이전 두 항의 값을 더한 값을 가지게 되는 수열

<aside>
💡 피보나치 수열
1
1
2 = 1+1
3 = 2+1
5 = 3+2
8 = 5+3
13 = 8+5
      .
      .
      .

</aside>

```java
int[] arr = new int[10];

// 방법 1
arr[0] = 1;
arr[1] = 1;
for (int i = 2; i < arr.length; i++) {
  arr[i] = arr[i - 1] + arr[i - 2];
}

// 방법 2
Arrays.setAll(arr, i -> (i < 2) ? 1 : arr[i - 1] + arr[i - 2]);

System.out.println("피보나치 수열 출력 Go!");
for (int i = 0; i < arr.length; i++) {
  System.out.println(arr[i]);
}
```