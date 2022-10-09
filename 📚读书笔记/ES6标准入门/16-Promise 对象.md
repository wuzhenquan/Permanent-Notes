Promise 构造函数接受一个 `function(resolve, reject){}` 作为参数

- Promise 有两种状态变化
  - 从 Pending 变为 Resolved
  - 从 Pending 变为 Rejected
- new 一个 Promise, 实际上就是创建一个这样的对象: `Promise {[[PromiseStatus]]: "resolved", [[PromiseValue]]: "hello promise"}`, 其中, 属性 PromiseStatus 的属性值优 resolve 或者 reject 函数决定, 属性 PromiseValue 的属性值由 resolve 或者 reject 函数所带的值决定.
- `new Promise((resolve, reject)=>{})` 里
  - 执行了 resolve , 这个 Promise 实例的状态(PromiseStatus)就会变成 Resolved
  - 执行了 reject , 这个 Promise 实例的状态(PromiseStatus)就会变成 Rejected

新建一个 promise 实例

```javascript
var promise = new Promise((resolve,reject)=>{
  // resolve 和 reject 是两个函数
  // 想让 Promise 从 Pending 变为 Resolved, 用 resolve(), 所带参数是 Promise 对象中 PromiseValue 属性的值
  // 想让 Promise 从 Pending 变为 Rejected, 用 reject(), 所带参数是 Promise 对象中 PromiseValue 属性的值
});
```

- resolve 
  - 是一个函数

  - 将 Promise 对象的状态从 Pending 变为 Resolved

  - 在异步操作成功时调用并将结果作为参数传递出去

  - ```javascript
    var p1 = new Promise(function(resolve, reject){
        resolve('hello promise')
    });
    console.log(p1);
    //Promise {[[PromiseStatus]]: "resolved", [[PromiseValue]]: "hello promise"}
    ```
- reject 
  - 是一个函数

  - 将 Promise 对象的状态从 Pending 变为 Rejected 

  - 在异步操作失败时调用, 并将异步操作报出的错误作为参数传递出去



- resolve 和 reject 的参数

  - resolve: 

    - 可能是正常的值, 表示异步操作的结果有可能是一个值. 
    - 可能是另一个 Promise 实例, 表示异步操作的结果有可能是另一个异步操作

  - reject:

    - 通常是 Error 对象的实例

  - ```javascript
    // 好难理解
    var p1 = new Promise(function(resolve, reject){
      setTimeout(() => reject(new Error('fail')), 3000);
    });
    var p2 = new Promise(function(resolve, reject){
      setTimeout(() => resolve(p1), 1000);
    })
    p2.then(result => console.log(result));
    p2.catch(error => resolve(p1), 1000)
    // p1 是个 Promise , 3秒之后变为 Rejected. p2的状态由 p1 决定, 1秒之后, p2 调用 resolved 方法, 但是此时 p1 的状态还没有改变, 因此 p2 的状态也不会改变. 又过了 2 秒, p1 变为 Rejected , p2 也跟着变为 Rejected. 
    ```


#### resolve 的参数如果是另一个 Promise 实例

```javascript
var p1 = new Promise((resolve, reject)=>{
  setTimeout(() => resolve('成功啦哈哈哈'), 3000)
})
var p2 = new Promise((resolve, reject)=>{
  setTimeout(() => resolve(p1), 3000)
})
p2.then(result => console.log(result))
p2.catch(error => console.log(error))
// 3秒之后, p2 是: Promise {[[PromiseStatus]]: "resolved", [[PromiseValue]]: "成功啦哈哈哈"}
var p1 = new Promise((resolve, reject)=>{
  setTimeout(() => reject('失败啦哈哈哈'), 3000)
})
var p2 = new Promise((resolve, reject)=>{
  setTimeout(() => resolve(p1), 3000)
})
p2.then(result => console.log(result))
p2.catch(error => console.log(error))
// 3秒之后 p2 是: Promise {[[PromiseStatus]]: "rejected", [[PromiseValue]]: "失败啦哈哈哈"}
```

#### 用 Promise 对象实现的 AJAX 操作的例子

```javascript
let getJSON = function(url){
  let promise = new Promise(function(resolve, reject){
    let client = new XMLHttpRequest()
    client.open("GET", url)
    client.onreadystatechange = handler
    client.responseType = "json"
    client.setRequestHeader("Accept", "application/json")
    client.send()
    function handler(){
      if(this.readyState !== 4){
        return
      }
      if(this.status === 200){
        resolve(this.response)
      }else{
        reject(new Error(this.statusText))
      }
    }
  })
  return promise
}
let url = "https://api.github.com/search/users?q=wuzhenquan"
getJSON(url).then(function(json){
  console.log('Contents: ' + json)
}).catch(function(error){
  console.error('出错了', error)
})
```



#### Promise.prototype.then(()=>{},()=>{})

指定 Resolved 状态和 Rejected 状态的回调函数,  当从 Pending 切换到 Resolved 或者 Rejected 时, 调用 then 方法里面的回调函数. then 方法可以接受两个回调函数作为参数:

- 第一个参数是当从 Pending 切换到 **Resolved** 时调用的回调函数
- 第二个参数是当从 Pending 切换到 **Rejected** 时调用的回调函数
- then 绑定的回调函数的参数是对应的 resolve 或 reject 函数的参数( resolve 函数和 reject 函数所带的参数会被传递给 then 中的回调函数作为这些回调函数的参数)
- then 方法返回的是一个新的 Promise 实例, 因此可以采用链式写法, 即 then 方法后面再调用一个 then 方法
- 第二个参数其实没必要了, 用 catch 方法就好了.


#### Promise.prototype.catch()

是 `.then(null, rejection)` 的别名(所以, 就没必要用`.then(null, rejection)`这样的写法了), 用于指定发生错误时的回调函数. 如果 Promise 对象状态变为 Rejected, 就会调用 catch 方法指定的回调函数处理这个错误. 

```javascript
var promise = new Promise(function(resolve, reject){
  resolve("ok")
  throw new Error('test')
})
promise.then(function(value){console.log(value)}).catch(function(error){console.log(error)})

var promise = new Promise(function(resolve, reject){
  resolve("ok")
  setTimeout(function(){throw new Error('test')}, 0)
})
promise.then(function(value){console.log(value)})
```

