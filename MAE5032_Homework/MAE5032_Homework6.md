# Homework6

> 12132191 陈鹏翰

### 1.

**本次作业目录为**

**`/work/ese-chenph/MAE_HW6/MAE5032-2022-spring/petsc-tutorial-code/`**

**其中ex1~5为tutorial中的例程，太乙任务脚本为对应练习中的`ty_script`，输出均在对应的`$LSB_JOBID.out`文件中** 



#### **ex1-demo**

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



#### ex2-basic

**内容是PETSc程序初始化及打印**

```
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



#### ex3-vec-viewer

**内容主要是创建、查看向量的方式**

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

