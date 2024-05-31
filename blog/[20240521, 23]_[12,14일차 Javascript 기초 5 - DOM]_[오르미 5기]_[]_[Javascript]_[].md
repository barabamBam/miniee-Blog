모든 출처: 이스트소프트 백엔드 개발자 오르미 5기


# DOM

- HTML 문서의 내용을 트리형태로 구조화하여 웹페이지와 프로그래밍 언어를 연결해줌
- 각 단위를 노드라고 함
- DOM API ( Document Object Model Application Programming Interface)
- 사용 방법

```jsx
// 트리 탐색
document.head
document.body
document.body.childNodes
document.body.childNodes[1]

// 해당하는 Id를 가진 요소에 접근하기
document.getElementById();

// 해당하는 모든 요소에 접근하기
document.getElementsByTagName();

// 해당하는 클래스를 가진 모든 요소에 접근하기
document.getElementsByClassName();

// css 선택자로 단일 요소에 접근하기
document.querySelector("selector");

// css 선택자로 여러 요소에 접근하기
document.querySelectorAll("selector");
```

<aside>
🤔 getElementsBy 🆚 querySelectorAll
- 반환형태
getElementsBy → HTMLCollection
querySelectorAll → NodeList

</aside>

<aside>
🤔 HTMLCollection 🆚 NodeList
- 공통점: 배열과 비슷한 객체 형태 → 인덱스로 각 원소에 접근
- 차이점: DOM 요소의 유형 / 객체 구성 값 변경 여부 / 메소드
HTMLCollection: HTML만 포함, 변경 사항 즉시 반영, 또 다른 기능
NodeList: 모든 유형의 DOM 요소 = text, 주석 포함, 변경 사항 즉시 반영 ❌, forEach 같은 배열 메소드 사용 가능

</aside>

- 제어 명령어

```jsx
// 이벤트 삽입 type = click, submit, mouveover 등
// target.addEventListener( type, listener )

const myBtn = document.querySelector("button");

myBtn.addEventListener('click', function(){
	console.log("hello world");
})

// 클래스 제어
// classList
const myBtn = document.querySelector("button");

myBtn.addEventListener('click', function(){

// blue 라는 클래스의 속성 값을 지정 할 수 있습니다.
	myBtn.classList.add("blue");

	// myBtn.classList.remove("blue");     클래스를 제거합니다.
	// myBtn.classList.toggle("blue");     클래스를 토글합니다. 없으면 넣어주고, 있으면 제거합니다.
	// myBtn.classList.contains("blue");   해당하는 클래스가 있는지 확인합니다.
})
```

- 요소 제어
    - `document.createElement(target)` : target 요소를 생성
    - `document.createTextNode(target)` : target 텍스트를 생성
    - `element.appendChild(target)` : target 요소를 element의 자식으로 넣어줌
    - `element.removeChild(target)` : element의 target 자식 요소를 제거
    - `element.append(target)` : target 요소를 element의 자식으로 넣어줌,  appendChild 와 달리 여러개의 노드를 한번에 삽입 가능하고 텍스트도 삽입 가능
    - `target.remove()` : target 요소를 제거
- element, text 노드 생성
    - `innerHTML`: 요소 내의 모든 내용을 마크업으로 보여줌 (자식 태그가 있으면 태그 그대로 보여줌)

      복잡한 구조도 동적으로 쉽게 생성할 수 있다는 장점이 있으나 사용자가 코드를 입력하도록 사용하면 의도적인 스크립트가 작동될 수 있으니 주의!

    - `innerText`: **렌더링된** 텍스트 콘텐츠를 보여줌, 태그나 display:none을 통해 제거되는 텍스트 등의 텍스트의 형태만 인식
    - `textContent`: 텍스트 콘텐츠를 노드로써 보여줌, css 속성까지 텍스트의 형태로 인식
