# 复习面向对象的编程 -- C++

## 基础内容

我们从一个简单但现代的 C++ 类开始，涵盖以下面向对象核心要素：

- 封装（成员变量与成员函数）
- 构造函数（含默认、带参、委托）
- 默认成员函数（= default / = delete）
- const 正确性
- 移动语义（C++11 起，但现代 C++ 必备）
- 使用 `std::string_view`（C++17）提升效率
- 使用 `explicit` 避免隐式转换

我们从一个基础类 `class Person` 开始。

``` cpp
// person.hpp
#pragma once
#include <string>
#include <string_view>
#include <ostream>

class Person {
public:
    // 构造函数
    Person(std::string_view name, int age = 0)
        : name_(name), age_(age) {}

    // 拷贝与移动（让编译器生成默认）
    Person(const Person&) = default;
    Person(Person&&) = default;
    Person& operator=(const Person&) = default;
    Person& operator=(Person&&) = default;

    // 禁止隐式转换为 string（更安全）
    // 如果需要字符串表示，显式提供 getName()
    std::string_view getName() const noexcept { return name_; }
    int getAge() const noexcept { return age_; }

    // 修改器
    void setAge(int age) noexcept { age_ = age; }

    // 为输出提供支持
    friend std::ostream& operator<<(std::ostream& os, const Person& p) {
        os << "Person{name: \"" << p.name_ << "\", age: " << p.age_ << "}";
        return os;
    }

private:
    std::string name_;
    int age_ = 0;
};
```

## C++ 中的继承（Inheritance）

``` cpp
class Base {
    // 成员
};

class Derived : <访问说明符> Base {
    // 派生类成员
};

```

成员访问控制：`private` / `protected` / `public`

先看 **基类内部** 的访问规则：

| 访问说明符 | 类内可访问 | 派生类可访问 | 类外可访问 |
|------------|------------|---------------|-------------|
| `private`   | ✅         | ❌            | ❌          |
| `protected` | ✅         | ✅（仅直接/间接派生） | ❌ |
| `public`    | ✅         | ✅            | ✅          |

**类的内部**访问规则

| 继承方式      | 基类 `public` 成员 | 基类 `protected` 成员 | 基类 `private` 成员 | 适用场景 |
|---------------|--------------------|------------------------|----------------------|--------|
| `public`      | → `public`         | → `protected`          | 不可见               | **标准 OOP，多态** |
| `protected`   | → `protected`      | → `protected`          | 不可见               | 限制接口暴露 |
| `private`     | → `private`        | → `private`            | 不可见               | 实现复用（慎用） |

## C++ 中的多态（Polymorphism）

- 虚函数（virtual）
- 纯虚函数（pure virtual）与抽象类
- 动态绑定机制 dynamic binding
- 现代 C++ 实践建议（含 C++11/14/17/20 要点，适用于 C++26 前沿开发）

多态（Polymorphism） 指的是“同一个接口，不同实现”。
在 C++ 中，通过 基类指针/引用调用派生类的重写方法 实现运行时多态（动态分发）。

> ✅ 前提：必须通过 指针或引用 调用 virtual 函数。

### 基础内容

``` cpp

#include <iostream>
#include <memory>

class Vehicle {
public:
    virtual ~Vehicle() = default;  // ✅ 虚析构函数（关键！）

    virtual void drive() const {
        std::cout << "Vehicle is driving.\n";
    }
};

class Car : public Vehicle {
public:
    void drive() const override {  // override 表示重写（C++11）
        std::cout << "Car is driving smoothly.\n";
    }
};

class Truck : public Vehicle {
public:
    void drive() const override {
        std::cout << "Truck is driving heavily.\n";
    }
};

int main() {
    std::unique_ptr<Vehicle> v1 = std::make_unique<Car>();
    std::unique_ptr<Vehicle> v2 = std::make_unique<Truck>();

    v1->drive();  // 输出：Car is driving smoothly.
    v2->drive();  // 输出：Truck is driving heavily.

    return 0;
}

```

### 纯虚函数（Pure Virtual Function）与抽象类

派生类必须实现所有纯虚函数（否则仍是抽象类）

``` cpp

class Shape {
public:
    virtual ~Shape() = default;

    // 纯虚函数 → Shape 成为抽象类
    virtual double area() const = 0;
    // 纯虚函数也可以有实现（但不常见）
    virtual void draw() const = 0;
};

void Shape::draw() const {
    std::cout << "Drawing a shape.\n";
}

// ------------

class Circle : public Shape {
    double radius_;
public:
    Circle(double r) : radius_(r) {}

    double area() const override {
        return 3.14159 * radius_ * radius_;
    }

    void draw() const override {
        Shape::draw(); // 可选：调用基类默认实现
        std::cout << "It's a circle.\n";
    }
};

```

> the end
