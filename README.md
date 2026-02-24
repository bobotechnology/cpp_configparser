# cpp_configparser

一个轻量级、现代 C++17 的 INI 配置解析库，支持：

- 分区（section）与默认分区（`DEFAULT`）
- 键值读取与类型转换（字符串、整数、浮点、布尔）
- 可选项缺失值（`allow_no_value`）
- 多行值
- 注释与行内注释配置
- 插值（`Basic` / `Extended`）
- 严格模式（重复 section/option 检查）
- 序列化回 INI 文本

## 目录结构

```text
.
├── CMakeLists.txt
├── include/ini/
│   ├── parser.hpp
│   └── error.hpp
├── src/
│   └── parser.cpp
├── tests/
│   └── basic_test.cpp
└── cmake/
    └── ini_configparserConfig.cmake.in
```

## 构建

### 1) 构建库与测试

```bash
cmake -S . -B build
cmake --build build
ctest --test-dir build --output-on-failure
```

### 2) 安装

```bash
cmake -S . -B build -DCMAKE_INSTALL_PREFIX=/usr/local
cmake --build build --target install
```

## 在项目中使用

### 方式一：直接链接构建目标

在你的 CMake 项目中通过 `add_subdirectory` 引入后链接：

```cmake
target_link_libraries(your_target PRIVATE ini_configparser)
```

也可以使用别名目标：

```cmake
target_link_libraries(your_target PRIVATE ini::configparser)
```

### 方式二：通过安装后的包查找

```cmake
find_package(ini_configparser REQUIRED)
target_link_libraries(your_target PRIVATE ini::configparser)
```

## 快速示例

```cpp
#include "ini/parser.hpp"
#include <iostream>

int main() {
    ini::ParseOptions opt;
    opt.interpolation = ini::InterpolationMode::Basic;

    ini::Parser p(opt);
    p.read_string(R"ini(
[DEFAULT]
base = /tmp

[app]
name = demo
path = %(base)s/%(name)s
enabled = yes
count = 7
pi = 3.14
)ini");

    std::cout << p.get("app", "path") << "\n";   // /tmp/demo
    std::cout << p.get_int("app", "count") << "\n"; // 7
}
```

## API 概览

主要头文件：

- `include/ini/parser.hpp`
- `include/ini/error.hpp`

核心类型：

- `ini::Parser`
- `ini::ParseOptions`
- `ini::InterpolationMode`
- `ini::Error` / `ini::ParsingError`

## 运行测试

```bash
ctest --test-dir build --output-on-failure
```

测试示例位于 `tests/basic_test.cpp`。

## 许可证

本项目使用 MIT License，详见 [LICENSE](LICENSE)。
