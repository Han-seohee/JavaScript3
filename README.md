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
