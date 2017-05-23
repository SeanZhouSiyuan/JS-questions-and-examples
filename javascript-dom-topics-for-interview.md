## JavaScript DOM 知识点

### DOM 介绍

文档对象模型（Document Object Model ， DOM）是 HTML 、 XML 和 SVG 文档的编程接口。它提供了上述文档的一种树结构表示。 DOM 定义了访问文档树的方法，这些方法被用于改变文档的结构、样式和内容。总体上， DOM 搭建了连接 HTML 页面与编程语言的一座桥梁。

![DOM HTML tree](https://www.w3schools.com/js/pic_htmltree.gif)

虽然开发者经常使用 JavaScript 来访问 DOM ， DOM 本身并不是 JavaScript 语言的一部分，其可以被其他编程语言所访问。

### DOM 创建，添加，修改，删除

HTML DOM 中，任何事物都是一个节点：

* 文档本身是一个文档节点（document node）；
* HTML 元素属于元素节点（element node）；
* HTML 属性属于属性节点（attribute node）；
* HTML 元素内的文字属于文字节点（text node）；
* 注释属于注释节点（comment node）。

**元素、属性、文字节点的创建方法如下：**

```javascript
// 创建一个 p 元素节点
var elem = document.createElement('p');
// 创建一个 class 属性节点
var attr = document.createAttribute('class');
// 创建一个文字节点
var text = document.createTextNode('Hello World!');
```

#### 元素操作

**添加**

创建了一个节点之后，我们就可以将它插入到现有的 DOM 中。

```javascript
// e 是文档中现有的一个元素， elem 是要添加的元素，
// 将 elem 添加到 e 的末尾
e.appendChild(elem);
```

我们可以指定元素插入的位置：

```javascript
// 将 elem 插入到 e 中 referenceElem 之前的位置
e.insertBefore(elem, referenceElem);
```

**替换**

我们也可以替换目标元素的某个子元素：

```javascript
// 将 elem 插入到 e 中，替换掉 target
e.replaceChild(elem, target);
```

**修改**

```html
<div id="demo">
  <p>Some text.</p>
</div>
```

```javascript
var elem = document.getElementById('demo');
// 查看或修改 elem 元素的内容
elem.innerHTML; // "<p>Some text.</p>"
// 查看或修改 elem 元素的文本内容
elem.innerText; // "Some text."
```

> 另有 `node.textContent` ，它与 `node.innerText` 相似，但有以下不同：
>
> * `textContent` 可以获取 `<script>` 和 `<style>` 标签的内容， `innerText` 不可以；
> * `innerText` 不会返回样式设为隐藏的文本；
> * 在 IE11 及以下版本的 IE 中，改变 `innerText` 的值将摧毁节点的所有子类文本节点。
>
> `textContent` 被认为比 `innerHTML` 更安全。
>
> 此外，还存在一个 `outerHTML` 属性，它获取的内容包含元素自身。例如：
>
> ```html
> <div id="demo">Some <span>text.</span></div>
> ```
>
> ```javascript
> var elem = document.getElementById('demo');
> elem.outerHTML; // "<div id="demo">Some <span>text.</span></div>"
> elem.innerHTML; // "Some <span>text.</span>"
> elem.innerText; // "Some text."
> ```

**删除**

最后，我们可以删除目标元素的某个子元素：

```javascript
// 删除 e 中的 target
e.removeChild(target);
```

#### 属性操作

**添加**

```javascript
// 创建一个 class 属性节点，赋值为 “demo”，添加到 elem 上

// 方法一
elem.setAttribute('class', 'demo');

// 方法二（不常用）
var attr = document.createAttribute('class');
attr.value = 'demo';
element.setAttributeNode(attr);
```

**获取**

```javascript
// 获取 elem 中 class 属性的值

// 方法一
var attrValue = elem.getAttribute('class');

// 方法二（获取的是一个属性节点，不常用）
var attrNode = elem.getAttributeNode('class');
attrNode.value; // 获取属性的值
```

**查找**

```javascript
// 查找 elem 中是否有 class 属性
var result = elem.hasAttribute('class');
```

**删除**

```javascript
// 删除 elem 中的 class 属性

// 方法一
elem.removeAttribute('class');

// 方法二（删除相应的属性节点，不常用）
var removedAttr = elem.getAttributeNode('class');
elem.removeAttributeNode(attrNode);
```

