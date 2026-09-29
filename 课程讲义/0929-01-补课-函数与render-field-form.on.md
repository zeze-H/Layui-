# 补课：函数、执行顺序，以及 `form.render()`、`data.field`、`form.on`

起因：综合练习第 4 段的代码能跑，但学习者说对函数的写法和逻辑、`render`、`field`、`form.on` 都生疏，且第一次提交时把 `function` 和 `form.on` 写进了点击回调里。按协作规范，"能运行但讲不清因果"不记为掌握，所以先补这一课，再做综合练习的验收。

学习者要求：**讲解时不要用 Python 做类比**，用页面里的真实代码和生活化比喻说明。

## 一、函数：一份起了名字的操作清单

先说它解决什么问题：**有些事现在不做，要等某个时刻再做。** 把这些事写成一个函数，起个名字，到时候再执行。

```javascript
function greet(name) {          // 定义：写清单，此时不执行
    return "你好，" + name;
}

greet("张三");                   // 调用：现在按清单执行
```

| 部分 | 含义 |
| --- | --- |
| `function` | 声明"这是一个函数" |
| `greet` | 函数名 |
| `(name)` | 参数：函数从外面接收的东西 |
| `{ ... }` | 函数体：要做的事 |
| `return` | 把结果交出去，函数到此结束 |
| `greet("张三")` | 调用，括号表示"现在执行" |

### 括号的区别（最重要）

```text
greet        函数本身，像一张没执行的清单
greet("x")   现在就执行清单，得到结果
```

这就是为什么 `form.on("submit(studentInfo)", handleStudentSubmit)` 后面**不写括号**：我们要把清单交给 Layui，让它以后按清单做事；写了括号就成了"现在立刻执行"。

### 回调：把清单交给别人，由别人决定什么时候执行

本页面有三个回调，对比起来最清楚：

| 谁调用 | 什么时候 | 传进来什么 | 代码 |
| --- | --- | --- | --- |
| Layui | 模块准备好之后 | 无 | `layui.use([...], function () {...})` |
| 浏览器 | 用户点击"查看填写说明"之后 | 本页没用 | `addEventListener("click", function () {...})` |
| Layui | 必填验证通过并点击保存之后 | `data`（含表单数据） | `form.on("submit(studentInfo)", handleStudentSubmit)` |

三个函数都不是我们自己调用的，调用者和时机不同。

### 作用域：函数在哪一层，就只在那一层生效

本次的错误就是这个。花括号 `{}` 围出一个范围，里面写的东西属于这个范围：

```text
layui.use(..., function () {                 范围 A
    const form = layui.form;                 在 A 里
    helpButton.addEventListener("click", function () {   范围 B，在 A 里面
        layer.msg("...");                    只在 B 里，点击后才会执行
    });
    function handleStudentSubmit(data) {...} 在 A 里，和 B 并排
    form.on("submit(...)", handleStudentSubmit);   在 A 里，页面一打开就登记
});
```

规则：里面能看到外面，外面看不到里面；写在哪个范围，就在那个范围的时机执行。学习者把提交函数和 `form.on` 误写进范围 B，结果只有点了"说明"按钮才会登记，而且登记那一刻还因为 `from` 拼错而报错。

阅读方法：找每个 `function ... {` 配对的 `}`，两者之间就是这个函数的内容。

## 一·五、执行顺序：为什么"代码写在前面"不等于"先执行"

**原理只有三句话：**

1. 浏览器从上到下读代码，一行一行执行。
2. 读到**函数定义**，只是保存；读到**调用**（带括号），才执行。
3. 读到**登记**（`use`、`addEventListener`、`form.on`），只是记下"以后某件事发生时，请调用这个函数"，然后接着往下读。

**为什么这样设计：** 网页要响应用户，而用户什么时候点、点不点，程序无法预知。程序不能停在原地一直等，所以采用"先登记，事件发生了再叫我"。像餐厅排队：留下电话号码是登记，不是打电话；轮到你时，是餐厅打给你。`form.on` 就是留号码，浏览器或 Layui 才是打电话的一方。

### 你这个页面从打开到点击的完整顺序

