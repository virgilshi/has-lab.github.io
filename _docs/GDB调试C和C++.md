# GDB调试C和C++

## 0 写在前面

观察代码执行路径常规的方法有三种：

- 在源代码中加打印`printf`确定代码执行路径，优点是不污染代码原有逻辑，常用于初级阅码阶段；
- 通过GNU GDB方式在源代码中加断点，利用`backtrace/bt`观察函数调用栈(function call stack)，追踪函数调用过程，优点是，能观察函数在内存中实时运行值；
- 利用系统日志，在特定执行点记录程序运行状态，工业上常用的系统日志有[Google/glog](https://github.com/google/glog)，优点是，可以设计日志警告等级。

## 1 启动调试

在调试代码之前一定要确保程序是以`-g`形式编译，目的是编译过程向目标文件写入函数、变量等symbol。

### 1.1 加载启动调试文件

加载启动方法有三种：

- 普通可执行文件启动
```bash
gdb a.out
```
- `file`加载可执行文件，优点是在gdb语境下利用`file`命令随时更换可执行文件。
```bash
gdb
->gdb
file a.out
```
- core文件启动，通常用于调试造成系统崩溃的程序，比如段错误，开启core后，若发生系统崩溃，内核会把崩溃时刻的程序状态记录在core文件中，core开启方法间附录1.
```bash
gdb $core_file
-> gdb
file a.out
```

通过`run/r`命令启动程序运行。

加载可执行文件时刻指定执行参数，方式有两种：

- gdb运行时指定
```bash
gdb ./a.out --args $arg1 $arg2
```
- gdb语境下指定
```bash
->gdb
set args --args $arg1 $arg2
```

- 观察执行参数
```bash
-->gdb
show args 
```

### 1.2 调试

#### 1.2.1 加断点并调试

```bash
break/b ${path_to_file}:${line_no} # 对行号加断点
break/b ${path_to_file}:${func_name} # 对函数加断点
run/r # 重新运行程序
continue/c # 运行到下一个断点，类似step over
step/s # 运行进入，i.e.,若在函数调用处，则运行到函数体内，类似step into
```

#### 1.2.2 观察

```bash
backtrace/bt # 观察函数调用栈
frame/f $frame_no # 将程序状态切换至第frame_no层函数调用
info/i $var # 观察变量
print/p $var # 打印变量值
ptype $var # 打印变量值
```

### 1.3 多线程调试

通过`$pid`捕获待调试程序

```bash
gdb attach $pid # 捕获进程$pid
->gdb
stop # 暂停程序运行

# 下面两步用于规避线程干扰
set scheduler-locking off # 所有线程均可执行
set scheduler-locking on # 只允许当前线程执行
```

### 1.4 

To be continue...







## 附录

1. 开启core文件

- 查看core文件大小
```bash
ulimit -c # 0则表示不生成core文件
```
- 设置core文件大小并开启core
```bash
ulimit -c $FILE_SIZE # 设置core文件尺寸为FILE_SIZE，单位为KB
ulimit -c unlimited # 设置core文件尺寸不受限
```


## 资料

- [GDB: The GNU Project Debugger](https://www.gnu.org/software/gdb/)
- [GDB Tutorial: Debugging with GDB](http://sourceware.org/gdb/current/onlinedocs/gdb/)