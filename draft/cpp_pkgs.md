# 一些常用的cpp pkg

1. [fmtlib/fmt](https://github.com/fmtlib/fmt): 
   
   fmtlib/fmt（通常简称为 {fmt}）是一个现代化、高性能、类型安全的 C++ 格式化库，旨在替代传统的 C 风格 printf 和 C++ 的 iostreams。它已成为 C++20 标准中 std::format 和 C++23 中 std::print 的参考实现，并被广泛用于工业级项目（如 PyTorch、Envoy、spdlog、Windows Terminal 等）。

1. [nlohmann/json](https://github.com/nlohmann/json)
   
   nlohmann/json 是一个用现代 C++（C++11 起）编写的、仅头文件（header-only）的 JSON 库，由 Niels Lohmann 开发并维护。它以直观的语法、极简的集成方式和高质量的代码实现而广受欢迎，被广泛用于各种 C++ 项目中。nlohmann/json 是 C++ 生态中最易用、最可靠的 JSON 库之一，特别适合希望快速集成 JSON 支持而不引入复杂构建依赖的开发者。
   

3. [Tencent/rapidjson](https://github.com/Tencent/rapidjson)
   
    是由腾讯（Tencent）和 Milo Yip 开发并开源的一个 高性能、轻量级、符合标准的 C++ JSON 库。它被广泛用于对性能和内存敏感的项目中，例如游戏引擎、嵌入式系统、高频服务等。
    这个库提供json schema 验证

4. [nholthaus/units](https://github.com/nholthaus/units)
   
   一个基于 C++14 构建的、仅包含头文件的编译时量纲分析和单位转换库，没有任何依赖项。`nholthaus/units` 是一个纯头文件、零依赖、高性能 的 C++ 库，用于在编译期实现 物理量（带单位的数值）的安全计算。它基于 编译期维度分析（dimensional analysis），可防止单位不匹配导致的错误（如将米加到秒上），同时支持广泛的单位制（SI、英制、天文、信息等），并保证 零运行时开销。

5. [Eigen](https://libeigen.gitlab.io/)

   Eigen 是一个 高性能、纯头文件、开源 的 C++ 模板库，专为 线性代数、矩阵运算、几何变换、数值分析 等科学计算任务设计。它广泛应用于 计算机视觉、机器人、机器学习、物理仿真、图形学、自动驾驶等领域。

