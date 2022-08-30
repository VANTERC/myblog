---
title: JS多个异步接口的顺序调用
date: 2021-02-26 17:53
tags:
---

**假如有这样的一个需求：有请求1、请求2、请求3，但是请求1的入参必须依赖请求2和请求3的返回才可以获取到数据，那么需要怎么做呢？**
常规的做法可能是以下这样：


```javascript
fetch2().then(result2=>{
	fetch3().then(result3=>{
		fetch1(result2,result3).then(result1=>{
			console.log(result1);
		})
	})
})
```


这样的写法虽然可以实现需求，但是写起来特别难受，层层嵌套。
ES6中新增了async await特性，完全可以满足这样的需求，下面换一种写法看看。


```javascript
async function getResult(){
	let result2 = await fetch2()
	let result3 = await fetch3()
	let result1 = await fetch1(result2,result3)
	console.log(result1);
})
```


`async`函数返回一个 Promise 对象，可以使用`then`方法添加回调函数。当函数执行的时候，一旦遇到`await`就会先返回，等到异步操作完成，再接着执行函数体内后面的语句。


```javascript
async function getResult(){
	let result2 = await fetch2()
	let result3 = await fetch3()
	return await fetch1(result2,result3)
})
getResult().then(res=>{
	console.log(res);
})
```


在方法前加上async 说明在这个方法体内存在异步调用，而await后面通常也是一个Promise对象，会返回Promise对象处理的结果，如果不是 Promise 对象，就直接返回对应的值，await不能单独使用，必须在async方法内部使用。


```javascript
async function f() {
  return await 123;
}
f().then(v => console.log(v))
// 123
```


但是这样的处理就会出现一个缺点，假如fetch2和fetch3请求非常慢，那么fetch1等待的时间就非常久，这样的处理结果非常影响页面的效率。
我们可以使用Promise.all()来处理：


```javascript
async function getResult(){
	let [result2,result3] = await Promise.all([fetch2(),fetch3()])
	return await fetch1(result2,result3)
})
getResult().then(result1=>{
	console.log(result1);
})
```


使用Promise.all()需要传递一个Promise实例的数组，即多个请求的数组，并且多个请求是同时进行请求的。这样就fetch1就无需等待过多的请求时间了。
Promise.all返回的结果是一个数组，并且 Promise.all获得的成功结果的数组里面的数据顺序和Promise.all接收到的数组顺序是一致的。
那么如何处理异常呢？


```javascript
async function getResult(){
	let [result2,result3] = await Promise.all([fetch2(),fetch3()]).catch(e=>{
		throw new Error(e);
	})
	return await fetch1(result2,result3)
})
getResult().then(result1=>{
	console.log(result1);
}).catch(e=>{
	throw new Error(e);
})
```


promise.all 中任何一个promise 出现错误的时候都会执行reject，同时将错误异常返回给async的Promise 对象并且抛出reject，抛出的错误对象reject又会被`catch`方法回调函数接收到。
只有`async`函数内部的异步操作执行完，才会执行`then`方法指定的回调函数。
`而await`后面的 Promise 对象如果变为`reject`状态，则`reject`的参数会被`catch`方法的回调函数接收到。
所以如果promise.all 中任何一个promise 出现错误我们可以用catch来接收promise.all的错误，也可以不用catch来接收，因为Promise.all抛出的异常会被async的reject抛出，然后通过async的catch方法来处理。



