# Day 3-03F：看懂提交回调收到的 `data.field`

开始条件：上一节已做到“填完整才执行回调，而且页面不跳转”。这节不改表单结构，只观察数据从哪里来。

## 参数是谁给的

上一节函数是：

```javascript
function handleStudentSubmit() {
    console.log("提交回调执行了");
    return false;
}
```

Layui 实际调用这个函数时，会把本次提交的信息交给它。要接住这份信息，只给函数加一个参数名：

```javascript
function handleStudentSubmit(data) {
    console.log(data.field);
    return false;
}
```

`data` 这个名字由我们起；真实的值由 Layui 调用回调时传入。`field` 是提交信息对象里的一个属性。`data.field` 读作“这次提交信息中的表单字段”，不是调用方法，因为后面没有 `()`。

## 字段对象如何形成

假设页面上有：

```html
<input name="studentName" value="王五">
<select name="gender">
    <option value="male" selected>男</option>
</select>
```

那么 `data.field` 大致是：

```javascript
{ studentName: "王五", gender: "male" }
```

左边的键来自控件的 `name`；右边的值来自当前输入内容或被选中 `option` 的 `value`。用户看到的“男”不一定等于程序拿到的 `male`。`id` 与 `label for` 负责定位控件和关联标签，**不决定 `data.field` 的键**。如果没有 `name`，那项通常不会作为具名字段收集。

这还是浏览器里的 JavaScript 数据对象，不等于已经“上传数据库”。本课不连接后端。

## 和普通点击函数对照

```text
普通按钮：浏览器调用点击函数；我们这次没使用事件参数
提交按钮：Layui form 调用提交函数；我们接收 data 参数
```

两者都是“先登记、稍后由别的代码调用”的回调，但调用者、时机和传入的信息不同。`return false` 仍留在提交函数最后，让测试时页面不跳转。

## 本节小实验

继续改 `9.复习-学生信息页.html`：只给已有的 `handleStudentSubmit` 增加 `data` 参数，把原来输出固定文字的 `console.log` 改成 `console.log(data.field)`。填写“王五”，选择“男”后点击保存。展开控制台对象，找出 `studentName`、`gender` 两个键和值各来自哪行 HTML。然后将姓名改成“李四”再保存，观察哪个值发生变化。

如果看到的是 `{ studentName: "王五", gender: "male" }` 一类对象、页面不跳转，并且你能指出每个键和值的来源，本节通过。之后我们再做从空文件串联的综合练习。官方依据：[Layui form 提交回调与 `data.field`](https://layui.dev/docs/2/form/)。
