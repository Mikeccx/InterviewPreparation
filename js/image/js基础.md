# js基础

# js基础

## js的基础类型

1.  undefined

2.  null

3.  string

4.  number (iee754双精度64位)

    ![](image/image_J604mYlUOG.png)

    计算机数字都用补码表示

    原因：1. 统一运算规则，统一为加

    2\. 可以让符号为一同参与运算

    3\. 防止0有两个编码 （-0 和 +0）

    js安全整数取值范围

    \[2^53,  -2^53]

    安全范围：\[2^53-1,  -2^53-1]

    2^53精度会丢失 2^53 + 1 === 2^53&#x20;

    *   为什么是53

        iee754有隐藏位，默认表示1.xxxxx（52位）所以实际是53位

    解决大整数：转换成字符串相加

    <https://leetcode.cn/problems/add-strings/>

    ```javascript
    var addStrings = function(num1, num2) {
        let i = num1.length - 1, j = num2.length - 1, adv = 0
        const res = []
        while(i >=0 || j >=0 || adv > 0) {
            const x = i >= 0 ? +num1[i] : 0 
            const y = j >= 0 ? +num2[j] : 0 
            const result = x + y + adv
            adv = Math.floor(result / 10) 
            res.push(result % 10)
            i--
            j--
        }
        return res.reverse().join('')
    };
    ```

5.  boolean

6.  symbol (es6)

7.  bigint (es11)

注意事项

    ```javascript
    undefined == null // true
    undefined === null // false

    '123' == new String(123) // true
    '123' === new String(123) // false
    ```

# this指向

1.  谁调用指向谁

2.  剪头函数只能在函数里找到上下文，对象找不到

    ````javascript
    name: '1'
    const a = {
      name: '2',
      say: () => console.log(this.name)
    }
    a.say() // 1
    ```*
    ````

隐式绑定全局

obj执行上下文

显式绑定(call、bind、apply)

构造函数绑定

# 继承

原型链

    缺点：实例共享引用属性

    ```javascript
    function P(name) {
      this.name = name
      this.play = [1,2,3]
      this.say = function(){
        console.log(this.name)
      }
    }

    function son(name1) {
      this.name1 = name1
    }
    son.prototype = new P('fa')
    ```*

构造函数

    缺点：1.无法共享原型链的公共方法

    2\. 每个子实例保存一份父实例，有消耗



    ```javascript
    function  fa(){
        this.color=["red","green","blue"];
        this.say = function() {
            console.log('say hello')
        }
    }

    fa.prototype.sayBye = () => {
        console.log('say bye')
    }

    function son() {
        SuperType.call(this)
    }

    ```*

组合

    缺点：父类重复调用，会增加多余的属性



    ```javascript
    function  fa(){
        this.color=["red","green","blue"];
    }

    fa.prototype.sayBye = () => {
        console.log('say bye')
    }

    function son() {
        // 调用一次父类
        fa.call(this)
    }

    son.prototype = new fa() // 第二次调用父类
    ```*

原型

    缺点：与原型链继承相仿，共享属性可被随意更改

    ```javascript
    // 创建一个对象
    var fa = {
        name:'tracy',
        hobbys:['soccer','football'] //共享变量
    }
    // 对fa进行浅拷贝
    var son = Object.create(fa);
    var son1 = Object.create(fa);

    son.hobbys.push(swim)
    son1.hobbys.push(run)

    console.log(fa.hobbys) // ['soccer','football','swim','run']  //共享变量被改变

    ```*

寄生继承

    缺点：属性共享，无法传递参数

    ```javascript
    function creatObj(obj){
        // 浅拷贝对象 不限于es5语法Object.create
        var clone = Object.create(obj);
        // 对象增强
        clone.say = function(){
            console.log('haha')
        }
        return clone;
    }
    var fa = {
        name:'tracy',
        hobbys:['soccer','football'] //共享变量
    }
    var son = creatObj(fa);
    son.say(); //haha

    ```*

寄身组合继承

    缺点：书写复杂，调用2次父类构造函数

    ```javascript
    // 对父类复制一个副本，让子类继承。
    function inherit(son,fa){
        var prototype = Object.create(fa.prototype); // 拷贝对象
        prototype.constructor = son; //增强对象
        son.prototype = prototype;   //子类继承
    }
    // 父类公共属性
    function fa(name){
        this.name = 'tracy'
    }
    // 父类原型公共方法
    fa.prototype.getName(){
        console.log(this.name)
    }
    // 子类构造函数
    function son(name.age){
        // 向父类传参
        fa.call(this,name);
        this.age = age;
    }
    inherit(son,fa);
    // 子类添加原型方法
    son.prototype.getAge(){
        console.log(this.age);
    }
    ```

# 防抖和节流

防抖(操作后一段时间再执行)

    ```javascript
    function debounce(fn, delay=2000){
            let timer = null
            let that = this
            return function() {
                if (timer) {
                    clearTimeout(timer)
                }
                timer = setTimeout(()=>{
                    fn()
                }, delay)
            }
        }
    ```*

节流(间隔时间内执行)

    ```javascript
    function throttle(fn, delay=5000) {
            let timer = null
            return function() {
                if (timer) {
                    console.log('kak')
                    return false
                }
                timer = setTimeout(() => {
                    fn()
                    timer = null
                }, delay)
            }
        }
    ```

# promise
```
class myPromise {
    // 为了统一用static创建静态属性，用来管理状态
    static PENDING = 'pending';
    static FULFILLED = 'fulfilled';
    static REJECTED = 'rejected';

