# PriorityQueue 常用 API（优先队列 / 堆）

> 优先队列底层是**二叉堆**：每次 `poll` / `peek` 取出的都是"当前最小"（默认**小顶堆**）。
>
> 常用于 TopK、第 K 大/小、K 路归并、贪心取最值。
>
> AI生成+人工优化

## 〇、先记住

1. **默认小顶堆**：`peek` / `poll` 拿到的是堆里最小的元素。
2. 要"每次取最大"，创建时传 `Comparator.reverseOrder()`（大顶堆）。
3. 内部**不是有序结构**，只有 `peek` / `poll` 保证取最值，遍历顺序不保证。
4. 核心方法只有三个：`offer`（入堆）、`poll`（出堆顶）、`peek`（看堆顶）。

## 一、创建与初始化

```java
// 1. 默认小顶堆：每次 poll 出最小值
PriorityQueue<Integer> pq = new PriorityQueue<>();

// 2. 指定初始容量
PriorityQueue<Integer> pq2 = new PriorityQueue<>(16);

// 3. 大顶堆：每次 poll 出最大值（Integer 用 reverseOrder 最安全）
PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Comparator.reverseOrder());

// 4. 从已有集合初始化（按自然序建堆）
PriorityQueue<Integer> pq3 = new PriorityQueue<>(list);

// 5. 堆里放 int[]（没有自然序，只能手写 lambda）
PriorityQueue<int[]> pq4 = new PriorityQueue<>((a, b) -> a[0] - b[0]);   // 小顶堆：按 a[0] 升序，poll 出 a[0] 最小的
PriorityQueue<int[]> pq5 = new PriorityQueue<>((a, b) -> b[0] - a[0]);   // 大顶堆：按 a[0] 降序，poll 出 a[0] 最大的
// 想按其他下标比较，把 [0] 换成 [其他下标] 即可
```

## 二、增（入堆）

```java
pq.offer(3);    // 入堆，失败返回 false（推荐）
pq.add(3);      // 入堆，失败抛异常
```

## 三、删（出堆）

```java
int min = pq.poll();   // 取出并移除堆顶（最小/最大），空返回 null（推荐）
pq.remove();           // 取出并移除堆顶，空抛异常
pq.remove(5);          // 移除指定元素 5，O(n)，返回 boolean
pq.clear();            // 清空
```

## 四、查（看堆顶）

```java
int top = pq.peek();   // 只看堆顶（最小/最大），不出堆，空返回 null（推荐）
pq.element();          // 只看堆顶，空抛异常
pq.size();             // 元素个数
pq.isEmpty();          // 是否为空
pq.contains(5);        // 是否包含，O(n)
```

## 五、改

堆没有直接的"改"，两种做法：

```java
// 方式1：删旧的 + 加新的
pq.remove(old);
pq.offer(newVal);

// 方式2：直接 offer 一个新值（Dijkstra 更新距离时常用，旧的留着不管）
pq.offer(newVal);   // 旧的过期元素之后 poll 出来时跳过即可（延迟删除）
```

## 六、遍历

```java
// ⚠️ for-each 遍历顺序不保证有序！只有 peek / poll 才按堆序输出
for (int x : pq) { ... }

// 按序取出（会清空堆）
while (!pq.isEmpty()) {
    int x = pq.poll();   // 小顶堆：从小到大依次弹出
}
```

## 七、转换

```java
// PriorityQueue → 数组
Integer[] arr = pq.toArray(new Integer[0]);

// List → 堆
PriorityQueue<Integer> pq = new PriorityQueue<>(list);

// 堆 → 有序 List（poll 全部）
List<Integer> sorted = new ArrayList<>();
while (!pq.isEmpty()) sorted.add(pq.poll());
```

## 八、复杂度对照

| 操作 | 复杂度 |
| ---- | ------ |
| offer / add | O(log n) |
| poll | O(log n) |
| peek | O(1) |
| remove(Object) / contains | O(n) |
| 遍历 | O(n)（顺序不保证） |

## 九、刷题高频套路

