# 1. Promise

## 1.1 개념

- **정의**: 비동기 작업의 완료 또는 실패를 나타내는 객체로, 그 결과값을 처리할 수 있는 메커니즘을 제공한다. 작업이 성공했을 때는 결과를 반환하고, 실패했을 때는 에러를 처리할 수 있다. Promise는 비동기 코드에서 콜백 함수의 대안으로 사용되며, `.then()`, `.catch()`, `.finally()`를 통해 처리된다.
- **역할**: 비동기 작업의 결과를 관리하고 연결하는 역할을 한다.

## 1.2 Promise의 상태

- **대기(Pending)**: 이행하지도, 거부하지도 않은 초기 상태. 비동기 작업이 아직 완료되지 않은 상태.
- **이행(Fulfilled)**: 연산이 성공적으로 완료된 상태.
- **거부(Rejected)**: 연산이 실패한 상태.
![promises사진](https://github.com/user-attachments/assets/275e0e2f-8d1f-43ff-a855-403e8ed0c1b8)

## 1.3 Promise 사용법

```javascript
const evenFlows = new Promise((resolve, reject) => {
  const cookingLevel = Math.random();
  if (cookingLevel >= 0.8) {
    resolve("고기가 even하게 익었어요!"); // Fulfilled 상태
  } else if (cookingLevel === 0.5) {
    reject("이거 빠쓰아니여?"); // 특수한 상황 처리
  } else {
    reject("고기가 고루 익지 않았어요."); // Rejected 상태
  }
});

evenFlows
  .then((message) => {
    console.log(message); // "고기가 even하게 익었어요!" 출력
  })
  .catch((error) => {
    console.error(error); // "고루 익지 않았어요." 또는 "이거 빠쓰아니여?" 출력
  });
```

## 1.4 Promise 체이닝

- 체이닝을 사용하면 복잡한 비동기 작업을 순차적으로 처리할 수 있으며, 가독성이 높아진다.
- 각각의 `.then()`은 새로운 Promise를 반환한다.
- 만약 어느 한 단계에서 실패하면, `.catch()`가 호출되어 에러 메시지를 처리한다.

```javascript
checkCookingLevel
  .then((message) => {
    console.log(message); // 첫 번째 성공 메시지 출력
    return new Promise((resolve) => {
      setTimeout(() => resolve("다른 요리도 완성됐어요!"), 2000); // 2초 후 다른 비동기 작업 완료
    });
  })
  .then((nextMessage) => {
    console.log(nextMessage); // "다른 요리도 완성됐어요!" 출력
  })
  .catch((error) => {
    console.error(error); // 실패 메시지 출력
  });
```

## 1.5 Promise 관련 메서드

- **Promise.all()**: 여러 개의 Promise를 병렬로 실행하고, 모든 Promise가 성공적으로 완료되었을 때 결과를 반환하는 메서드이다. 만약 실패하면 `.catch()`로 에러가 처리된다.
  - 모든 Promise가 성공하면 각각의 결과를 배열로 반환.
  - 하나라도 실패하면 즉시 실패되고 `.catch()`가 호출된다.

```javascript
const promise1 = new Promise((resolve) =>
  setTimeout(() => resolve("첫 번째 작업 완료!"), 1000)
);
const promise2 = new Promise((resolve) =>
  setTimeout(() => resolve("두 번째 작업 완료!"), 2000)
);
const promise3 = new Promise((resolve) =>
  setTimeout(() => resolve("세 번째 작업 완료!"), 1500)
);

Promise.all([promise1, promise2, promise3])
  .then((results) => {
    console.log(results); // ["첫 번째 작업 완료!", "두 번째 작업 완료!", "세 번째 작업 완료!"] 출력
  })
  .catch((error) => {
    console.error("에러 발생:", error);
  });
```

- **Promise.race()**: 가장 먼저 완료된 Promise의 결과만 반환한다. 즉, 배열에 전달된 Promise 중에서 가장 빠르게 완료된 하나의 Promise만 처리된다.
  - **용도**: 여러 비동기 작업 중 가장 빨리 완료된 작업의 결과만 필요할 때 유용하다.
  - **특징**:
    - 가장 먼저 완료된 Promise의 결과만 반환한다.
    - 첫 번째로 성공하거나 실패한 Promise의 결과만 처리된다.

```javascript
const promise1 = new Promise((resolve) =>
  setTimeout(() => resolve("첫 번째 작업 완료!"), 1000)
);
const promise2 = new Promise((resolve) =>
  setTimeout(() => resolve("두 번째 작업 완료!"), 500)
);

Promise.race([promise1, promise2])
  .then((result) => {
    console.log(result); // "두 번째 작업 완료!" 출력
  })
  .catch((error) => {
    console.error("에러 발생:", error);
  });
```

## 2. Promise가 콜백 대비 장/단점은?

### 2.1 콜백 함수의 개념

**콜백 함수**는 **다른 함수의 인자로 전달되어 특정 작업이 완료된 후 호출되는 함수**입니다. 비동기 작업이 끝난 후 그 결과를 처리할 때 자주 사용됩니다.

#### 콜백 함수의 예시

```javascript
function fetchData(callback) {
  setTimeout(() => {
    callback("데이터를 가져왔습니다."); // 비동기 작업이 완료되면 콜백 함수 호출
  }, 1000);
}

fetchData((data) => {
  console.log(data); // "데이터를 가져왔습니다." 출력
});
```

## 2.2 Promise의 장점

- **가독성 개선**: 콜백 함수의 중첩을 피할 수 있어 코드의 가독성이 좋아집니다.

  #### 콜백 지옥 예시

```javascript
fetchData1((result1) => {
  fetchData2(result1, (result2) => {
    fetchData3(result2, (result3) => {
      console.log(result3);
    });
  });
});
```

### Promise 체이닝

```javscript
  fetchData1()
    .then((result1) => fetchData2(result1))
    .then((result2) => fetchData3(result2))
    .then((result3) => console.log(result3))
    .catch((error) => console.error(error));
```

    - 에러 처리 간편성 : `.catch()`를 통해 모든 단계의 에러를 한 번에 처리할 수 있다.
    - 동시 처리의 편리성 : `Promise.all()`과 같은 메서드를 사용하면 여러 비동기 작업을 동시에 실행하고, 모든 작업이 완료되었을 때 결과를 한꺼번에 처리할 수 있다.

## 2.3 Promise의 단점

- 구형 브라우저에서의 호환성 문제 : 구형 브라우저는 Promise를 기본적으로 지원하지 않으므로, 호환성을 위해 `Promise`에 대한 **polyfill**을 추가해야 한다. 이로 인해 프로젝트 설정이 다소 복잡해질 수 있다.

## 2.4 콜백 함수의 장점

- 간단한 비동기 작업에 적합

- 구형 환경에서도 사용 가능

## 2.5 콜백 함수의 단점

- 콜백 지옥
- 에러 처리의 복잡성

- 콜백 함수에서는 각 단계마다 에러를 개별적으로 처리해야 한다. Promise와 달리 체이닝된 모든 작업에서 하나의 catch()로 에러를 처리할 수 없으며, 콜백 내부에서 에러를 핸들링해야 한다.

```js
firstTask((error1, result1) => {
  if (error1) {
    console.error("에러 발생:", error1);
  } else {
    secondTask(result1, (error2, result2) => {
      if (error2) {
        console.error("에러 발생:", error2);
      } else {
        thirdTask(result2, (error3, result3) => {
          if (error3) {
            console.error("에러 발생:", error3);
          } else {
            console.log("결과:", result3);
          }
        });
      }
    });
  }
});
```

# 3. 동기방식과 비동기 방식 함수의 차이점

## 3.1 동기 함수의 정의

**동기 함수**는 **작업이 완료될 때까지 다음 작업을 진행하지 않는 방식**을 의미한다. 즉, 하나의 작업이 완료된 후에야 그 다음 작업이 실행된다. 이러한 방식에서는 모든 작업이 순차적으로 실행된다.

### 동기 코드 예시

```js
function task1() {
  console.log("작업 1 시작");
  console.log("작업 1 완료");
}

function task2() {
  console.log("작업 2 시작");
  console.log("작업 2 완료");
}

task1();
task2();
```

```js
출력:
작업 1 시작
작업 1 완료
작업 2 시작
작업 2 완료
```

## 3.2 비동기 함수의 정의

**비동기 함수**는 **작업이 완료될 때까지 기다리지 않고, 다음 작업을 즉시 실행하는 방식**을 의미한다. 비동기 작업이 완료되면, 그 결과를 처리하는 콜백 함수나 Promise를 통해 후속 작업을 처리할 수 있다.

```js
function asyncTask() {
  console.log("비동기 작업 시작");
  setTimeout(() => {
    console.log("비동기 작업 완료");
  }, 2000);
}

function otherTask() {
  console.log("다른 작업 시작");
}

asyncTask();
otherTask();
```

```js
출력:
비동기 작업 시작
다른 작업 시작
(2초 후)
비동기 작업 완료
```

## 3.3 동기 방식의 장단점

- 장점:

  - 코드 흐름이 직관적: 코드가 순차적으로 실행되므로 이해하기 쉽고, 작성한 그대로 실행된다.
  - 디버깅이 쉬움: 코드가 한 줄씩 실행되기 때문에 문제가 발생한 부분을 쉽게 찾아낼 수 있다.

- 단점:
  - 오래 걸리는 작업에서 블로킹 발생: 서버에서 데이터를 가져오는 작업이나 파일을 읽는 작업처럼 시간이 오래 걸리는 작업이 있으면, 그 작업이 끝날 때까지 다른 작업이 중단된다..

## 3.4 비동기 방식의 장단점

- 장점 : 블로킹 없이 여러 작업 병행 가능: 시간이 오래 걸리는 작업을 처리하면서도 다른 작업을 병행할 수 있다. 비동기 작업은 완료되었을 때 후속 작업을 처리할 수 있어 성능 향상이 가능하다.

- 단점 : 코드 흐름이 복잡해질 수 있음: 비동기 작업이 많아질수록 콜백 함수나 Promise 체이닝 등으로 인해 코드의 흐름을 이해하기 어려워질 수 있다.
