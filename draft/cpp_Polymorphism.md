# 复习一下关于多态的内容，特别是virtual 和 pure virtual 的内容

我们来系统复习 C++ 中的多态（Polymorphism），重点讲解：

- 虚函数（virtual）
- 纯虚函数（pure virtual）与抽象类
- 动态绑定机制
- 现代 C++ 实践建议（含 C++11/14/17/20 要点，适用于 C++26 前沿开发）
  
## 一、什么是多态？
多态（Polymorphism） 指的是“同一个接口，不同实现”。
在 C++ 中，通过 基类指针/引用调用派生类的重写方法 实现运行时多态（动态分发）。

> ✅ 前提：必须通过 指针或引用 调用 virtual 函数。

## 二、virtual 关键字：开启多态

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
## 三、纯虚函数（Pure Virtual Function）与抽象类

1. 什么是纯虚函数？
用 `= 0` 声明的虚函数，没有实现（或可有默认实现），强制派生类实现。
2. 抽象类（Abstract Class）
包含至少一个纯虚函数的类。
不能实例化（Shape s; ❌ 编译错误）。
只能作为基类被继承。
3. 派生类必须实现所有纯虚函数（否则仍是抽象类）

```cpp
class Shape {
public:
    virtual ~Shape() = default;

    // 纯虚函数 → Shape 成为抽象类
    virtual double area() const = 0;
    //      pure virtual ---------^

    // 纯虚函数也可以有实现（但不常见）
    virtual void draw() const = 0;
};

void Shape::draw() const {
    std::cout << "Drawing a shape.\n";
}
```
