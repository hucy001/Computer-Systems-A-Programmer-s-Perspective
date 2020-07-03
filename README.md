# 可执行程序的符号表 ELF格式

符号表.symtab的每个连接符号包含两个属性：作用域和存放位置

#### 符号的作用域
11. 文件作用域 local
12. 函数作用域 local
13. 全局作用域 global

#### 常用的段
1. text ： 函数
2. data ： 初始化值不为0的全局或静态变量
3. bbs  ： 初始化值为0或者未初始化的全局或静态变量
4. rodata ： 所有的常量


#### 代码
(···)  
include "stdio.h"  

ifdef __cplusplus  
extern "C" {  
endif  

const double l_rodata=0; // 文件作用域 rodata段  
double g_bbs; // 全局作用域 bbs段  
const static double l_rodata_s=1.0; // 文件作用域 rodata段  
static double l_bbs; // 文件作用域 bbs段  

double g_data=1.0; // 全局作用域 data段
static double l_data=2.0; // 文件作用域 data段

// 全局作用域 text段  
void f_test(int)  
{  
  static int l_f_bbs; // 函数作用域 bbs段  
  static int l_f_bbs_0=0; // 函数作用域 bbs段  
  static int l_f_data=20; // 函数作用域 data段  
}  

// 文件作用域 text段  
static void g_test(double)  
{  
}  

// 全局作用域 text段  
int main()  
{  
  static int l_bbs; // 函数作用域 bbs段  
  const static int l_rodata= 11; // 函数作用域 rodata段  

  f_test(1);  
  g_test(1.9);  

  printf("hello world\n");  
}  
(···)

