# 1. 즉시호출함수표현식이란?

- 즉시 실행 함수 표현(IIFE, Immediately Invoked Function Expression): 정의되자마자 즉시 실행되는 JavaScript Function

- 특징

  - Grouping Operater : ()안에 어휘 범위(렉시컬 스코핑)로 둘러싸인 익명함수다. 전역 스코프에 불필요한 변수를 추가해서 오염시키는 걸 방지 할 수 있고, IIFE내부안으로 다른 변수들이 접근하는 것을 막을 수 있는 방법이다.
    - ex) a \* (b + c) ()로 우선순위를 높이는 방법
  - 즉시 실행 함수를 생성하는 괄호() : js엔진은 함수를 즉시 해석해서 실행한다.

- 사용 예시

  ```js
  (function () {
    const a = 7;
    const b = 10;
    const sum = a + b;
    console.log(`a: ${a}, b: ${b}, sum: ${sum}`);
  })(); // 함수가 끝나면 초기화한 변수는 사라지기 때문에 a,b, sum은 다른 곳에서 사용이 불가능하다.

  (() => {
    const a = 7;
    const b = 10;
    const sum = a + b;
    console.log(`a: ${a}, b: ${b}, sum: ${sum}`);
  })();

  const fetchData = async (url) => {
    const response = await fetch(url);
    if (!response.ok) {
      throw new Error("Network response was not ok");
    }
    const data = await response.josn();
    return data;
  };

  // async IIFE를 사용하여 비동기 함수 실행
  (async () => {
    try {
      const data = await fetchData(
        "https://jsonplaceholder.typicode.com/posts/1"
      );
      console.log("Fetched data: ", data);
    } catch (error) {
      console.error("Error fetching data:".error);
    }
  })();

  // IIFE에 매개변수 전달
  (function (global) {
    global.qqq = "IIFE로 전역객체에 qqq속성 추가";
    console.log(global.qqq);
  })(this); // window Or globalThis

  console.log(qqq);
  ```

![스크린샷 2024-11-11 오후 2 19 48](https://github.com/user-attachments/assets/8e3e449e-1eec-436a-9fd5-bfbcf0d2cb4f)

# 2. 다음 코드가 즉시 호출 함수 표현식(IIFE)로 동작하지 않는 이유에 관해서 설명해보세요: function foo(){ }(); IIFE로 만들기 위해서는 어떻게 해야 하나요?

- ()를 붙이지 않아서 실행이 안된다.
- ()를 붙이지 않으면 함수선언식으로 인식이 되는데 함수선언식은 함술르 정의하기만 하고 정의된 함수는 따로 호출을 시켜줘야하기 떄문이다
- ()를 붙이면 함수표현식으로 인식이 되는데 함수 표현식은 변수처럼 즉시 값을 쓸 수 있기 때문에 IIFE를 사용할 수 있다.

# 3. JavaScript를 디버깅할 때 사용하는 도구가 있으면 설명해주세요.

- Debugger: 바닐라 js를 이용할 때는 debugger를 적고 웹블라우저에 Source탭에서 디버깅을 확인하지만 react나 next와 같은 프레임워크에서는 webpack이나 Vite같은 번들러를 통해 JavaScript 코드를 번들링하여 웹에 전달하므로, 브라우저에서 볼 때는 eval()형태로 압축되어 source탭을 활용할 수 없다.
  - vscode안에서 source탭과 똑같이 디버깅을 할 수 있다.
  - 방법 프로젝트 루트에 .vscode < launch.json파일을 만들고
  ```js
  {
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Next.js: debug server-side",
      "type": "node-terminal",
      "request": "launch",
      "command": "npm run dev"
    },
    {
      "name": "Next.js: debug client-side",
      "type": "chrome",
      "request": "launch",
      "url": "http://localhost:3000"
    },
    {
      "name": "Next.js: debug full stack",
      "type": "node-terminal",
      "request": "launch",
      "command": "npm run dev",
      "serverReadyAction": {
        "pattern": "- Local:.+(https?://.+)",
        "uriFormat": "%s",
        "action": "debugWithChrome"
        }
        }
      ]
    }
  ```
  코드를 붙여넣는다.
  - 디버깅을 하고 싶은 곳에 브레이크 포인트를 찍어주고 F5로 시작하면 Source탭과 똑같이 디버깅을 할 수 있다.
  - 사진
  - 타입스크립트를 지원하기 때문에 Typescript에서의 타입 검사와 오류를 vscode 디버거에서 즉시 확인할 수 있다.
  - 조사식에 특정 변수를 추가하여 코드 실행 중 언제든지 해당 변수의값을 실시간으로 모니터링을 할 수 있다.
  - next.js와 같은 프레임워크에서 서버 사이드 코드와 클라이언트 사이드 코드를 모두 디버깅 할 수 있어서 전체적인 흐름을 디버깅하기 좋다.
