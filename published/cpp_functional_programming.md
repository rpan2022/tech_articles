# 介绍一下c++中的函数式编程，使用c++ 26 ranges, views

C++ 从 C++11 开始逐步引入函数式编程（Functional Programming, FP）的特性，而 C++20 引入的 **Ranges 库**（`<ranges>`）以及 C++26 的进一步增强，使 C++ 在表达函数式风格的数据处理管线（pipeline）方面变得更强大、简洁和安全。虽然 C++ 并非纯函数式语言，但借助现代特性，我们可以写出接近函数式风格的代码。

以下将基于 **C++26（截至 2026 年 1 月，C++26 标准仍在最终定稿阶段，但大部分 Ranges/Views 特性已在 C++20 引入，并在 C++23/26 中扩展）** 介绍 C++ 中的函数式编程实践，重点聚焦于 **ranges、views 和组合式算法**。

---

### 1. 核心思想：不可变性 + 组合 + 声明式

函数式编程强调：
- **避免副作用**（side-effect-free transformations）
- **不可变数据**（通过视图而非修改原数据）
- **高阶函数**（函数作为参数或返回值）
- **组合（composition）**：将简单操作串联成复杂逻辑

C++ 的 `std::views` 正是为此设计：**惰性、非拥有、可组合的 range 适配器**。

---

### 2. 基础：Range 与 View

- **Range**：任何可迭代的对象（如 `vector`, `array`, 或满足 `begin()/end()` 的类型）。
- **View**：一种轻量级、非拥有的 Range，支持惰性求值（lazy evaluation），不复制数据。

C++20 引入了：
```cpp
#include <ranges>
#include <vector>
#include <iostream>
```

---

### 3. 常用 Views（C++20 起，C++26 更完善）

| View | 作用 |
|------|------|
| `views::filter(pred)` | 过滤元素 |
| `views::transform(func)` | 映射（map） |
| `views::take(n)` | 取前 n 个 |
| `views::drop(n)` | 跳过前 n 个 |
| `views::reverse` | 反转 |
| `views::join` | 扁平化嵌套 range（如 vector of vectors）|
| `views::enumerate` | C++23/26 新增：提供索引 + 元素（类似 Python 的 `enumerate`）|

> ⚠️ 注意：C++26 正式加入了 `std::views::enumerate`（此前需自定义或用第三方库）。

---

### 4. 函数式风格示例（C++26 风格）

#### 示例 1：过滤偶数、平方、取前 5 个
```cpp
#include <ranges>
#include <vector>
#include <iostream>

int main() {
    std::vector<int> nums = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};

    auto result = nums
        | std::views::filter([](int x) { return x % 2 == 0; })   // 偶数
        | std::views::transform([](int x) { return x * x; })     // 平方
        | std::views::take(5);                                  // 取前5

    for (int x : result) {
        std::cout << x << " ";  // 输出: 4 16 36 64 100
    }
}
```

这完全符合函数式风格：
- 无显式循环（声明式）
- 无中间容器（惰性求值）
- 无修改原始数据

---

#### 示例 2：使用 `views::enumerate`（C++23/26）
```cpp
std::vector<std::string> words = {"apple", "banana", "cherry"};

auto indexed = words
    | std::views::enumerate
    | std::views::filter([](auto&& pair) {
        auto [i, word] = pair;
        return word.size() > 5;
    });

for (auto [i, word] : indexed) {
    std::cout << i << ": " << word << "\n";
}
// 输出:
// 1: banana
// 2: cherry
```

---

#### 示例 3：扁平化 + 转换（类似 flatMap）
```cpp
std::vector<std::vector<int>> matrix = {{1,2}, {3,4,5}, {6}};

auto flattened = matrix
    | std::views::join
    | std::views::transform([](int x) { return x * 10; });

// 结果: 10, 20, 30, 40, 50, 60
```

---

### 5. 与算法结合（C++20+）

传统 `<algorithm>` 需传迭代器，现在可直接作用于 range：

```cpp
#include <algorithm>

auto evens = nums | std::views::filter([](int x) { return x % 2 == 0; });

// 直接对 view 使用算法
auto sum = std::accumulate(evens.begin(), evens.end(), 0);

// 或更 FP 风格（C++26 可能支持 range 版 accumulate，目前需手动）
```

> C++26 正在推进 **range-based algorithms**（如 `std::ranges::accumulate`），但截至 2026 年初，部分实现仍在完善中。GCC 13+/Clang 16+ 已支持较多特性。

---

### 6. 自定义 View（高级）

可通过 `std::ranges::view_interface` 或组合现有 view 创建 DSL：

```cpp
auto square_evens = [](const auto& rng) {
    return rng
        | std::views::filter([](int x) { return x % 2 == 0; })
        | std::views::transform([](int x) { return x * x; });
};

auto result = my_vec | square_evens;
```

---

### 7. 优势与注意事项

✅ **优势**：
- 代码更简洁、可读（接近 Python/JS 的链式处理）
- 惰性求值，避免临时容器
- 组合性强，易于复用
- 编译期优化（通常零成本抽象）

⚠️ **注意**：
- View 是“窗口”，底层数据必须存活
- 不能对临时 range 创建持久 view（悬空）
- 调试时可能堆栈较深（但现代编译器已优化）

---

### 总结

C++20/23/26 通过 **Ranges + Views** 极大地提升了函数式编程能力。虽然 C++ 仍以命令式为主，但用 `views` 构建数据处理管线已成为现代 C++ 的推荐范式，特别适合：
- 数据清洗
- 日志处理
- 数值计算流水线
- 响应式编程雏形

建议在支持 C++20 或更高标准的编译器（如 GCC 11+, Clang 13+, MSVC 16.11+）中尝试。记得添加 `--std=c++26`。

如需进一步探索，可研究：
- `std::ranges::ref_view` / `owning_view`
- C++26 新增的 `views::cartesian_product`
- 与 `std::execution` 并行策略结合

> The end
