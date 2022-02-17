# browser

* 宏任务微任务
  ```javascript
    function sleep(time) {    
      let startTime = new Date();    
      while (new Date() - startTime < time) {}    
      console.log('<--Next Loop-->');
    }

    setTimeout(() => {
      console.log('timeout1');
      setTimeout(() => {        
        console.log('timeout3');
        sleep(1000);
      });    
      new Promise((resolve) => {        
        console.log('timeout1_promise');
        resolve();
      }).then(() => {        
        console.log('timeout1_then');
      });
      sleep(1000);
    });
        
    setTimeout(() => {
      console.log('timeout2');
      setTimeout(() => {        
        console.log('timeout4');
        sleep(1000);
      });    
      new Promise((resolve) => {        
        console.log('timeout2_promise');
        resolve();
      }).then(() => {       
        console.log('timeout2_then');
      });
      sleep(1000);
    });
  ```
  ```javascript
    async function async1(){	
      console.log('async1 start')	
      await async2()	
      console.log('async1 end')	
    }	
    async function async2(){	
      console.log('async2')	
    }	
    console.log('script start')	
    setTimeout(function(){	
      console.log('setTimeout0') 	
    },0)  	
    setTimeout(function(){	
      console.log('setTimeout3')
    },3)  	
    setImmediate(() => console.log('setImmediate'));	
    async1();	
    process.nextTick(() => console.log('nextTick'));	

    new Promise(function(resolve){	
      console.log('promise1')	
      resolve();	
      console.log('promise2')	
    }).then(function(){	
      console.log('promise3')	
    })	
    console.log('script end')
  ```
* Event Loop
https://juejin.cn/post/6844903761949753352#heading-12
https://blog.csdn.net/LuckyWinty/article/details/104765786
<br />
* v8 对 await 的优化
https://zhuanlan.zhihu.com/p/53944576
<br />
* async/await 实现原理
https://zhuanlan.zhihu.com/p/112800700
  ```javascript
    function executor(generator, ...args) {
      const iter = generator(args)
      const n = iter.next
      if (n.done) {
        return Promise.resolve(n.value)
      } else {
        return new Promise(resolve => {
          Promise.resolve(n.value).then(ret => {
            _r(iter, ret, resolve)
          })
        })
      }
    }

    function _r(iter, ret, resolve) {
      const n = iter.next(ret)
      if (n.done) {
        resolve(n.value)
      } else {
        n.next.then(ret => {
          _r(iter, ret, resolve)
        })
      }
    }
  ```
  https://juejin.cn/post/6844904102053281806
    ```javascript
      function asyncToGenerator(generatorFunc) {
        return function() {
          const gen = generatorFunc.apply(this, arguments)
          return new Promise((resolve, reject) => {
            function step(key, arg) {
              let generatorResult
              try {
                generatorResult = gen[key](arg)
              } catch (error) {
                return reject(error)
              }
              const { value, done } = generatorResult
              if (done) {
                return resolve(value)
              } else {
                return Promise.resolve(value).then(val => step('next', val), err => step('throw', err))
              }
            }
            step("next")
          })
        }
    }

    ```
* 垃圾回收
https://juejin.cn/post/6981588276356317214


* 代码输出
  ```javascript
    var length = 10;
    function fn() {
    return this.length + 1;
    }
    var obj1 = {
    length: 5,
      test1: function() {
        return fn()
    }
    }
    obj1.test2 = fn;
    obj1.test1.call()
    obj1.test1()
    obj1.test2.call()
    obj1.test2()
  ```