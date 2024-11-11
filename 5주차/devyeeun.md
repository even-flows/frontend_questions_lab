# 1. 즉시호출함수표현식이란?

즉시 실행 함수 표현(IIFE, Immediately Invoked Function Expression) 은 정의되자마자 즉시 실행되는 함수를 말한다. <br/>
소괄호(())로 함수를 감싸서 실행하는 문법을 사용한다.  
일반적으로 `()()` 형태이다. 첫 번째 괄호 안에 함수 표현식을 적고, 두 번째 괄호를 적으면 해당 함수를 즉시 실행한다. 매개변수가 있는 경우 두 번째 괄호 안에 입력한다.

```javascript
(function () {
  console.log("IIFE");
})();

// 화살표 함수로도 사용 가능하다
(() => {
  console.log("IIFE");
})();
```

즉시 실행 함수를 연속적으로 사용할 때는 표현식 끝에 `세미콜론(;)`을 꼭 붙여야 한다. 그렇지 않으면 에러가 발생한다.
```javascript
// no semi-colon
(function foo() {
  console.log("foo")
})() // foo

(function bar(str) {
  console.log(str)
})("bar") // TypeError: (intermediate value)(...) is not a function

// has semi-colon
(function foo() {
  console.log("foo")
})(); // foo

(function bar(str) {
  console.log(str)
})("bar"); // bar
```

### 💡즉시실행함수에 익명 함수를 사용해야 할까?

즉시실행함수에도 이름을 붙여 기명 즉시실행함수로 사용 할 수 있다. 하지만 즉시실행함수는 선언과 동시에 호출되어 반환되어 재사용 할 수 없기 때문에 이름을 지어주는 것이 의미가 없다.

### 즉시 실행 함수 사용법

```javascript
// 기명 즉시 실행 함수
(function multiply(x) {
  console.log(x * x);
})(2);

// 익명 즉시 실행 함수
(function (x) {
  console.log(x * x);
})(2);
```

<br/>

### 즉시실행함수를 왜 사용할까?

1. 필요없는 전역 변수의 생성을 줄일 수 있다.  
   함수를 생성하면 그 함수는 전역 변수로써 남아있게 되고, 많은 변수의 생성은 전역 스코프를 오염시킬 수 있다.  
   즉시실행함수를 선언하면 내부 변수가 전역으로 저장되지 않기 때문에 전역 스코프의 오염을 줄일 수 있다.

2. private한 변수를 만들 수 있다.  
   즉시실행함수는 외부에서 접근 할 수 없는 자체적인 스코프를 가지게된다. 이는 클로저의 사용 목적과도 비슷하며 내부 변수를 외부로부터 private하게 보호 할 수 있다는 장점이 있다.

```javascript
(function multiply(x) {
  console.log(x * x);
})(2);

multiply(1);

// 4
// ReferenceError: multiply is not defined
```

즉시실행함수는 한 번의 실행 이후 없어지기 때문에 함수명으로 호출하면 위와 같은 에러가 나타난다.  
따라서 즉시실행함수는단 한번의 사용한 필요한 함수에 사용된다.(ex. 변수를 초기화 하는 함수)

<br/>

즉시 실행 함수도 함수이기 때문에, 변수에 즉시 실행 함수 저장이 가능하다.

```javascript
(test = function (x) {
  console.log(x * x);
})(2);
test(3);

// 4
// 9
```

위의 multiply 함수와 다르게 test라는 변수에 실행 함수를 저장하고 있기 때문에 재호출이 가능하다.

```javascript
var myMultiply = (function (x) {
  return x * x;
})(2);
console.log(myMultiply);

// 4
```

마찬가지로 즉시 실행 함수도 함수이기 때문에, 변수에 즉시 실행 함수의 리턴 값 저장도 가능하다.  
❗️괄호의 위치에 주의 필요!

<br/>

# 2. 다음 코드가 즉시 호출 함수 표현식(IIFE)로 동작하지 않는 이유에 관해서 설명해보세요: function foo(){ }(); IIFE로 만들기 위해서는 어떻게 해야 하나요?

기명 함수를 정의했는데 () 괄호 없이 즉시 호출하여 에러가 발생했다.

### 즉시 실행 함수의 에러가 발생하는 2가지 이유
1. 즉시 실행 함수를 괄호()로 감싸지 않은 경우
```javascript
function() {
	let a = 3; 
    let b = 5; 
    return a * b;
}();

// Uncaught SyntaxError: Function statements require a function name 
```
> 이 경우는 함수 정의가 함수 선언문의 형식에 맞지 않기 때문이다. 함수 선언문은 함수 이름을 생략할 수 없기 때문이다.

2. 기명 함수를 정의하고, 괄호() 없이 즉시 호출하는 경우
```javascript
function foo() {
	let a = 3; 
    let b = 5; 
    return a * b;
}();

// Uncaught SyntaxError: Unexpected token ')'
```

> 이 경우는 자바스크립트 엔진이 암묵적으로 수행하는 세미콜론 자동 삽입 기능에 의해 함수 선언문이 끝나는 위치, 즉 함수 코드 블록의 닫는 중괄호 뒤에 ";"이 암묵적으로 추가되기 때문에 에러가 발생한다.

`function foo () {}();` 이런 함수를 실행시켰을 경우,

`function foo() {};();` 의 모습으로 실행이 되기 때문이다.  
그렇기 때문에 함수 선언문 뒤의 괄호()는 함수 호출 연산자가 아니라 그룹 연산자로 해석이 되고, 그룹 연산자에 피연산자가 없기 때문에 에러가 발생한다.


<br/>

# 3. JavaScript를 디버깅할 때 사용하는 도구가 있으면 설명해주세요.
