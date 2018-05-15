# Java 编程思想
## 第一章 对象导论
### 1.1 抽象过程
面向对象程序设计方式：
1. 万物皆对象
2. 程序是对象的集合，他们通过发送消息来告知彼此所有做的
3. 每个对象都有自己的有其他对象所构成的存储 
4. 每个对象都拥有其类型
5. 某一特定类型的所有对象都可以接受同样消息

简洁描述：对象具有状态、行为和标识。

### 1.2 每个对象都有一个接口
1、在类型中，每一个可能的请求都有一个方法与之相关联，当向对象发送请求时，与之相关联的方法就会被调用
![类图与接口](https://github.com/lyk2655/lyk2655.github.io/raw/master/img/1.2_1.png)
### 1.3 每个对象都提供服务
将对象想象为“服务提供者”

### 1.4 被隐藏的具体实现
- 类创建者：构建类，只向客户端程序员提供必须的部分，而隐藏其他部分；
- 客户端程序员：只能访问类暴露的部分。

访问控制：
 
访问修饰符 |	本类|	同包	|子类	|其他|
private	 | √   | x    | x  | x|
默认	    |√    | √    | x  |x|
protected|√	   |√	  |√   |x|
public	|√    |√	   |√ 	|√	|

### 1.5 复用具体实现
最简单的服用某个类的方式就是直接使用该类的一个对象，还有就是可以将那个类的一个对象置于某个新的类中。
```
//直接使用
User user = new User();
user....
//新的类中的成员
public class Student{
String name；
User user；
}
```

### 1.6 继承
![继承](https://github.com/lyk2655/lyk2655.github.io/raw/master/img/1.6_1.png)

类与基类具有相同的类型：所有可以发送给基类对象的消息同时也可以发送给派生类对象。
使基类与派生类产生差异的两种方法：
1. 直接在派生类中添加新方法
2. 方法覆盖，改变派生类中继承基类现有方法的行为。
![添加新方法](https://github.com/lyk2655/lyk2655.github.io/raw/master/img/1.6_2.png)
![方法覆盖](https://github.com/lyk2655/lyk2655.github.io/raw/master/img/1.6_3.png)

### 伴随多态的可互换对象
编译器确保被调用的方法的存在，并对调用参数和返回值执行类型检查（无法提供此类保证的语言被称为弱语言），但是并不知道将被执行的确切代码。
当向对象发送消息时，被调用的代码直到运行时才能确定。
Java中动态绑定是默认行为，不需要添加额外关键字来实现。（C++使用virtual关键字）
![多态](https://github.com/lyk2655/lyk2655.github.io/raw/master/img/1.7_1.png)
```
void doSomething(Shape shape){
shape.draw();
....
}
```

```
//调用doSomething
Circle circle = new Circle();
Square square = new Square();
doSomething(circle);
doSomething(square);
```