    // 构造函数：通过new命令生成对象实例时，自动调用类的构造函数
    constructor(func) { // 给类的构造方法constructor添加一个参数func
        this.PromiseState = myPromise.PENDING; // 指定Promise对象的状态属性 PromiseState，初始值为pending
        this.PromiseResult = null; // 指定Promise对象的结果 PromiseResult
        this.onFulfilledCallbacks = []; // 保存成功回调
        this.onRejectedCallbacks = []; // 保存失败回调
        try {
            /**
             * func()传入resolve和reject，
             * resolve()和reject()方法在外部调用，这里需要用bind修正一下this指向
             * new 对象实例时，自动执行func()
             */
            func(this.resolve.bind(this), this.reject.bind(this));
        } catch (error) {
            // 生成实例时(执行resolve和reject)，如果报错，就把错误信息传入给reject()方法，并且直接执行reject()方法
            this.reject(error)
        }
    }

    resolve(result) { // result为成功态时接收的终值
        // 只能由pedning状态 => fulfilled状态 (避免调用多次resolve reject)
        if (this.PromiseState === myPromise.PENDING) {
            /**
             * 为什么resolve和reject要加setTimeout?
             * 2.2.4规范 onFulfilled 和 onRejected 只允许在 execution context 栈仅包含平台代码时运行.
             * 注1 这里的平台代码指的是引擎、环境以及 promise 的实施代码。实践中要确保 onFulfilled 和 onRejected 方法异步执行，且应该在 then 方法被调用的那一轮事件循环之后的新执行栈中执行。
             * 这个事件队列可以采用“宏任务（macro-task）”机制，比如setTimeout 或者 setImmediate； 也可以采用“微任务（micro-task）”机制来实现， 比如 MutationObserver 或者process.nextTick。 
             */
            setTimeout(() => {
                this.PromiseState = myPromise.FULFILLED;
                this.PromiseResult = result;
                /**
                 * 在执行resolve或者reject的时候，遍历自身的callbacks数组，
                 * 看看数组里面有没有then那边 保留 过来的 待执行函数，
                 * 然后逐个执行数组里面的函数，执行的时候会传入相应的参数
                 */
                this.onFulfilledCallbacks.forEach(callback => {
                    callback(result)
                })
            });
        }
    }

    reject(reason) { // reason为拒绝态时接收的终值
        // 只能由pedning状态 => rejected状态 (避免调用多次resolve reject)
        if (this.PromiseState === myPromise.PENDING) {
            setTimeout(() => {
                this.PromiseState = myPromise.REJECTED;
                this.PromiseResult = reason;
                this.onRejectedCallbacks.forEach(callback => {
                    callback(reason)
                })
            });
        }
    }

