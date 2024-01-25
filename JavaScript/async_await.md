## 비동기의 꽃 🌷 </br> JavaScript async 와 await 그리고 유용한 Promise APIs

<br/>

### async/ await 이해하기

---

promise를 계속하여 체이닝하면 코드가 난잡해질 수 있다.
그럴 때 async / await 을 사용하면
동기식으로 코드를 순서대로 작성하는것처럼 간편하게 작성할 수 있게 도와준다.

<span style="background-color:#fff5b1">async / await은 새로운 것이 추가된것이 아니라 기존에 존재하는 promise 위에 조금 더 간편한 API를 제공한다.</span>
(try, catch문도 가능)

이처럼 기존에 존재하는 것 위에 또는 기존에 존재하는것을 감싸서 우리가 조금 더 간편히 쓸 수 있도록 API를 제공하는 것을 syntactic sugar 이다.

<span style="background-color:#fff5b1">async / await 은 promise를 깔끔하게 사용할 수 있게 해준다.</span> 하지만 항상 promise가 나쁘고 무조건 async / await 을 써야한다는 것은 아니다. 때에 따라 맞게 사용하면 된다.
<br>

### async/ await 예제 👩🏻‍💻

---

#### 1. async

```js
// 10초 후 네트워크에 요청해서 값을 가져오는 함수가 있다고 가정해보자
// 1.) 비동기 처리를 하지 않으면 10초동안 fetchUser 함수에 머물러 있고
// 2.) 엘리란 값을 리턴
function fetchUser() {
    return ‘ellie’;
};

// promise 사용하여 비동기처리
function fetchUser() {
    return new Promise((resolve, reject)⇒{
        // resolve와 reject 호출하지 않고 아래와 같이 리턴하면 promise가 pending 상태인것을 확인할 수 있다.
        // return ‘ellie’;

        return(’ellie’);
    });

};

// async를 함수앞에 써주면 함수가 자동으로 promise로 변환이 되어진다.
// 화살표 함수 사용시 const fetchUsers = async () => {} )
async function fetchUser() {
    return ‘ellie’;
};

// 이렇게 무언가 오래 걸리는 코드에 대해 비동기적 처리를 하지 않으면
// 자바스크립트 엔진은 동기적으로 코드를 수행(한줄 한줄 한줄이 끝나야 다음 코드로 넘어감)하기 때문에
const user = fetchUser ();

console.log(user); // 3) user값 콘솔로그에 표현

/*
비동기 처리 해줘야하는 경우
사용자 데이터를 받아오는데 10초가 걸리기 때문에 만약 이 뒤에 웹페이지 ui를 표시하는 기능을 수행하는 코드들이 있다면 이것이 끝나는 동안 데이터가 웹페이지에 표시되지 않기에 사용자는 10초정도 텅텅비어있는 웹페이지만 보게 된다. 때문에 오래걸리는 일들은 비동기적으로 처리해줘야한다.
*/
```

#### 2. await

```js
// await이란 키워드는 async가 붙은 함수안에서만 사용할 수 있다.

function delay(ms) {

    return new Promise((resolve)⇒ setTimeout(resolve, ms));

    });

}

async function getApple() {

await delay(3000); // await을 쓰면 delay가 끝날때까지 기다려줌

throw ‘error’;

return ‘🍎’;

}

// getBanana라는 프로미스를 만들어주는 함수

async function getBanana() {

// 동기적인 코드를 쓰는 것처럼 이렇게 표현하면 딜레이가 끝날 떄까지 기다렸다가 바나나를 리턴한다고 코드를 더 쉽게 이해가능

await delay(1000);

return ‘🍌’;

}

// 위 코드를 promise를 사용하여 표현하면 아래와 같다.

function getBanana() {

return delay(1000)

.then(()⇒’🍌’);

}

// 콜백지옥을 연상하는 코드 ^^

// 프로미스도 너무 중첩하여 체이닝하면 콜백지옥과 비슷한 문제점이 생긴다.

function pickFruits () {

return getApple()

.then(apple ⇒ {

return getBanana().then(banana ⇒ `${apple} + ${banana}`);

})

}


// async / await 를 이용한 코드

async function pickFruits () {

// 바나나와 사과를 받아오는데는 서로 연관이 없기에 일초씩 기다리는것이 비효율적

const apple = await getApple();

const banana = await getBanana();

 // 프로미스를 만드는 순간 바로 코드블럭이 실행됨

// 이렇게 병렬적으로 기능을 수행하는 경우 아래와같이 더럽게 코드를 작성하지 않는다.

const applePromise = getApple();

const bananaPromise = getBanana();

const apple = await applePromise();  // 동기화를 시켜준다.

const banana = await bananaPromise(); // 동기화를 시켜준다.

return `${apple} + ${banana}`; // 1초만에 병렬적으로 실행

}

pickFruits().then(console.log);
```

#### 3. Promise APIs

```js
function pickAllFruits () {

// Promise.all api를 사용 배열을 전달하게 되면 모든 프로미스들이 병렬적으로 데이터를 받을 떄까지 모아준다.

//getApple 프로미스와 getBanana 프로미스의 배열을 전달하게 되면

return Promise.all([getApple(), getBanana()])

.then(fruits ⇒ fruits.join(’ + ‘) );  // 배열을 string 으로 묶어주는 join함수

}

pickAllFruit().then(console.log); // 🍎 + 🍌

// Promise race api를 이용하면 배열에 전달된 프로미스들 중 가장 먼저 값을 리턴하는 프로미스의 값만 전달됨.

pickOnlyOne() {

return Promise.race([getApple(), getBanana()]);

}

pickOnlyOne().then(console.log);
```

<br>
Reference

https://www.youtube.com/watch?v=aoQSOZfz3vQ
