## 1. Promise에 대해 공부해오기


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


** 프로세스와 스레드 

 