- 속성 제어
    - `target.style.속성`: 스타일 속성 제어

    <aside>
    🤔 **style 객체의 속성 식별자 규칙**

    - 속성 이름이 한 글자라면 그대로 사용
    - 속성 이름이 여러 단어로 나눠져있는 경우는 카멜케이스 (`background-image` ⇒  `backgroundImage`)
    - `float` 속성의 경우 이미 자바스크립트의 예약어로 존재하기 때문에 `cssFloat`

  style 객체를 통해 설정된 스타일은 CSS inline 스타일과 동일한 가중치를 가집니다. 때문에 CSS를 통해 수정의 여지가 있는 스타일에는 많이 사용되지 않는 편입니다. 이럴경우 classList를 이용한 클래스 제어가 더 효과적

    </aside>

    - `target.getAttribute / target.setAttribute`: 속성에 접근 / 속성 값 수정
    - `<태그 data-key=”value”>`: 객체처럼 사용할 수 있게 만들어줌(콜론, 영어 대문자 사용 불가)
- 인접한 곳에 배치하기

    ```html
    <span class="hi"> Hi! </span>
    
    <script>
    	const hi = document.querySelector('.hi');
    	hi.insertAdjacentHTML('beforebegin', '<span>안녕 </span>');
    	hi.insertAdjacentHTML('afterbegin', '<span>hello </span>');
    	hi.insertAdjacentHTML('beforeend', '<span> nice to meet you </span>');
    	hi.insertAdjacentHTML('afterend', '<span>see you</span>');
    </script>
    
    <!-- begin는 여는 태그를, end는 닫는 태그 
    		결과: 안녕 hello Hi! nice to meet you see you-->
    ```

- DOM안에서 노드 탐색

    ```jsx
    	const cont = document.querySelector(".cont");
    console.log(cont.firstElementChild);  // 첫번째 자식을 찾습니다.
    console.log(cont.lastElementChild);   // 마지막 자식을 찾습니다.
    console.log(cont.nextElementSibling); // 다음 형제요소를 찾습니다.
    console.log(cont.previousSibling);    // 이전 형제노드를 찾습니다.
    console.log(cont.children);           // 모든 자식요소를 찾습니다.
    console.log(cont.childNodes);         // 모든 자식노드를 찾습니다.
    console.log(cont.parentElement);      // 부모 요소를 찾습니다.
    // 자기 자신부터 시작해 부모로 타고 올라가며 가장 가까운 cont 클래스 요소를 찾습니다. 단, 형제요소는 찾지 않습니다.
    console.log(cont.querySelector('strong').closest('.cont').innerHTML);  
    ```

- 이벤트
    - 이벤트 객체: 이벤트에서 호출되는 핸들러에서 이벤트와 관련된 모든 정보를 가지고 있는 매개변수
    - 흐름: 이벤트 발생 시 가장 먼저 이벤트 대상을 찾아가는데 이 때 각 단계에서 마주한 이벤트가 있으면 차례대로 실행함 ⇒ 이러한 과정을 이벤트 전파(event propagation)라고 함
        - 캡처링 단계: window → document → body → 부모에서 자식 순으로 이어지기 때문에 부모에게 이벤트가 있으면 먼저 실행됨
        - 버블링 단계: 캡처링과 역순으로 자식에서 부모 순으로 이어지기 때문에 부모에게 이벤트가 있으면 나중에 실행됨
    - `target`: 이벤트 발생 진원지 정보가 담김, 현재 진행중인 이벤트 요소에 접근
    - `currentTarget`: 이벤트 리스너가 연결된 요소가 참조됨
    - 이벤트 위임: 이벤트 리스너가 없는 요소에 리스너가 있는 것처럼 사용할 수 있게 해주는 것

    ```html
    <article class="parent">
            <ol>
                <li><button class="btn-first" type="button">버튼1</button></li>
                <li><button type="button">버튼2</button></li>
                <li><button type="button">버튼3</button></li>
            </ol>
        </article>
    
        <script>
            const parent = document.querySelector('.parent');
            parent.addEventListener('click', function (event) {
                console.log(event.target);
                if (event.target.nodeName === "BUTTON") {
                    event.target.textContent = "버튼4";
                }
            })
        </script>
    ```

    - this: 이벤트가 연결된 노드를 참조
    - `event.preventDefalut()`: 태그들의 기본 동작 취소
    - `event.stopPropagation()`: 이벤트 전파 막기