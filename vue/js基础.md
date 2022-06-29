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
