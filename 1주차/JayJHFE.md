## 1. Promise에 대해 공부해오기
하기내용과 겹침침

## 2. Promise가 가지는 콜백 대비 장/단점은?
둘 다 자바스크립드에서 비동기 처리를 위해서 사용되는 패턴이며,

Callback 같은 경우 함수의 처리 순서를 보장하기 위해서 사용한다. 

컴파일러는 기본적으로 위에서 아래로 코드를 읽고 컴파일하지만

비동기 작업은 순서가 보장되지 않음

그러면 그 순서를 보장해줘야 하는데 그때 쓰는게 콜백임.

콜백은 함수를 중첩하게 사용되는 경우가 발행해

콜백헬이 발행하는 단점과 에러 처리가 힘들다라는 단점이 있음.

콜백헬 예시
```Javascript
step1(function (value1) {
    step2(function (value2) {
        step3(function (value3) {
            step4(function (value4) {
                step5(function (value5) {
                    step6(function (value6) {
                        // Do something with value6
                    });
                });
            });
        });
    });
});
function getBread(bread, callback) {
  callback(bread);
}
function toastBread(bread, callback) {
  callback(bread);
}
function applyJam(bread, callback) {
  callback(bread);
}
let bread = "식빵";
getBread =
  (bread,
  function (bread) {
    console.log(bread + " 사오기 완료");
    toastBread(bread, function (toastedBread) {
      console.log(bread + " 굽기 완료");
      applyJam(toastedBread, function () {
        console.log(bread + "에 잼 바르기 완료");
        console.log(bread + " 토스트 준비 완료");
      });
    });
  });
```
callback을 사용해서 비동기 로직의 결과값을 처리하기 위해서는 callback안에서만 처리를 해야하고, 콜백 밖에서는 비동기에서 온 값을 알 수가 없음

이를 해결하기위해 나온 Promise는

Promise는 객체. 비동기 작업의 상황을 pending, resolved, failed 3가지 상태로 묘사하고 값으로 할당 가능한 객체의 형태로 표현됨

then으로 이루어진 Promise 체인으로 구성할 수 있음.

Promise 예시
```Javascript
function stepOne() {
  return new Promise(function (resolve) {
    setTimeout(function () {
      const result = 2;
      resolve(result);
    }, 1000);
  });
}

function stepTwo(prevResult) {
  return new Promise(function (resolve) {
    setTimeout(function () {
      const result = prevResult + 3;
      resolve(result);
    }, 1000);
  });
}

function stepThree(prevResult) {
  return new Promise(function (resolve) {
    setTimeout(function () {
      const result = prevResult + 5;
      resolve(result);
    }, 1000);
  });
}

function finalStep(prevResult) {
  console.log("결과값:", prevResult);
}

stepOne()
  .then(stepTwo)
  .then(stepThree)
  .then(finalStep)
  .catch(function (error) {
    console.error("에러 발생:", error);
  });
```
Callback과 Promise의 장단점은 비교적 간단.

코드의 유지보수성과 가독성이라는 것.

로직이 길지않고 단순하다면 본인에게 편한방식으로 적용할 수 있음.

Promise all 예시
```Javascript
Promise.all([
    new Promise(resolve => setTimeout(() => resolve(1), 1000)),
    new Promise(resolve => setTimeout(() => resolve(2), 2000)),
    new Promise(resolve => setTimeout(() => resolve(3), 3000))
]).then(console.log) // 프로그램을 실행하고 3초뒤에 실행됨
.catch(console.log)
```
Promise.all 메소드를 이용해 한번에 병렬처리를 진행함.

가장 마지막으로 끝나는 프로미스를 기준으로 수행되고, 모든 프로미스가 fullfilled 상태가 되면 결과값을 배열에 담아 새로운 프로미스를 반환함.

프로미스를 수행하던 도중 하나라도 에러(rejected)가 발생하면 rejected 상태가 되고 수행을 종료함.


