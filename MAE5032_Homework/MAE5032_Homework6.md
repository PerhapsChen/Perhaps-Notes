# Homework6

> 12132191 陈鹏翰
>
> 可以前往[Github网页](https://github.com/PerhapsChen/Perhaps-Notes/blob/main/MAE5032_Homework/MAE5032_Homework6.md)获得更好的阅读体验

## 1.

**本次作业目录为**

**`/work/ese-chenph/MAE_HW6/MAE5032-2022-spring/petsc-tutorial-code/`**

**其中ex1~5为tutorial中的例程，太乙任务脚本为对应练习中的`ty_script`，输出均在对应的`$LSB_JOBID.out`文件中** 

------

### **ex1-demo**

相关FLAG解释

| FLAG                                                         | 功能                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| -snes_monitor                                                | 监视非线性求解器的迭代误差进度                               |
| -snes_view                                                   | 查看任务中使用的非线性求解器属性，包括最大迭代次数、tolerance等 |
| -snes_type XX                                                | 设置非线性求解器为XX类型                                     |
| -ksp_type XX                                                 | 设置线性求解器为XX类型                                       |
| -pc_type asm<br />-pc_asm_blocks 4<br />-pc_asm_overlap<br />-pc_asm_local_type<br />-sub_pc_type | 设置预处理器类型及属性                                       |
| -snes_monitor_short                                          | 在残差范数变小时打印更少的数字                               |
| -snes_converged_reason                                       | 打印收敛或发散的原因                                         |
| -log_view                                                    | 打印程序的性能表现等                                         |

进行了5次不同的调试

```bash
make ex1.out

mpirun -np 1 ./ex1.out

mpirun -np 1 ./ex1.out -snes_view

mpirun -np 1 ./ex1.out -help

mpirun -np 1 ./ex1.out -snes_monitor -snes_view

mpirun -np 1 ./ex1.out -snes_type newtontr -snes_monitor -snes_view

mpirun -np 2 ./ex1.out -ksp_type richardson -pc_type asm \
  -pc_asm_blocks 4 -pc_asm_overlap 0 -pc_asm_local_type additive \
  -sub_pc_type lu \
  -snes_monitor_short -snes_converged_reason -snes_view \
  -log_view
```

输出文件`3733712-petsc.out`中使用`XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX`区分不同的调试

------

### ex2-basic

**内容是PETSc程序初始化及打印**

```bash
make ex2.out

mpirun -np 1 ./ex2.out

mpirun -np 2 ./ex2.out

mpirun -np 3 ./ex2.out

mpirun -np 4 ./ex2.out -log_view
```

分别使用1~4个处理器内核进行任务，发现打印对应数量的字符串

结果详见太乙输出的.out文件

```
XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
=COMAND LINE=
mpirun -np 3 ./ex2.out
=OUTPUT=
Number of processors = 3, rank = 0
Hello World from rank 0 
Hello World from rank 1 
Hello World from rank 2 
XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```

**该例程中部分函数解释**

| 函数名                                       | 功能                                                         |
| -------------------------------------------- | ------------------------------------------------------------ |
| **PetscInitialize**()                        | 初始化PETSc及MPI                                             |
| **MPI_Comm_size()**<br />**MPI_Comm_rank()** | 确定communicator的rank和size                                 |
| **PetscPrintf()**                            | PETSC_COMM_WORLD，整个comm单一打印<br />PETSC_COMM_SELF，所有的进程各自打印 |
| **MPI_Barrier()**                            | 阻塞，等待所有进程运行至此                                   |
| **PetscFinalize()**                          | 终止PETSc程序                                                |

------

### ex3-vec-viewer

**内容主要是创建（包括setvalue方式和array方式）、查看向量（包括标准输出和ascii文件）的方式**

```bash
make ex3.out

#创建全局长度为6的vec(代码内设置) 分配给1个processor
mpirun -np 1 ./ex3.out

#创建全局长度为2的vec 分配给2个processors
mpirun -np 2 ./ex3.out -n 2

#创建全局长度为6的vec 分配给2个processors
mpirun -np 2 ./ex3.out -n 6

#创建全局长度为20的vec 分配给4个processors
mpirun -np 4 ./ex3.out -n 20
```

太乙任务的具体输出在.out文本文件中

**该例程中部分函数解释**

| 函数名                                       | 功能                                                         |
| -------------------------------------------- | ------------------------------------------------------------ |
| **PetscOptionsGetInt()**                     | 自定义PETSc程序的命令行参数                                  |
| **VecCreate()**                              | 创建一个向量（vec）                                          |
| **VecSetSizes()**                            | 指定vec的local和global size，可以给定一个另一个由PETSc决定<br />例如 `mpirun -np 2 ./ex3.out -n 4`<br />即可以对应`VecSetSizes(vec,2 , 4)` |
| **VecSetFromOptions()**                      | 指定vec可以被命令行FLAG配置                                  |
| **VecGetOwnershipRange()**                   | 获得vec所分配给各进程的初始和结束索引                        |
| **VecSetValues()**                           | 设置vec中元素的值，可以指定设置元素的个数，元素的索引，元素的值，以及元素是赋值INSERT还是自增ADD |
| **VecAssemblyBegin() VecAssemblyEnd()**      | 组装向量                                                     |
| **VecView()**                                | 查看一个vec，可以选择查看的方法                              |
| **VecGetLocalSize()**                        | 获得vec在当前进程的本地长度n_local                           |
| **VecGetArray()**<br />**VecRestoreArray()** | 将数组作为vec的存储地址，可以通过操作数组对vec赋值<br />当不需要通过数组操作时候，调用VecRestoreArray()释放。 |
| **PetscViewerCreate()**                      | 创建Viewer对象                                               |
| **PetscViewerASCIIOpen()**                   | 通过ascii码形式存放数据到指定文件                            |
| **PetscViewerDestroy()**                     | 释放vec                                                      |

------

### ex4-mat

内容主要是创建稀疏矩阵，对local矩阵的对角线和非对角线部分进行赋值操作，并查看矩阵。

太乙脚本和标准输出文件在ex4-mat文件夹中

```bash
make ex4.out

mpirun -np 1 ./ex4.out

mpirun -np 2 ./ex4.out

mpirun -np 3 ./ex4.out

mpirun -np 4 ./ex4.out
```

```
#其中一个输出 mpirun -np 3 ./ex4.out
XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
=COMAND LINE=
mpirun -np 2 ./ex4.out

=OUTPUT=
Matrix size is 10 by 10 
rank [0] rstart = 0 , rend = 5 
rank [1] rstart = 5 , rend = 10 
Mat Object: 2 MPI processes
  type: mpiaij
row 0: (0, 2.)  (5, 1.) 
row 1: (1, 2.)  (5, 1.) 
row 2: (2, 2.)  (5, 1.) 
row 3: (3, 2.)  (5, 1.) 
row 4: (4, 2.)  (5, 1.) 
row 5: (5, 2.) 
row 6: (6, 2.) 
row 7: (7, 2.) 
row 8: (8, 2.) 
row 9: (9, 2.) 
XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```

**该例程中部分函数的解释**

| 函数名                                       | 功能                                                         |
| -------------------------------------------- | ------------------------------------------------------------ |
| **PetscMalloc2()**<br />**PetscFree2()**     | 分配\释放给定两个变量的空间                                  |
| **MatCreateAIJ()**                           | 创建矩阵，需要给出的信息包括：<br />矩阵大小，可以给出local大小或者global大小<br />矩阵每一行对角线部分非零元素的个数，可以给local或者global<br />矩阵每一行非对角线部分非零元的个数，可以给local或者global |
| **MatSetFromOptions()**                      | 指定矩阵可以被命令行FLAG配置                                 |
| **MatSetUp()**                               | 设置内部矩阵数据结构供以后使用                               |
| **MatGetOwnershipRange()**                   | 获得mat所分配给各进程的初始和结束的行索引                    |
| **MatGetSize()**                             | 获得mat的形状                                                |
| **MatSetValue()**                            | 设置给定矩阵给定位置元素的值，可以设置INSERT或者ADD          |
| **MatAssemblyBegin()<br />MatAssemblyEnd()** | 组装矩阵                                                     |
| **MatView()**                                | 查看mat，可以选择查看方式                                    |
| **MatDestroy()**                             | 释放mat                                                      |

------

### ex5-ksp

**该例程创建了一个三对角矩阵A，并与单位向量u相乘，得到b，然后我们希望通过PETSc线性求解器ksp求解Ax=b的数值解（x的解析解即为b），可以通过代码或命令行设置求解器的属性。**

```bash
make ex5.out

# 查看矩阵和线性求解器
mpirun -np 2 ./ex5.out -mat_view -ksp_view

# 设置求解器类型为CG，监视求解状态，查看求解器
mpirun -np 2 ./ex5.out -ksp_type cg -pc_type none  -ksp_monitor  -ksp_view

# 设置求解器类型为GMRES，设置求解器属性，包括绝对/相对容忍误差、最大迭代次数等
# 设置预处理器的类型及属性
# 设置查看收敛或发散原因，并查看求解器
mpirun -np 3 ./ex5.out -ksp_type gmres \
 -ksp_gmres_restart 30 -ksp_rtol 1.0e-10 \
 -ksp_atol 1.0e-50 -ksp_max_it 1500 \
 -ksp_gmres_modifiedgramschmidt \
 -pc_type asm \
 -ksp_rtol 1.0e-10 -sub_ksp_type richardson \
 -sub_pc_type icc -ksp_monitor_short \
 -ksp_converged_reason \
 -ksp_view
```

