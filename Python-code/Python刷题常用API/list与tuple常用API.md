# list / tuple（列表与元组）常用 API

> 刷题 99% 用 `list`；`tuple` 用的场景少（不可变，可当 `dict` 的 key、放进 `set`）。
>
> AI生成+人工优化

## 一、创建与初始化

```py
nums = [1, 2, 3]                # 字面量（最常用）
empty = []                      # 空列表
nums2 = list("abc")             # list(可迭代) → ['a', 'b', 'c']
zeros = [0] * 5                 # [0, 0, 0, 0, 0] ⚠️ 见"常见坑"的引用共享
nums3 = list(range(5))          # [0, 1, 2, 3, 4]

# 二维数组：一定要用推导式！
matrix = [[0] * 3 for _ in range(3)]   # ✅ 3 个独立内层列表

tup = (1, 2, 3)                 # tuple 字面量
one = (1,)                      # ⚠️ 单元素必须带逗号；(1) 只是个 int
```

> ⚠️ 千万别写 `[[0] * 3] * 3` —— 它是同一个内层列表的 3 个引用，改一格全变。

## 二、增

```py
nums.append(x)         # 末尾加一个元素，O(1) 均摊
nums.extend([x, y])    # 末尾扩展多个元素（等价于 nums += [x, y]）
nums.insert(i, x)      # 在下标 i 前插入，O(n)
```

## 三、删

```py
nums.pop()             # 删末尾，返回被删元素，O(1)
nums.pop(i)            # 删下标 i，返回被删元素，O(n)
nums.remove(x)         # 删第一个等于 x 的元素（按值），不存在会抛 ValueError
nums.clear()           # 清空
del nums[i]            # 按下标删
del nums[a:b]          # 按切片删
```

> ⚠️ `remove` / `pop(空)` 不存在都会抛异常：先 `x in nums` 判断或 `try` 包住。

## 四、查

```py
nums[i]            # 按下标取值，O(1)；支持负数：nums[-1] 是最后一个
x in nums          # 是否包含，O(n)
nums.index(x)      # x 第一次出现的下标，不存在抛 ValueError
nums.count(x)      # x 出现的次数
len(nums)          # 长度
```

> ⚠️ 下标越界抛 `IndexError`；`nums[-0]` 就是 `nums[0]`（-0 == 0）。

## 五、改

```py
nums[i] = x            # 改单个元素
nums[a:b] = [x, y]     # 切片赋值：可以同时改/增/删一段
```

## 六、遍历

```py
for x in nums: ...                 # 只要值
for i in range(len(nums)): ...     # 只要下标
for i, x in enumerate(nums): ...   # 最常用：同时要下标和值
for x, y in zip(a, b): ...         # 并行遍历两个列表
```

> ⚠️ 遍历过程中**不要增删元素**（会跳元素或越界）；需要过滤时用推导式新建列表。

## 七、转换

```py
list(t)                  # tuple/set/str → list
tuple(nums)              # list → tuple
list(s)                  # 字符串 → 字符列表
"".join(ch_list)         # 字符列表 → 字符串
list(map(int, input().split()))   # ACM 读一行数字（高频！）

nums[:]                  # 浅拷贝一份（nums 本身不变）
sorted(nums)             # 返回新排序列表（不改原列表）
```

## 八、排序

```py
nums.sort()                     # 原地升序；⚠️ 返回 None，不是新列表！
nums.sort(reverse=True)         # 原地降序
new = sorted(nums)              # 返回新列表（原列表不动）
sorted(strs, key=len)           # 按长度排序
sorted(pairs, key=lambda x: x[1])   # 按第 2 个维度排序
sorted(pairs, key=lambda x: (-x[0], x[1]))  # 多关键字：x[0] 降序、x[1] 升序
```

> 想按**值排序、只取下标**（高频套路）：
> `idx = sorted(range(len(nums)), key=lambda i: nums[i])`

## 九、切片（Python 灵魂）

```py
s[::-1]         # 反转
s[1:]           # 去掉第一个（回溯/子集题常用）
s[:-1]          # 去掉最后一个
s[i:j]          # 截取 [i, j) 区间
s[::2]          # 步长取
nums[a:b] = []  # 删除一段
```

## 十、复杂度对照

| 操作 | list | tuple |
| ---- | ---- | ----- |
| 按下标取值 | O(1) | O(1) |
| 末尾 append / pop | 均摊 O(1) | 不支持（不可变） |
| 中间 insert / 删除 | O(n) | 不支持 |
| 包含 in / index / count | O(n) | O(n) |
| 切片 | O(k) | O(k) |

> 结论：**队列千万别用 `list.pop(0)`**（O(n)），用 `collections.deque`；栈直接用 `list.append` / `pop()` 即可。

## 十一、刷题高频套路

### 1. 存二维结果（每轮拷贝当前路径）

```py
ans = []
path = []
ans.append(path[:])   # ⚠️ 必须拷贝，否则后面改 path 会污染已存的答案
```

### 2. 去重且保留原顺序

```py
seen = set()
res = [x for x in nums if x not in seen and not seen.add(x)]
```

### 3. 反转

```py
nums.reverse()    # 原地
nums[::-1]        # 返回新列表
```

### 4. 用 list 模拟栈

```py
stack.append(x)   # push
stack.pop()       # pop，O(1)
```

### 5. 快慢 / 对撞双指针（已排序时）

```py
i, j = 0, len(nums) - 1
while i < j:
    ...
    i += 1; j -= 1
```

### 6. 滑动窗口存下标

```py
q = collections.deque()   # 队首放窗口内最大值/最小值的下标
```

## 十二、常见坑

1. **`[[]] * n` / `[[0] * m] * n` 引用共享**：改一个内层全变；用 `[[] for _ in range(n)]`。
2. **`sort()` 返回 `None`**：`x = nums.sort()` 会把 x 变成 None；要新列表用 `sorted`。
3. **遍历时删除会跳过元素**：倒着删、或推导式重建。
4. **`list.pop(0)` / `insert(0, x)` 是 O(n)**：频繁头部操作改用 `deque`。
5. **函数默认参数别用可变对象**：`def f(arr=[])` 有记忆；改 `def f(arr=None)`。
6. **浅拷贝**：`b = a` 只是引用，`b = a[:]` 或 `list(a)` 才是拷贝（二维需 `copy.deepcopy`）。
7. **tuple 单元素**：`(1)` 是 int，`(1,)` 才是 tuple。
8. **`nums[i]` 越界**：用 `-1` 取最后一个元素前先想清楚列表是否为空。
