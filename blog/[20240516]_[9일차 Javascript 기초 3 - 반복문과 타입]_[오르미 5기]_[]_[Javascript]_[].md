모든 출처: 이스트소프트 백엔드 개발자 오르미 5기

# 반복문

## while 문

```jsx
while (조건식) {
  // 조건식이 참일 때 실행될 코드
  // 증감식 혹은 조건문에서 빠져나갈 수 있도록 코드 넣어주기
}
```

- do-while 문

```jsx
do{
	// 무조건 한번은 실행시킬 코드
} while (조건식) {
  // 조건식이 반복문에 들어온 이후에 평가되기 때문
}
```

- `break` 반복문을 멈추고 빠져나감
- `continue` 아래의 코드를 더이상 실행하지 않고 다음 반복문으로 넘어감

# Type

- 타입(=자료형): 다양한 데이터를 용도에 맞게 쓰기 위해 사용
- 컴퓨터에 저장되는 값은 모두 2진수 형태지만, 결과가 다름
- 자바스크립트에서 자료형 명시는 필수가 아님
- 종류: 원시 타입, 참조 타입 = 객체 타입

## 📝 원시 타입 Primitive Types

- 값이 변경 불가능
- 값을 변수에 저장하거나 전달할 때 값에 의한 전달이 이뤄짐
- 가리키고 있는 값이 실제 메모리에서 저장되어 있는 주소가 복사되어 저장됨
- 종류: string, number, bigint, boolean, undefined, symbol, null

## 📝 객체 타입 Object Types

- 객체의 프로퍼티 = 값 (객체의 상태) + 메서드 (객체의 동작)
- 값 자체가 아닌 값의 참조(위치)가 저장

<pre>
🧐 - 원시타입은 str2입장에서 str1의 값을 바꿀 수 ❌
   참조타입은 str2입장에서 str1의 값을 바꿀 수 ⭕

- 자바스크립트는 원시타입에 autoboxing 이라는 과정을 거쳐 객체타입처럼 사용할 수 있게 함으로써 일관적인 사용 경험을 사용자에게 제공해줌

</pre>

### 1. 배열 Array

- 여러 개의 데이터를 한 변수에 순서대로 저장하는 객체
- 인덱스를 이용하여 데이터에 접근
- 사용 방법

```jsx
// 선언 방법
const arr = [];
const arr = [1, 2, 3];
const arr2 = new Array(4, 5, 6);

// 데이터 접근 방법
console.log(arr[0]); // 1
console.log(arr[1]); // 2
console.log(arr[2]); // 3
console.log(arr[3]); // undefined

// 길이 확인
console.log(arr2.length); // 3

// 배열 안의 배열
// 1차원 배열 = 벡터
// 2차원 배열 = 매트릭스, 행렬
// 3차원 배열 = 텐서
const arr2 = [
  [1, 2],
  [3, 4],
  [5, 6]
];
console.log(arr[0][0]);
console.log(arr[2][1]);

const arr3 = [
  [
    [1, 2],
    [3, 4]
  ],
  [
    [5, 6],
    [7, 8]
  ]
];
console.log(arr[0][1][0]);
console.log(arr[1][0][1]);
```

