title: javascript-arguments
date: 2016-01-06 20:40:17
tags:
- javascript

---

## arguments

先从问题出发
```javascript
(function () {
    try {
        throw arguments.map(x => x * 3);
    } catch (e) {
        console.log(e);
    }
})(1, 2, 3)
```
结果是什么呢？大家可以复制到console中尝试下。

从代码可以看到我们使用了arguments，这是javascript function的特殊变量，用来表示整个参数列表，是一个Array like的对象，为什么说它是Array like的对象呢？因为arguments并不是一个Array对象，所以上面问题的结果是`Uncaught TypeError: arguments.map is not a function`，可以看到arguments并不是真正的Array对象，那么如何让上面的代码输出`[3, 6, 9]`这样的结果呢？

ES6前的javascript代码通常需要这样写
```javascript
(function () {
    try {
        throw Array.prototype.map.call(arguments, function (x) { return x * 3;});
    } catch (e) {
        console.log(e);
    }
})(1, 2, 3)
```
或者将其转化为数组再应用`map`这样的数组方法
```javascript
(function () {
    try {
        throw Array.prototype.slice.call(arguments)
                .map(function (x) { return x * 3;});
    } catch (e) {
        console.log(e);
    }
})(1, 2, 3)
```
那么在ES6中我们可以怎样实现呢？
```javascript
((...args) => {
    try {
        throw args.map(x => x * 3);
    } catch (e) {
        console.log(e);
    }
})(1, 2, 3)
```
或着更简洁的转化Array like为Array的方法
```javascript
(function () {
    try {
        throw Array.from(arguments).map(x => x * 3);
    } catch (e) {
        console.log(e);
    }
})(1, 2, 3)
```
无论哪种看起来都比ES5的实现漂亮很多，这里Array.from是ES6中新增的一个用来将类数组对象转化为数组对象的方法，前面我们提到了多次Array like对象，下面的对象就是Array like的
```javascript
var arrayLike = {
    "0": 9,
    "2": 8,
    "1": 7,
    length: 3
}
console.log(Array.from(arrayLike)) // [9, 7, 8]
```

## rest arguments

我们再看下另外一种ES6实现当中的`...args`，看起来似乎和Java的变长参数很像，这在ES6的语法中叫做剩余参数，使用剩余参数可以代替`arguments`，比如前面提到的例子。剩余参数叫做剩余参数，当然也可以和正常参数一起使用，比如
```javascript
function sum(a, ...args) {
    return args.reduce((x,y) => x + y, a);
}
console.log(sum(1,2,3,4,5));
```
需要注意，rest参数只能是最后一个参数，不能再有其他参数，否则会报错。

## default arguments

除了rest参数，ES6还提供了默认参数的功能，我们先看一个问题
```javascript
function log(x, y) {
    y = y || 'World';
    console.log(x, y);
}
log('Hello');
log('Hello', 'China');
log('Hello', '');
```
三个log分别是什么结果呢？结果都是`Hello China`，第三个log的结果和我们调用的预期是不一样的，那么如何避免呢？
```javascript
// 写法一
if (typeof y === 'undefined') {
    y = 'World';
}

// 写法二
if (arguments.length === 1) {
    y = 'World';
}
```
那么使用了ES6的默认参数后是就可以直接按照问题的写法去写了
```javascript
function log(x, y = 'World') {
    console.log(x, y);
}
log('Hello');
log('Hello', 'World');
log('Hello', '');
```
默认参数最大的好处就是可以直接从函数参数表知道哪些参数是可选参数，而不用具体阅读其实现。

## destructuring

Quiz
```javascript
var a = 3, b = 4;
[a, b] = [b, a];
console.log(a, b);
```
用过python或者ruby的同学对这种写法一定已经非常熟悉了，现在ES6也支持这种fancy的特性了，这种语法被叫做解构
```javascritp
var [a, [[b], c]] = [1, [[2], 3]];
console.log(a,b,c);
var [ , , third] = ["foo", "bar", "baz"];
console.log(third);
var [x, , y] = [1, 2, 3];
console.log(x,y);
var [head, ...tail] = [1, 2, 3, 4];
console.log(head,tail);
var [x, y, ...z] = ['a'];
console.log(x,y,z);
```
解构也可以有默认值
```javascript
var [y = 3] = [];
console.log(y);
var [x, y = 3] = ['a'];
console.log(x, y);
var [x = 3, y = 4] = [undefined, 1]
console.log(x, y);
```

，可以对数组解构，也可以对对象结构，那么对象的解构是什么样的呢？
```javascript
var {foo: x, bar: y} = {foo: 'aaa', bar: 'bbb'};
console.log(x);
console.log(y); 

var { prop, prop2} = {prop: 5, prop2: 10};
console.log(prop, prop2);


```
那么函数的参数也可以写成这样的形式
```javascript
function foo({x, y = 5}) {
    console.log(x, y);
}

foo({}) // undefined, 5
foo({x: 1}) // 1, 5
foo({x: 1, y: 2}) // 1, 2
foo() // TypeError: Cannot read property 'x' of undefined
```
解构有很多用途，比如可以做这种事情
```javascript
var something = [
    {url: 'baidu.com'},
    {url: 'google.com'},
    {url: 'facebook.com'}
];
//pluck
var urls = something.map(({ url }) => url);
console.log(urls);
```
但对象的解构需要注意，如果将声明与实现分开，需要将表达式放置于括号中
```javascript
var a, b;
{a,b} = {a:3,b:4};
console.log(a,b);
//error

var a, b;
({a,b} = {a:3,b:4});
console.log(a,b);
```
