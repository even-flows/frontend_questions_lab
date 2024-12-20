## 1. Promise에 대해 공부해오기

### Promise란?

Promise는 **자바스크립트 비동기 처리에 사용되는 객체**이다.<br/>

> _비동기 처리란?_<br/>
> 특정 코드의 연산이 끝날 때까지 기다리지 않고 다음 코들를 먼저 수행하는 자바스크립트의 특성을 의미한다.

### Promise가 필요한 이유?

주로 서버에서 받아온 데이터를 화면에 표시할 때 사용한다.<br/>
서버에 '데이터 보내줘'라고 요청을 보내는데, 데이터를 받아오기 전에 마치 데이터를 받아온 것처럼 화면에 표시하려고 하면 에러가 발생한다. 이때, Promise를 사용하면 데이터를 받아오기 전에 화면에 표시할 수 있다.

### Promise의 상태(프로미스의 처리과정)

비동기 처리가 성공하였을 때와 실패하였을 때의 결과를 처리하기 위한 메서드를 제공한다.<br/>
Promise는 세 가지 상태를 가지고 있다.

- Pending(대기): 비동기 처리가 아직 완료되지 않은 상태
- Fulfilled(이행): 비동기 처리가 완료되어 프로미스가 결과 값을 반환해준 상태
- Rejected(실패): 비동기 처리가 실패하거나 오류가 발생한 상태

#### Pending(대기)

`new Promise()` 메서드를 호출하면 대기 상태가 된다.<br/>
메서드를 호출할 때 콜백 함수를 선언할 수 있고, 콜백 함수는 resolve와 reject를 인자로 가진다.

```javascript
new Promise((resolve, reject) => {
  // 아직 비동기 처리가 완료되지 않은 상태
});
```

### Fulfilled(이행)

콜백 함수의 인자 resolve를 호출하면 이행 상태가 된다.<br/>
이때, resolve 메서드의 인자로는 비동기 처리의 결과 값이 들어간다.

```javascript
new Promise((resolve, reject) => {
  resolve("이행 상태");
});
```

### Rejected(실패)

콜백 함수의 인자 reject를 호출하면 실패 상태가 된다.<br/>
이때, reject 메서드의 인자로는 실패 이유를 나타내는 오브젝트가 들어간다.

```javascript
new Promise((resolve, reject) => {
  reject(new Error("실패 상태"));
});
```

### 프로미스 적용한 코드

```javascript
function getData(){
    return new Promise((resolve, reject){
        $.get('주소/product/1', function(response){
            if(response){
                resolve(response)
            }
            reject(new Error("요청실패"));
        });
    });
}


getData().then(function(data){
    console.log(data);   // 성공 시, response 값 출력
}).catch(function(error){
    console.error(error);  // 실패 시, error 출력
})
```

## 2. Promise가 콜백 대비 장/단점은?
둘 다 자바스크립트에서 비동기처리를 위해 사용되는 방법이다.

**콜백**의 경우 **함수의 처리 순서를 보장**하기 위해서 콜백 안에 콜백을 계속 중첩하게 사용되는 경우가 발생하는 **콜백 지옥**이 발생할 수 있고 **에러처리가 힘들다**는 단점이 있다.

이 때, **Promise**를 사용하면,
1. 가독성이 향상된다.<br/>
**콜백 지옥을 해결**하여 코드의 가독성, 복잡성 문제를 해결할 수 있다.
또한 비동기 로직에서 처리된 결과값이 프로미스 객체에 저장되기 때문에 로직 밖에서도 값의 사용이 가능하다.

2. 일관된 에러처리가 가능하다.<br/>
여러 비동기 작업 중 하나에서 에러가 발생하더라도, ```.catch()```를 통해 모든 에러를 한 곳에서 처리할 수 있다.


## 3. 동기방식과 비동기방식 함수의 차이점
![동기,비동식 방식](https://adrianmejia.com/images/async-vs-sync-concurrency-in-javascript-large.png)
<br/>
동기방식은 순차적, 직렬적으로 실행되는 방식이고, 비동기방식은 병렬적으로 실행되는 방식이다.

1.  동기 : 
- 직렬적으로 task를 수행하는 방식.
- 요청을 보낸 후 응답을 받아야만 다음 동작이 이루어지는 방식.
- 모든 일은 순차적으로 실행되며 이전 작업이 수행중이라면 다음 작업은 대기하게 된다.
- 코드의 가독성이 좋고 디버깅이 쉽다.
- 코드가 순차적으로 실행되기 때문에 실행 순서를 예측하기 쉽다.
- 실제로 cpu가 느려지는 것은 아니지만 시스템의 전체적인 효율이 저하된다고 할 수 있다.

        ```javascript
        const func1 = () => {
            console.log('func1');
            func2();
        }

        const func2 = () => {
            console.log('func2');
            func3();
        }

        const func3 = () => {
            console.log('func3');
        }

        func1();

        // 결과
        // func1
        // func2
        // func3
        ```

2.  비동기 :
- 병렬적으로 task를 수행하는 방식.
- 요청을 보낸 후 응답을 기다리지 않고 다음 동작이 이루어지는 방식.
- a task가 실행되는 동안 b task가 실행될 수 있으므로 자원을 효율적으로 사용할 수 있다.
- 비동기 요청 시 응답 후 처리할 '콜백 함수'를 함께 알려주고 이후 응답이 오면 미리 등록해둔 '콜백 함수'를 실행한다.
- ❗️주의 : 비동기 처리를 위해 콜백 패턴을 사용하면 처리 순서를 보장하기 위해 콜백 안에 콜백을 계속 중첩해야 하는 콜백 지옥이 발생할 수 있다.
