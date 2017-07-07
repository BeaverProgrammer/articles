# Javascript 错误处理

当页面不符合预期时，我通常会到开发者工具查看 Console 的报错信息以帮助调试，如果没有报错信息我可能需要花更多的时间查看源代码。

可见，抛出错误是实用的技术。

## throw
除了 JavaScript 引擎默认的抛出错误方式，我们可以使用 throw 语句抛出错误。

```js
throw new Error('something wrong');
throw 'something wrong';
throw 404
```

虽然我们可以抛出任何 JavaScript 值，但是除了 Error 类型及其子类的其它值是不被推荐的，因为这些值在某些浏览器（通常是旧浏览器）中是不被显示的，只能得到一个笼统的报错。

throw 抛出的错误如果没有被 try catch 语句捕获会导致程序终止。

如果原生的 7 种错误类型不能满足需要，我们可以定义自己的错误类型。

```js
class MyError extends Error {}

let customError = new MyError('something wrong');
customError instanceof Error;		// true
```

throw 一般用于较底层，比如 API、库 和基础函数。

## try catch finally
对于程序整体逻辑层面的处理，为了防止进行某些操作时抛出的错误导致程序挂掉，可以使用 try catch [finally] 语句。

一般来讲，try 不报错不执行 catch, 报错执行 catch , 如有 finally 那么 finally任何时候都执行。

```js
try {
	// plan A
} catch (e) {
	// if plan A throw error then plan B
	// plan B
} finally {
	// execute anytime
}

```

**但也有需要注意的地方，笼统来讲，try 和 catch 里如果有跳出整个 try catch finally 语句块的语句，这些语句要被 delay 到 finally 之后，更特殊地，如果这时 finally 里有跳出整个 try catch finally 的语句，则立即执行这些语句，之前被 delay 的语句被 kill 掉。**

详细来讲，这些所谓跳出 try catch finally 语句块的语句有 throw、return、break和 continue 等。下面是其中一部分情形的例子。

catch 重新抛出了错误：
```js
try {
	throw new Error('error 1');
} catch (e) {
	console.error(e);
	throw new Error('error 2');
} finally {
	console.log('finally');
}
// error 1
// finally
// 最后再报错 error 2
```

函数内的 try catch finally 语句， try 或 catch 内的 return 被 delay:
```js
function test() {
	try {
		return 10;
	} catch (e) {
		return 20;
	} finally {
		return 30;
	}
}
test();		// 30
```

## 总结
最终的产品运行中抛出错误是 bug， 但开发调试时抛出的错误是福音，因为没有产品是一气呵成完美无瑕的。对于基础件中预期的错误使用 throw, 对程序逻辑层面预期的错误使用 try catch finally（不是一定的，推荐）。使用 ES6 的 class 方便的自定义错误类型。

## 参考
1. Nicholas C. Zakas - Maintainable JavaScript(中文版第十章-抛出自定义错误)
2. [http://javascript.ruanyifeng.com/grammar/error.html](http://javascript.ruanyifeng.com/grammar/error.html)
