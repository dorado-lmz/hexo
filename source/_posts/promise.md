---
title: Promise A规范
date: 2017-01-05 10:03:06
tags: promise nodejs when bluebird
---
# Intro
> promise是es6规范的一部分,但是也有许多开源工具库(bluebird,when).我之前使用promise往往停留在Readme.md中的demo中,浮于表面,不明原理,所以常常怕使用promise出错,自己无从下手.隔了半年,终于下定决心要弄懂其中原理,才发现无从下手,所以写下这篇博客希望可以减轻学习Promise的难度.

promise,翻译成中文是承诺.而他本身的作用也类似于承诺.你看,对于异步操作,往往要把callback放到异步函数的参数中.很多时候你并不烦callback地狱,你可能会对异步操作和它的callback无法解藕.

```javascript
//我们假定async是一个异步操作(如:setTimeout,process.nextTick)
async(function(err,data){
	if(err){
    	//处理err
    }else{
    	//处理data
    }
});
```
如果
## 术语
1.1 Defer: 任何异步操作.因为异步操作的结果不是立刻得出的,所以说它是延迟操作.
1.2 Promise: 上面说了异步操作没法立刻返回,所以先返回一个promise(承诺当自己有返回值时,会调用then添加的后续操作)
1.3 value: 当异步操作有返回值时,将该值赋值给promise.value
1.4 reason: 当异步操作有异常抛出导致执行中断,将该值赋值给reason

# 规范
## Promise状态
## then方法
> promise最核心的方法只有一个,就是then.后面的catch也是基于then的.其他第三方库除了核心功能,还提供了其他方便的操作,但一定要记住promise只有一个方法then,所以promise还有一个名称叫thenable,任何有then函数的对象....

### 方法签名
```javascript
promise.then(onFulfilled, onRejected)
```
### 参数
2.2.1 onFulfilled,onRejected是可选的,但必须是函数.
2.2.2 onFulfilled存在

	2.2.2.1 在promise变成fulfilled时,该函数被调用,第一个参数是promise.value
	2.2.2.2 如果promise不是fulfilled,该函数不可以被调用
	2.2.2.3 只能被调用一次

2.2.2 onFulfilled存在

	2.2.2.1 在promise变成fulfilled时,该函数被调用,第一个参数是promise.value
	2.2.2.2 如果promise不是fulfilled,该函数不可以被调用
	2.2.2.3 只能被调用一次

2.2.3
2.2.4
2.2.5
2.2.6 then可以在promise上调用多次

	2.2.6.1 


## catch方法


# 规范的简易实现
## promise class


```javascript
class Promise{
	constructor(fn){
    	this._state = Promise.PENDING;  //当前状态
        this._value = null;		  //异步操作返回值
        this._reason = null;	  //异常说明
        this._deferreds = null;   //保存then后续操作
    }
    static PENDING = 0;
    static FULFILLED = 1;
    static REJECTED = 2;
    static isPromise(obj){
    	return obj != null && typeof obj['then'] == 'function';
    }
}
```
一个promise包含4个属性.一个_state属性用来存放当前的Promise对象的状态，一个data属性用来存放调用resolve时传递参数，一个_reason属性用来存放调用reject时传递的参数。

```javascript
Promise.prototype.then = function(onFulfilled, onRejected){
    var deferred = new Defer();
    function fulfill(data){
      var ret = onFulfilled ? onFulfilled(data) : data;
      if(Promise.isPromise(ret)){
        ret.then(function(data){
          deferred.resolve(data);
        });
      }else{
        deferred.resolve(ret);
      }
      return ret;
    }

    if(this._readyState === Promise.PENDING){
      this._resolves.push(fulfill);

      if(onRejected){
        this._rejects.push(onRejected);
      }else{
        //为了让reject向后传递
        this._rejects.push(function(reason){
          deferred.reject(reason);
        });
      }
    }else if(this._readyState === Promise.FULFILLED){
      var self = this;
      setTimeout(function(){
        fulfill(self._data);
      });
    }
    return deferred.promise;
}
```




