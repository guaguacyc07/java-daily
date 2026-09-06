# **BGN2 田忌赛马**

- **题目 : [BGN2 田忌赛马](https://www.nowcoder.com/practice/49d799f65a0749588e9cd7e6135a4a9a)**

> 难度: 中等
>
> 标签: 暴力枚举

## 解法一

> 这玩意能是中等?

- **思路:**
  - 把 a 数组的最大值比 v 数组的中间值
  - 把 a 数组的中间值比 v 数组的最小值
  - 都满足就yes

**AC代码:**

```py
v_nums = list(map(int, input().split()))

max_v = max(v_nums)

min_v = min(v_nums)

mid_v = sum(v_nums) - max_v - min_v

a_nums = list(map(int, input().split()))

max_a = max(a_nums)

min_a = min(a_nums)

mid_a = sum(a_nums) - max_a - min_a

if max_a > mid_v and mid_a > min_v :
    print("Yes")
else :
    print("No")
```

- **时间复杂度：O(1)**

  只读入并处理固定的 3+3 个数字，`max`/`min`/`sum` 都是常数时间。

- **空间复杂度：O(1)**

  只用了常数个变量（两个输入列表长度固定为 3）。


---

