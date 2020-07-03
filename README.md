# 可执行程序的符号表 ELF格式

符号表.symtab的每个链接符号包含两个属性：作用域和存放位置

#### 符号的作用域
1. 文件作用域 local
2. 函数作用域 local
3. 全局作用域 global

#### 常用的段
1. text ： 函数
2. data ： 初始化值不为0的全局或静态变量
3. bbs  ： 初始化值为0或者未初始化的全局或静态变量
4. rodata ： 所有的常量

#### 示例代码
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

#### 使用readelf工具查看符号表
1. 编译可执行文件: gcc -o test test.cpp
2. 查看段信息： readelf -S test
(···)
Section Headers:
  [Nr] Name              Type             Address           Offset
       Size              EntSize          Flags  Link  Info  Align
  [13] .text             PROGBITS         0000000000400440  00000440
       00000000000001b2  0000000000000000  AX       0     0     16
  [15] .rodata           PROGBITS         0000000000400600  00000600
       0000000000000034  0000000000000000   A       0     0     8
  [24] .data             PROGBITS         0000000000601030  00001030
       000000000000001c  0000000000000000  WA       0     0     8
  [25] .bss              NOBITS           0000000000601050  0000104c
       0000000000000028  0000000000000000  WA       0     0     8
  [26] .comment          PROGBITS         0000000000000000  0000104c
       000000000000002d  0000000000000001  MS       0     0     1
  [27] .symtab           SYMTAB           0000000000000000  00001080
       0000000000000720  0000000000000018          28    56     8
(···)
3. 查看变量信息: readelf -s test|grep "OBJECT"
(···)
    28: 0000000000600e20     0 OBJECT  LOCAL  DEFAULT   20 __JCR_LIST__
    32: 0000000000601050     1 OBJECT  LOCAL  DEFAULT   25 completed.6355
    33: 0000000000600e18     0 OBJECT  LOCAL  DEFAULT   19 __do_global_dtors_aux_fin
    35: 0000000000600e10     0 OBJECT  LOCAL  DEFAULT   18 __frame_dummy_init_array_
    37: 0000000000601060     8 OBJECT  LOCAL  DEFAULT   25 _ZL5l_bbs
    38: 0000000000601040     8 OBJECT  LOCAL  DEFAULT   24 _ZL6l_data
    40: 0000000000400620     8 OBJECT  LOCAL  DEFAULT   15 _ZL8l_rodata
    41: 0000000000400628     8 OBJECT  LOCAL  DEFAULT   15 _ZL10l_rodata_s
    42: 0000000000601048     4 OBJECT  LOCAL  DEFAULT   24 _ZZ6f_testE8l_f_data
    43: 0000000000601068     4 OBJECT  LOCAL  DEFAULT   25 _ZZ6f_testE9l_f_bbs_0
    44: 000000000060106c     4 OBJECT  LOCAL  DEFAULT   25 _ZZ6f_testE7l_f_bbs
    45: 0000000000400630     4 OBJECT  LOCAL  DEFAULT   15 _ZZ4mainE8l_rodata
    46: 0000000000601070     4 OBJECT  LOCAL  DEFAULT   25 _ZZ4mainE5l_bbs
    48: 00000000004007a8     0 OBJECT  LOCAL  DEFAULT   17 __FRAME_END__
    49: 0000000000600e20     0 OBJECT  LOCAL  DEFAULT   20 __JCR_END__
    52: 0000000000600e28     0 OBJECT  LOCAL  DEFAULT   21 _DYNAMIC
    55: 0000000000601000     0 OBJECT  LOCAL  DEFAULT   23 _GLOBAL_OFFSET_TABLE_
    57: 0000000000601038     8 OBJECT  GLOBAL DEFAULT   24 g_data
    65: 0000000000400608     0 OBJECT  GLOBAL HIDDEN    15 __dso_handle
    66: 0000000000400600     4 OBJECT  GLOBAL DEFAULT   15 _IO_stdin_used
    67: 0000000000601058     8 OBJECT  GLOBAL DEFAULT   25 g_bbs
    74: 0000000000601050     0 OBJECT  GLOBAL HIDDEN    24 __TMC_END__
(···)
4. 查看函数信息: readelf -s test|grep "FUNC"
(···)
     1: 0000000000000000     0 FUNC    GLOBAL DEFAULT  UND puts@GLIBC_2.2.5 (2)
     2: 0000000000000000     0 FUNC    GLOBAL DEFAULT  UND __libc_start_main@GLIBC_2.2.5 (2)
    29: 0000000000400470     0 FUNC    LOCAL  DEFAULT   13 deregister_tm_clones
    30: 00000000004004a0     0 FUNC    LOCAL  DEFAULT   13 register_tm_clones
    31: 00000000004004e0     0 FUNC    LOCAL  DEFAULT   13 __do_global_dtors_aux
    34: 0000000000400500     0 FUNC    LOCAL  DEFAULT   13 frame_dummy
    39: 0000000000400536    11 FUNC    LOCAL  DEFAULT   13 g_test
    56: 00000000004005f0     2 FUNC    GLOBAL DEFAULT   13 __libc_csu_fini
    59: 0000000000000000     0 FUNC    GLOBAL DEFAULT  UND puts@@GLIBC_2.2.5
    61: 00000000004005f4     0 FUNC    GLOBAL DEFAULT   14 _fini
    62: 0000000000000000     0 FUNC    GLOBAL DEFAULT  UND __libc_start_main@@GLIBC_
    68: 0000000000400580   101 FUNC    GLOBAL DEFAULT   13 __libc_csu_init
    70: 0000000000400440     0 FUNC    GLOBAL DEFAULT   13 _start
    72: 0000000000400541    59 FUNC    GLOBAL DEFAULT   13 main
    73: 000000000040052d     9 FUNC    GLOBAL DEFAULT   13 f_test
    75: 00000000004003e0     0 FUNC    GLOBAL DEFAULT   11 _init
(···)
