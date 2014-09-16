JavaScriptGarden
================

JavaScriptGarden

##原型
JavaScript不包含传统的类继承模型，而是使用prototypal原型模型。

虽然这经常呗当做是JavaScript的缺点被提及，其实基于原型的继承模型比传统的类继承还要强大。实现传统的类继承模型是很简单，但是实现JavaScript中的原型继承则要困难的多。

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

//
```