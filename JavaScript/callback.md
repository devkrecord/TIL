# 콜백 이해 및 콜백 지옥 체험 ✨

<br>
## 콜백 이해 📞
---
자바스크립트는 동기적이다.(synchronous)
즉, hoisting이 된 이후부터 코드 작성 순서에 맞춰 하나하나씩 동기적으로 실행된다.

`호이스팅(hoisting) 이란 ? var 변수, 함수 선언들이 코드 최상단으로 끌어올려지는 것.`

```js
"use strict";

console.log("1");
console.log("2");
console.log("3");

// 콘솔 출력
1;
2;
3;
```

이와 반대로 asynchronous는 비동기적으로 언제 코드가 실행될지 예측할 수 없는 것을 말한다.
좋은 예로 setTimeout() 이라는 브라우저에서 제공하는 웹 API가 있다.
이 함수는 코드 작성자가 지정한 시간이 지나면 콜백함수를 호출한다.

`콜백함수(Callback Function)란 ? 다른 함수의 인자로 사용되는 함수 또는 어떤 이벤트에 의해 호출되는 함수를 뜻한다.`

```js
console.log("1");
setTimeout(() => console.log("2"), 1000);
console.log("3");

// 콘솔 출력
1;
3;
2;
```

그렇다면 콜백은 항상 비동기적으로만 쓰일까? `NO!`
<br/>
synchronous callback (즉각적으로 동기적으로 실행하는 콜백함수)

```js
// 호이스팅을 통해 printImmediately 함수가 최상단으로 끌어올려진다.
console.log("1");
setTimeout(() => console.log("2"), 1000);
console.log("3");
printImmediately(() => console.log("hello"));
function printImmediately(print) {
  print();
}
printImmediately(() => console.log("hello"));

// 콘솔 출력
1;
3;
hello;
2;
```

asynchronous callback (언제 시작될지 예측할 수 없는 비동기적으로 실행하는 콜백함수 )

```js

console.log('1');
setTimeout(() => console.log('2') ,1000);
console.log('3');
function printImmediately (print) {
    print();
};
printImmediately(()=> console.log('hello'));
function printWithDelay(print, timeout) {
    setTimeout(print, timeout)
 }
printWithDelay(() => console.log("async callback"), 2000);

// printWithDelay함수는 콜백함수를 2초후에 실행하게 됩니다.
// 콘솔 출력
1
3
hello
2
async callback

```

브라우저는 위의 코드를 아래와 같이 실행한다.

```js

// 호이스팅에 의해 함수들이 최상단으로 끌어올려짐.
function printImmediately (print) {
    print();
};

function printWithDelay(print, timeout) {
    setTimeout(print, timeout)
 }

console.log('1'); // 동기
setTimeout(() => console.log('2') ,1000); // 비동기 1초 후 실행
console.log('3'); // 동기
printImmediately(()=> console.log('hello')); // 동기
printWithDelay(() => console.log("async callback"), 2000); // 비동기 2초 후 실행

// 콘솔 출력
1
3
hello
2
async callback

```

## 콜백 지옥 😈

---

`콜백 지옥(callback hell)이란 콜백 함수를 익명 함수로 전달하는 과정에서 또 다시 콜백 안에 함수 호출이 반복되어 코드의 들여쓰기 순준이 감당하기 힘들 정도로 깊어지는 현상을 말한다`

데이터를 서버로부터 받아오는 UserStorage 클래스 작성

UserStorage에는 2가지 API가 존재

- loginUser API(로그인하는 API)
- getRoles API(유저인지 관리자인지 확인하는 API)

```js
// Callback Hell example

// setTimeout()을 사용해 네트워크통신을 하는 것처럼 가정 후 로그인 유저 정보를 가져옴
// 유저 역할을 잘 받아온다면 onSuccess, onError

class UserStorage {
  loginUser(id, password, onSuccess, onError) {
    setTimeout(() => {
      if (
        (id === "ellie" && password === "dream") ||
        (id === "coder" && password === "academy")
      ) {
        onSuccess(id);
      } else {
        onError(new Error("not found"));
      }
    }, 2000);
  }

  // 유저 역할을 받아오는 곳
  getRoles(user, onSuccess, onError) {
    setTimeout(() => {
      if (user === "ellie") {
        onSuccess({ name: "ellie", role: "admin" });
      } else {
        onError(new Error("no access"));
      }
    }, 1000);
  }
}
```

UserStorage 클래스를 이용해서 아래의 과정을 구현해보자.

1. 프론트에서 ID & Password를 입력하여 로그인

2. loginUser 로그인 성공시 받아오는 ID를 통해 getRoles API 요청

3. getRoles API요청에 성공하면 이름과 역할을 출력

### 콜백 지옥 코드 ! 👿

```js
const userStorage = new UserStorage();
// uesr id, pw 입력받기
const id = prompt("enter your id");
const password = prompt("enter your passrod");

// loginUser 인자  id, password, onSuccess, onError
userStorage.loginUser(
  id,
  password,
  // onSuccess
  (user) => {
    // getRoles 인자 user, onSuccess, onError
    // 로그인 되었다면 user 역할을 받아옴
    userStorage.getRoles(
      // user
      user,
      // 데이터 잘 받아왔을 시 onSuccess
      (userWithRole) => {
        alert(
          //
          `Hello ${userWithRole.name}, you have a ${userWithRole.role} role`
        );
        userStorage.getRoles(
          user,
          (userWithRole) => {
            alert(
              `Hello ${userWithRole.name}, you have a ${userWithRole.role} role`
            );
          },
          (error) => {
            console.log(error);
          }
        );
      },
      (error) => {
        console.log(error);
      }
    );
  },
  // onError
  (error) => {
    console.log(error);
  }
);
```

### 콜백 지옥의 문제점

1. 가독성 떨어짐
2. 에러가 발생하거나 디버깅을 해야할 때 굉장히 힘들어진다
3. 유지보수도 어려워진다

Promise와 async / await 를 사용하여 콜백 지옥에서 벗어날 수 있다

<br>
Reference

https://www.youtube.com/watch?v=s1vpVCrT8f4
