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

**克隆一个现有元素的方法如下：**

```javascript
var dupNode = node.cloneNode(deep);
// deep she，表示同时克隆 node 的所有子节点，
// 若设为 false ，则只克隆 node 自身
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

**查找**

```javascript
// 获取具有给定 id 的元素
var elem = document.getElementById(idName);

// 以 rootElement 为查找起点（不含自身），
// 获取具有给定 class 的元素，返回一个 HTMLCollection
var elems = rootElement.getElementsByClassName(className);

// 以 rootElement 为查找起点（不含自身），
// 获取匹配给定标签名的元素，返回一个 HTMLCollection
var elems = rootElement.getElementsByTagName(tagName);

// 获取与给定选择器（一个或一组）相匹配的第一个元素
var elems = rootElement.querySelector(selectors);

// 获取与给定选择器（一个或一组）相匹配的元素，
// 返回一个静态的节点列表
var elems = rootElement.querySelectorAll(selectors);
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
> * `textContent` 可以获取 `node` 中其他标签内的文本；
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

### 使用 DOM 更改样式 - CSSOM

**语法：**

```javascript
// 方法一
HTMLElement.style.property = value;

// 方法二（将抹去现存的所有样式）
HTMLElement.style = 'property: value';

// 方法三（将抹去现存的所有样式）
HTMLElement.setAttribute('style', 'property: value');
```

使用 DOM 设置的样式相当于内联样式，具有最高优先级。

> 虽然以上语法也可以用于读取现有样式，但它们只能返回元素的内联样式（使用 style 属性设置的样式）。要获取某个元素拥有的全部样式，使用 `window.getComputedStyle()` ：
>
> ```javascript
> var style = window.getComputedStyle(element);
> ```
>
> 上面的代码获取作用于 `element` 的所有样式，返回一个动态的 `CSSStyleDeclaration` 对象。

### DOM 导航

根据 W3C 标准， HTML 文档中的任何事物都是节点。所有节点构成了一棵节点树。以下方法可以用于节点树的导航：

```javascript
// 以下方法均没有创建新的节点，
// 而是获得了现存节点的引用

// 获取当前节点的父亲节点
var parentNode = node.parentNode;
// 获取当前节点的父元素节点
var parentElement = node.parentElement;

// 获取当前节点的第一个子节点
var childNode = node.firstChild;
// 获取当前节点的第一个子元素节点
var childElement = node.firstElementChild;
// 获取当前节点的最后一个子节点
var childNode = node.lastChild;
// 获取当前节点的最后一个子元素节点
var childElement = node.lastElementChild;
// 获取当前节点的所有子节点
var childNodes = node.childNodes;
// 获取当前节点的所有子元素节点
var childElements = node.children;

// 获取当前节点的前一个兄弟节点
var previousNode = node.previousSibling;
// 获取当前节点的前一个兄弟元素节点
var previousElement = node.previousElementSibling;
// 获取当前节点的后一个兄弟节点
var nextNode = node.nextSibling;
// 获取当前节点的后一个兄弟元素节点
var nextElement = node.nextElementSibling;

// 获取当前节点的类型
var nodeType = node.nodeType;
// 获取当前节点的名称
var nodeName = node.nodeName;
// 获取当前节点的值，其中：
// 元素节点返回 null
// 属性节点返回属性的值
// 文本节点返回文本内容
var nodeValue = node.nodeValue;
```

### DOM 中的空白符

HTML 文档中的空白符会给 DOM 操作带来麻烦。这些空白符会出现在 DOM 中，意味着：

* 某些文本节点只含有空白符；
* 某些文本节点的头尾两端有空白符。

请看下面文档的 DOM 树表示：

```html
<!-- My document -->
<html>
<head>
  <title>My Document</title>
</head>
<body>
  <h1>Header</h1>
  <p>
    Paragraph
  </p>
</body>
</html>
```

![img](https://mdn.mozillademos.org/files/854/whitespace_tree.png)

对 DOM 进行遍历操作时，这些空白符也会被遍历到，造成不期望的结果。

下面例举了一些有助于处理空白符的函数：

```javascript
// DOM 中的空白符可以是以下几种：
// "\t" TAB 制表符
// "\n" LF(Line Feed) 换行
// "\r" CR(Carriage Return) 回车
// " " SPC 空格

// 1. 检查一个节点的文本内容是否只包含空白符
function isAllWhitespace (node) {
  // 是否可以找到除空白符之外的字符
  return !(/[^\t\n\r]/.test(node.textContent));
}
// 参数 node 应为拥有 CharacterData 接口的节点，
// 如文本，注释等

// 2. 检查一个节点是否应该被遍历器忽视，
// 如空白的文本节点，注释节点
function isIgnorable (node) {
  return (node.nodeType === 8) || // 注释节点
         (node.nodeType === 3 && isAllWhitespace(node)); // 空白文本节点
}

// 3. 重写 previousSibling ，忽略空白文本及注释
function nodeBefore (node) {
  while (node) {
    node = node.previousSibling;
    if (!isAllWhitespace(node)) return node;
  }
  return null;
}

// 4. 重写 nextSibling ，忽略空白文本及注释
function nodeAfter (node) {
  while (node) {
    node = node.nextSiling;
    if(!isAllWhitespace(node)) return node;
  }
  return null;
}

// 5. 重写 lastChild ，忽略空白文本及注释
function lastChild (node) {
  node = node.lastChild;
  while (node) {
    if(!isAllWhitespace(node)) return node;
    node = node.previousSibling;
  }
  return null;
}

// 6. 重写 firstChild ，忽略空白文本及注释
function firstChild (node) {
  node = node.firstChild;
  while (node) {
    if(!isAllWhitespace(node)) return node;
    node = node.nextSibling;
  }
  return null;
}

// 7. 重写文本节点的 data 属性（用于获取文本内容），
// 去除空白文本
function dataOf (textNode) {
  return data = textNode.data.trim();
}
```