## 3. 동기방식과 비동기 방식 함수의 차이점
![image](https://github.com/user-attachments/assets/1afe6458-e1ef-4185-baee-e061d0c829d2)
![image](https://github.com/user-attachments/assets/bcd0d249-addb-4124-b8d4-4dcd41104758)
![image](https://github.com/user-attachments/assets/96fcc9d7-e200-464f-909d-258be1f01c38)

### 동기(Synchronous) 

개념 : 프로그래밍에서 동기는 작업이 순차적으로 진행되는 것을 의미하고 작업이 시작되면 해당 작업이 완료될 때까지 다른 작업이 기다려야함

특징 : 동기 방식은 호출한 함수 또는 작업의 리턴값이 들어올때까지 대기하기 때문에 웹이아닌 프로그래밍에서는 괜찮지만 웹에서의 경험이 나쁠 수 있음.

장점 : 코드가 직관적이고 쉬움

단점 : 여러개의 작업이 동시에 실행되어야 하는 경우(멀티) 작업의 완료를 기다리는 동안 시간이 소요되어 전체 프로세스의 성능이 저하될 수 있음. - 웹페이지에서의 경험이 나쁜이유
      또한 네트워크 상태가 불안정하여 작업이 지연되면 나머지 작업들도 모두 지연될 수 있음.

### 비동기(Asynchronous)

개념 : 프로그래밍에서 비동기는 각각의 작업이 독립적으로 실행되며, 독립적인 작업의 완료 여부를 기다리지 않고 다른 작업이 실행되는 방식.  

특징 : 비동기 방식은 작업이 시작되면 해당 작업이 완료될 때까지 기다리지 않고 다음 코드를 실행가능 - 따라서 웹에서의 경험이 좋음 (웹이 JS기반인 이유)

장점 : I/O 작업이나 네트워크 요청과 같이 시간이 오래 걸리는 작업에 유용

단점 : 비즈니스 로직을 짤 때 로직이 동기로 작동해야하거나, 특정함수 이후에 동작해야한다면 callback, Promise, async/await를 사용해야 함 

** 블로킹과 논블로킹
* 동기/비동기는 전체적인 작업에 대한 순차적인 흐름에 대한개념
* 블로킹/논블로킹은 전체적인 작업의 흐름을 막음/막지않음
* ex) 파일을 읽는 작업이 있을 때, 블로킹 방식으로 읽으면 해당 파일을 다 읽을 때까지 대기하고, 논블로킹 방식으로 읽으면 파일을 다 읽지 않아도 다른 작업을 할 수 있음
  -블로킹
  ![image](https://github.com/user-attachments/assets/ef337294-6dbc-4f59-a74c-9e1d0f52879e)
  -논블로
  ![image](https://github.com/user-attachments/assets/678e28be-24ad-4b16-b774-58c46e8cbe2d)


** 스레드 
![image](https://github.com/user-attachments/assets/098031ff-ee51-4e7b-8a77-65a6af8ff52c)

스레드란

스케줄러가 독립적으로 관리할 수 있는 프로그래밍된 명령의 소규모 시퀀스

또는

운영체제에서 실행되는 가장 작은 작업 단위로, 하나의 프로세스 내에서 독립적으로 실행되는 흐름

자바스크립트는 싱글 스레드로 작동하면서 비동기로 동작할 수 있는 원리는 바로 브라우저에 있음

브라우저에 있는 Web API가 멀티스레드로 구현되어 있는데,

메인 스레드(Call Stack)가 작업을 Web API에 요청하여 Web API가 대신 실행하고,

그 작업이 완료되면 이벤트나 콜백 함수를 받아 결과를 실행하는 방식으로 비동기 동작이 가능해짐

이런식으로 싱글 스레드인 자바스크립트의 작업을 멀티 스레드로 돌려 작업을 동시에 처리시키게 한다던지,

여러 작업 중 어떤 작업을 우선으로 동작시킬 것인지 결정하는 컨트롤을 위해 존재하는 것이 이벤트 루프라고 함

브라우저 내부의 Call Stack, Callback Queue, Web APIs 등의 요소들을

이벤트 루프가 모니터링하면서 비동기적으로 실행되는 작업들을 관리함

이를 순서대로 처리하여 프로그램의 실행 흐름을 제어하는 관리자라고 보면 됨

🔎 Heap : 동적으로 생성된 자바스크립트 객체가 저장되는 공간

🔎 Call Stack : 자바스크립트 엔진이 코드 실행을 위해 사용하는 메모리 구조

🔎 Web APIs: 브라우저에서 제공하는 API 모음으로, 비동기적으로 실행되는 작업들을 전담하여 처리함
 (AJAX 호출, 타이머 함수, DOM 조작 등)

🔎 Callback Queue : 비동기적 작업이 완료되면 실행되는 함수들이 대기하는 공간

🔎 Event Loop : 비동기 함수들을 적절한 시점에 실행시키는 관리자
 ![image](https://github.com/user-attachments/assets/cc79a435-56ed-40ee-8293-3512931c1a5d)

1) bar가 호출되면 setTimeout을 반환하고 콜스택에 추가 
 미래에 실행될 것을 약속한 후에 setTimeout의 콜백 함수가 Web API에 추가되어 비동기적으로 실행 됨

2) Web API에서 콜백함수의 타이머가 실행되는 동안 foo가 호출되어 콜스택에 추가. 실행 완료 후 콜스택을 빠져나가며 First를 출력

3) baz가 호출되어 콜스택에 추가된다. 실행 완료 후 콜스택을 빠져나가며 Second를 출력

4) foo와 baz가 실행되는 동안 콜백함수의 타이머가 완료되면 콜백함수는 콜백 큐에 들어가서 콜스택이 비워질 때까지 기다림
   
5) 이 때, 이벤트 루프가 콜스택과 콜백 큐의 상태를 확인하며 콜스택이 비워지면 콜백 함수를 콜스택으로 이동시킴
 
 이렇게 해서 콜백함수의 실행이 완료되면 콜스택을 빠져나감
