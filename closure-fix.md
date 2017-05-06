#### 闭包 for 循环取 i 问题

**例子**

```html
<p>产品一</p>
<p>产品二</p>
<p>产品三</p>
<p>产品四</p>
<p>产品五</p>
```

```javascript
function onMyLoad() {
  var arr = document.getElementByTagName('p');
  for(var i = 0; i < arr.length; i++) {
    arr[i].onclick = function() {
      alert(i);
    }
  }
}
onMyLoad();
```

**存在问题：**

期望每次点击一个目标时弹出对应的数字（0~4），结果是无论点击哪个目标都会弹出5。

**问题原因：**

for 循环中的 ```onclick``` 绑定了一个匿名函数，该函数与其所处的上下文环境构成了闭包。变量 ```i``` 位于该上下文环境中，而匿名函数对 ```i``` 进行了引用，因此当上下文环境中 ```i``` 的值改变时，匿名函数中的引用值也会改变。

**解决方案：**

方法一

```javascript
//思路：增加一个闭包域空间，存储被引用的内容
for(var i = 0; i < arr.length; i++) {
  (function(arg) {
    arr[i].onclick = function() {
      alert(arg);
    }
  })(i); //立即执行该函数，将i的值传递给形参arg
}
```

由于是值传递，```i``` 值的改变不会对上一次循环中的 ```arg``` 造成影响。此方法仅限于基本类型。

方法二

```javascript
//思路：为数组中的每一项添加一个名为“i”的属性并赋值为i
for(var i = 0; i < arr.length; i++) {
  arr[i].i = i;
  arr[i].onclick = function() {
    alert(this.i);
  }
}
```

注意，这个名为“i”的属性跟变量 ```i``` 并没有什么关系。同样地，```i``` 值的改变不会对已经赋值的属性造成影响。

方法三

```javascript
//思路：将onclick事件绑定在一个匿名函数返回的函数上
for(var i = 0; i < arr.length; i++) {
  arr[i].onclick = (function(arg) {
    return function() {
      alert(arg);
    }
  })(i);
}
```

同理， ```i``` 值的改变不会对上次循环中的 ```arg``` 造成影响。

方法四

```javascript
//思路：与方法一相同
for(var i = 0; i < arr.length; i++) {
  (function() {
    var temp = i;
    arr[i].onclick = function() {
      alert(temp);
    }
  })();
}
```

方法五

```javascript
//思路：与方法三相同
for(var i = 0; i < arr.length; i++) {
  arr[i].onclick = (function() {
    var temp = i;
    return function() {
      alert(temp);
    }
  })();
}
```

方法六

```javascript
//思路：使用ES2015中的let关键字
for(let i = 0; i < arr.length; i++) {
  arr[i].onclick = function() {
    alert(i);
  }
}
```

