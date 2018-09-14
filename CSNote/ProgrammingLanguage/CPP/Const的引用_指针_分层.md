# Const的引用、指针、分层

## 基本定义

### 常量引用

1. 常见格式：

    ```c++
      const int ci = 1024;
      const int &r1 = ci;
    ```

2. 允许为一个常量引用绑定非常量的对象、字面值或表达式：

   ```c++
   int i = 42;
   const int &r1 = i;			// i是非常量对象
   const int &r2 = 42;			// 42是字面值
   const int &r3 = r1 * r2;	// 表达式
   
   r2 = 0;						// 错误，因为r2是一个常量引用,但我们可以直接修改i达到目的
   i = 0;						// 正确
   ```

3. 当一个常量引用被绑定到另外一中类型(绑定临时量)

   ```c++
   double dval = 3.14;
   const int &ri = dval;
   
   // 实际做了以下操作
   
   const int temp = dval;
   const int &ri = te
   
   // 注意ri不能为非常量，因为这样会绑定在一个临时变量中
   ```

### const和指针

1. 指向常量的指针

   #### 可另指针指向常量或者非常量。

   类似于常量引用，不能用于改变其所指向的值。

   ```c++
   const double pi = 3.14;
   double *ptr = &pi;
   const double *cptr = &pi;
   *cptr = 42;					// 错误，不能给指向常量的指针赋值
   ```

2. 常量指针

   把指针定为常量，一旦初始化完成，它的值（指向的地址）将不能再改变

   ```c++
   double pi = 3.14;
   double *const pi1 = &pi;		// pi1永远指向pi
   
   const double cpi  = 3.14;
   const double *const cpi1 = &cpi;// cpi1是一个指向常量对象的常量指针，既不能修改指向，
   								// 也不能修改其指向的值
   ```


### 顶层const(top-level const)

表示指针本身是个常量

### 底层const(low-level const)

表示指针所指的对象是一个常量

## 举例

```c++
int i = 0;
int *const p1 = &i;	// 常量指针，不可改变p1的指向，这是一个顶层const
const int ci = 42;	// 常量，不可改变c1的值，这是一个顶层const
const int *p2 = &ci; // 指向常指针，允许改变p2的指向，这是一个底层const
```

