# Conan 包管理

Conan 是一个开源的 C/C++ **包管理器**（Package Manager），专为现代 C++ 开发设计，旨在解决 C/C++ 生态中依赖管理复杂、跨平台构建困难等问题。它由 JFrog 公司维护，广泛用于工业级 C++ 项目，尤其适合需要管理大量第三方库、多平台（Windows/macOS/Linux）、多编译器（MSVC、GCC、Clang）和多 ABI（如 Debug/Release、C++ 标准版本）的场景。

## 核心特性

1. **跨平台 & 多配置支持**  
   Conan 支持 Windows、Linux、macOS、嵌入式系统等，可同时管理多个编译器、架构、标准库（如 libc++ vs libstdc++）、构建类型（Debug/Release）等配置，通过 **settings** 和 **options** 精细控制包变体。

2. **中心化或去中心化仓库**  
   - 默认使用 [ConanCenter](https://conan.io/center/)（社区维护的中央仓库，包含数千个常用 C++ 库如 Boost、OpenSSL、fmt、spdlog 等）。
   - 也支持私有仓库（如 JFrog Artifactory、Conan Server），适合企业内网部署。

3. **声明式依赖管理**  
   通过 `conanfile.txt`（简单场景）或 `conanfile.py`（高级定制）声明依赖，例如：
   ```txt
   [requires]
   fmt/10.2.1
   spdlog/1.13.0

   [generators]
   CMakeDeps
   CMakeToolchain
   ```

4. **与构建系统深度集成**  
   支持 CMake、Meson、MSBuild、Makefile 等，通过 **generators** 自动生成集成文件（如 `conan install` 会生成 `conan_toolchain.cmake` 和 `*-config.cmake`），使 CMake 能直接 `find_package(fmt)`。

5. **二进制缓存 & 复用**  
   Conan 默认优先下载预编译的二进制包（binary package），若不存在则根据 recipe 从源码构建并缓存，极大加速 CI/CD 流程。

6. **可定制的包配方（Recipe）**  
   每个包由 `conanfile.py` 定义如何获取源码、配置、构建、打包，支持条件逻辑、钩子、测试等，适合复杂构建流程。

## 与 vcpkg、Hunter 等对比的优势

- **更灵活的配置模型**：Conan 的 settings/options 机制能精确表达“同一个库在不同平台/编译器下的不同二进制变体”。
- **企业友好**：成熟的私有仓库、权限控制、CI 集成。
- **活跃生态**：ConanCenter 持续更新，社区贡献活跃。
- **CMake 集成现代化**：通过 `CMakeDeps` + `CMakeToolchain` 实现“透明”依赖引入，不污染全局环境。

## 适用场景

- 大型 C++ 项目依赖众多第三方库。
- 需要支持多平台、多编译器的 CI/CD。
- 企业内部需要统一管理 C++ 依赖版本和二进制分发。
- 想避免“手动下载 + 编译 + 安装”第三方库的繁琐流程。


## 安装

``` bash
pipx install conan
```

我们创建一个默认profile:

``` bash
# <input>: conan default profile
$ conan profile detect --force

# <output>
detect_api: Found cc=gcc-11.4.0
detect_api: gcc>=5, using the major as version
detect_api: gcc C++ standard library: libstdc++11

Detected profile:
[settings]
arch=x86_64
build_type=Release
compiler=gcc
compiler.cppstd=gnu17
compiler.libcxx=libstdc++11
compiler.version=11
os=Linux

WARN: This profile is a guess of your environment, please check it.
WARN: The output of this command is not guaranteed to be stable and can change in future Conan versions.
WARN: Use your own profile files for stability.
Saving detected profile to /home/x450jf/.conan2/profiles/default

# <input>
$ conan profile show --profile default

# <output>
... same as above ...

```

### 定制一个 profile

添加一个新的配置`touch /home/x450jf/.conan2/profiles/gcc-11-ninja` 

内容如下

``` txt
[settings]
os=Linux
arch=x86_64
compiler=gcc
compiler.version=11
compiler.libcxx=libstdc++11
compiler.cppstd=gnu20
build_type=Release

[conf]
tools.cmake.cmaketoolchain:extra_variables={'CMAKE_CXX_STANDARD': '20'}
tools.cmake.cmaketoolchain:generator=Ninja

```

验证方式 `conan profile show --profile gcc-11-ninja`


## create a project

`mkdir cpp_ws && cd cpp_ws`
`conan new cmake_exe -d name=my_project -d version=0.1`
`conan new bazel_exe -d name=my_project -d version=0.1`

setup project
`conan install . --profile=gcc-11-ninja --output-folder=build --build=missing`
