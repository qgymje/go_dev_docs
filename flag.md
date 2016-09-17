# package flag 简介

## 概述
flag包用于解析命令行的参数

### 与os.Args的区别

os.Args的类型是[]string, 第一位参数为程序名; 由类型可以看出: os.Args变量只能获取参数列表, 无法做到flag包提供的参数类型的解析与绑定. 事实上, flag包里的FlagSet类型的参数来自于os.Args, 因此说flag包是os.Args的升级版也不为过.


### 类型

##### FlagSet

### 函数

##### Var

### 接口

##### Value

### 第三方库

#### [cobra](https://github.com/spf13/cobra)


