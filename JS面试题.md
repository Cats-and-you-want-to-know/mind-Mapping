### 1. 下面输出什么？如果是在严格模式下呢？
```
var a = function(){
    this.b = 3
}
var c = new a() 
a.prototype.b = 9
var b = 7 
a()

console.log(b)  //3
console.log(c.b) //3
```
如果是严格模式，那么就会报错，因为严格模式下，`this`指向不是`window`，而是`undefined`。

### 2. 输出什么？
```
var a = 0;
if(true){
  var a = 1;
  function a(){};
  var a = 21;
  console.log(a)
}
console.log(a) 
// 21 1
```
新浏览器为了兼容ES3、ES6
- `{}`块级作用域下的`function`，在全局下只声明，不定义（不赋值）
- `{}`出现`function、const、let`创建一个块级上下文

### 3. 不借助变量,交换两个数
```
function swap(a,b){
  a = a + b;
  b = a - b;
  a = a - b;
  return a,b
}
```

### 4. `==`比较，数据转换的规则
```
console.log([] == false)    // true
console.log(![] == false)   // true
```
- 数据类型一样的几个特殊点:
  - `{} == {} //false` 对象比较的是堆内存的地址
  - `[] == [] //false` 
  - `NaN == NaN // false`
- 数据类型不一样的转换规则
  - `null == undefined //true`
  - `字符串 == 对象` 要把对象转换为字符串
  - 剩下如果 `==` 两边数据类型不一致，但是需要转换为数字类型在进行比较
- 把其他数据类型转换为布尔值
  - 基于以下方式可以把其他数据类型转换为布尔
    - `!`转换为布尔值后取反
    - `!!` 转换为布尔值
    - `Boolean(val)`
  - 隐式转换
    - 在循环或者条件判断中，条件处理的结果就是布尔类型值
    
    
  **规定:  只有0、NaN、null、undefined、空字符串 五个值变成布尔的false,其余都是true**
  
### 5. 变量赋值
```
let a = {n:1};
let b = a;
a.x = a = {n:2};
console.log(a.x) // undefined
console.log(b)   // {n:1,x:{n:2}}
```
- **变量赋值都是指针指向过程，先创建值，再创建变量，最后指针关联**
- **带成员访问的优先处理**

1. 先创建`{n:1}`,a指向`{n:1}`的地址
2. b与a指向同一个地址
3. 先创建一个`{n:2}`的地址
4. `{n:1}`中的x指向`{n:2}`,然后a的地址更改为`{n:2}`

### 6. es5与es6的继承有什么不同？
- es5继承，即原型链继承，先创造子类的实例对象this，然后再把父类的方法添加到this上。
- es6继承，即`class`类继承，子类没有自己的this，是通过`constructor`中的`super`继承父类的this，然后对this进行加工。

### 7. 为什么`typeof null`是`object`
这是一个历史遗留bug，在JS中二进制前三位都是0的话会被判断为`object`,null的二进制表示全为0，所以`typeof null`为`object`

### 8. null和undefined的区别
```
null == undefined //true 
null === undefined //false 
```
1. null的类型是Object类型，代表空值，一个空对象指针。
2. undefined的类型是undefined类型

null表示“没有对象”，即此处不应该有值，典型用法是:
1. 作为函数的参数，表示该函数的参数不是对象
2. 作为对象原型链的终点

undefined表示“缺少值”，就是该处应该有一个值，但是还没有定义，典型用法是:
1. 变量被声明，但还没有赋值
2. 调用函数时，应该提供的参数没有提供
3. 对象没有赋值的属性，该属性的值为undefined
4. 函数没有返回值或者return后面什么都没有（例如Promise的return），返回undefined

### 9. 内存泄露
> 不会用到的内存，没有及时释放，就叫做内存泄露

内存泄露产生的原因:
1. 缓存
有时候为了方便数据的快捷复用，我们会使用缓存，但是缓存必须有一个大小上限才有用。高内存消耗将会导致缓存突破上限，因为缓存内容无法被回收
2. 计时器引用没有被消除
当浏览器队列消除不及时，会导致一些作用域变量得不到及时的释放，因而导致内存泄露
3. 全局变量
除了常规设置了比较大的对象在全局变量，可能是意外导致的全局变量。例如函数中，没有使用var/let/const定义变量，实际上是定义在`window`上面。
4. 闭包
5. 时间监听（例如滑动监听）