```text
【页面打开：自上而下】
1. head：加载 layui.css
2. body：从上到下创建导航、卡片、表单……（此时页面元素才存在）
3. 读到 <script src=layui.js>：加载并执行，得到全局对象 layui
4. 读到你的 <script>：执行 layui.use(...)
   这一步只是"告诉 Layui：模块准备好后，调用我这个函数"，读完这行就往下走
5. Layui 准备好模块，并等页面元素建好，调用你交给它的函数 A（use 回调）
   函数 A 里面，自上而下：
     a. 取得 element / form / layer 三个模块对象
     b. form.render()        —— 执行：把下拉框换成 Layui 外观
     c. querySelector(...)   —— 执行：找到说明按钮
     d. addEventListener     —— 登记：点击说明按钮后的函数（此时不弹）
     e. function studentInfo —— 只是保存函数
     f. form.on(...)         —— 登记：提交通过验证后的函数（此时不执行）
   函数 A 执行完，页面安静下来，没有任何消息弹出

【之后：等用户操作，事件发生才执行】
6. 用户点"查看填写说明"  → 浏览器调用点击函数 → layer.msg
7. 用户点"保存学生"      → Layui 检查必填
                          不通过：提示，结束
                          通过：调用 studentInfo(data) → 打印 → 弹消息 → return false
```

### 三个"为什么"

- **为什么 `layui.js` 要放在你的脚本前面？** 因为第 4 步要用 `layui` 这个对象，它是第 3 步才出现的。
- **为什么你的脚本放在页面内容后面？** 第 2 步先把元素创建好，第 5 步里 `querySelector` 才找得到。
- **为什么 `layui.use` 不直接执行、而要传一个函数？** 模块可能需要时间准备（甚至要下载文件），不能保证第 4 步这一刻已经好了，所以让 Layui 准备好之后回头调用。

记住一句话：**代码在文件里的位置，决定它什么时候被读到；而它什么时候执行，取决于它是"直接写的语句"，还是"被登记的函数"。**

## 二、`form.render()`：让 Layui 接管控件的外观

- 页面里原本写的是浏览器原生的 `<select>`。原生下拉框的样式浏览器说了算，Layui 无法改。
- `form.render()` 会扫描页面里的 `layui-form`，把 `select`（还有 checkbox、radio 等）**替换成 Layui 自己画的一套外观**，原来的控件仍在，但被隐藏了，数据仍从它读取。
- 它只管外观，**不提交、不验证、不收集数据**。
- 什么时候要再调用一次：页面加载后，用 JS 动态添加了新的控件，或者用 JS 改了下拉框的选项，需要再 `render` 一次，否则新的还是原生样子。

判断方法：注释掉 `form.render()`，性别下拉框会变回浏览器原生样式，就是它的作用。

## 三、`data.field`：这次提交收集到的表单数据

先看 `layui.js` 里的实现（2.13.9）：点击带 `lay-submit` 的按钮后，Layui 先检查所有带 `lay-verify` 的控件，验证不通过就停在这里，回调根本不会被调用；通过后，它组装出一个对象再交给回调：

```javascript
{
    elem: 被点击的那个按钮,
    form: 所在的 form 元素,
    field: 表单数据对象
}
```

这个对象就是回调收到的参数 `data`。目前只用 `field`，它是：

```javascript
{
    studentName: "张三",
    studentNum: "2026001",
    studentGender: "male"
}
```

- 键：来自控件的 **`name`**（不是 `id`）。
- 值：来自输入的文字，或被选中的 `option` 的 **`value`**（不是用户看到的文字）。
- 没有 `name` 的控件不会被收集。
- `data.field` 中间是点，表示"读取属性"，后面没有括号，所以不是调用方法。

这个对象以后会用 `fetch` 变成 JSON 发给 FastAPI，字段名和 Pydantic 模型里的字段一一对应，所以 `name` 现在就要起得规范。

## 四、`form.on("submit(studentInfo)", handleStudentSubmit)`：登记"谁来处理提交"

这一行有三块：