### 1. 第 K 大 / 第 K 小（堆里只留 K 个）

**求第 K 大 → 维护大小为 K 的小顶堆**，堆顶就是第 K 大。

```java
// 215. 数组中的第K个最大元素
PriorityQueue<Integer> minHeap = new PriorityQueue<>();   // 小顶堆
for (int num : nums) {
    minHeap.offer(num);
    if (minHeap.size() > k) minHeap.poll();   // 超过 K 个就扔掉最小的
}
return minHeap.peek();
```

> 记忆口诀：**求最大（K 大）用小顶堆，求最小（K 小）用大顶堆。**
> 因为堆里只留 K 个时，要能方便地"踢掉最不想要的"，而堆顶正好是它。

### 2. 前 K 个高频元素（Map 计数 + 堆）

```java
// 347. 前K个高频元素
Map<Integer, Integer> cnt = new HashMap<>();
for (int x : nums) cnt.put(x, cnt.getOrDefault(x, 0) + 1);

// 小顶堆，按出现次数排：堆顶是频率最低的
PriorityQueue<Integer> minHeap =
        new PriorityQueue<>((a, b) -> cnt.get(a) - cnt.get(b));

for (int key : cnt.keySet()) {
    minHeap.offer(key);
    if (minHeap.size() > k) minHeap.poll();   // 踢掉频率最低的
}
// 堆里剩下的就是频率最高的 K 个
```

### 3. K 路归并（合并 K 个有序链表 / 数组）

```java
// 23. 合并K个升序链表（ListNode 示例）
PriorityQueue<ListNode> pq = new PriorityQueue<>(
        (a, b) -> a.val - b.val);
for (ListNode head : lists) {
    if (head != null) pq.offer(head);
}

ListNode dummy = new ListNode(0), tail = dummy;
while (!pq.isEmpty()) {
    ListNode node = pq.poll();
    tail.next = node;
    tail = node;
    if (node.next != null) pq.offer(node.next);   // 弹出一个补一个
}
return dummy.next;
```

### 4. 自定义对象进堆（实现 Comparable 或传 Comparator）

```java
// 方式1：构造时传比较器（最常用）
PriorityQueue<int[]> pq = new PriorityQueue<>(
        (a, b) -> a[1] - b[1]);   // 按区间右端点小的优先

// 方式2：类实现 Comparable
class Node implements Comparable<Node> {
    int val;
    public int compareTo(Node o) { return this.val - o.val; }
}
```

### 5. 两个堆求中位数（可选，进阶）

左半边**大顶堆** + 右半边**小顶堆**，两个堆大小相差不超过 1，中位数就是堆顶之一。

```java
// 295. 数据流的中位数：addNum 时先入 maxHeap 再平衡，或统一走"先入再分流"套路
PriorityQueue<Integer> left  = new PriorityQueue<>(Comparator.reverseOrder()); // 大顶堆，存较小的一半
PriorityQueue<Integer> right = new PriorityQueue<>();                          // 小顶堆，存较大的一半
```

## 十、常见坑

1. **默认是小顶堆**：要"每次取最大"，创建时必须传 `Comparator.reverseOrder()`。
2. **遍历不是有序的**：`for-each` / 迭代器拿到的不是排序结果，只有 `poll` / `peek` 按序。
3. **不能存 `null`**：`offer(null)` 抛 `NullPointerException`。
4. **自定义类型必须能比较**：不实现 `Comparable` 也没传 `Comparator` 的话，`offer` 时抛 `ClassCastException`。
5. **比较器写 `a - b` 最简洁（刷题常用）**：但极端值会溢出（如 `Integer.MAX_VALUE - (-1)` 会变成负数），担心溢出就换 `Integer.compare(a, b)`。
6. **`remove(Object)` / `contains` 是 O(n)**：堆的快速增删只对堆顶生效，删除中间元素很慢。
7. **入堆后别修改参与比较的字段**：堆不会自动调整，会破坏堆序（结果不正确）。
8. **`PriorityQueue` 不是线程安全**：刷题单线程无所谓；多线程用 `PriorityBlockingQueue`。