- 배열의 메소드
    1. push / pop
        - push 마지막 요소 추가
        - pop 마지막 요소 꺼내기
    2. shift / unshift
        - shift 첫번째 요소 꺼내기
        - unshift 첫번째 위치에 추가
    3. splice
        - splice(인덱스, 삭제할 요소의 개수, 추가할 요소)
        - 배열의 요소 추가/제거/교체
    4. slice
        - slice(시작 인덱스, 마지막 인덱스+1)
        - 요소 추출, 두번째 인자에 바로 이전 요소까지 추출
    5. sort
        - 사전식으로 정렬이 이루어짐
        - 숫자는 문자열로 전환 후 유니코드로 변환하여 정렬하므로 비교 함수 사용
        - 비교 함수의 두 인자 a, b를 비교해서(즉, 빼서) 0보다 작은 수 즉, 음수가 나오면 a를 앞으로 위치하고, 두 인자를 뺐는데 0보다 큰 양수가 나오면  b를 앞으로 위치,  0이 나오면 위치를 변경하지 않음
    6. forEach
        - forEach(function(변수명, 인덱스, 배열){}); → 인덱스와 배열 생략 가능
        - 배열의 각 요소를 순환하면서 각 요소에 대해 주어진 함수 실행
        - 다른 배열을 만들거나, 요소 삭제, 값 변경 등에 사용
    7. map
        - map(function(변수명, 인덱스, 배열){}); → 인덱스와 배열 생략 가능
        - 배열의 각 요소에 대해 주어진 함수를 실행하고, 그 결과를 새로운 배열로 반환
        - 객체에서 데이터를 뽑는 형태로도 사용

    <pre>
    🤔 forEach 🆚 map : 전자는 반환값이 ❌, 후자는 반환값이 ⭕

    ```jsx
    // forEach
    const arr = ['참외', '키위', '감귤'];
    const arr2 = arr.forEach(item => item);
    console.log(arr2);
    VM503:3 undefined
    
    // map
    const arr = ['참외', '키위', '감귤'];
    const arr2 = arr.map(item => item);
    console.log(arr2);
    VM486:3 (3) ['참외', '키위', '감귤']
    ```

    </pre>

    1. filter
        - filter(변수명 ⇒ 조건);
        - 기존 배열에서 특정 조건을 만족하는 요소 추출 후 새로운 배열 생성
        - 특정한 숫자나 날짜 등의 필요한 정보를 가진 원소만 추출할 때 매우 유용함

    <pre>
    🤔 map 🆚 filter
        <code>
        ```jsx
        // map
        const arr11 = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
        const newArr = arr11.map(el => el % 2 === 0);
        console.log(newArr);
        VM209:4 (10) [false, true, false, true, false, true, false, true, false, true];
        
        // filter
        const arr11 = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
        const newArr = arr11.filter(el => el % 2 === 0);
        console.log(newArr);
        VM197:4 (5) [2, 4, 6, 8, 10]
        ```
        </code>
    </pre>

    1. includes
    - 요소가 포함이 되어 있으면 true 아니면 false를 반환

    - 메소드 전체 예시

    ```jsx
    // push 마지막 요소 추가 & pop 마지막 요소 꺼내기
    const arr = [1, 2, 3];
    arr.push(4);
    console.log(arr); // [1, 2, 3, 4]
    arr.pop();
    console.log(arr); // [1, 2, 3]
    
    //shift 첫번째 요소 꺼내기 & unshift 첫번째 위치에 추가
    const myArray = ["사과", "바나나", "수박"];
    myArray.shift();
    console.log(myArray);
    myArray.unshift("오이", "배");
    console.log(myArray);
    
    // splice(인덱스, 삭제할 요소의 개수, 추가할 요소)
    const arr = [1, 2, 3];
    arr.splice(1, 0, 4);
    console.log(arr); // [1, 4, 2, 3]
    arr.splice(2, 1, 5);
    console.log(arr); // [1, 4, 5, 3]
    
    // slice(시작 인덱스, 마지막 인덱스+1) 
    const myArray = ["apple", "banana", "cherry", "durian", "elderberry"];
    console.log(myArray.slice(1, 4));
    console.log(myArray.slice());
    console.log(myArray.slice(0, 10));
    
    // sort - 사전식 정렬
    const avengers = ['아이언맨', '스파이더맨', '헐크', '토르'];
    console.log(avengers.sort());
    
    // sort - 숫자 정렬
    const num3 = [13, 9, 10];
    num3.sort((a, b) => a - b);
    
    // forEach 예제
    const avengers = ['spiderman', 'ironman', 'hulk', 'thor'];
    
    const newAvengers = [];
    avengers.forEach((item) => newAvengers.push('💖' + item + '💖'));
    
    // map 예제
    const arr = [1, 2, 3];
    const newArr = arr.map(function (item, index) {
        return item * index;
    });
    
    console.log(newArr);
    
    // map 예제 the most
    const data = [
        {
            "_id": "642ba3980785cecff3f39a8d",
            "index": 0,
            "age": 28,
            "eyeColor": "green",
            "name": "Annette Middleton",
            "gender": "female",
            "company": "KINETICA"
        },
        {
            "_id": "642ba398d0fed6e17f2f50c9",
            "index": 1,
            "age": 37,
            "eyeColor": "green",
            "name": "Kidd Roman",
            "gender": "male",
            "company": "AUSTECH"
        },
        {
            "_id": "642ba39827d809511d00dd8d",
            "index": 2,
            "age": 39,
            "eyeColor": "brown",
            "name": "Best Ratliff",
            "gender": "male",
            "company": "PRISMATIC"
        }
    ];
    
    const ages = data.map((item) => item.age);
    
    // filter 예제
    const arr11 = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
    const newArr = arr11.filter(el => el >= 6);
    
    console.log(newArr);
    
    // includes
    const arr1 = ['hello', 'world', 'hojun']
    arr1.includes('world')
    
    const arr1 = ['hello', 'world', 'hojun']
    arr1.includes('leehojun')
    
    const arr1 = ['hello', 'world', 'hojun']
    arr1.includes('jun')
    ```


### 2. 객체 Object

- 여러개의 데이터를 한 변수에 저장할 수 있는 자료형
- 배열과 달리 키-값 쌍으로 데이터에 접근 가능
- 객체 이름 = { key: value, key: value, … }
- 키+값 = 자산 properties / 프로퍼티 값이 함수 = 메소드

```jsx
const babaYaga = {
  name: "John Wick",
  age: 53,
  from: "벨라루스",
	askingHim: function(){
		console.log("Yeah, I'm thinking I'm back!");
	}
	// askingHim(){
	//	console.log("Yeah, I'm thinking I'm back!");
	// } 이렇게도 가능
};
```

- 속성 값 접근 방법

```jsx
babaYaga.name
babaYaga['name']

console.log(`${babaYaga.name} from ${babaYaga.from}`);
console.log(`${babaYaga['name']} from ${babaYaga['from']}`);
```