### 1주차 Question Lab

### 📣 주제

1. 동기와 비동기란?
2. Promise란?
3. Callback 패턴 대비 Promise의 장점

---

### 1. 동기와 비동기란?

**✔️ 동기 처리(Synchronous Processing)**

- 현재 실행 중인 태스크가 종료할 때까지 다음에 실행될 태스크가 대기하는 방식

![image](https://github.com/user-attachments/assets/c0c337e5-5484-41d7-8d81-e1cad4a6af1d)

- 자바스크립트 엔진은 한 번에 하나의 태스크만 실행할 수 있는 <b>싱글 스레드</b> 방식으로 동작한다. 이 때문에 처리에 시간이 걸리는 태스크를 실행하는 경우 <b>블로킹(작업 중단)</b>이 발생한다.
- 태스크를 순서대로 처리하므로 실행 순서가 보장됨.

#### 💻 예제 코드:

```js
function sleep(func, delay) {
  const delayUntil = Date.now() + delay; // 현재 시간에서 delay 만큼 더한 시간을 계산
  while (Date.now() < delayUntil); // 그 시간이 될 떄까지 빈 루프 실행
  func(); // 시간이 지나면 func 실행
}

function foo() {
  console.log("foo");
}

function bar() {
  console.log("bar");
}

sleep(foo, 3 * 1000);
bar();
// 3초 경과 후 foo 호출 -> bar 호출
```

**🚨 이 방식의 문제점**

1. <b>cpu 점유율</b>: 이 코드는 `while` 루프 내에서 지속적으로 현재 시간을 체크하며, 그 동안 아무 일도 하지 않기 때문에 cpu 리소스를 불필요하게 소모하게 됨.
2. <b>비동기적 동작이 아님</b>: 이 함수가 실행되는 동안 웹 페이지나 애플리케이션의 다른 작업은 멈춰버리게 됨.

---

**✔️ 비동기 처리(Asynchronous Processing)**

- 현재 실행 중인 태스크가 종료되지 않은 상태라 해도 다음 태스크를 곧바로 실행하는 방식

![image](https://github.com/user-attachments/assets/0fa01990-0ca1-453e-8fec-8b58c34dbba7)

#### 💻 예제 코드(상단의 동기 처리 코드 수정):

```js
function foo() {
  console.log("foo");
}

function bar() {
  console.log("bar");
}

setTimeout(foo, 3 * 1000);
bar();
// bar 호출 -> 3초 경과 후 foo 호출
```

- `setTimeout` 함수는 앞서 본 `sleep` 함수와 유사하게 일정 시간이 경과한 이후에 콜백 함수를 호출하지만 비동기 함수이므로 이후의 태스크 `bar()` 를 블로킹하지 않고 곧바로 실행한다.
- 이 코드는 CPU 자원을 낭비하지 않고 비동기적으로 원하는 시간 이후에 함수가 실행되도록 한다.

**🚨 이 방식의 문제점**

1. 비동기 처리를 위한 콜백 패턴은 콜백 헬을 발생시켜 가독성을 나쁘게 함.
2. 비동기 처리 중 발생한 에러의 예외 처리가 곤란함.
3. 여러 개의 비동기 처리를 한 번에 처리하는 데 한계가 있음.

---

### 2. Promise 란?

- 자바스크립트 비동기 처리 상태와 처리 결과를 관리하는 객체
- 작업의 성공(resolve)이나 실패(reject)를 표현하는 객체로, 미래에 완료될 작업을 나타내는 약속이라고 볼 수 있다.

**✔️ `Promise`의 상태**

- `Promise`에는 3가지 상태가 있다.
  1. <b>Pending</b>(대기): 아직 완료되지 않은 상태
  2. <b>Fulfilled</b>(이행): 작업이 성공적으로 완료된 상태. `resolve()`함수가 호출됨.
  3. <b>Rejected</b>(거부): 작업이 실패한 상태. `reject()`함수가 호출됨.

**💻 예제 코드(상단의 비동기 처리 코드 수정)**

```js
function sleep(delay) {
  return new Promise((resolve, reject) => {
    if (delay < 0) {
      reject(new Error("Delay must be a positive number"));
    } else {
      setTimeout(resolve, delay); // delay 시간이 지나면 resolve 호출
    }
  });
}

function foo() {
  console.log("foo");
}

function bar() {
  console.log("bar");
}

sleep(3 * 1000)
  .then(foo) // 성공적으로 3초 후에 foo 실행
  .catch((error) => console.error(error)); // 에러가 발생하면 에러 메시지를 출력

bar(); // 바로 bar 실행
```

```js
return new Promise((resolve) => setTimeout(resolve, delay));
```

- `sleep`함수는 지연(delay)을 처리하는 `Promise`를 반환함.
- `new Promise`를 사용하여 새로운 `Promise` 객체를 생성하고, 이 객체는 비동기 작업 완료 후 `resolve()`를 호출함. `Promise`가 실패하면 `reject()`를 호출함
- `setTimeout(resolve, delay)`는 `delay` 밀리초 후에 `resolve()`를 호출함. 이로써 `Promise`는 이행(Fulfilled) 상태가 됨.

```js
sleep(3 * 1000)
  .then(foo)
  .catch((error) => console.error(error));
```

- `then()` 메서드는 `Promise`가 이행(Fulfilled)된 후에 실행될 코드를 정의함. 여기서 foo 함수는 sleep 함수의 지연 시간이 끝난 후에 실행됨.
- `.catch()` 메서드를 통해 `Promise`에서 발생한 오류를 처리 가능
- `reject`가 호출되거나 `Promise` 내부에서 예외가 발생하면 `then()`이 호출되지 않고 `catch()`로 에러가 전달됨.

---

### 3. Callback 패턴 대비 Promise의 장점

**✔️ 콜백 패턴**

- 비동기 작업이 끝난 후 실행할 함수를 인자로 넘겨주는 방식이다. 비동기 작업이 성공하면 콜백 함수가 호출된다.

**🚨 콜백 패턴의 문제점**

1. 콜백 지옥(Callback Hell): 여러 비동기 작업을 중첩해서 처리해야 할 때 가독성이 떨어지고 매우 복잡해지는 현상
2. 에러 처리 복잡성: 각 콜백에서 에러를 검사해야 하고, 여러 단계로 중첩될수록 에러 처리 로직이 복잡해짐.

**💻 콜백 지옥 예제**

```js
sleep(1000, (err, result) => {
  if (!err) {
    console.log("Step 1 complete");
    sleep(1000, (err, result) => {
      if (!err) {
        console.log("Step 2 complete");
        sleep(1000, (err, result) => {
          if (!err) {
            console.log("Step 3 complete");
          }
        });
      }
    });
  }
});
```

**💎 `Promise`의 장점**

1. 가독성 향상: 비동기 작업을 체이닝(`then`) 방식으로 연결할 수 있어서 중첩 구조를 피할 수 있음.
2. 에러 처리 일관성: `catch()`를 사용하면 `then()`체인 중 어디서 에러가 발생하더라도 이를 쉽게 처리할 수 있음.

**💻 `then(),catch()` 예제**

```js
sleep(1000)
  .then(() => {
    console.log("Step 1 complete");
    return sleep(1000);
  })
  .then(() => {
    console.log("Step 2 complete");
    return sleep(1000);
  })
  .then(() => {
    console.log("Step 3 complete");
  })
  .catch((error) => console.error(error)); // 에러 처리는 한 곳에서
```

3. 여러 개의 비동기 작업 처리를 할 떄, `Promise.all`을 사용하면 병렬 작업 처리를 쉽게 할 수 있음.

```js
Promise.all([sleep(1000), sleep(2000), sleep(3000)])
  .then(() => console.log("All tasks complete"))
  .catch((error) => console.error(error));
```