```text
form.on(          调用 form 模块的 on 方法：登记一个事件处理
"submit(studentInfo)"   事件名（字符串）：名叫 studentInfo 的提交
handleStudentSubmit     处理这个事件的函数（只写名字，不加括号）
)
```

- `submit(studentInfo)` 是一整个字符串，其中 `studentInfo` 必须和 HTML 里保存按钮的 `lay-filter="studentInfo"` **文字完全一致**。它是标签，不是变量，也不是函数名。
- `on` 是"登记"：写下它只是留了个号码，不是现在执行。
- 完整链条：

```text
保存按钮（lay-submit + lay-filter="studentInfo"）被点击
→ Layui 检查所有 lay-verify 的控件
→ 不通过：提示必填，到此为止
→ 通过：收集数据得到 { elem, form, field }
→ 调用登记过的 handleStudentSubmit(data)
→ 函数里 return false，Layui 阻止浏览器默认的提交和刷新
```

### 四·补：`form.on` 的严谨说明（依据本地 layui.js 2.13.9 源码）

**结论：`form.on` 是"登记"：把"事件名"和"你的函数"配成一对，存进 Layui 内部的登记表。它本身不检测按钮，也不执行提交。**

- 形式：`form.on(事件字符串, 回调函数)`。源码里 `form.on` 只是把这两个参数转交给 Layui 的通用事件登记方法 `layui.onevent`，模块名固定为 `form`。
- 事件字符串会被拆开：用 `\((.*)\)$` 匹配末尾括号，`submit(studentInfo)` 拆成 **事件类型 `submit`** 和 **过滤名 `studentInfo`**。登记表的形状（示意）：`{"form.submit": {"studentInfo": [你的函数]}}`。
- 同一个事件类型下，**同一个过滤名再登记一次，会覆盖上一次**（源码：有过滤名时是替换，而不是追加）。
- 触发流程（源码 `submit` 处理函数）：
  1. Layui 在 `document` 上监听：所有带 `lay-submit` 的元素被点击，或 `.layui-form` 发生 submit 事件。
  2. 读取被点元素的 `lay-filter`，找到它所在的表单。
  3. 找出表单内所有带 `lay-verify` 的控件并验证；**不通过则返回 false，提示后结束，不会调用你的函数**。
  4. 通过后，收集表单里所有有 `name` 的控件：得到 `field`。
  5. 组装 `data = { elem: 被点的按钮, form: 所在的 form 元素, field: 字段对象 }`。
  6. 用 `"submit(" + 过滤名 + ")"` 到登记表里查找，找到就调用你的函数，并把 `data` 传进去。
  7. 你的函数若 `return false`，整个处理过程返回 false，浏览器不再做默认的提交和刷新。
- 事件类型：源码中确认有 `submit`、`select`、`radio`；官方文档还列有 `checkbox`、`switch` 等，本阶段只用 `submit`，需要时再查文档。回调收到的参数因事件类型而不同。
- `form` 对象里常用的方法：`render`（渲染控件外观）、`on`（登记事件）、`val`（给表单赋值/取值）、`getValue`（读取字段）、`validate`（验证）、`submit`（用代码触发某个过滤名的提交）。本阶段只用 `render` 和 `on`。

## 五、三个小实验（一次做一个，做完还原）

1. **注释掉 `form.render()`**，刷新页面：性别下拉框变回原生样式。还原。
2. **把 `lay-filter` 改成 `studentInfo2`**（HTML 那边改，JS 不动），填完整再点保存：回调不再触发，页面会刷新。还原。
3. **把 `form.on` 的第二个参数写成 `handleStudentSubmit()`**（加了括号），刷新页面，看控制台报什么，想想为什么。还原。

## 六、验收：不看讲义，用自己的话回答

1. `handleStudentSubmit` 和 `handleStudentSubmit()` 有什么区别？
2. `form.on` 里的 `studentInfo` 要和 HTML 的哪个属性一致？两处不一致会怎样？
3. `data.field` 里的键来自哪个属性？值来自哪里？
4. `form.render()` 做了什么，没做什么？
5. 为什么提交函数和 `form.on` 要写在 `layui.use` 的回调里，但不能写进点击回调里？

回答得清楚后，综合练习才算完成。
