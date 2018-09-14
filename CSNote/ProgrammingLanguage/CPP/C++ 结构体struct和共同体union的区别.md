# C++ 结构体struct和共同体union的区别

## 基本概念

Struct: 把不同数据类型的数据组合成一个整体

union: 定义一组互相排斥的变量，在任何时刻只能有一个成员被定义

## 使用

1. Struct

   ```c++
   // 定义
   struct perks
   {
       int key_number;
       char car[12];
   } mr_smith;
   
   // 初始化
   mr_smith = {
       7,
       "BMW"
   };
   
   // 使用
   std::cout << mr_smith.key_number << std::endl;
   ```

2. union

   ```c++
   #include <iostream>
   
   int main() {
       // 定义
       union id {
           long id_num;
           char* id_char;
       } id_val;
   
       // 初始化
       // id_num与id_char所占用的地址相同，因此同一时刻只能存在一种情况
       id_val.id_num = 1;
       id_val.id_char = "tom";
   
       // 使用
       // 在使用union对象时，我们必须知道union对象当前存储的是什么类型的值
       std::cout << id_val.id_char << std::endl; // 正确
       std::cout << id_val.id_num << std::endl;  // id_val.id_num未定义
       return 0;
   }
   
   /* OUT:
   
   tom
   4340756252
   
   */
   ```

## 区别

结构体：每个成员都有独立的地址。sizeof(struct)等于内存对齐后所有成员长度的和。

联合：当某个成员存入数据后，其他成员的数据就会失去。因此能够达到节省内存的目的。sizeof(union)相当于最长的数据成员长度。