    /**
     * [注册fulfilled状态/rejected状态对应的回调函数] 
     * @param {function} onFulfilled  fulfilled状态时 执行的函数
     * @param {function} onRejected  rejected状态时 执行的函数 
     * @returns {function} newPromsie  返回一个新的promise对象
     */
    then(onFulfilled, onRejected) {
        /**
         * 参数校验：Promise规定then方法里面的两个参数如果不是函数的话就要被忽略
         * 所谓“忽略”并不是什么都不干，
         * 对于onFulfilled来说“忽略”就是将value原封不动的返回，
         * 对于onRejected来说就是返回reason，
         *     onRejected因为是错误分支，我们返回reason应该throw一个Error
         */
        onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : value => value;
        onRejected = typeof onRejected === 'function' ? onRejected : reason => {
            throw reason;
        };

        // 2.2.7规范 then 方法必须返回一个 promise 对象
        let promise2 = new myPromise((resolve, reject) => {
            if (this.PromiseState === myPromise.FULFILLED) {
                /**
                 * 为什么这里要加定时器setTimeout？
                 * 2.2.4规范 onFulfilled 和 onRejected 只有在执行环境堆栈仅包含平台代码时才可被调用 注1
                 * 这里的平台代码指的是引擎、环境以及 promise 的实施代码。
                 * 实践中要确保 onFulfilled 和 onRejected 方法异步执行，且应该在 then 方法被调用的那一轮事件循环之后的新执行栈中执行。
                 * 这个事件队列可以采用“宏任务（macro-task）”机制，比如setTimeout 或者 setImmediate； 也可以采用“微任务（micro-task）”机制来实现， 比如 MutationObserver 或者process.nextTick。
                 */
                setTimeout(() => {
                    try {
                        // 2.2.7.1规范 如果 onFulfilled 或者 onRejected 返回一个值 x ，则运行下面的 Promise 解决过程：[[Resolve]](promise2, x)，即运行resolvePromise()
                        let x = onFulfilled(this.PromiseResult);
                        resolvePromise(promise2, x, resolve, reject);
                    } catch (e) {
                        // 2.2.7.2 如果 onFulfilled 或者 onRejected 抛出一个异常 e ，则 promise2 必须拒绝执行，并返回拒因 e
                        reject(e); // 捕获前面onFulfilled中抛出的异常
                    }
                });
            } else if (this.PromiseState === myPromise.REJECTED) {
                setTimeout(() => {
                    try {
                        let x = onRejected(this.PromiseResult);
                        resolvePromise(promise2, x, resolve, reject);
                    } catch (e) {
                        reject(e)
                    }
                });
            } else if (this.PromiseState === myPromise.PENDING) {
                // pending 状态保存的 resolve() 和 reject() 回调也要符合 2.2.7.1 和 2.2.7.2 规范
                this.onFulfilledCallbacks.push(() => {
                    try {
                        let x = onFulfilled(this.PromiseResult);
                        resolvePromise(promise2, x, resolve, reject)
                    } catch (e) {
                        reject(e);
                    }
                });
                this.onRejectedCallbacks.push(() => {
                    try {
                        let x = onRejected(this.PromiseResult);
                        resolvePromise(promise2, x, resolve, reject);
                    } catch (e) {
                        reject(e);
                    }
                });
            }
        })

        return promise2
    }
}

/**
 * 对resolve()、reject() 进行改造增强 针对resolve()和reject()中不同值情况 进行处理
 * @param  {promise} promise2 promise1.then方法返回的新的promise对象
 * @param  {[type]} x         promise1中onFulfilled或onRejected的返回值
 * @param  {[type]} resolve   promise2的resolve方法
 * @param  {[type]} reject    promise2的reject方法
 */
