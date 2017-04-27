####产生不重复的随机数
```javascript
function fn(n){
	if(n){
		return "Please input a number!";
	}
	if(n.isNaN){
		return "Not a number!";
	}
	if(n < 1 || n > 31){
		return "n must have a value of 1~31!";
	}
	//准备容器存放结果
	var arr=[];
	//临时变量
	var temp;
	//主循环
	for(var i = 0; i < n; i++){
		//生成随机数
		temp = Math.floor(Math.random() * (32 - 2 + 1)) + 2;
		//检查是否重复
		if (arr.indexOf(temp) > -1)
			i--;
		else
			arr[i] = temp;
	}
	return arr;
}
```

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

#### 计算两个日期的天数差

思路：使用 ```getTime()``` 方法获取当前日期距离1970/01/01的毫秒数，然后相减、取绝对值、转换为天数。

```javascript
function daySpan(date1, date2) {
  var d = Math.abs(date1.getTime() - date2.getTime())/(1000*60*60*24);
  console.log(d);
}
// sample
daySpan(new Date(2016, 2, 7), new Date(2016, 4, 12)); // 66
```

#### 计算两个日期的天数差 - 高级版

思路：先使用 ```new Date(string)``` 语句创建两个 ```Date``` 对象，之后步骤同上。

```javascript
function daySpan(date1, date2) {
  var d1 = new Date(date1);
  var d2 = new Date(date2);
  var d = Math.abs(d1.getTime() - d2.getTime())/(1000*60*60*24);
  console.log(d);
}
```

#### 计算数组中元素重复次数

思路：因数组元素局限于0~9，创建一个数组 ```tempArr``` ，赋初值为0。之后遍历原数组，根据读取的元素的值，对应 ```tempArr``` 中的值增一。最后将出现次数大于零的元素汇总到 ```obj``` 中。

```javascript
function countRepeat(arr) {
  var obj = {};
  for(var i in arr) {
    if(obj[arr[i]])
      obj[arr[i]]++;
    else
      obj[arr[i]] = 1;
  }
  console.log(obj);
}
// sample
countRepeat([2, 9, 8, 4, 5, 3, 4, 8, 9, 9, 1, 0, 2, 1]);
```

方法二：

```javascript
function countRepeat(arr) {
  var obj = {};
  arr.forEach(v => {
    if(obj[v])
      obj[v]++;
    else
      obj[v] = 1;
  });
  console.log(obj);
}
// sample
countRepeat([2, 9, 8, 4, 5, 3, 4, 8, 9, 9, 1, 0, 2, 1]);
```

#### 驼峰转换函数

思路：先全部转换为小写字符，然后用空格替代原字符串中的非字母字符，接着以空格划分字符串存入一个数组，再从第二个元素开始，每个元素首字母转换为大写，最后合并数组元素至新的字符串。

```javascript
function camelCase(str) {
  var i;
  str = str.toLowerCase();
  str = str.replace(/(\W)+/g, ' ');
  var arr = str.split(' ');
  var result = arr[0];
  for(var i = 1; i < arr.length; i++) {
    arr[i] = arr[i].replace(/\b(\w)/, function(m) {return m.toUpperCase();});
    result += arr[i];
  }
  console.log(result);
}
```

#### 寻找字符串中第一个未重复的字符

思路：遍历字符串中的字符，找出第一个 ```indexOf()``` 和 ```lastIndexOf()``` 值相同的字符，并输出之。

```javascript
function firstNonRepeat(str) {
  var result = '';
  for(var i = 0; i < str.length; i++) {
    if(str.indexOf(str.charAt(i)) == str.lastIndexOf(str.charAt(i))) {
      result = str.charAt(i);
      break;
    }
  }
  console.log(result);
}
```

#### 展平数组

```javascript
function flatten(arr) {
  var str = arr.toString();
  var result = str.split(',');
  for(var i in result)
    result[i] = Number(result[i]);
  console.log(result);
}
```

#### 判断空对象

```javascript
function isEmptyObject(obj) {
  var x = typeof obj;
  if(x != 'object' || obj === null || Array.isArray(obj) 
     || Object.keys(obj).length != 0)
    console.log(false);
  else
    console.log(true);
}
```

#### 数组去重

```javascript
function unique(arr) {
  for(var i = 0; i < arr.length; i++) {
    for(var j = i + 1; j < arr.length; j++) {
      if(arr[j] == arr[i]) {
        arr.splice(j, 1);
      }
    }
  }
  console.log(arr);
}
```

#### 实现一个 reduce 函数

```javascript
function reduce(arr, func, initialValue) {
  var sum = initialValue ? func(initialValue, arr[0]) : arr[0];
  for(var i = 1; i < arr.length; i++) {
    sum = func(sum, arr[i]);
  }
  return sum;
}
```

#### 基本随机数函数

```javascript
// 能取到max
function getRndInt(min, max) {
  return Math.floor(Math.random() * (max - min + 1)) + min;
}
// 取不到max
function getRndInt(min, max) {
  return Math.floor(Math.random() * (max - min)) + min;
}
```

