모든 출처: 이스트소프트 백엔드 개발자 오르미 5기


# 객체 Object

## 속성

- 접근 방법

<aside>
🤔 < 객체의 속성 값 접근 방법 >
점 연산자 🆚 대괄호

- 변수명 규칙을 지키지 않으면 점 연산자 사용 불가능!
- 대괄호 사용하기!

```jsx
ex)
const testObj = {
	"1004" : "천사"
}

-> testObj['1004'] // testObj.1004는 오류)
```

- 변수명 규칙 짚고 넘어가기 ⚠️

  [변수명 정할 때 주의 사항❗](https://www.notion.so/7094aafd96b4468a951a696bd46cafed?pvs=21)

</aside>

- 속성 추가: key 값이 존재하지 않거나 새로운 value 값을 추가할 때는 다음과 같이 사용

```jsx
객체이름.key = value;
```

- 속성 삭제

```jsx
delete 객체이름.key;
```

- 존재 여부 확인: in 연산자 사용

```jsx
console.log(key in 객체);
```

## 객체의 메소드

- hasOwnProperty(): 객체가 특정 프로퍼티를 가지고 있는지 확인하여 true / false 로 반환

```jsx
객체이름.hasOwnProperty('속성'));
```

- for in: 객체 내의 속성들 전체 확인 및 수정 시 사용

```jsx
for (const variable in object) {
	// ...
}
// 여기서 const는 계속 새로고침되므로 가능!
// for … in 문 안에서 처리되는 프로퍼티들은 무조건 순서대로는 아님

// 따라서 순서가 중요하면 아래와 같이 일반 반복문 사용! (혼자서는 생각이 안나서 강사님께서 알려주신 코드입니다...ㅎㅎ)
const keys = Object.keys(객체이름);
for (let i = 0; i < keys.length; i++) {
  const key = keys[i];
}
```

- keys(), values(): 배열로 반환해줌

```jsx
// 여기서 Object는 자바스크립트 내장 객체
Object.keys(객체이름)
Object.values(객체이름)
```

# This

- 객체를 가리키는 참조 변수
- 호출되는 위치에 따라 다른 값을 출력
- 특히, 나를 호출한 객체가 없는 경우는 window를 가리킴
- 함수가 만들어질 때가 중요 ❌ → 실행될 때가 중요⭐⭐

<aside>
💡 참조 정리 (벗 예외 존재)

1. 메서드로 호출한 경우 this는 멤버접근연산자 앞의 객체
2. 함수로 호출할 경우 this는 window(node는 global)
3. 화살표 함수의 경우 this는 상위스코프
4. 생성자 함수 혹은 class의 경우 this는 인스턴스

👉 메소드가 아닌 호출 시 전역공간 참조

</aside>

```html
<body>
<button id="btn1">클릭해봐요!</button>
<button id="btn2">클릭해봐요!</button>
<script>
    let myObj = {
        val1: 100,
        func1: function () {
            console.log(this);
        },
    };

    let test = myObj.func1;
    console.log("test() 실행결과: ");
    test(); // window
    console.log("myObj.func1() 실행결과: ");
    myObj.func1(); // myObj

    let button1 = document.getElementById("btn1");
    button1.addEventListener("click", myObj.func1);
    // button1 tag
    let button2 = document.getElementById("btn2");
    button2.addEventListener("click", test);
    // button2 tag
</script>
</body>
```

```html
<script>
    // this example

    function sayName() {
        console.log(this.name);
    }

    var name = 'Hero';
    // 전역으로 선언한 name 변수의 앞에는 window 가 생략되어 있습니다.
    // 때문에 window.name === "Hero" 가 성립합니다.
    let peter = {
        name: 'Peter Parker',
        sayName: sayName
    }

    let bruce = {
        name: 'Bruce Wayne',
        sayName: sayName
    }

    sayName(); // 'Hero'
    peter.sayName(); // 'Peter Parker'
    bruce.sayName(); // 'Bruce Wayne'

    // 위의 예제와 비교
    let bruce2 = {
        name: 'Bruce Wayne',
        sayName: peter.sayName
    };

    bruce2.sayName(); // 'Bruce Wayne'

</script>
```

# 객체지향 프로그래밍

- 프로그래밍 방법론 中 1
- 객체들을 서로 소통하도록 함
- 객체지향 프로그래밍에서 객체란❓ 표현하고자 하는 구체적인 사물을 **추상적**으로 표현
    - 추상화: 필요한 최소한의 정보로 대상을 표현

## 객체 Object

```jsx
// let obj = new Obj;
const Obj = {
	// key: value
	// 함수명: function(){}
}
```

## 생성자 Constructor

- 객체 생성 시 new 연산자와 함께 사용하는 함수
- 같은 생성자로 생성된 객체끼리 같은 프로퍼티와 메서드 공유
- `객체이름 instanceof 생성자` 생성자 함수와 객체의 관계 확인

```jsx
// let obj = new Obj(); obj는 인스턴스
function Obj(){
  // this.key = value
	// this.함수명 = function(){}
	// 여기서 this는 생성자가 만들어낸 객체 / 인스턴스 참조
}
```

## 프로토타입 Prototype

- 객체를 등록할 때마다 새로운 함수를 생성하는 자원 낭비를 해결하기 위해 등장
    - 생성자 혹은 객체: obj.함수 === obj2.함수 ➡️ false
    - 프로토타입: obj.함수 === obj2.함수 ➡️ true
- 특정 객체에 대한 참조 / 어떠한 공간을 가리킴 → 함수를 계속 생성하지 않고 한번만 생성
- 생성자 함수가 인스턴스를 생성하면 그 안에 숨겨진 프로퍼티인 [[Prototype]]이 존재하게 됨
    - 코드 상: `__proto__` 로 표현
    - new 키워드를 통해 생성자 함수의 `prototype`과 인스턴스의 `__proto__` 가 연결됨

```jsx
// let obj = new Obj(); obj는 인스턴스
function Obj(){
  // this.key = value
	// 여기서 this는 생성자가 만들어낸 객체 / 인스턴스 참조
}
Obj.prototype.함수명 = function() {
	// 실행시킬 코드
}
```

<aside>
🤔 잠시만요 … `prototype` 과 `__proto__` 는 다른건가요???
네 맞습니다. prototype은 오직 function안에 존재하는 참조값입니다. `__proto__`  는 객체 안에 존재하는 숨겨진 프로퍼티입니다. 인스턴스는 `__proto__`를 통해 생성자 함수의 `prototype`에 접근하여 필요한 여러가지 값과 메서드를 사용할 수 있습니다.

```jsx
function Test(){};

const obj = new Test();

console.log(obj.__proto__); // {}
console.log(Test.prototype); // {}
console.log(obj.prototype); // undefined
console.log(obj.__proto__ === Test.prototype);  // true 
```

</aside>

## 상속 Inheritance

- prototype을 통해 일어남 👉  prototype을 이용해 메소드를 인스턴스들이 공유하도록 하여 메모리 공간을 절약할 수 있음
- 자바스크립트 타입들은 기본적으로 Object 타입을 상속 받음
- prototype chaining: 자기 자신에게 존재하지 않는 프로퍼티나 메서드를 프로토타입을 통해 추적하는 과정

<aside>
🤔 객체의 메소드는 배열이 쓸 수 있으나 배열의 메소드는 객체가 못쓴다…?
➡️ 배열 생성 시, Array 함수의  `__proto__` 가 Object 함수의  `__proto__` 를 참조하고 있기 떄문에 배열은 Object 함수의 프로퍼티와 메서드 사용 가능

</aside>

- 부모 함수.call(this) = 부모 생성자 함수의 this를 바라봄 / 자식.prototype = Object.create(부모.prototype) = 프로토타입 참조

```jsx
// 부모
function Parent() {
    this.name = '재현';
}
Parent.prototype.rename = function (name) {
    this.name = name;
}
Parent.prototype.sayName = function () {
    console.log(this.name);
}

---------------------------------------
// 자식
function Child() {
    Parent.call(this);
}

Child.prototype = Object.create(Parent.prototype); // 지정된 프로토타입 객체를 갖는 새 객체를 만듭니다.

Child.prototype.canWalk = function () {
    console.log('now i can walk!!');
}
```

- 위의 코드의 단점
    - 코드가 중괄호 내에서 많이 빠져나오면서 예쁘지 않아짐
    - 보편적인 객체지향 코드의 모습과 차이가 있음

## 클래스 Class

- 클래스는 붕어빵 틀!
- 생성자를 이용한 타입 생성의 결과와 같이 인스턴스를 생성함

```jsx
class 클래스 명 {
	// 클래스의 생성자 함수입니다. 하나의 클래스는 하나의 생성자만
	// new 키워드가 호출될때 자동으로 실행
	constructor(){
		this.변수 = 값;
	}
	 // 메소드를 정의, 메소드는 클래스가 생성한 인스턴스를 통해 사용
	함수() {
	}
}
```

- 인캡슐레이션 Encapsulation: 데이터와 해당 데이터를 조작하는 메서드들을 하나의 단위로 묶는 것

<aside>
💡 슈가신텍스 Syntactic sugar
내부적인 동작은 동일하지만 더 보기 좋고 편리하게 개선된 문법

</aside>

<aside>
🤔 **자바스크립트는 프로토타입 기반 객체지향 언어**

</aside>

- class 상속: extends 키워드 사용
    - 상속을 받는 클래스 = 파생 클래스 derived classes
    - 부모 클래스의 프로퍼티 상속은 super 함수 사용 = 부모 생성자 참조 = 파생클래스에 생성자 함수가 없다면 자동 호출됨
    - 생성자 함수에서 this를 사용할 경우 super 함수 뒤에 실행해야함

```jsx
class 파생 클래스 extends 부모 클래스  {
	// 클래스의 생성자 함수입니다. 하나의 클래스는 하나의 생성자만
	// new 키워드가 호출될때 자동으로 실행
	constructor(){
		super();
		this.변수 = 값;
	}
	 // 메소드를 정의, 메소드는 클래스가 생성한 인스턴스를 통해 사용
	함수() {
	}
}
```

- 객체지향 예제

```jsx
// 객체
const me = {
    name: 'minju Kim',
    job: 'home protecter',
    change: function (recent) {
        recent.goingUp();
    }
}

const tall = {
	  height: 160,
	  goingUp: function () {
	      this.height++;
	      console.log(this.height);
	  }
}

--------------------------------------
// 생성자
function Me() {
    this.name = 'minju Kim';
    this.job = 'home protecter';
		this.change = function (recent) {
	    recent.goingUp();
	  }
}

function Tall() {
    this.height = 160;
		this.goingUp = function () {
	    this.height++;
	    console.log(this.height);
	  }
}
--------------------------------------
// 프로토타입
function Me() {
    this.name = 'minju Kim';
    this.job = 'home protecter';
}

Me.prototype.change = function (recent) {
    recent.goingUp();
}

function Tall() {
    this.height = 160;
}

Tall.prototype.goingUp = function () {
    this.height++;
    console.log(this.height);
}

// 생성자와 같음
// const me = new Me()
// const obj = new Tall();
// me.change(obj)

--------------------------------------       
// 클래스
class me {
    constructor() {
        this.name = 'minju Kim';
        this.job = 'home protecter';
    }

    change(recent) {
        recent.goingUp();
    }
}

class tall {
    constructor() {
        this.height = 160;
    }

    goingUp() {
        this.height++;
        console.log(this.height);
    }
}

// let obj = new me;
// undefined
// let obj2 = new tall;
// undefined
// obj.change(obj2);
// 161
// undefined
// obj.change(obj2);
// 162
```