function resolvePromise(promise2, x, resolve, reject) {
    // 2.3.1规范 如果 promise 和 x 指向同一对象，以 TypeError 为据因拒绝执行 promise
    if (x === promise2) {
        return reject(new TypeError('Chaining cycle detected for promise'));
    }

    // 2.3.2规范 如果 x 为 Promise ，则使 promise2 接受 x 的状态
    if (x instanceof myPromise) {
        if (x.PromiseState === myPromise.PENDING) {
            /**
             * 2.3.2.1 如果 x 处于等待态， promise 需保持为等待态直至 x 被执行或拒绝
             *         注意"直至 x 被执行或拒绝"这句话，
             *         这句话的意思是：x 被执行x，如果执行的时候拿到一个y，还要继续解析y
             */
            x.then(y => {
                resolvePromise(promise2, y, resolve, reject)
            }, reject);
        } else if (x.PromiseState === myPromise.FULFILLED) {
            // 2.3.2.2 如果 x 处于执行态，用相同的值执行 promise
            resolve(x.PromiseResult);
        } else if (x.PromiseState === myPromise.REJECTED) {
            // 2.3.2.3 如果 x 处于拒绝态，用相同的据因拒绝 promise
            reject(x.PromiseResult);
        }
    } else if (x !== null && ((typeof x === 'object' || (typeof x === 'function')))) {
        // 2.3.3 如果 x 为对象或函数
        try {
            // 2.3.3.1 把 x.then 赋值给 then
            var then = x.then;
        } catch (e) {
            // 2.3.3.2 如果取 x.then 的值时抛出错误 e ，则以 e 为据因拒绝 promise
            return reject(e);
        }

        /**
         * 2.3.3.3 
         * 如果 then 是函数，将 x 作为函数的作用域 this 调用之。
         * 传递两个回调函数作为参数，
         * 第一个参数叫做 `resolvePromise` ，第二个参数叫做 `rejectPromise`
         */
        if (typeof then === 'function') {
            // 2.3.3.3.3 如果 resolvePromise 和 rejectPromise 均被调用，或者被同一参数调用了多次，则优先采用首次调用并忽略剩下的调用
            let called = false; // 避免多次调用
            try {
                then.call(
                    x,
                    // 2.3.3.3.1 如果 resolvePromise 以值 y 为参数被调用，则运行 [[Resolve]](promise, y)
                    y => {
                        if (called) return;
                        called = true;
                        resolvePromise(promise2, y, resolve, reject);
                    },
                    // 2.3.3.3.2 如果 rejectPromise 以据因 r 为参数被调用，则以据因 r 拒绝 promise
                    r => {
                        if (called) return;
                        called = true;
                        reject(r);
                    }
                )
            } catch (e) {
                /**
                 * 2.3.3.3.4 如果调用 then 方法抛出了异常 e
                 * 2.3.3.3.4.1 如果 resolvePromise 或 rejectPromise 已经被调用，则忽略之
                 */
                if (called) return;
                called = true;

                /**
                 * 2.3.3.3.4.2 否则以 e 为据因拒绝 promise
                 */
                reject(e);
            }
        } else {
            // 2.3.3.4 如果 then 不是函数，以 x 为参数执行 promise
            resolve(x);
        }
    } else {
        // 2.3.4 如果 x 不为对象或者函数，以 x 为参数执行 promise
        return resolve(x);
    }
}
作者：圆圆01
链接：https://juejin.cn/post/7043758954496655397
来源：稀土掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

```

# call、apply、bind

## call

```javascript
Function.prototype.call2 = function (context) {
  // 判断传入的this，为null或者是undefined时要赋值为window或global
    if (!context) {
        // 传入null 或 undefine 指向全局
        context = window ?? global
    }
    // 获取调用call的函数，用this可以获取
    context.fn = this; ////this指向的是使用call方法的函数(Function的实例，即下面测试例子中的bar方法)
    let rest = [...arguments].slice(1);//获取除了this指向对象以外的参数, 空数组slice后返回的仍然是空数组
    let result = context.fn(...rest); //隐式绑定,当前函数的this指向了context.
    delete context.fn;
    return result;
}

