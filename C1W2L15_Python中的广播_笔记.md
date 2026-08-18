# Broadcasting in Python | Python 中的广播

> Andrew Ng《深度学习专项课程》Course 1, Week 2, Lesson 15 (C1W2L15)
> 视频:https://www.youtube.com/watch?v=tKcLaGdvabM
> 补完 L13 留下的伏笔:b 是怎么被自动扩展的

## 核心知识点 | Key Points

### 1. 例子:100 克营养成分表 | The Example

- 一张表:行是脂肪/蛋白质/碳水,列是苹果/牛肉/鸡蛋/土豆(4×3 矩阵 A)。
- 目标:算出**每种食物中每种营养的热量占比 (%)**,不用任何 for 循环。
- Goal: compute the percentage of calories from each nutrient for each food, without any for loop.

### 2. 广播的操作 | The Broadcasting Operation

```python
cal = A.sum(axis=0)          # 按列求和 → 1×4 行向量(每种食物的总热量)
percentage = 100 * A / cal.reshape(1, 4)   # 4×3 除以 1×4
```

- **axis=0**:沿**列方向**(竖着)求和,得到每列的总和。
- **axis=1**:沿**行方向**(横着)求和。
- `cal` 本是 (1,4),`A` 是 (3,4);两者形状不同却能直接相除——这就是**广播**在起作用。
- axis=0 sums vertically (down each column); axis=1 sums horizontally. Even though cal (1,4) and A (3,4) have different shapes, Python lets you divide them directly — that's broadcasting.

### 3. 广播的通用规则 | The General Principle ⭐

- **(m, n) 的矩阵,与 (1, n) 的行向量做四则运算,行向量会被自动复制 m 次,变成 (m, n) 再逐元素运算。**
- **(m, n) 的矩阵,与 (m, 1) 的列向量做运算,列向量会被自动复制 n 次,变成 (m, n)。**
- 更一般地:只要两个数组在某一维度上,一个是 1、另一个是 m/n,较小的那个就会被**复制扩展 (copy/duplicate)** 到匹配的大小。
- 回顾 L13:`Z = np.dot(w.T, X) + b`——b 是标量(可看作 1×1),被扩展成 1×m 再和 Z 相加,原理完全相同。
- If one array has size 1 along a dimension and the other has size m or n, the size-1 array gets virtually copied to match. This is exactly what happened with scalar b in Z = wᵀX + b back in L13.

### 4. 更一般的情况 | Even More General

- (m, n) 矩阵和 (1, n) 或 (m, 1) 运算是最常见的情况;规则可以推广到更多维度,但原理一致:**维度为 1 的一边被复制扩展去匹配另一边**。
- The rule generalizes to higher dimensions, but the core idea stays the same: dimensions of size 1 get expanded to match.

### 5. 优点与代价 | Benefit and Caution

- **优点**:代码极其简洁,一行完成本该需要循环的运算,而且底层执行同样很快。
- **代价(留给下一讲的伏笔)**:广播的"灵活"有时会掩盖 bug——比如不小心用了形状 (n,) 的"秩为 1 的数组 (rank 1 array)",导致广播出乎意料的结果。下一讲会讲怎么避免。
- Benefit: extremely concise code. Caution: broadcasting's flexibility can silently produce unexpected shapes/bugs — covered next lecture.

## 名词解释 | Glossary

| 英文 | 中文 | 解释 |
|---|---|---|
| Broadcasting | 广播 | 形状不同的数组做运算时,自动复制扩展较小一方以匹配形状 |
| axis=0 | 按列方向(竖着) | 沿第一维(行)方向聚合,如 A.sum(axis=0) |
| axis=1 | 按行方向(横着) | 沿第二维(列)方向聚合 |
| .reshape(1, 4) | 重塑形状 | 显式指定数组的形状,常用于确保广播按预期工作 |
| A.sum(axis=...) | 求和函数 | numpy 沿指定维度求和 |
| (m, n) vs (1, n) | 广播场景一 | 行向量被复制 m 次匹配矩阵行数 |
| (m, n) vs (m, 1) | 广播场景二 | 列向量被复制 n 次匹配矩阵列数 |
| Rank 1 Array | 秩为 1 的数组 | 形状如 (n,) 的数组,容易在广播时产生意外结果(下一讲详解)|
