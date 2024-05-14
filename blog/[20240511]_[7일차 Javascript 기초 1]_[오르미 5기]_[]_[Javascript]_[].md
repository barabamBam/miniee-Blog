모든 출처: 이스트소프트 백엔드 개발자 오르미 5기

# Javascript

- 1995년 넷스케이프사에서 웹페이지에서 동작하는 프로그램을 만들기 위해 만들어진 프로그래밍 언어
- 웹브라우저가 해석해서 실행할 수 있는 프로그래밍 언어(이 외에 웹어셈블리가 존재)
- HTML, CSS를 프로그래밍적으로 제어 가능
- 브라우저 환경 외 게임프로그래밍, 서버프로그래밍에서도 쓰여짐
- 표준명칭: ECMAScript(ECMA-262)
- 사용 방법

```html
<!-- HTML 태그 내 삽입 -->
<button onclick="window.alert('hello world');">hello</button>

---------------------------------------------
<!-- script 태그 이용하여 삽입 -->
<body>
    <script>
        window.alert('hello world!');
    </script>
</body>

---------------------------------------------
<!-- 외부 파일 로드 -->
<body>
    <script src="test.js"></script>
</body>

---------------------------------------------
<!-- 브라우저 콘솔창 이용 Crtl + Shift + i -->
```

## 동적인 웹❓ (↔ 정적인 웹)

- 사용자 **상호작용에 따라 변경**되는 웹사이트(정적인 웹과 달리 모든 방문자에게 동일한 내용을 표시하지 않음)
- 상황, 시간, 요청 등에 따라 동적으로 변화

  > * 출처: https://cloud.google.com/solutions/dynamic-website?hl=ko, https://blog.naver.com/sincc0715/221899757173