### 10. 闭包机制
> 函数执行，某些上下文没有被销毁，就是闭包机制。

**闭包作用**

1. 保护作用
   1. 上下文中会有一些私有变量，这些私有变量和外界变量不会冲突
   2. 类似立即执行函数，例如jquery库
2. 保存作用
   1. 上下文中的某些内容被外界占用后，当前上下文并不会出栈销毁，这样可以把上下文中的信息保存起来。类似柯里化函数

**闭包优缺点**

1. 能保护和保存代码不受污染
2. 因为闭包会产生不销毁的上下文，这样会导致内存消耗过大，容易造成内存泄露，影响性能。

### 11. async/await如何通过同步的方式实现异步？
内部是通过类似生成器的方式来实现的:
```
function A(){
    return new Promise(()=>{
        resolve(2)
    },1000)
}
function *B(){
    var b = yield A()
    var c = yield A()
    console.log(b+c)
}
run(B) 
function B(func){
    var f = func() 
    var result = f.next() 
    handle(result) 
    function handle(result){
        if(result.done === true){
            return result;
        }else{
            result.then(res => {
                var result = result.next(res)
                handle(result)
            })
        }
    }
}
```
### 12. Promise 构造函数是同步执行还是异步执行，那么 then 方法呢？
Promise 构造函数是同步执行的，then方法是异步执行

### 13. 下面的代码输出结果是多少？
```
Promise.resolve(1).then(2).then(Promise.resolve(3)).then(console.log)
```
输出1: 因为then参数一定是一个函数，如果不是函数，就当没有写，直接跳过

### 14. 下面的代码输出结果是多少？
```
Promise.resolve().then(()=>{return new Error('error!!!')})
                 .then(res=>{console.log('then',res})
                 .catch(err =>{console.log('catch',err)})
```
输出 `then Error`,只要是`return`,就是`resolve` 只不是这里传递的是`error`对象,只要是`throw`,必然是`reject`,哪怕是`reject 1`

### 15. 下面Set结构，打印出的Size值是多少?
```
let s = new Set() 
s.add([1]);
s.add([1]);
console.log(s.size);
// 1
```
因为`[1]`和`[1]`地址不同。

### 16. JS异步模式
JS语言的执行环境是单线程的，一个任务的执行，必须等待上一个任务执行完毕。如果一个任务执行耗时很长时，后面的任务都必须排队等着，会拖延整个程序的执行，为了解决这个问题，JS语言将任务的执行模式分为两种： 同步和异步。“异步模式”就是前一个任务结束后，不是执行后一个任务，而是执行回调函数，后一个任务无需等待上一个任务结束才执行，程序的执行顺序与任务的排列顺序是不一致的。“异步模式”非常重要，在浏览器端，耗时很长的操作都应该是异步执行，避免浏览器失去响应，例如AJAX操作；而在服务器端，因为执行环境是单线程的，如果允许同步执行所有http请求，性能会大大损耗，很快失去响应，所以也必须使用“异步模式”

### 17. JS异步解决方案的发展历程
1. 回调函数callbacl
```
setTimeout(()=>{
    console.log('hello')
},1000)
```
使用回调函数，不能用`try catch`捕获错误，而且容易造成**回调地狱**

2. Promise
Promise实现了链式调用，每次`then`或`catch`后都是一个全新的Promise,交给后面的处理函数处理，解决了回调地狱
```
ajax('http')
    .then(res => {
        return ajax('http1')
    })
    .then(res => {
        return ajax('http2')
    })
    .then(res => {
        console.log(res)
    })
```
3. async/await 
不用写一堆then链，处理了回调地狱，结构更加清晰。await将异步代码改造成了同步代码，如果多个操作有依赖性，效果较好。但如果没有依赖性，性能会降低。
```
async function test(){
    await ajax('http1')
    await ajax('http2')
    await ajax('http3')
}
```
