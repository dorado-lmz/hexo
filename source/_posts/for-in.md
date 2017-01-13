---
title: for-in
date: 2017-01-05 16:48:55
tags: javascript
---

```javascript
var arr = [1];
arr.attribute = 'attribute';
Array.prototype.other = 'other';
for(var index in arr){
	console.log(index+"   "+arr[index]);
}

var obj = {a:2};
Object.prototype.other = 'other';
for(var key in obj){
	console.log(key+"   "+obj[key]);
}
```

> 遍历数组或有length属性的对象时，最后使用for(var i=0;i<arr.length;i++);