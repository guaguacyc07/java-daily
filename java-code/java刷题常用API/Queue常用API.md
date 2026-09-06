# Queue / Deque 常用 API

> 队列是 **FIFO**（先进先出），双端队列 `Deque` 两端都能进出；刷题一律用 `ArrayDeque`。
>
> `Deque` 既能当队列，也能当栈（替代 `Stack`）。
>
> AI生成+人工优化

## 〇、先记住：两组方法

Queue 每个操作都有两种写法：**抛异常版** 和 **返回 null / false 版**。

| 操作 | 抛异常（失败） | 返回 null/false（失败） |
| ---- | -------------- | ----------------------- |
| 入队尾 | `add` | `offer` ✅ |
| 获取并移除队头 | `remove` | `poll` ✅ |
| 仅查看队头（不移除） | `element` | `peek` ✅ |

> 刷题一律用 `offer` / `poll` / `peek`，空队列时返回 `null` 不报错。

## 一、创建与初始化

```java
// 队列（FIFO）—— 刷题用 ArrayDeque
Queue<Integer> queue = new ArrayDeque<>();
// 也能用 LinkedList 当队列（少用）
Queue<Integer> queue2 = new LinkedList<>();

// 双端队列
Deque<Integer> deque = new ArrayDeque<>();

// 当栈用（替代 Stack）
Deque<Integer> stack = new ArrayDeque<>();
```

## 二、增（入队）

```java
queue.offer(1);       // 入队尾，失败返回 false（推荐）
queue.add(1);         // 入队尾，失败抛异常

// Deque 两端入队
deque.offerLast(2);   // 加到队尾
deque.offerFirst(0);  // 加到队头
```

## 三、删（出队 = 获取并移除）

```java
queue.poll();         // 获取并移除队头元素，空返回 null（推荐）
queue.remove();       // 获取并移除队头元素，空抛异常

// Deque 两端出队（获取并移除）
deque.pollFirst();    // 获取并移除队头元素，空返回 null
deque.pollLast();     // 获取并移除队尾元素，空返回 null
```

## 四、查（仅查看队头 / 队尾，不移除）

```java
queue.peek();         // 仅查看队头元素（不移除），空返回 null（推荐）
queue.element();      // 仅查看队头元素（不移除），空抛异常

deque.peekFirst();    // 仅查看队头元素（不移除）
deque.peekLast();     // 仅查看队尾元素（不移除）
```

## 五、改

队列没有"改"——FIFO 只能进出。`Deque` 有 `removeFirstOccurrence` / `removeLastOccurrence`，但刷题几乎用不到。

## 六、遍历

```java
// 1. for-each（从头到尾，不出队）
for (int x : deque) { ... }

// 2. 边遍历边出队（BFS 常用）
while (!queue.isEmpty()) {
    int x = queue.poll();
    ...
}
```

## 七、其他 / 当栈用

```java
deque.size();     // 元素个数
deque.isEmpty();  // 是否为空
deque.clear();    // 清空

// Deque 当栈（栈顶在队头）
deque.push(1);    // 压栈 = addFirst
deque.pop();      // 弹栈 = removeFirst
deque.peek();     // 看栈顶 = peekFirst
```

## 八、复杂度对照

| 操作 | ArrayDeque | LinkedList |
| ---- | ---------- | ---------- |
| 头部 / 尾部 增删 | O(1) | O(1) |
| 看头 / 看尾 | O(1) | O(1) |
| 按下标访问 | 不支持 | O(n) |

> 结论：刷题用 **ArrayDeque**（更快、少 GC），不用 `LinkedList`。

## 九、刷题高频套路

### 1. BFS（层序遍历 / 最短路径）

```java
Queue<TreeNode> q = new ArrayDeque<>();
q.offer(root);
while (!q.isEmpty()) {
    int size = q.size();          // 当前层节点数
    for (int i = 0; i < size; i++) {
        TreeNode node = q.poll();
        if (node.left != null)  q.offer(node.left);
        if (node.right != null) q.offer(node.right);
    }
}
```

### 2. 单调队列（滑动窗口最大值）

```java
// 存下标，队头始终是窗口内最大值
Deque<Integer> dq = new ArrayDeque<>();
for (int i = 0; i < n; i++) {
    // 1. 队头滑出窗口
    if (!dq.isEmpty() && dq.peekFirst() <= i - k) dq.pollFirst();
    // 2. 队尾比当前小的都弹出（保持队头最大）
    while (!dq.isEmpty() && nums[dq.peekLast()] <= nums[i]) dq.pollLast();
    dq.offerLast(i);
    // 3. 窗口形成后记录答案
    if (i >= k - 1) ans[i - k + 1] = nums[dq.peekFirst()];
}
```

### 3. Deque 当栈（括号匹配 / 单调栈）

```java
Deque<Character> st = new ArrayDeque<>();
for (char c : s.toCharArray()) {
    if (c == '(') st.push(c);
    else {
        if (st.isEmpty()) return false;   // 右括号多了
        st.pop();
    }
}
return st.isEmpty();   // 左括号多了也返回 false
```

## 十、常见坑

1. **空队列时**：`poll` / `peek` 返回 `null`，`remove` / `element` 抛 `NoSuchElementException`。
2. **用 ArrayDeque 不用 LinkedList**：ArrayDeque 更快、内存更省。
3. **用 ArrayDeque 当栈，别用 Stack**：`Stack` 是线程安全的旧类，性能差，官方建议用 `Deque`。
4. **ArrayDeque 不能存 null**：`offer(null)` 会抛 `NullPointerException`（LinkedList 允许）。
5. **单调队列存下标不存值**：存下标才能判断队头是否滑出窗口。