>
- 동적인 웹을 위해 자바스크립트가 할 수 있는 일은?
  1. 데이터 저장
     - 저장 공간: var, let, const
     - 저장 형태: 숫자, 문자열, 빈 값(null, undefined), boolean, 배열, 객체
     - 코드 뭉치: 함수
  2. 값 계산: 사칙연산, 논리연산, 조건문, 반복문, 자료형의 내장함수
  3. 결과 반영
     - DOM(Document Object Model) API: HTML 문서의 구조를 나타내는 표기법
     - BOM(Browser Object Model) API: 웹 브라우저 자체를 객체들로 표현하는 방식

        ![출처: https://it-eldorado.tistory.com/58, https://sanctacrux.tistory.com/2027](https://prod-files-secure.s3.us-west-2.amazonaws.com/e8f11927-b70c-4524-9227-a3efac08e7aa/0a2d1b73-0051-47df-a28a-7ca380a05ebb/Untitled.png)

        출처: https://it-eldorado.tistory.com/58, https://sanctacrux.tistory.com/2027

  4. 다른 컴퓨터와 통신: Ajax

     : 웹 서버와 비동기적으로 데이터를 교환하고 조작을 도와주는 웹 개발 기법 → 비동기적: 웹페이지를 리로드하지 않고 데이터를 불러오는 방식

       > 출처: https://ko.wikipedia.org/wiki/Ajax, [https://velog.io/@surim014/AJAX란-무엇인가](https://velog.io/@surim014/AJAX%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80)
>

## 변수

- 변할 수 있는 정보라는 뜻으로, 프로그램 제작 시 필요한 **데이터를 보관할 공간**의 역할(like 네임택)
- 변수명 정할 때 주의 사항❗
    - 변수이름은 $, _ 를 제외한 공백, 특수문자, 구두점(글의 여러 가지 경계를 구분하기 위해 사용되는 반점(,), 온점(.), 물음표(?) 등등…) 사용 ❌
    - 첫 글자 숫자, 예약어와 동일한 단어 사용 ❌
    - 대소문자 구별
    - 유니코드 문자 가능(한글 가능)
- 특징
    - 변수 선언 키워드: var, let 변경 ⭕ / const 변경 ❌
    - 키워드는 변수 선언 시 한번만 사용
    - var 키워드는 초기화 필요 없고 재할당 가능, 키워드 생략 가능 (단, 엄격 모드 strict mode 빼고)

    ```jsx
    'use strict';  // 자바스크립트를 엄격모드에서 실행
    
    valueA;        // 변수 키워드 생략은 엄격모드에서 에러발생!
    var valueA;
    var valueA;
    let valueA;    // 재정의로 인한 에러발생!
    const my_name; // 초기화가 없어서 에러발생!
    ```

    - let과 const 키워드는 블록-레벨 선언으로, 선언된 코드 블록 밖에서 접근 및 재정의 불가능
    - const는 반드시 초기화 필요

    ```jsx
    valueA = 1;
    const my_name ="WADE";
    
    if(true){ // -- 코드블록의 시작입니다. -- //
    
    	let valueB = 'Hello!';
    	const my_name ="WADE";       // 코드블록 밖의 my_name과 별개의 상수입니다.
    
    } // -- 코드블록의 끝입니다. -- //
    
    valueB = 'nice to meet you!';  // 변수 정의 이전에 값을 할당 할 수 없습니다!
    let valueB = 'Hi!';            // 코드블록 안의 valueB와 별개의 변수입니다.
    ```

    <aside>
    💡 **const 사용 이유**

    - **`let`**은 재할당이 가능한 변수를 선언할 때 사용
    - **`const`**는 재할당이 불가능한 상수를 선언할 때 사용
    - **`let`** 변수와 **`const`** 변수는 블록 스코프를 가지므로, 변수를 선언한 블록 안에서만 유효합니다.

    <aside>
    🤔 블록 스코프 (block scope)
    여러줄의 코드를 하나로 묶어주는 단위인 블록( { } ) 안에서만 유효한 코드의 범위

    </aside>

  그러나 **`const`**를 사용하는 것이 더 좋은 경우가 많습니다.

  1. 의도하지 않은 값의 변경을 방지할 수 있습니다.

       **`const`**를 사용하면 변수의 값이 한 번 할당되면 변경될 수 없습니다. 따라서 의도하지 않은 값의 변경을 방지할 수 있습니다. 이것은 코드의 예측 가능성을 높이며, 따라서 버그를 줄일 수 있습니다.

  2. 가독성을 높일 수 있습니다.

       **`const`**를 사용하면 다른 개발자들이 변수의 값이 변경될 가능성이 없다는 것을 빠르게 인지할 수 있으며, 반드시 초기화를 해야하기 때문에 어떤 데이터가 사용되는지 초기에 확인 할 수 있습니다. 이것은 코드의 가독성을 높이고 유지 보수성을 향상시킵니다.


    그러나 변수의 **가리키는 값**이 반드시 변경되어야 하는 경우에는 **`let`**을 사용해야 합니다. 예를 들어 +, - 같은 연산자에 의해 할당된 값이 변경되야 할 경우가 있습니다. 따라서 **`const`**와 **`let`**을 적절하게 사용하여 코드를 작성하는 것이 중요합니다.
    
    ❗ 이런 경우는 변경으로 치지 않아요
    
    아래와 같이 배열 값이 변경이 되더라도 Error가 나지 않는 이유는 값이 추가되더라도 values가 가리키고 있는 값은 그대로 해당 배열이기 때문에 그렇습니다.
    
    ```jsx
    const values = []; // 1번
    
    values.push(10); // 2번
    ```
    
    </aside>


## 함수

- 반복적으로 필요한 코드를 하나로 묶어 재사용 할 수 있도록 만든 것
- 구조

```jsx
function 함수이름(parameter1, parameter2...) { // 함수의 선언
    // 실행코드... 
    // return을 만나면 반환 값을 전달하며 함수가 종료됨
    return 반환값
}

함수이름(argument1, argument2...) // 함수의 호출
```

- 구성 요소

| 용어 | 번역 | 의미 |
| --- | --- | --- |
| Parameter | 매개변수 | 함수와 메서드가 전달받은 값 |
| Argument | 전달인자, 인자, 인수 | 함수와 메서드에 전달한 값 |
- 예시

```jsx
function 안녕(파라미터){
    console.log(파라미터);
    console.log('hello');
    return 100;
}

let 아규먼트 = 1000;
안녕(아규먼트);
console.log(안녕(아규먼트) + 안녕(아규먼트));
```

```jsx
// 2번
function 안녕(){
    console.log('hello');
}

안녕();
console.log(String(안녕()) + String(안녕()));
```

```jsx
// 3번
function 안녕(){
    console.log('hello')
    return 10
}

안녕()
console.log(String(안녕()) + String(안녕()))
```

```jsx
// 4번
function 안녕(){
    console.log('hello')
    console.log('hello')
    console.log('hello')
    return
    console.log('hello')
    console.log('hello')
    console.log('hello')
}

안녕()
```

---

** flex 참고 링크

[Flexbox | MDN](https://developer.mozilla.org/ko/docs/Learn/CSS/CSS_layout/Flexbox)

[Flexbox Froggy](https://flexboxfroggy.com/#ko)

[flexngrid](https://flexngrid.com/)