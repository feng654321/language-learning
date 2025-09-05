# c++ const 使用场景

## 总结
1. 非类定义中使用:
    - 顶层const：修饰对象本身不可变
    - 底层const: 修饰对象所指向的内容不可变(指针或引用)
    - 引用一定是底层


## const **修饰常量**(这是最基本的)
- 语法： const 数据类型 常量名 = 常量值；
- 例子： const int day = 10;

## const **修饰指针**
1. const 修饰指针 --- 常量指针
- 语法： const 数据类型 * 常量名；
2. const 修饰常量 --- 指针常量
- 语法： 数据类型 *const 变量名；
3. const 即修饰指针也修饰常量
- 语法： const 数据类型 *const 变量名；
4. 例子：
```
int a = 10;
int b = 20;
const int *ptr1 = &a; // 指针指向的值不可以更改
ptr1 = &b;
//*ptr = 10;//错误
int *const ptr2 = &a; //指针不可以改，指向的值可以改
//ptr2 = &b; //错误
*ptr2 = 100;//正确

const int * const ptr3 = &a;
//ptr3 = &b;//错误
//*ptr3 = 10;//错误
```
## const 修饰函数形参
- 防止形参改变实参
    ```
    void printvalue(const int& v){
        cout << v <<endle;
    }
    void printvalue(const int *pv){
        cout << *pv << endle;
    }
    ```
## const 修饰函数返回值
- 如果返回的是基本类型，没有意义，因为返回值是一个副本
- 如果返回的是复合引用或对象，可以防止对象或者复合引用被修改.特别是返回类的成员变量
    ```
    const myclass& getclass() const{
        return myobject;
    }

    class myclass{
    private:
        int value;
    public:
        const int& getvalue() const{
            return value;
        }
    }
    //使用时
    myclass obj;
    int a = getvalue;
    //obj.getvalue = 10;//错误 
    ```
## const 修饰成员函数： 常函数
- 成员函数后面加const
- 常函数不可以修改成员
- 成员属性声明时加关键字mutable 后，在常函数中依然可以修改
- 语法： 返回值类型 函数名 const {}

## const 修饰类对象：常对象
- 声明对象前加const 称该对象为常对象
- 常对象只能调用常函数
- 语法： const 类名 对象名;
```
class person{};
const person myperson;
```

## const 修饰类成员
- 只能在构造函数后的`参数初始化列表`中初始化
- 将const 变量同时声明为`static` 类型进行初始化

## const 和constexpr
- constexpr 用于声明表达式为编译时常量，意味着它的值必须在编译时就已知
- constexpr **常量**可以用在需要编译时常量表达式的上下文，如数组大小，整数模板
- constexpr **函数**在编译时对其输入进行计算，只要所有输入也都是编译时已知的常量
- constexpr 要求其初始化表达式必须是一个编译时常量表达式

### 主要区别
- **编译时** VS **运行时**： constexpr 确保变量或函数的值能在编译时被确定，const 可以在运行时
- **使用场景**： constexpr 适用于需要在编译时进行计算的场景，比如模板参数或者数组大小。const更适用于程序运行中不需要修改的值
```
constexpr int max_arry_size = 100; 
constexpr double calarea(double radius){
    reture 3.14*radius*radius; //编译时计算面积
}
```

## 使用const 成员的代价与陷阱
- `牺牲了赋值能力` 编译器不会为它自动生成默认的拷贝赋值运算符
- `牺牲了移动语义` 编译器不会为它自动生成默认的移动构造函数(T(T&&))和移动赋值运算符(operator=(T&&))
- `降低灵活性` 无法交换和重置

## 推荐使用场景
const成员变量的使用应该被严格限制在那些对象的核心身份与该成员终生绑定，且对象被设计为创建后完全不可变的场景。
- `值对象 (Value Objects)`: 这类对象完全由其数据定义，例如一个二维坐标点 Point(const int x, const int y)，或一个颜色 Color(const short r, const short g, const short b)。这些对象一旦创建，其值就有意义，改变任何一部分都会使其成为一个“不同”的对象。它们通常不需要赋值，而是通过创建新对象来表示新值。
- `真正的生命周期不变量`: 某个ID或句柄，它是在构造时从外部获取的，并且定义了对象的唯一标识，在逻辑上绝不应该改变。例如，一个包装了数据库记录的对象，其const long recordId是合理的。

## 更好的替代方案
- `私有成员 + 公有const访问器 (Private Member + Public const Accessor)`
