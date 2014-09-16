JavaScriptGarden
================
![book logo](http://www.pptok.com/wp-content/uploads/2013/01/garden.jpg)

JavaScriptGarden  你不知道的JavaScript在一个秘密花园里 

#对象
**对象使用和属性**

javascript中所有变量都是对象，除了两个例外`null`和`undefined`
```javascript
false.toString();//false
[1,2,3].toString();//1,2,3

function Foo(){}
Foo.bar = 1;
Foo.bar; //1
```

一个常见的误解是数字的字面值（literal）不是对象，这是因为javascript解析器的一个错误，他试图将点操作符解析为浮点数字面值的一部分。
```javascript
2.toString();//error:SyntaxError
```

有很多变通方法可以让数字的字面值看起来像对象：


**对象作为数据类型**

Javascript的对象可以作为`哈希表`使用，主要用来保存命名的键与值的对应关系。
使用对象的字面语法{} 可以创建一个简单的对象。这个新创建的对象从Object.prototype继承下面。没有任何自定义属性。

```javascript
var foo = {} //一个 空对象
//一个新对象，拥有一个值为12的自定义属性'test'
var bar = {test:12};
```

**访问属性**

有两种方式来访问对象的属性，点操作符或者中括号操作符。
```javascript
var foo = {name:'kitten'};
foo.name;//kitten
foo['name'];//kitten

var get = 'name';
foo[get]; //kitten

foo.1234;//SyntaxError
foo['1234'] //works
```

两种语法是等价的，但是中括号操作符在下面两种情况下依然有效：
动态设置属性
属性名不是一个有效的变量名(译者注：比如一个属性名中包含空格、或者属性名是JS关键字)

**删除属性**

删除属性的唯一方法是使用delete操作符；设置属性为undefined或者null并不能真正的删除属性，而仅仅是移除了属性与值的关联。

```javascript
var obj = {
    bar:1,
    foo:2,
    baz:3
};
obj.bar = undefined;
obj.foo = null;
delete obj.baz;

for(var i in obj){
    if(obj.hasOwnProperty(i)) {
        console.log(i, '' +obj[i]);
    }
}
```
上面的输出结果有 bar undefined 和foo null 只有baz被真正删除了，所以从输出结果中消失。
属性名的语法
```javascript
var test = {
    'case':'I am a keyword so I must be notated as a string ',
    delete:'I am a keyword to so me' //SyntaxError
}
```

对象的属性名可以使用字符串或者普通字符声明。但是由于JavaScript解析器的另外一个错误设计，上面的第二种声明方式在ECMAScript5之前会抛出SyntaxError的错误。
这个错误的运营是delete 是JavaScript语言的一个关键字，因此为了在更低版本中也能正常运行，必须使用字符串面值声明方式。

##原型
JavaScript不包含传统的类继承模型，而是使用prototypal原型模型。

虽然这经常被当做是JavaScript的缺点被提及，其实基于原型的继承模型比传统的类继承还要强大。实现传统的类继承模型是很简单，但是实现JavaScript中的原型继承则要困难的多。

由于JavaScript是唯一一个被广泛使用的基于原型继承的语言，所以理解两种继承模式的差异是需要一定时间的。

第一个不同之处在于JavaScript使用原型链的继承方式。
```javascript
funtion Foo(){
	this.value = 42;
}
Foo.prototype = {
	method:function(){}
};

function Bar(){}

//设置Bar的prototype属性为Foo的实例对象
Bar.prototype = new Foo();
Bar.prototype.foo = 'Hello World!';

//修正Bar.prototype.constructor为Bar本身
Bar.prototype.constructor  = Bar;
vav test = new Bar() //创建Bar的一个新实例


//原型链
test [Bar的实例]
	Bar.prototype [Foo的实例]
		{foo:'hello world '}
			Foo.prototype
				{method:...};
				Object.prototype
					{toString:.../*etc.*/};

```

上面的例子中，test对象从Bar.prototype和Foo.prototype继承下来；因此，他能访问Foo的原型方法method。同时，它能够访问那个定义在原型上的Foo实例属性value。需要注意的是new Bar()不会创造一个新的Foo实例，而是重复使用它原型上的那个实例；因此，所有Bar实例都会共享相同的value属性。

#函数

##函数的声明与表达式

函数是javascript中的一等对象，这意味着可以函数像其他值一样传递。一个常见的用法是把匿名函数作为回调传递到异步函数中。

**函数声明**
```javascript
function foo(){}
```
上面的方法会在执行前被解析(hoisted),因此它存在于当前上下文的任意一个地方，即使在函数定义体的上面被调用也是对的。
```javascript
foo(); //正常运行，以为foo在代码之前已经被创建
funtion foo() {}
```

**函数赋值表达式**

```javascript
var foo = function() {};
```

这个例子把一个匿名函数赋值给变量foo。
```javascript
foo;//'undefined'
foo(); //出错:TypeError
var foo = function() {};
```
由于var定义了一个声明语句，对变量的解析是在代码运行之前，因此foo变量在代码运行之前已经被定义过了。
但是由于赋值语句只在运行是被执行，因此在相应代码执行之前，foo的值缺省值为undefined。

**命名函数的赋值表达式**

另外一个特殊的情况是将命名函数赋值给一个变量。
```javascript
var foo = function bar(){
	bar(); //正常运行
}
bar();//出错：ReferenceError
```

bar 函数声明外是不可见的，这是因为我们已经把函数赋值给了foo；然而在bar内部依然可见。这是由于JavaScript的命名处理所致，函数名在函数内总是可见的。

##this的工作原理
JavaScript 有一套完全不同于其它语言的对 this 的处理机制。 在五种不同的情况下 ，this 指向的各不相同。

**全局范围内**
```javascript
this;
```
当全局范围内使用this，它将指向全局对象。

>浏览器中运行的JavaScript脚本，这个全局对象是window

**函数调用**
```
foo();
```
这里this也会指向全局对象。
>ES5注意：在严格模式下（strict mode），不存在全局变量。这种情况下this将会是undefined。

**方法调用**

```
test.foo();
```
这个例子中，this指向test对象。

**调用构造函数**
```
new foo();
```

如果函数倾向于和new 关键字一起使用，则我们成这个函数为构造函数。在函数内部，this指向一个新创建的对象。

**显式设置的this**
```javascript
function foo (a,b,c) {}

var bar = {};
foo.apply(bar,[1,2,3]); //数组将会扩展如下
foo.call(bar,1,2,3);//结果 a=1,b= 2,c=3 
```




#核心

##为什么不用`eval`
`eval` 函数会在当前作用域中执行一段 JavaScript 代码字符串。
```javascript
var foo = 1;
function test() {
    var foo = 2;
    eval('foo = 3');
    return foo;
}
test(); // 3
foo; // 1
```
但是 `eval` 只在被直接调用并且调用函数就是 `eval` 本身时，才在当前作用域中执行。

```javascript
var foo = 1;
function test() {
    var foo = 2;
    var bar = eval;
    bar('foo = 3');
    return foo;
}
test(); // 2
foo; // 3
```

译者注：上面的代码等价于在全局作用域中调用 eval，和下面两种写法效果一样：
```javascript
// 写法一：直接调用全局作用域下的 foo 变量
var foo = 1;
function test() {
    var foo = 2;
    window.foo = 3;
    return foo;
}
test(); // 2
foo; // 3

// 写法二：使用 call 函数修改 eval 执行的上下文为全局作用域
var foo = 1;
function test() {
    var foo = 2;
    eval.call(window, 'foo = 3');
    return foo;
}
test(); // 2
foo; // 3
```

在任何情况下我们都应该避免使用 `eval` 函数。99.9% 使用 `eval` 的场景都有不使用 `eval` 的解决方案。