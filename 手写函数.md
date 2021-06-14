### 数组去重
- 利用es6 set。 不能去除`{}`空对象
- 利用object属性不能重复去重
- 利用Map结构去重
- 新建空[],利用includes，for循环遍历数组

### call的实现
```javascript
Function.prototype.lynCall = function (obj, ...rest) {
  let ctx = obj || window
  ctx.func = this
  let res = rest.length > 0 ? ctx.func(...rest) : ctx.func()
  delete ctx.func
  return res
}
```
### apply 实现
```javascript
Function.prototype.lynApplay = function (obj, rest) {
  let ctx = obj || window
  ctx.func = this
  let res = rest && rest.length > 0 ? ctx.func(...rest) : ctx.func()
  delete ctx.func
  return res
}
```
### bind实现
```javascript
Function.prototype.lynBind = function (obj) {
  let ctx = this
  return function (...rest) {
    return ctx.apply(obj, rest)
  }
}
```
### reduce实现
```javascript
Function.prototype.lynReduce  = function (fn, initVal) {
  let self = this
  if (Array.isArray(self)) {
    throw Error('Must to be array!')
  }
  let res = initVal || this[0]
  const start = initVal !== undefined ? 0 : 1
  for (let len = self.length, i = start; i < len; i++) {
    res = fn(res, self[i], i, this)
  }
  return res
}
```
### promise实现
> promise/A+ 规范：promise 是一个包含 then 方法的对象或函数，promise 有3个状态，分别是pending, fulfilled 和 rejected。状态一旦确定不可更改。then 方法必须返回promise。then方法可以被调用很多次，每次注册一组onFulfilled和onRejected的callback。它们如果被调用，必须按照注册顺序调用。
```javascript
handlePromise = (promise, res, resolve, reject) => {
  if (promise === res) {// 循环引用报错
    // 自己等待自己完成是错误的实现，用一个类型错误，结束掉 promise
    return reject(new TypeError('Chaining cycle detected for promise'))
  }
  // Promise/A+ 2.3.3.3.3 只能调用一次
  let called
  // 后续的条件要严格判断 保证代码能和别的库一起使用
  if (res != null && (typeof res === 'object' || typeof res === 'function')) {
    try {
      // 为了判断 resolve 过的就不用再 reject 了
      let then = res.then
      if (typeof then === 'function') {
        // 不要写成 x.then，直接 then.call 就可以了 因为 x.then 会再次取值
        then.call(res, data => {
          if (called) return
          called = true
          // 递归解析的过程（因为可能 promise 中还有 promise
          handlePromise(promise, data, resolve, reject)
        }, err => {
          if (called) return
          called = true
          reject(err)
        })
      } else {
        // if (called) return
        // called = true
        resolve(res)
      }
    } catch (e) {
      if (called) return
      called = true
      reject(e)
    }
  } else {
    // if (called) return
    // called = true
    resolve(res)
  }
}

const pending = 'PENDING'
const fulfilled = 'FULFILLED'
const rejected = 'REJECTED'

class LynPromise {
  state = pending
  value = undefined
  reason = undefined
  resolveCbs = []
  rejectCbs = []
  
  constructor(executor) {
    if (typeof executor !== 'function') {
      throw new Error('Promise must accept a function as a parameter')
    }
    
    let resolve = (data) => {
      console.log('resolve method')
      if (this.state === pending) {
        this.state = 'FULFILLED'
        this.value = data
        this.resolveCbs.forEach(fn => fn())
      }
    }
    let reject = (reason) => {
      console.log('reject method')
      if (this.state === pending) {
        this.state = 'REJECTED'
        this.reason = reason
        this.rejectCbs.forEach(fn => fn())
      }
    }
    try {
      executor(resolve, reject)
    } catch (e) {
      reject(e)
    }
  }
  then(onResolve, onReject) {
    console.log('then method')
    onResolve = typeof onResolve === 'function' ? onResolve : v => v;
    onReject = typeof onReject === 'function' ? onReject : err => {throw err};
    let otherPromise = new LynPromise((resolve, reject) => {
      if (this.state === fulfilled) {
        setTimeout(() => {
          const res = onResolve(this.value)
          handlePromise(otherPromise, res, resolve, reject)
        })
      }
      if (this.state === rejected) {
        setTimeout(() => {
          const res = onReject(this.reason)
          handlePromise(otherPromise, res, resolve, reject)
        })
      }
      if (this.state === pending) {
        this.resolveCbs.push(() => {
          setTimeout(() => {
            const res = onResolve(this.value)
            handlePromise(otherPromise, res, resolve, reject)
          })
        })
        this.rejectCbs.push(() => {
          setTimeout(() => {
            const res = onReject(this.reason)
            handlePromise(otherPromise, res, resolve, reject)
          })
        })
      }
    })
    return otherPromise
  }
}
```
### 防抖节流函数
> 防抖： 触发高频事件n秒后函数**只会执行一次**，如果n秒内高频事件再次被触发，则重新计算时间
```javascript
function debounce(fn, time = 300) {
  let timer = null
  return function () {
    clearTimeout(timer)
    timer = setTimeout(function () {
      fn.apply(this, arguments)
    }, time)
  }
}
```
> 节流： 高频事件触发，但在n秒内只会执行一次，所以节流会稀释函数的执行频率
```javascript
function throttle(fn, time = 300) {
  let pre = new Date().getTime()
  return function () {
    let now = new Date().getTime()
    if (now - pre >= time) {
      fn.apply(this, arguments)
    }
  }
}
``` 
