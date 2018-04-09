# promise
异步处理
#与app的数据交互


*现在前端开发基本都是通过后端接口来渲染页面,在一些页面,往往接口之间都存在依赖关系. 比如一个就诊人页面,在一个页面首先要请求就诊人列表,然后根据就诊人的id获取,就诊人的列表信息,然后,点击列表的某条信息显示详情,大致就会形成这种写法*

```
 $.ajax({
  url: url,
  data: data,
  success: function(data){
        if(data.code === 'ok'){
                $.ajax({
                    url:url,
                    data:data,
                    success:function(data){
                        if(data.code === 'ok'){
                            $.ajax({
                                url:url,
                                data:data,
                                success:function(data){
                                    // do something
                                }
                            })
                        }
                    }
                })
        }
  },
});
 这也就是常说的回调地狱,代码看上去结构十分混乱
```


*es6里面一个新属性很受欢迎----promise*

promise 提供了以下方法:
1. Promise.prototype.catch()
2. Promise.prototype.then() 
3. Promise.all(),
4. Promise.race(),
5. Promise.reject(),
6. Promise.resolve()方法


*Promise.resolve() 实例*
 ```
   function p(str){
       return new Promise((resolve, reject) => {
            resolve(str)
       }).then(
           data => {
             console.log(`sucess:${data}`)
           },
           data => {
            console.log(`error:${data}`)
           }
       )
   };
   p('hello world'); // 打印出 sucess:hello world
 ```

*Promise.race()实例*
```
var p1 = new Promise(function(resolve, reject) { 
    setTimeout(resolve, 500, 'one'); 
});
var p2 = new Promise(function(resolve, reject) { 
    setTimeout(resolve, 100, 'two'); 
});

Promise.race([p1, p2]).then(function(value) {
  console.log(value); // "two"
  // Both resolve, but p2 is faster
});
// 输出 two
```

 *Promise.prototype.catch() 实例*
 ```
 var p1 = new Promise(function(resolve, reject) {
  resolve('Success');
 });

p1.then(function(value) {
  console.log(value); // "Success!"
  throw 'oh, no!';
}).catch(function(e) {
  console.log(e); // "oh, no!"
}).then(function(){
  console.log('after a catch the chain is restored');
}, function () {
  console.log('Not fired due to the catch');
});
 // 依次输出  Success-->oh, no!--->after a catch the chain is restored
```

 *Promise.reject() 实例*

```
    function p(str){
       return new Promise((resolve, reject) => {
            reject(str)
       }).then(
           data => {
             console.log(`sucess:${data}`)
           },
           data => {
           console.log(`error:${data}`)
           }
       )
   };
   p('hello world'); // 打印出 error:hello world
```

*Promise.all() 实例*
``` 
    var p1 = Promise.resolve(3);
    var p2 = 1337;
    var p3 = new Promise((resolve, reject) => {
        setTimeout(function(){
            resolve('foo')
        },1000)
    }); 

    Promise.all([p1, p2, p3]).then(values => { 
        console.log(values); 
    });

    // 依次打印出 [3, 1337, "foo"] 

```

> 回调函数真正的问题在于他剥夺了我们使用 return 和 throw 这些关键字的能力。而 Promise 很好地解决了这一切
所谓 Promise，就是一个对象，用来传递异步操作的消息。它代表了某个未来才会知道结果的事件（通常是一个异步操作），并且这个事件提供统一的 API，可供进一步处理。

> Promise 对象有以下两个特点。
1. 对象的状态不受外界影响。Promise 对象代表一个异步操作，有三种状态：Pending（进行中）、Resolved（已完成，又称 Fulfilled）和 Rejected（已失败）。只有异步操作的结果，可以决定当前是哪一种状态，任何其他操作都无法改变这个状态。这也是 Promise 这个名字的由来，它的英语意思就是「承诺」，表示其他手段无法改变。
2. 一旦状态改变，就不会再变，任何时候都可以得到这个结果。Promise 对象的状态改变，只有两种可能：从 Pending 变为 Resolved 和从 Pending 变为 Rejected。只要这两种情况发生，状态就凝固了，不会再变了，会一直保持这个结果。就算改变已经发生了，你再对 Promise 对象添加回调函数，也会立即得到这个结果。这与事件（Event）完全不同，事件的特点是，如果你错过了它，再去监听，是得不到结果的。

*有了 Promise 对象，就可以将异步操作以同步操作的流程表达出来，避免了层层嵌套的回调函数。此外，Promise 对象提供统一的接口，使得控制异步操作更加容易。*

> Promise 缺点

*Promise 也有一些缺点。首先，无法取消 Promise，一旦新建它就会立即执行，无法中途取消。其次，如果不设置回调函数，Promise 内部抛出的错误，不会反应到外部。第三，当处于 Pending 状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。*

*异步操作是 JavaScript 编程的麻烦事,虽然Promise改进了,异步操作的的使用方式,但是还是不够彻底,如果让异步编程写起来更优雅,做到不用关心它s是不是异步, async/await是最终解决方法*

*实例*
``` 
var f = function(str,t){
  return new Promise((resolve, reject) => {
      setTimeout(() => {
          resolve(str)
      },t)
  }).then(
      data => {
          console.log(data)
      }
   )
};
async function a(){
  await f('one', 3000);
  await f('two', 1000)
};

// 输出 one  two

```

*用async/await 我们不用关心一个接口成功所需要的时间,让我们编写异步代码更优雅*

