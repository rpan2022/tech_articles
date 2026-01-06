# CPP 的常见数据结构

C++ 标准库（Standard Template Library, STL）提供了丰富且高效的数据结构（容器），适用于各种编程场景。以下是 C++ 中常见的数据结构分类及其典型用法：

## 一、序列式容器（Sequence Containers）

元素按线性顺序存储，支持按位置访问。

1. **`std::vector`**  
   - 动态数组，支持快速随机访问（O(1)）。
   - 尾部插入/删除高效（均摊 O(1)），中间插入/删除较慢（O(n)）。
   - 连续内存，缓存友好。
   - 示例：`std::vector<int> vec = {1, 2, 3};`

2. **`std::deque`（双端队列）**  
   - 支持首尾快速插入/删除（O(1)）。
   - 支持随机访问，但内存不连续。
   - 适用于需要频繁在两端操作的场景。

3. **`std::list`（双向链表）**  
   - 插入/删除任意位置为 O(1)（需迭代器定位）。
   - 不支持随机访问（只能顺序遍历）。
   - 内存开销较大（每个节点存储前后指针）。

4. **`std::forward_list`（单向链表）**  
   - 更节省内存（只有 next 指针）。
   - 只能单向遍历，功能比 `list` 少。

5. **`std::array`（C++11）**  
   - 固定大小的数组，封装 C 风格数组，提供 STL 接口。
   - 编译期大小确定，无动态分配，性能高。

## 二、关联式容器（Associative Containers）

基于红黑树实现（有序），自动排序，支持对数时间复杂度操作。

1. **`std::set` / `std::multiset`**  
   - 存储唯一键（`set`）或允许重复（`multiset`）。
   - 元素自动按升序（或自定义比较）排列。
   - 插入、查找、删除均为 O(log n)。

2. **`std::map` / `std::multimap`**  
   - 键值对容器，键唯一（`map`）或可重复（`multimap`）。
   - 常用于字典、配置表等。
   - 示例：`std::map<std::string, int> age = {{"Alice", 30}};`

## 三、无序关联容器（Unordered Associative Containers，C++11 引入）

基于哈希表实现，平均 O(1) 查找，但最坏 O(n)。

1. **`std::unordered_set` / `std::unordered_multiset`**  
   - 无序、基于哈希的集合。
   - 需提供哈希函数（对自定义类型需重载 `std::hash` 或提供函数对象）。

2. **`std::unordered_map` / `std::unordered_multimap`**  
   - 哈希表实现的键值对容器。
   - 比 `map` 更快的平均查找速度，但不保证顺序。

## 四、容器适配器（Container Adapters）

基于其他容器封装，提供特定接口。

1. **`std::stack`**  
   - 后进先出（LIFO），默认基于 `deque`。
   - 支持 `push`, `pop`, `top`。

2. **`std::queue`**  
   - 先进先出（FIFO），默认基于 `deque`。
   - 支持 `push`, `pop`, `front`, `back`。

3. **`std::priority_queue`**  
   - 默认为最大堆（可通过 `greater<T>` 改为最小堆）。
   - 基于 `vector` + 堆算法实现。
   - 支持 `push`, `pop`, `top`（最大/最小元素）。

## 五、字符串与特殊容器

- **`std::string` / `std::wstring`**  
  - 本质是 `std::basic_string<char>`，支持丰富操作（拼接、查找、替换等）。
  - 内部通常使用 SSO（Small String Optimization）优化小字符串性能。

- **`std::span`（C++20）**  
  - 非拥有式的连续内存视图，用于安全高效地传递数组或容器切片。

## 选择建议：

| 场景 | 推荐容器 |
|------|--------|
| 需要快速随机访问 | `vector`, `deque` |
| 频繁在头部/尾部插入 | `deque` |
| 频繁在中间插入/删除 | `list` |
| 需要自动排序/范围查询 | `set`, `map` |
| 只需快速查找/插入，不关心顺序 | `unordered_set`, `unordered_map` |
| 固定大小数组 | `array` |
| 栈/队列/优先队列语义 | `stack`, `queue`, `priority_queue` |
