---
title: 手写一个Promise
tags: [Javascript]
categories: learning
toc: true
mathjax: true
---
Promise经常在使用，却了解的不太深，来从头开始学习一下吧

 <!-- more -->

 #### Promise的基本特征

Promise简单的使用方式

 ``` js
const p1 = new Promise((resolve, reject) => {
  console.log('create a promise');
  resolve('成功');
})

console.log('after promise');

const p2 = p1.then(data => {
  console.log(data)
  throw new Error('失败')
})

const p3 = p2.then(data => {
  console.log('success', data)
}, err => {
  console.log('faild', err)
})

 ```

 在控制台会输出：

 ```
 'create a promise'
 'after promise'
 '成功'
 'faild' '失败'

 ```

 由此我们可以大概得出Promise的一些特征：

 1. 首先我们在调用 Promise 时，会返回一个 Promise 对象。
 2. new promise时， 需要传递一个executor()执行器，执行器立即执行，executor 接受 resolve 和 reject 两个参数，Promise 的主要业务流程都在 executor 函数中执行。
 3. 如果运行在 excutor 函数中的业务执行成功了，会调用 resolve 函数；如果执行失败了，则调用 reject 函数。
 4. Promise 的状态不可逆，同时调用 resolve 函数和 reject 函数，默认会采取第一次调用的结果。