//测试代码
var foo = {
    name: 'Selina'
}
var name = 'Chirs';
function bar(job, age) {
    console.log(this.name);
    console.log(job, age);
}
bar.call2(foo, 'programmer', 20);
// Selina 
// programmer 20
bar.call2(null, 'teacher', 25);
// undefined
// teacher 25

```

## apply

```javascript
Function.prototype.apply = function (context) {
  // 判断传入的this，为null或者是undefined时要赋值为window或global
    if (!context) {
        // 传入null 或 undefine 指向全局
        context = window ?? global
    }
    // 获取调用call的函数，用this可以获取
    context.fn = this; ////this指向的是使用call方法的函数(Function的实例，即下面测试例子中的bar方法)
    let rest = [...arguments].slice(1)[0];//获取除了this指向对象以外的参数, 空数组slice后返回的仍然是空数组
    let result = context.fn(rest); //隐式绑定,当前函数的this指向了context.
    delete context.fn;
    return result;
}

//测试代码
var foo = {
    name: 'Selina'
}
var name = 'Chirs';
function bar(job, age) {
    console.log(this.name);
    console.log(job, age);
}
bar.apply(foo, 'programmer', 20);
// Selina 
// programmer 20
bar.apply(null, 'teacher', 25);
// undefined
// teacher 25

```

## bind

```javascript
Function.prototype.bind2 = function (context) {
    if (typeof this !== "function") {
        throw new TypeError("not a function");
    }
    let self = this;
    // 这里的arguments是使用bind方法时传入的参数列表，即bind方法第一个传参的arguments
    let args = [...arguments].slice(1);

    function Fn() {};
    // 为了实现继承
    // 利用空函数，防止原函数原型链被修改
    // eg: person.prototype.b = 2, 原函数person.prototype原型被修改
    Fn.prototype = this.prototype;
    let bound = function () {
        // bind方法返回一个函数，并且这个函数可以继续传参，此时定义的bound方法就是最后bind返回的方法，
        // 所以bound里的arguments就是新方法执行时传的参数列表
        let res = [...args, ...arguments]; //bind传递的参数和函数调用时传递的参数拼接
        /* 
        对三目运算符两种情况的解释：
        1.当作为构造函数时，this 指向实例（注意！！！这里的this是bind返回的新方法里执行时的this，
        和上面的this不是一个！！！），Fn 为绑定函数，因为上面的 `Fn.prototype = this.prototype;`，
        已经修改了 Fn.prototype 为 绑定函数的 prototype，此时结果为 true，
        当结果为 true 的时候，this 指向实例。

        2.当作为普通函数时，this 指向 window，Fn 为绑定函数，此时结果为 false，
        当结果为 false 的时候，this 指向绑定的 context。 */
        context = this instanceof Fn ? this : context || this;
        // 我们用apply来指定this的指向
        return self.apply(context, res);
    }
    // 实现继承，仅在new 实现构造函数的时候
    bound.prototype = new Fn();
    return bound;
}



var name = 'cx';

function person(age, job, gender) {
    console.log(this.name, age, job, gender);
}
person.prototype.c = 2
var Yve = {
    name: 'Yvette'
};
let result = person.bind2(Yve, 22, 'enginner')('female');
```

# new 操作符行为

```javascript
    function foo (name) {
        this.name = name
    }
    var a = new foo('Mike')
    /***    
   1.以 Object.prototype 为原型创建一个新对象；*    
   2.以新对象为 this，执行函数的[[call]]；*    
   3.如果[[call]]的返回值是对象，那么，返回这个对象，否则返回第一步创建的新对象。**
    /
    function _new(fn) {
        // let n = Object.create(fn.prototype) <===> n.__proto__ = fn.protoType 
        const newObj = Object.create(fn.prototype)
        // newObj.__proto__ = fn.prototype
        var resObj = fn.apply(newObj, [].slice.call(arguments, 1))
        return typeof resObj === 'Object' ? resObj : newObj
    }

    var b =_ new(foo, 'dj')
```
