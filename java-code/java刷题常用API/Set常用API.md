# Set 常用 API

> Set 用于**去重**和**判存在**。
>
> 三个实现：`HashSet`（无序，最常用）、`LinkedHashSet`（保持插入顺序）、`TreeSet`（元素升序）。
>
> AI生成+人工优化

## 一、创建与初始化

```java
Set<Integer> set = new HashSet<>();              // 最常用，无序
Set<Integer> set2 = new LinkedHashSet<>();       // 保持插入顺序
Set<Integer> set3 = new TreeSet<>();             // 元素升序排列

// 从数组 / List 直接初始化（顺带去重）
Set<Integer> s = new HashSet<>(Arrays.asList(1, 2, 2, 3));  // {1, 2, 3}
```

## 二、增

```java
boolean added = set.add(1);    // 返回 true：新加进去
boolean added2 = set.add(1);   // 已存在，返回 false（不覆盖）
```

> `add` 的返回值是**判重神器**：`if (set.add(x))` → x 是第一次出现。

## 三、删

```java
set.remove(1);    // 删除元素，返回 boolean（删成功 true）
set.clear();      // 清空
```

## 四、查

```java
set.contains(x);   // 是否存在，O(1)（HashSet）
set.size();        // 元素个数
set.isEmpty();     // 是否为空
```

## 五、改

Set 没有"改"，也不能按下标访问（没有 `get`/`set`）。要改就 `remove` 旧的再 `add` 新的。

## 六、遍历

```java
for (int x : set) { ... }       // 注意：HashSet 顺序不保证
set.forEach(x -> { ... });
```

## 七、转换

```java
// 数组 → Set（去重）
Set<Integer> set = new HashSet<>(Arrays.asList(arr));

// Set → 数组
Integer[] arr = set.toArray(new Integer[0]);

// Set → List
List<Integer> list = new ArrayList<>(set);
```

## 八、集合运算（交 / 并 / 差，Set 的核心用法）

```java
Set<Integer> a = new HashSet<>(Arrays.asList(1, 2, 3));
Set<Integer> b = new HashSet<>(Arrays.asList(2, 3, 4));

Set<Integer> union = new HashSet<>(a); union.addAll(b);     // 并集 a∪b = {1,2,3,4}
Set<Integer> inter = new HashSet<>(a); inter.retainAll(b);  // 交集 a∩b = {2,3}
Set<Integer> diff  = new HashSet<>(a); diff.removeAll(b);   // 差集 a-b = {1}
```

> 注意 `addAll`/`retainAll`/`removeAll` 会**原地修改**调用它的那个 Set，所以先 `new HashSet<>(a)` 拷贝一份，避免改坏原集合。

## 九、复杂度对照

| 操作 | HashSet | TreeSet |
| ---- | ------- | ------- |
| add / remove / contains | O(1) | O(log n) |
| 遍历 | O(n) | O(n) |
| 是否有序 | 无序 | 升序 |

## 十、刷题高频套路

### 1. 去重

```java
Set<Integer> seen = new HashSet<>(list);
```

### 2. 判存在（两数之和、找重复元素）

```java
Set<Integer> set = new HashSet<>();
for (int x : nums) {
    if (set.contains(target - x)) { ... }   // 找到了
    set.add(x);
}
```

### 3. 用 add 返回值判"第一次出现"

```java
if (set.add(x)) { /* x 第一次出现 */ } else { /* 已重复 */ }
```

## 十一、常见坑

1. **HashSet 无序**：遍历顺序不稳定，要稳定顺序用 `LinkedHashSet`，要升序用 `TreeSet`。
2. **`add` 返回 false** 表示元素已存在（不是报错）。
3. **自定义对象当元素**：必须重写 `hashCode()` 和 `equals()`，否则去重失效。
4. **TreeSet 元素要能比较**：放入的对象需实现 `Comparable`，或用带 `Comparator` 的构造。
5. **Set 没有下标**：不能 `get(i)`、`set(i, e)`。