再结合[Promise/A+](https://promisesaplus.com/)的规范，可以知道更加规范化的Promise特征：
1. Promise 有三个状态：pending，fulfilled，or rejected，Promise  的默认状态是 pending；「规范 Promise/A+ 2.1」
2. Promise 有一个value保存成功状态的值，可以是undefined/thenable/promise；「规范 Promise/A+ 1.3」
3. Promise 有一个reason保存失败状态的值；「规范 Promise/A+ 1.5」
4. Promise 只能从pending到rejected, 或者从pending到fulfilled，状态一旦确认，就不会再改变；
5. Promise 必须有一个then方法，then 接收两个参数，分别是 promise 成功的回调 onFulfilled,参数是 promise 的value 和 promise 失败的回调 onRejected, 参数是promise的reason；「规范 Promise/A+ 2.2」
6. 如果 then 中抛出了异常，那么就会把这个异常作为参数，传递给下一个 then 的失败的回调onRejected；

#### 开始手写Promise

根据上述特征，我们可以着手完成一个Promise的基本实现

``` js
// 三个状态：PENDING、FULFILLED、REJECTED
const PENDING = 'PENDING';
const FULFILLED = 'FULFILLED';
const REJECTED = 'REJECTED';


class Promise {
  constructor(executor) {
    // 默认状态为 PENDING
    this.status = PENDING;
    // 存放成功状态的值，默认为 undefined
    this.value = undefined;
    // 存放失败状态的值，默认为 undefined
    this.reason = undefined;

    // 存放成功的回调
    this.onResolvedCallbacks = [];
    // 存放失败的回调
    this.onRejectedCallbacks= [];


    // 调用此方法就是成功
    let resolve = (value) => {
      // 状态为 PENDING 时才可以更新状态，防止 executor 中调用了两次 resovle/reject 方法
      if(this.status ===  PENDING) {
        this.status = FULFILLED;
        this.value = value;
          // 依次将对应的函数执行
        this.onResolvedCallbacks.forEach(fn=>fn());
      }
    } 

    // 调用此方法就是失败
    let reject = (reason) => {
      // 状态为 PENDING 时才可以更新状态，防止 executor 中调用了两次 resovle/reject 方法
      if(this.status ===  PENDING) {
        this.status = REJECTED;
        this.reason = reason;
      // 依次将对应的函数执行
        this.onRejectedCallbacks.forEach(fn=>fn());
      }
    }

    try {
      // 立即执行，将 resolve 和 reject 函数传给使用者  
      executor(resolve,reject)
    } catch (error) {
      // 发生异常时执行失败逻辑
      reject(error)
    }
  }

  // 包含一个 then 方法，并接收两个参数 onFulfilled、onRejected
  then(onFulfilled, onRejected) {
      
     // 如果 executor 中存在异步操作，status可能会一直处于 PENDING 的状态
     // 需要将对应的处理函数先存放起来，等待状态确定后，再依次将对应的函数执行
    if (this.status === PENDING) {
      this.onResolvedCallbacks.push(() => {
        onFulfilled(this.value)
      });

      this.onRejectedCallbacks.push(()=> {
        onRejected(this.reason);
      })
    }

    if (this.status === FULFILLED) {
      onFulfilled(this.value)
    }

    if (this.status === REJECTED) {
      onRejected(this.reason)
    }
  }
}

```

#### 进一步完善

在我们使用 Promise 的时候，当 then 函数中 return 了一个值，不管是什么值，我们都能在下一个 then 中获取到，这就是所谓的**then 的链式调用**。而且，当我们不在 then 中放入参数，例：promise.then().then()，那么其后面的 then 依旧可以得到之前 then 返回的值，这就是所谓的**值的穿透**

所以还要对上面的代码进行进一步的优化，来实现这两个特征

``` js
const PENDING = 'PENDING';
const FULFILLED = 'FULFILLED';
const REJECTED = 'REJECTED';

const resolvePromise = (promise, thenCb, resolve, reject) => {
  // 自己等待自己完成是错误的实现，用一个类型错误，结束掉 promise  Promise/A+ 2.3.1
  if (promise === thenCb) { 
    return reject(new TypeError('Chaining cycle detected for promise #<Promise>'))
  }
  // Promise/A+ 2.3.3.3.3 只能调用一次
  let called;
  // 后续的条件要严格判断 保证代码能和别的库一起使用
  if ((typeof thenCb === 'object' && x != null) || typeof x === 'function') { 
    try {
      // 为了判断 resolve 过的就不用再 reject 了（比如 reject 和 resolve 同时调用的时候）  Promise/A+ 2.3.3.1
      let then = thenCb.then;
      if (typeof then === 'function') { 
        // 不要写成 thenCb.then，直接 then.call 就可以了 因为 thenCb.then 会再次取值，Object.defineProperty  Promise/A+ 2.3.3.3
        then.call(thenCb, thenResolve => { // 根据 promise 的状态决定是成功还是失败
          if (called) return;
          called = true;
          // 递归解析的过程（因为可能 promise 中还有 promise） Promise/A+ 2.3.3.3.1
          resolvePromise(promise, thenResolve, resolve, reject); 
        }, thenReject => {
          // 只要失败就失败 Promise/A+ 2.3.3.3.2
          if (called) return;
          called = true;
          reject(thenReject);
        });
      } else {
        // 如果 thenCb.then 是个普通值就直接返回 resolve 作为结果  Promise/A+ 2.3.3.4
        resolve(thenCb);
      }
    } catch (e) {
      // Promise/A+ 2.3.3.2
      if (called) return;
      called = true;
      reject(e)
    }
  } else {
    // 如果 thenCb 是个普通值就直接返回 resolve 作为结果  Promise/A+ 2.3.4  
    resolve(thenCb)
  }
}

class Promise {
  constructor(executor) {
    this.status = PENDING;
    this.value = undefined;
    this.reason = undefined;
    this.onResolvedCallbacks = [];
    this.onRejectedCallbacks= [];

    let resolve = (value) => {
      if(this.status ===  PENDING) {
        this.status = FULFILLED;
        this.value = value;
        this.onResolvedCallbacks.forEach(fn=>fn());
      }
    } 

    let reject = (reason) => {
      if(this.status ===  PENDING) {
        this.status = REJECTED;
        this.reason = reason;
        this.onRejectedCallbacks.forEach(fn=>fn());
      }
    }

    try {
      executor(resolve,reject)
    } catch (error) {
      reject(error)
    }
  }

  then(onFulfilled, onRejected) {
    //解决 onFufilled，onRejected 没有传值的问题
    //Promise/A+ 2.2.1 / Promise/A+ 2.2.5 / Promise/A+ 2.2.7.3 / Promise/A+ 2.2.7.4
    onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : v => v;

    //因为错误的值要让后面访问到，所以这里也要抛出个错误，不然会在之后 then 的 resolve 中捕获
    onRejected = typeof onRejected === 'function' ? onRejected : err => { throw err };
    
    // 每次调用 then 都返回一个新的 promise  Promise/A+ 2.2.7
    let promiseNew = new Promise((resolve, reject) => {
      if (this.status === FULFILLED) {
        //Promise/A+ 2.2.2
        //Promise/A+ 2.2.4 --- setTimeout
        setTimeout(() => {
          try {
            //Promise/A+ 2.2.7.1
            let onFulfilledCb = onFulfilled(this.value);

            // onFulfilledCb可能是一个proimise
            resolvePromise(promiseNew, onFulfilledCb, resolve, reject);
            
          } catch (e) {
            //Promise/A+ 2.2.7.2
            reject(e)
          }
        }, 0);
      }

      if (this.status === REJECTED) {
        //Promise/A+ 2.2.3
        setTimeout(() => {
          try {
            let onRejectedCb = onRejected(this.reason);
            resolvePromise(promiseNew, onRejectedCb, resolve, reject);
          } catch (e) {
            reject(e)
          }
        }, 0);
      }

      if (this.status === PENDING) {
        this.onResolvedCallbacks.push(() => {
          setTimeout(() => {
            try {
              let onFulfilledCb = onFulfilled(this.value);
              resolvePromise(promiseNew, onFulfilledCb, resolve, reject);
            } catch (e) {
              reject(e)
            }
          }, 0);
        });

        this.onRejectedCallbacks.push(()=> {
          setTimeout(() => {
            try {
              let onRejectedCb = onRejected(this.reason);
              resolvePromise(promiseNew, onRejectedCb, resolve, reject)
            } catch (e) {
              reject(e)
            }
          }, 0);
        });
      }
    });
  
    return promiseNew;
  }
}


```