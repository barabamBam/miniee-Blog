모든 출처: 이스트소프트 백엔드 개발자 오르미 5기

# AJAX?

- AJAX 이전에는 브라우저 주소창에 특정 URL을 입력하거나 HTML 태그를 이용하여 서버와 통신하였음
- JS 기술을 이용해 **비동기적**으로 서버와 통신할 수 있는 방법을 묶어서 AJAX라고 함

<aside>
🤔 동기 🆚 비동기

동기: 순차 실행
비동기: 어떤 행동이 실행되는데 시간이 걸리면 다른 행동부터 먼저 실행해서 효율성을 높임!

</aside>

## XMLHttpRequest

- 서버와의 비동기 통신을 가능하게 하는 여러 기능들을 가진 자바스크립트 객체

```jsx
// XHR 객체를 생성합니다.
const requestObj = new XMLHttpRequest();
requestObj.open('GET', 'url'); // 요청을 초기화합니다. 통신방법과 요청을 발신할 대상의 주소를 전달합니다.
requestObj.onreadystatechange = () => { // readystate 가 변화하면 실행되는 이벤트리스너 입니다.
		// readystate : 요청을 보내는 클라이언트의 상태를 의미합니다.
    // readystate의 종류
    // 0 (UNSENT) - XHR 객체가 생성되었지만 아직 초기화되지 않았습니다.
    // 1 (OPENED) - open()함수가 호출되어 요청이 초기화되었습니다.
    // 2 (HEADERS_RECEIVED) - send()함수가 호출되었습니다.
    // 3 (LOADING) - 데이터를 다운받는 중 입니다.
    // 4 (DONE) - 통신이 완료되었습니다.
    if (requestObj.readyState == 4 && requestObj.status == "200") {
				// readyState 서버와의 통신상태, 잘 도착했는지 확인 / status 서버의 응답상태, 올바르게 통신이 됐는지 확인
				// 요청 내용이 성공했으면 실행
        const result = requestObj.responseText;

    }
};
requestObj.send(); // 서버로 요청을 보냅니다. send 메소드가 실행되어야만 우리가 위에서 설정한 내용들이 의미를 가지게 됩니다.
```

- callback 지옥
    - 비동기 코드의 결과를 확신할 수 없음
    - 비동기 코드가 실행이 완료되기 전까지 다른 코드의 실행을 멈추지 않음
    - 통신이 끝난 후 다음 함수가 실행되도록 콜백함수로 코드를 작성해야하는데 이 경우 callback 지옥에 빠질 수 있음

```jsx
const total = 비동기통신함수( 
        input,
        통신함수의결과를가공하는함수1 ( 
            result, 
            비동기통신함수2(
                통신함수의결과를가공하는함수2(
                    result, 
                    result2
                )
            ) 
        ) 
    );

// 이렇게 비동기 함수가 끝나기 전에 중간 중간에 필요한 콜백함수를 실행시키며 사용할 수 밖에 없었습니다.
```

- XMLHttpRequest 가 생성하는 인스턴스는 통신의 기능을 수행하는 XMLHttpRequest 객체를 반환

## fetch API

- callback 지옥에서 벗어나기 위한 대체 API
- fetch는 인스턴스를 만들지 않고, 대신 ‘약속’을 반환

### 약속 Promise

- 동기적인 코드로 만드는데 도움을 줌, 어쨌든 약속의 결과가 나올테니 일단 다음 코드 수행해!
    - fulfilled 약속을 이행, 완수
    - resolved 해결된, 결정된 상태
    - rejected 거절된 상태

```jsx
// 커피를 주문하는 프로미스 객체를 생성합니다. 생성자에는 약속을 지키기 위한 resolve와, 약속을 지키지 못했을 때를 대비한 reject 두 가지를 인자로 전달합니다. 
// 프로미스 객체를 생성하는 순간 프로미스 생성자함수의 콜백 함수가 실행됩니다. 이를 실행자(executor)라 부릅니다.
const orderCoffee = new Promise((resolve, reject) => {

  const requestObj = new XMLHttpRequest();
  requestObj.open('GET', 'orderCoffee.txt');
  requestObj.onreadystatechange = () => {
      if (requestObj.readyState === 4) {
          if (requestObj.status === 200) {
              const result = requestObj.responseText;
              // resolve 메소드가 실행되면 then 메소드가 자동으로 호출됩니다.
              resolve(result);
          } else {
              // resolve 메소드 호출이 없는 상태에서 reject 메소드가 실행되면 catch 메소드가 자동으로 호출됩니다.
              reject(new Error(`커피주문이 정상적으로 이뤄지지 않았습니다.: ${requestObj.status}`));
          }
      }
  };
  requestObj.send();

});

// 이 부분에 주목해주세요. then 메소드를 사용하면 비동기 코드를 마치 동기적인 코드처럼 작성할 수 있습니다. 앞에서 작성한 XHR 코드와 비교해보는것도 좋습니다. 

// resolve 메소드가 실행될때 전달된 인자는 then 메소드의 콜백함수의 인자로 전달됩니다.
orderCoffee.then((asyncResult) => {
  console.log(asyncResult);
  console.log('약속이 이루어졌습니다.');
  return asyncResult; 
}).catch((error) => { // then 메소드는 프라미스 객체를 반환하기 때문에 catch 메소드를 이어서 쓰는것이 가능합니다.
// resolve 메소드와 마찬가지로 reject 메소드가 실행될때 전달된 인자는 catch 메소드의 콜백함수의 인자로 전달됩니다.
  console.log(error);
})
```

- fetch 함수 실행 시, 바로 url에서 데이터를 다운받는데 완료되면 fulfilled 상태의 약속이 반환되고, resolve 함수를 자동 호출
- 오류 발생 시, reject 상태 반환 후 reject 함수 자동 호출
- 콘솔에 보이는 Response 는 요청에 대한 종합적인 응답 정보가 저장되어 있는 객체

```jsx
fetch('url')
	.then((response) => {
			// response.ok 는 응답이 성공적(200-299)일 경우 true, 아니면 false를 반환합니다. 
			// fetch는 서버와의 통신(서버에서 실패라는 값이 올 수 있음)이 성공했다면 무조건 resolve 상태의 프로미스를 반환하기 때문에 예외 처리를 해줘야합니다.
	    if (!response.ok) {
	        throw new Error(`HTTP error! Status: ${response.status}`);
	    }
	    return response.json();
	})
	.then((data) => { //response.json()이 data로 들어옴
	    console.log(data);
	    return data;
	})
	// fetch 함수는 네트워크 오류가 발생하면 reject 상태의 프로미스를 반환합니다. 
	.catch((error) => {
	    console.error(error);
	});
```

### async, await

- `async` 어떤 함수든 프로미스 객체를 반환하게 함

```jsx
function test(){
	return Promise.resolve();
}
// 위 아래 동작 동일
async function test() {}
```

- `await`  반드시 async 함수 안에서 사용 가능하며 promise 객체의 상태가 결정될 때 까지 다음 코드를 실행시키지 않고 기다리게 만듦 → 동기적으로 전환, 프로미스 객체를 반환하지 않고 프로미스 객체의 fulfilled 값을 반환

```jsx
async function message() {
    const hello = await new Promise((resolve) => {
        setTimeout(() => {
            resolve('hello');
        }, 100)
    })

    const world = await new Promise((resolve) => {
        setTimeout(() => {
            resolve('world');
        }, 100)
    })
    
	// hello 실행이 끝나야 world가 실행됨
	// 각 함수는 setTimeout()에 의해 1초 뒤에 결과를 보여줌
    console.log(`${hello} ${world}`);
}

message();
```