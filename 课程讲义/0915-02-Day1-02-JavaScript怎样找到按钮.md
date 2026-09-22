# Day 1-02：JavaScript 怎样找到按钮

## 一、为什么要“找到”按钮

HTML 和 JavaScript 分工不同：

```text
HTML 创建按钮
JavaScript 控制按钮发生什么
```

想控制一个页面元素，JavaScript 必须先取得这个元素。就像想让某个人做事，要先找到那个人。

## 二、DOM 是什么

浏览器读取 HTML 后，会把页面中的标签整理成一组 JavaScript 可以操作的对象，这套结构叫 DOM。

```text
HTML 标签 → 浏览器读取 → DOM 对象 → JavaScript 可以操作
```

例如，HTML 中有：

```html
<button id="startButton">开始学习</button>
```

浏览器会把它变成一个“按钮对象”。JavaScript 可以找到并保存这个对象。

## 三、document 是什么

JavaScript 中的 `document` 代表当前整个网页文档。现在可以记成：

```text
document = 当前网页
```

我们要从整个网页中查找按钮，所以从 `document` 开始。

## 四、querySelector 是什么

`querySelector` 是 `document` 提供的查找方法：

```javascript
document.querySelector("选择器")
```

- `document`：在当前网页中；
- `.`：使用这个对象拥有的功能；
- `querySelector`：查找第一个符合选择器的元素；
- 圆括号：把查找条件传给这个方法。

按钮有 `id="startButton"`。CSS 中使用 `#` 表示 id 选择器，所以写成：

```javascript
document.querySelector("#startButton")
```

HTML 属性中写 `id="startButton"`，只有在选择器中才加 `#`。

## 五、为什么用变量保存

```javascript
var button = document.querySelector("#startButton");
```

从右往左理解：先找到按钮对象，再把它保存到 `button` 变量。以后写 `button`，就代表这个按钮。

## 六、怎样确认真的找到了

```javascript
console.log(button);
```

`console.log` 会把内容输出到浏览器开发者工具的 Console（控制台），不会显示在网页正文中。

如果成功，会看到类似：

```html
<button class="layui-btn" id="startButton">...</button>
```

如果显示 `null`，就是没有找到，通常检查 id 拼写、`#` 和代码执行顺序。

## 七、为什么 JavaScript 放在按钮后面

浏览器通常从上往下读取 HTML。如果查找代码在按钮前面，执行时下面的按钮可能还没有创建。

初学阶段统一使用：先写页面元素，后写自己的 JavaScript。

## 本小节练习

1. 把现有的自定义 `<script>...</script>` 整块移动到按钮下面、`</body>` 上面。
2. 在 `var layer = layui.layer;` 后添加：

```javascript
var button = document.querySelector("#startButton");
console.log(button);
```

3. 保留 `layer.msg("Hello,world");`，暂时不改成点击触发。
4. 保存并刷新，按 `F12` 打开开发者工具，查看 Console。

本节只要求控制台成功显示按钮元素，不要求记住 `querySelector`。
