# Day 2-05：导航下拉与 element 模块

## 一、这一节要实现什么

把“学生管理”从一个普通导航项目改成带两个子项目的下拉菜单：

```text
学生管理
├── 学生列表
└── 新增学生
```

这一节会同时用到：

- HTML 父子结构：描述主菜单和子菜单；
- Layui CSS：提供导航外观；
- Layui JavaScript 的 `element` 模块：处理导航交互。

## 二、下拉菜单的 HTML 结构

```html
<li class="layui-nav-item">
    <a href="javascript:;">学生管理</a>
    <dl class="layui-nav-child">
        <dd><a href="javascript:;">学生列表</a></dd>
        <dd><a href="javascript:;">新增学生</a></dd>
    </dl>
</li>
```

结构关系：

```text
li：学生管理这个主导航项目
├── a：主项目文字
└── dl：它的子菜单
    ├── dd → a：学生列表
    └── dd → a：新增学生
```

这里的 `dl`、`dd`、`a` 都是 HTML 标签；`layui-nav-child` 是 Layui class。

子菜单写在“学生管理”的 `li` 里面，是为了表达：这个子菜单属于学生管理，而不是属于整个导航栏。

## 三、为什么现在要引入 layui.js

之前的静态导航只需要外观，所以使用 `layui.css` 就能显示。

现在出现下拉交互，除了外观，还需要 Layui 的 JavaScript 模块处理导航行为，因此在 `</body>` 前引入：

```html
<script src="./layui-v2.13.9/layui/layui.js"></script>
```

继续使用本地文件，不使用 CDN。

## 四、element 模块是什么

`element` 是 Layui 的常用模块之一，负责导航、选项卡、进度条等页面元素的相关交互。

本节使用完整写法：

```html
<script>
    layui.use(["element"], function () {
        const element = layui.element;
    });
</script>
```

逐层翻译：

```text
layui.use(...)
请 Layui 准备我们需要的模块。

["element"]
这一次明确需要 element 模块。

function () { ... }
模块准备好之后，由 use 调用这个回调函数。

const element = layui.element
从 layui 中取得 element 模块对象，交给变量 element。
```

这里虽然暂时没有手动调用 `element` 的方法，但显式加载它可以让当前代码清楚表达“本页使用了 element 模块”。后面监听导航或操作选项卡时，会继续使用这个变量。

执行顺序是：

```text
浏览器读取 layui.js
        ↓
执行 layui.use
        ↓
Layui 准备 element 模块
        ↓
use 调用回调函数
        ↓
取得 layui.element
```

## 五、整理页面区域

顶部导航应该位于页面标题上方。建议把结构整理为：

```text
body
├── 顶部导航 ul
└── layui-container
    ├── 页面标题 h1
    └── 统计卡片行
```

导航横跨页面顶部；标题和卡片放进同一个 `layui-container`，它们的左右边缘就能对齐。

## 六、本小节练习

继续修改 `04-day2-栅格入门.html`：

1. 把导航移动到 `<body>` 开始后的第一块内容。
2. 把一级标题移动到 `layui-container` 内、`layui-row` 前面。
3. 把“学生管理”改成包含子菜单的结构，子项目为“学生列表”和“新增学生”。
4. 在 `</body>` 前通过本地路径引入 `layui.js`。
5. 在它后面写 `layui.use(["element"], 回调函数)`，并在回调中取得 `layui.element`。
6. 刷新页面，把鼠标移动到“学生管理”上，观察是否显示子菜单。

如果没有下拉效果，先检查浏览器控制台，再检查 `layui.js` 路径、`layui-nav-child` 拼写和父子结构。

## 七、验收时需要会说

1. 为什么子菜单要写在“学生管理”的 `li` 里面？
2. 这次为什么只有 `layui.css` 不够？
3. `element`、`layui.element`、变量 `element` 三者是什么关系？
