모든 출처: 이스트소프트 백엔드 개발자 오르미 5기

# Javascript

## 함수

- 장점
    1. 재사용성이 높아짐
    2. 유지보수 용이
    3. 구조 파악 용이
- 함수에 전달된 인자가 매개변수 보다 적거나 많아도 에러가 발생 ❌

    ```jsx
    function 함수1 (a, b, c){
        return a + b + c
    }
    
    // 콘솔창의 기능이에요. 마지막 라인에 한하여 console.log()를 찍지 않아도 return값을 console창에 출력해줍니다.
    함수1(10, 20, 30) -> 60
    함수1(10, 20, 50) -> 80
    
    // 필요 이상의 아규먼트를 넣었을 때
    함수1(10, 20, 30, 40) -> 60
    
    // 필요 이하의 아규먼트를 넣었을 때
    함수1(10, 20) -> 30undefined
    ```

- 함수 선언 방법
    - 함수 선언문
    - 함수 표현식
    - 화살표 함수: 이런 방식의 익명 함수 표현식을 다른 언어에서는 람다식이라고 부름
    - 즉시실행함수(IIFE, Immediately Invoked Function Expression):  코드를 캡슐화하여 모듈화된 코드를 작성할 수 있게 도움

    ```jsx
    // 함수 선언문
    function sum(x, y){
      return x + y;
    }
    console.log(sum(10, 20));
    
    ----------------------------------
    // 함수 표현식
    let sumXY = function(x, y){
      return x + y;
    };
    console.log(sumXY(10, 20));
    
    ----------------------------------
    // 화살표 함수
    let sum = (x, y) => x + y
    
    console.log(sum(10,20));
    
    // 만악 함수 실행시 전달하는 인자가 한 개라면 소괄호를 생략할 수 있습니다.
    let 함수2 = x => x + 10
    
    ----------------------------------
    // 즉시 실행 함수
    (function() {
      console.log('이 함수는 만들어지자마자 바로 실행됩니다!');
    })();
    ```
    
    <pre>
    💡 <b>구문(Statement)과 표현식(Expression)</b>
    <b>구문</b>은 자바스크립트 명령문으로, 어떤 작업을 수행하기 위한 코드 블록이라 할 수 있습니다. 예를 들어 우리가 뒤에서 배울 if문, switch문, for문 등이 여기에 포함됩니다.
    <b>표현식</b>은 값으로 평가될 수 있는것을 의미합니다. 숫자나 문자열 같은 값 자체나 5 < 3 와 같은 비교 연산자등이 여기에 해당합니다. 위에서 함수표현식이라 함은 함수를 값 처럼 사용하기 때문에 그렇게 표현합니다.

    </pre>


## 조건문

- 조건에 따라 실행되는 코드

### if 문

```jsx
if (조건식) {
  // 조건식이 참일 때 실행될 코드
} else if(조건식) {
	// 위에 if 문의 조건이 거짓일 때 판별 
	// 이 블록의 조건식이 참일 때 실행
} else {
	// 어느 조건도 거짓일 때 실행
}
```

- 코드가 한줄이면 중괄호 생략 가능
- 특정 코드 구문을 실행하는 문 / 즉, statement

### 삼항연산자(Conditional ternary operator)

```jsx
// 조건식 ? 조건식이 참일 때 실행될 코드 : 조건식이 거짓일 때 실행될 코드
// a 보다 b가 크면, a를 출력하고 아니면 b를 출력
let res = a > b ? a : b;
console.log(res);
```

- 삼항연산자, 삼항조건연산자, 삼항식 등으로도 불림
- 값으로 판단되는 표현식 / 즉, expression

### switch 문

```jsx
switch (표현식) { //표현식은 case문의 값과 비교할 변수 / 같은지 비교함
  case 값1:
    // 값1에 대한 실행 코드
    break; //break가 없으면 모든 코드가 실행됨
  case 값2:
    // 값2에 대한 실행 코드
    break;
  ...
  default:
    // 모든 case에 해당하지 않을 때 실행될 코드, 필수 아
    break;
}
```

- 예시

    ```jsx
    switch (new Date().getDay()) { //getDay(): 요일을 숫자로 추출함
      case 1:
        document.write('월요일입니다.');
        break;
      case 2:
        document.write('화요일입니다.');
        break;
      case 3:
        document.write('수요일입니다.');
        break;
      case 4:
        document.write('목요일입니다.');
        break;
      case 5:
        document.write('금요일입니다.');
        break;
      default:
        document.write('금금요일입니다. 주말이 뭐죠?');
        break;
    }
    ```


## 반복문

### for 문

```jsx
for(초기화식; 조건식; 증감식) {
	// 조건식이 참일 때 실행되는 실행문;
}
```

- 예시

    ```jsx
    const cars2 = ["BMW", "Volvo", "Saab", "Ford", "Flat", "Audi"];
    let text2 = ''
    for (let i = 0; i < cars2.length; i++) {
        text2 += '<section><h2>' + cars2[i] + '</h2></section>' 
    }
    text2
    // document.body.innerHTML = text2 
    // 위의 코드를 삽입하면 콘솔창이 아닌 페이지에 글자가 입력
    ```


![document.body.innerHTML = text2 ](https://prod-files-secure.s3.us-west-2.amazonaws.com/e8f11927-b70c-4524-9227-a3efac08e7aa/232eed9b-6a26-44b5-9632-e74693b6ab13/Untitled.png)

document.body.innerHTML = text2

---

✍️ `prompt('입력하고 싶은 내용')` 웹페이지에서 사용자의 입력을 받을 때 사용하는 javascript 내장 함수 → 입력받은 데이터는 문자열

✍️ 이름 짓는 방법

케밥 get-hello-world / 스네이크 get_hello_world

---

❗ 이 부분은 공부 도중 제가 알고 있던 지식과 더불어 궁금해져서 찾아본 내용입니다!

✍️ `Number() / parseFloat()` 문자열을 숫자로 바꿔주는 javascript 내장 함수

- Number(string): 문자열을 소수점까지도 숫자로 바꿔주며 숫자로 변환하지 못하는 일반 문자열은 NaN을 리턴
- parseFloat(string, radix): radix는 2~36진수까지 설정 가능, 무조건 숫자가 앞에 있으면 뒤에는 다 무시하고 숫자로 출력해주며 숫자 앞에 오는 공백은 무시, 앞에 문자가 오면 NaN을 리턴
- 즉, Number이 조금 더 엄격한 파싱을 할 수 있다면 parseFloat는 진수를 설정할 수 있다는 장점 존재!
- 소수점 없이 정수로 출력하려면 `parseInt(string, radix)`