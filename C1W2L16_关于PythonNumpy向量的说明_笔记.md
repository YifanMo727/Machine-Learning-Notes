# A Note on Python/Numpy Vectors | 关于 Python/Numpy 向量的说明

> Andrew Ng《深度学习专项课程》Course 1, Week 2, Lesson 16 (C1W2L16)
> 视频:https://www.youtube.com/watch?v=V2QlTmh6P2Y
> 拆解 L15 留下的伏笔:秩为 1 的数组到底哪里危险

## 核心知识点 | Key Points

### 1. 广播的灵活性是把双刃剑 | Flexibility Is a Double-Edged Sword

- numpy 的广播和灵活的形状规则让代码简洁,但也让**很难察觉的 bug**容易混进代码。
- Python/numpy 的这套特性既是"优点"也是"缺点(bug 的温床)"。
- The flexibility of numpy's broadcasting is both a strength (concise code) and a weakness (hard-to-catch bugs).

### 2. 元凶:秩为 1 的数组 | The Culprit: Rank 1 Arrays

- 代码:`a = np.random.randn(5)` → `a.shape` 输出 **(5,)**,不是 (5,1) 也不是 (1,5)。
- 这种形状叫**秩为 1 的数组 (rank 1 array)**——它既不是行向量也不是列向量,是一种"不三不四"的一维结构。
- `a` 和它的转置 `a.T` **看起来一模一样**(打印出来分不清行列),这是最容易埋雷的地方。
- `a = np.random.randn(5)` gives shape (5,) — neither a row nor a column vector. This is a "rank 1 array." a and a.T look identical when printed, which is exactly where bugs hide.

### 3. 秩为 1 数组引发的诡异 bug | The Weird Bug

- 用秩为 1 数组算内积:`np.dot(a, a.T)` 期望得到一个矩阵,实际上得到的是**一个数**(标量)。
- 原因:(5,) 和 (5,) 转置后还是 (5,),numpy 不会把它当成真正的行/列向量来做矩阵乘法,而是退化成普通的向量点积。
- 这类 bug **既不报错也不崩溃**,只是默默给出一个形状不对/数值不对的结果,调试起来非常痛苦。
- With a rank 1 array, np.dot(a, a.T) unexpectedly returns a single number instead of a matrix — because (5,) transposed is still (5,). Such bugs don't crash; they silently produce wrong shapes, making them very hard to debug.

### 4. 解决办法一:明确指定形状 | Fix 1: Always Specify Shape ⭐

- **不要用** `a = np.random.randn(5)`(秩为 1 数组)。
- **要用**:
  - `a = np.random.randn(5, 1)` → 明确的**列向量** (5,1)
  - `a = np.random.randn(1, 5)` → 明确的**行向量** (1,5)
- 这样 a 和 a.T 就有明显不同的形状,行为符合直觉。
- Avoid rank 1 arrays. Use `np.random.randn(5,1)` for a column vector or `np.random.randn(1,5)` for a row vector — then a and a.T behave as expected.

### 5. 解决办法二:用 assert 检查 | Fix 2: Use assert

- 在代码里加一行检查:`assert(a.shape == (5, 1))`
- **好处**:执行成本极低,又能当**文档**用——别人一看就知道这个变量应该是什么形状,写错了程序立刻报错提醒。
- assert(a.shape == (5,1)) is cheap to run and doubles as documentation — it makes your intent explicit and catches shape mistakes immediately.

### 6. 解决办法三:用 reshape 纠正 | Fix 3: Reshape When Needed

- 万一不小心得到了秩为 1 的数组,用 `a = a.reshape(5, 1)` 或 `a.reshape(1, 5)` 显式转成正确形状。
- reshape 操作本身开销很低,不用担心性能。
- If you end up with a rank 1 array, fix it with a.reshape(...). Reshape is a cheap operation.

## 名词解释 | Glossary

| 英文 | 中文 | 解释 |
|---|---|---|
| Rank 1 Array | 秩为 1 的数组 | 形状如 (n,) 的一维数组,非行非列,行为不直观 |
| a.shape | 形状属性 | 查看 numpy 数组维度的方式 |
| np.random.randn(n) | 随机秩 1 数组 | 容易埋雷的写法,应避免 |
| np.random.randn(n, 1) | 随机列向量 | 明确形状的写法(推荐)|
| a.T (Transpose) | 转置 | 秩 1 数组转置后形状不变,是 bug 高发点 |
| assert | 断言 | 检查变量形状是否符合预期,出错立即报错 |
| .reshape(...) | 重塑形状 | 把数组显式转换成期望的形状,开销很低 |
| Silent Bug | 静默 bug | 不报错也不崩溃,只是悄悄算错的错误类型 |
