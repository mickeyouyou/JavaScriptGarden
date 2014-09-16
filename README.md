JavaScriptGarden
================
![book logo](http://www.pptok.com/wp-content/uploads/2013/01/garden.jpg)

JavaScriptGarden

##对象
**对象使用和属性**

javascript中所有变量都是对象，除了两个例外null 和undefined
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

Javascript的对象可以作为哈希表使用，主要用来保存命名的键与值的对应关系。
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

