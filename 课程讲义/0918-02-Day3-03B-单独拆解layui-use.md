# Day 3-03B：单独拆解 layui.use

## 一、这节只解决一个问题

下面这段代码到底按什么顺序执行：

```javascript
layui.use(["layer"], afterModulesReady);
```

本节不处理表单、不监听提交，也不使用匿名函数嵌套。

## 二、先定义一个有名字的函数

```javascript
function afterModulesReady() {
    const layerModule = layui.layer;
    layerModule.msg("layer 模块已经准备好");
}
```

函数名 `afterModulesReady` 可以翻译成：

```text
模块准备完成之后要做的事
```

浏览器读到函数定义时，只是保存这段代码，还不会弹出消息。

```text
afterModulesReady     函数本身
afterModulesReady()   立即调用函数
```

## 三、再把函数交给 layui.use

```javascript
layui.use(["layer"], afterModulesReady);
```

拆开看：

```text
layui               Layui 提供的总对象
use                  layui 对象的方法
use(...)             现在调用 use 方法
["layer"]            第一个参数：需要准备的模块名数组
afterModulesReady    第二个参数：函数本身
```

这里不能写：

```javascript
layui.use(["layer"], afterModulesReady());
```

因为加上 `()` 会让浏览器先立即执行函数，再把函数执行结果交给 `use`。我们的目标是把函数本身交给 `use`，让 `use` 在模块准备好以后调用它。

## 四、完整执行时间线

```text
1. 浏览器加载 layui.js
2. 浏览器保存 afterModulesReady 函数，但不执行
3. 浏览器调用 layui.use
4. use 根据 ["layer"] 准备 layer 模块
5. layer 准备好后，use 调用 afterModulesReady
6. 函数内部取得 layui.layer
7. 调用 layerModule.msg
8. 页面出现消息
```

因此 `afterModulesReady` 是回调函数：不是我们直接调用，而是交给 `use`，以后由 `use` 调用。

它是有名字的回调函数，不是匿名函数。

## 五、函数内部两句话

```javascript
const layerModule = layui.layer;
```

```text
const         声明变量
layerModule   我们自己起的变量名
=             把右边的值交给左边变量
layui.layer   获取 layer 模块对象，没有调用函数
```

然后：

```javascript
layerModule.msg("layer 模块已经准备好");
```

```text
layerModule   保存模块对象的变量
msg           模块对象的方法
msg(...)      调用方法
字符串        传给 msg 方法的参数
```

## 六、为什么变量不直接叫 layer

下面两种写法都正确：

```javascript
const layer = layui.layer;
layer.msg("完成");
```

```javascript
const layerModule = layui.layer;
layerModule.msg("完成");
```

本节故意使用 `layerModule`，只是为了让你一眼看出：左边是我们创建的变量，右边才是 `layui` 上的模块对象。以后熟悉后通常简写为 `layer`。

## 七、本小节练习

新建 `8.day3-use拆解.html`：

1. 写 HTML 基本骨架和一级标题“layui.use 拆解”。
2. 在 `head` 中引入本地 `layui.css`。
3. 在页面内容之后引入本地 `layui.js`。
4. 在第二个 script 中先定义命名函数 `afterModulesReady`。
5. 函数中取得 `layui.layer`，保存到变量 `layerModule`。
6. 调用 `layerModule.msg("layer 模块已经准备好")`。
7. 在函数定义下面写：

```javascript
layui.use(["layer"], afterModulesReady);
```

8. 刷新页面，确认页面加载后自动显示消息。

不要增加按钮、DOM 查找、表单或匿名函数。本节只观察模块准备与回调执行。

## 八、验收时需要会说

1. `layui.use(["layer"], afterModulesReady)` 的两个参数分别是什么？
2. 为什么传入 `afterModulesReady`，而不是 `afterModulesReady()`？
3. `afterModulesReady` 最终由谁调用，在什么时候调用？
4. `const layerModule = layui.layer` 有没有调用函数？`layerModule.msg(...)` 呢？
