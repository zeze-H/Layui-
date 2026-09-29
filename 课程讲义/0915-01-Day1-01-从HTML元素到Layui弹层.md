# Day 1-01：从 HTML 元素到 Layui 弹层

## 这节课要解决什么

浏览器默认只认识 HTML、CSS 和 JavaScript。Layui 是别人写好的一套 CSS 和 JavaScript 工具。我们先用 HTML 放一个标题和按钮，再引入 Layui 改变按钮外观，最后调用 Layui 显示消息。

## 一、HTML 元素是什么

HTML 用“标签”描述页面内容。多数标签都有开始和结束：

```html
<标签名>内容</标签名>
```

例如：

```html
<h1>我的 Layui 学习</h1>
```

- `h` 表示 heading（标题）。
- `1` 表示最高一级标题。
- `<h1>` 是开始标签，`</h1>` 是结束标签。
- 中间的文字才是页面显示的内容。

按钮也是 HTML 元素：

```html
<button>开始学习</button>
```

这时它只是浏览器默认按钮，还没有 Layui 外观，也没有点击行为。

## 二、属性是什么

属性写在开始标签中，用来补充描述元素：

```html
<button class="layui-btn" id="startButton">开始学习</button>
```

- `class="layui-btn"`：让 CSS 找到这一类元素。`layui-btn` 是 Layui 已经写好的按钮样式类。
- `id="startButton"`：给这个按钮一个页面内唯一的名字，之后 JavaScript 可以准确找到它。

`class` 负责“它属于哪一类”，`id` 负责“它具体是哪一个”。属性两边是否留空格不影响运行，但统一写成 `class="..."` 更容易阅读。

## 三、为什么要引入 Layui CSS

浏览器并不知道 `layui-btn` 应该长什么样。下面这行把 Layui 的样式表交给浏览器：

```html
<link href="//unpkg.com/layui@2.13.9/dist/css/layui.css" rel="stylesheet">
```

- `<link>`：把外部资源连接到当前网页。
- `href`：资源地址。
- `rel="stylesheet"`：告诉浏览器，这个资源是 CSS 样式表。

因此，按钮变绿不是 `class` 自己产生的，而是引入的 `layui.css` 中恰好定义了 `.layui-btn` 的外观。

## 四、为什么还要引入 Layui JavaScript

CSS 只负责外观。弹层、日期、表格等交互需要 JavaScript：

```html
<script src="//unpkg.com/layui@2.13.9/dist/layui.js"></script>
```

- `<script>`：加载或编写 JavaScript。
- `src`：外部 JavaScript 文件的位置。

这一步只是加载工具箱，并不会自动弹出消息。

## 五、怎样调用弹层

```html
<script>
layui.use(function () {
    var layer = layui.layer;
    layer.msg("Hello World");
});
</script>
```

逐行理解：

1. `layui.use(...)`：等 Layui 可以使用后，执行传进去的函数。
2. `function () { ... }`：一个函数，花括号中放稍后要执行的代码。
3. `var layer = layui.layer;`：从 `layui` 对象中取得弹层模块，并给它一个短名字 `layer`。
4. `layer.msg(...)`：调用弹层模块的 `msg` 方法。
5. `"Hello World"`：传给 `msg` 的文字参数，决定消息内容。
6. `);` 和 `}`：分别结束方法调用、函数主体和 `layui.use` 调用。

它们之间的关系：

```text
layui.js 提供 layui
    ↓
layui.layer 提供弹层模块
    ↓
layer.msg("文字") 显示消息
```

## 六、代码放置顺序

调用代码必须写在引入 `layui.js` 之后，因为浏览器按顺序执行。如果先使用、后引入，执行时还不存在 `layui`。

推荐的初学顺序：

```text
HTML 页面内容
↓
引入 layui.js
↓
编写自己的 JavaScript
↓
结束 body
```

## 本小节练习

现在只做一件事：在你已有的 `02-页面元素与点击事件.html` 中，亲手加入第五部分的调用代码，让页面打开时自动弹出 `Hello World`。

暂时不绑定按钮点击。成功后，下一小节才学习“JavaScript 怎样找到按钮”。
