title: 关于使用JavaScript闭包
tags:
    - JavaScript

---

闭包在实际运用中，还是非常少，对它理解也不是很清晰，下面经过学习和使用，做了个总结。

# 目的
- 开发过程中，由于需要用到作用域，但不想使用全局变量，因而考虑使用闭包，避免全局污染。

# 闭包理解
- 闭包（Closure）是指函数可以有自己的独立变量。闭包在教科书上的定义很抽象，依我个人的理解，闭包是能读取其父函数内部变量的函数，也就是函数中的内部函数。

# 变量作用域
> 变量作用域分为局部变量和全局变量。

- 局部变量
外部函数无法访问局部变量。

```
function privateInnerVariable() {
	var name = "musenboy";
}
privateInnerVariable();
console.log(name); //undefined
```

- 全局变量
内部函数可以访问全局变量。

```
var name = "musenboy";
function accessGlobalVariable() {
	console.log(name);//musenboy
}
accessGlobalVariable()；
```

内部函数可以访问外部的变量，外部函数不可以访问内部函数的变量。但是如果在内部函数中没有定义var，该变量就会声明为全局变量了。

```
function privateInnerVariable() {
    name = "musenboy";
}
privateInnerVariable();
console.log(name); //musenboy
```

# 闭包用法
## 内部函数

```
function parentFunction() {
	var name = "musenboy";
	function childFunction() {
		console.log(name);
	}
	childFunction();
}
parentFunction();//musenboy
childFunction();//error: childFunction is not defined
```
- childFunction就是一个被包在parentFunction函数作用域中的内部函数。
在parentFunction内部调用childFunction是有效的，而在parentFunction外部调用childFunction则是无效的。

- childFunction变成了一个闭包，闭包是一种特殊的对象，它由两部分构成：函数以及创建该函数的环境。环境由闭包创建时在作用域中的任何局部变量组成。在我们的例子中，childFunction是一个闭包，由name 函数和闭包创建时存在的"musenboy"字符串形成。

- 难道我们在外部真的无法再外部调用parentFunction的内部函数childFunction吗？答案是否定的。

- JavaScript允许像传递任何数据类型一样传递函数。
我们可以定义一个全局变量，将全局变量引用为childFunction。

```
var globalFunction;
function parentFunction() {
	var name = "musenboy";
	function childFunction() {
		console.log(name);
	}
	globalFunction = childFunction;
}
parentFunction();
globalFunction ();//musenboy
```
我们还可以

```
function parentFunction(x) {

	function childFunction(y) {
		console.log(x + " " +y);
	}
	return childFunction;
}
var newChild = parentFunction("call me");
newChild("musenboy"); //call me musenboy
```

上面的newChlid其实就是childFunction，当然y传的是"musenboy"。newChild它还是一个闭包。

# 闭包容易出错的地方

```
for(var i = 0; i < 3; i ++) {
	setTimeout(function(){
	console.log(i);
	}, 10)
}
```
上面的结果出乎意料吧，并没输出我们想要的结果0,1,2， 而是输出了三次3。

原因是setTimeout只是记录了对外层i的引用，当执行setTimeout时，只是记录了i的引用，而不是值，此时循环早已完成，i指向了i的最后一项。

为了解决这个问题，我们使用匿名函数，把i定义为一个局部变量。

```
for(var i = 0; i < 3; i ++) {
	(function(index){
		setTimeout(function(){
		console.log(index);
		}, 10)
	})(i);
}
```

# 性能考虑

- 使用闭包，函数变量会一直保存在内存中，消耗很大，不能滥用闭包，否则会造成网页的性能问题，在IE中可能导致内存泄露。解决方法是，在退出函数之前，将不使用的局部变量全部删除。




