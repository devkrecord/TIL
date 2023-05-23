## 프로미스(promise) 개념 & 활용 ✨

<br>
### Promise 이해하기 🤙🏻
---
promise는 간편한 비동기 처리를 위해 JS에서 제공하는 오프젝트로 callback을 대체하여 사용된다.

프로미스는 어떤 기능을 수행하고 나서 정상적으로 수행하면 처리된 결과값을, 예상치 못한 에러가 발생하면 에러를 전달한다.

추상적으로 설명하자면

언제 오픈할지 모르는 강의가 있다고하자. 이 때 영희는 강의를 사전등록하고 몇시간에서 며칠의 시간이 지난 후 강의가 오픈하였고 영희는 강의가 오픈했다는 이메일을 받았다.

만약 철수가 이미 오픈 된 사전등록창을 발견하여 등록할경우 바로 이메일을 받아볼 수 있다.
<br>

**promise 특징**

1. 프로미스는 자바스크립트 내장 오브젝트.
2. 콜백함수 대신 비동기적인것을 수행해줌.

<br>

### Promise key point 🔑

---

#### state: 프로미스는 비동기 처리 여부에 따라 상태 정보를 갖는다.

- pending (보류) : resolve 또는 reject 함수가 아직 호출되지 않은 상태
- fulfilled (성공) : resolve 함수가 호출된 상태
- rejected (실패) : reject 함수가 호출된 상태

> 기능을 정상 수행 후 최종 데이터를 전달하는 resolve,
> 기능 수행 중 중간에 문제가 생길 경우 호출하는 reject

<br>
#### Producer와 Consumer의 차이를 아는 것. / 정보제공, 소비자

**1. Producer**

새로운 프로미스 함수가 만들어질때는 우리가 전달한 executor라는 함수가 자동적으로 실행되므로 이점을 염두하고 코드작성할 것.

```js
/*
    새로운 프로미스 함수가 만들어질때는 우리가 전달한 executor라는 함수가 자동적으로 실행되므로
    이점을 염두하고 코드 작성할 것.
*/

const promise = new Promise((resolve, reject) => {
  // doing some heavy work ( network, read files들은 비동기적으로 처리하는것이 좋음)
  console.log("doing something..."); // promise가 만들어지는 순간 바로 실행

  // resolve("ellie");

  // setTimeout 비동기 함수를 통해 데이터 통신을 한 것처럼 가정
  setTimeout(() => {
    // resolve(’ellie’); 서버에서 성공적으로 받아온 데이터 or 가공한 데이터값을 리졸브를 통해 전달.

    reject(new Error("no network")); // 자바스크립트에서 제공하는 Error 오브젝트를 통해 값을 전달
  }, 2000);
});
```

<br/>

**2. Consumer: then, catch, finally**

```js
// consumer는 then & catch & finally를 사용해 값을 받아올 수 있다.

promise
  .then((value) => {
    //resolve 값이 넘어온것 확인 가능
    console.log(value); // ellie
  })
  .catch((error) => {
    //reject 확인 가능
    console.log(error); // no network
  })
  .finally(() =>
    // 성공여부와 상관없이 마지막에 무조건 호출.
    console.log("finally")
  );
```

<br/>

**3. Promise chaining**

```js
const fetchNumber = new Promise((resolve, reject) => {
  setTimeout(() => resolve(1), 1000);
});

// then은 값을 바로 전달할 수 있으며 프로미스를 전달해도 된다 .
fetchNumber
  .then((num) => num * 2) // 2
  .then((num) => num * 3) // 6
  .then((num) => {
    return new Promise((resolve, reject) => {
      setTimeout(() => resolve(num - 1), 1000); //5
    });
  })
  .then((num) => console.log(num));
```

<br/>

**4. Error handling**

```js
// 4. 프로미스를 체이닝했을 때 에러 핸들링하는 방법
const getHen = () =>
  new Promise((resolve, reject) => {
    setTimeout(() => resolve("🐔"), 1000);
  });
const getEgg = (hen) =>
  new Promise((resolve, reject) => {
    //setTimeout(() => resolve(`${hen} => 🥚`), 1000);
    setTimeout(() => reject(new Error(`error! ${hen} => 🥚`), 1000));
  });
const cook = (egg) =>
  new Promise((resolve, reject) => {
    setTimeout(resolve(`${egg} => 🍳`), 1000);
  });
getHen()
  .then((hen) => getEgg(hen))
  .catch((error) => {
    // 계란을 받아올때 에러가 생겨도 전체 프로미스 체이닝에 오류가 생기지 않도록 처리.
    return "🥖";
  })
  .then((egg) => cook(egg))
  .then((meal) => console.log(meal))
  .catch(() => console.log()); // 실행되는데 삼초 걸림 🐔 => 🥚 => 🍳
//then(getEgg).then(cook).then(console.log) 이런 식으로 생략가능!
```

<br>
Reference
https://www.youtube.com/watch?v=JB_yU6Oe2eE
