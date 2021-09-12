# 자바스크립트에서 비동기처리 다루기
2021.09.10
### :dog:비동기 처리의 이해

> 동기적 처리

```
function work() {
  const start = Date.now();
  for (let i = 0; i < 1000000000; i++) {
    
  }
  const end = Date.now();
  console.log(end - start + 'ms');
}

work();
console.log('다음 작업')

// 512ms
   다음 작업
```

:point_down:

>비동기적 처리

```
function work() {
  setTimeout(() => {
    const start = Date.now();
    for (let i = 0; i < 1000000000; i++) {
    
    }
    const end = Date.now();
    console.log(end - start + 'ms');
    }, 0)
  }

console.log('작업 시작!');
work();
console.log('다음 작업')

// 작업 시작!
   다음 작업
   526ms
```

>콜백 함수를 파라미터로 전달

```
function work(callback) {
  setTimeout(() => {
    const start = Date.now();
    for (let i = 0; i < 1000000000; i++) {
    
    }
    const end = Date.now();
    console.log(end - start + 'ms');
    callback(end - start)
    }, 0)
  }

console.log('작업 시작!');
work((ms) => {
  console.log('작업이 끝낫어요!');
  console.log(ms +'ms 걸렸다고 해요.')
});
console.log('다음 작업')

// 작업 시작!
   다음 작업
   518ms
   작업이 끝났어요!
   518ms 걸렸다고 해요.
```

---
### :dog:Promise
+ 비동기작업을 조금 더 편하게 처리할 수 있도록 ES6에 도입 된 기능

>callback 지옥

```
function increaseAndPrint(n, callback) {
  setTimeout(() => {
    const increased = n + 1;
    console.log(increased);
    if (callback) {
      callback(increased)
    }
  }, 1000)
}

increaseAndPrint(0, n => {
  increaseAndPrint(n, n => {
    increaseAndPrint(n, n => {
      increaseAndPrint(n, n => {
        increaseAndPrint(n, n => {
          console.log('작업 끝!')
        })
      })
    })
  });
});

// 1
   2
   3
   4
   5
   작업 끝!
```

:point_down: Promise
>1초 뒤에 성공하는 예제 (resolve)

```
const myPromise = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('result');
  }, 1000)
});

myPromise.then(result => {
  console.log(result);
})

// result
```

:point_down: Promise
>1초 뒤에 실패하는 예제 (reject)

```
const myPromise = new Promise((resolve, reject) => {
  setTimeout(() => {
    reject(new Error());
  }, 1000)
});

myPromise.then(result => {
  console.log(result);
}).catch(e => {
  console.error(e);
})

// Error
```

>Promise를 만드는 함수

```
function increaseAndPrint(n) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      const value = n + 1;
      if (value === 5) {
        const error = new Error();
        error.name = 'ValueIsFiveError';
        reject(error);
        return;
      }
      console.log(value);
      resolve(value);
    }, 1000);
  });
}

increaseAndPrint(0).then(n => {
  console.log('result: ', n);
})

// 1
   result: 1
```

---
### :dog: async, await

```
function sleep(ms) {
  return new Promise(resolve => setTimeout(resolve ,ms));
}

async function process() {
  console.log('안녕하세요!');
  await sleep(1000);
  console.log('반갑습니다!');
}

process();

// 안녕하세요!
   반갑습니다!
```

>true 출력

```
function sleep(ms) {
  return new Promise(resolve => setTimeout(resolve ,ms));
}

async function process() {
  console.log('안녕하세요!');
  await sleep(1000);
  console.log('반갑습니다!');
  return true;
}

process().then(value => {
  console.log(value);
});

// 안녕하세요!
   반갑습니다!
   true
```

>Error 발생시키기

```
function sleep(ms) {
  return new Promise(resolve => setTimeout(resolve ,ms));
}

async function makeError() {
  await sleep(1000);
  const error = new Error();
  throw error;
}

async function process() {
  try {
    await makeError();
  } catch (e) {
    console.error(e);
  }
}

process()

// Error
```

---
2021.09.12
### :dog: Promise all, Promise.race

```
function sleep(ms) {
  return new Promise(resolve => setTimeout(resolve ,ms));
}

const getDog = async () => {
  await sleep(1000);
  return '멍멍이';
}

const getRabbit = async () => {
  await sleep(500);
  return '토끼';
}

const getTurtle = async () => {
  await sleep(3000);
  return '거북이';
}

async function process() {
  const dog = await getDog();
  console.log(dog);
  const rabbit = await getRabbit();
  console.log(rabbit);
  const turtle = await getTurtle();
  console.log(turtle);
}

process()

// 멍멍이
   토끼
   거북이
```

+ Promise all
>여러개의 promise를 동시에 처리

```
function sleep(ms) {
  return new Promise(resolve => setTimeout(resolve ,ms));
}

const getDog = async () => {
  await sleep(1000);
  return '멍멍이';
}

const getRabbit = async () => {
  await sleep(500);
  return '토끼';
}

const getTurtle = async () => {
  await sleep(3000);
  return '거북이';
}

async function process() {
  const result = await Promise.all([getDog(), getRabbit(), getTurtle()]);
  console.log(results);
}

process()

// ["멍멍이", "토끼", "거북이"]
```

:point_down:

```
function sleep(ms) {
  return new Promise(resolve => setTimeout(resolve ,ms));
}

const getDog = async () => {
  await sleep(1000);
  return '멍멍이';
}

const getRabbit = async () => {
  await sleep(500);
  return '토끼';
}

const getTurtle = async () => {
  await sleep(3000);
  return '거북이';
}

async function process() {
  const [dog, rabbit, turtle] = await Promise.all([getDog(), getRabbit(), getTurtle()]);
  console.log(dog);
  console.log(rabbit);
  console.log(turtle);
}

process()

// 멍멍이
   토끼
   거북이
```

+ Promise.race

```
function sleep(ms) {
  return new Promise(resolve => setTimeout(resolve ,ms));
}

const getDog = async () => {
  await sleep(1000);
  return '멍멍이';
}

const getRabbit = async () => {
  await sleep(500);
  return '토끼';
}

const getTurtle = async () => {
  await sleep(3000);
  return '거북이';
}

async function process() {
  const first = await Promise.race([getDog(), getRabbit(), getTurtle()]);
  console.log(first);
}

process()

// 토끼
```
---
# HTML과 JavaScript 연동하기
