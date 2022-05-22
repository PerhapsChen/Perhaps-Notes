# Homework6

> 12132191 陈鹏翰
>
> 可以前往[Github网页](https://github.com/PerhapsChen/Perhaps-Notes/blob/main/MAE5032_Homework/MAE5032_Homework6.md)获得更好的阅读体验

## 1.

**本次作业目录为**

**`/work/ese-chenph/MAE_HW6/MAE5032-2022-spring/petsc-tutorial-code/`**

**其中ex1~5为tutorial中的例程，太乙任务脚本为对应练习中的`ty_script`，输出均在对应的`$LSB_JOBID.log`文件中** 

**Q1,Q2 为两个题目的解答，源代码为q1.c和q2.c ，太乙脚本输出为各自文件夹内的.log文件**

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

结果详见太乙输出的.log文件

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

太乙任务的具体输出在.log文本文件中

**该例程中部分函数解释**

| 函数名                                       | 功能                                                         |
| -------------------------------------------- | ------------------------------------------------------------ |
| **PetscOptionsGetInt()**                     | 自定义PETSc程序的命令行参数                                  |
| **VecCreate()**                              | 创建一个向量（vec）                                          |
| **VecSetSizes()**                            | 指定vec的local和global size，可以给定一个另一个由PETSc决定<br />例如 `mpirun -np 2 ./ex3.out -n 4`<br />即可以对应`VecSetSizes(vec,2 , 4)` |
| **VecSetFromOptions()**                      | 指定vec可以被命令行FLAG配置                                  |
| **VecGetOwnershipRange()**                   | 获得vec所分配给各进程的初始和结束索引                        |
| **VecSetValues()**                           | 设置vec中元素的值，可以指定设置元素的个数，元素的索引，元素的值，以及元素是赋值INSERT还是自增ADD |
| **VecAssemblyBegin() VecAssemblyEnd()**      | 组装向量，然后才可以view向量                                 |
| **VecView()**                                | 查看一个vec，可以选择查看的方法                              |
| **VecGetLocalSize()**                        | 获得vec在当前进程的本地长度n_local                           |
| **VecGetArray()**<br />**VecRestoreArray()** | 将数组作为vec的存储地址，可以通过操作数组对vec赋值<br />当不需要通过数组操作时候，调用VecRestoreArray()释放。 |
| **VecDuplicate()**                           | 复制一个向量的值给另一个向量                                 |
| **PetscViewerCreate()**                      | 创建Viewer对象                                               |
| **PetscViewerASCIIOpen()**                   | 通过ascii码形式存放数据到指定文件                            |
| **PetscViewerDestroy()**                     | 释放vec                                                      |

------

### ex4-mat

内容主要是创建稀疏矩阵，对local矩阵的对角线和非对角线部分进行赋值操作，并查看矩阵。

太乙脚本和输出文件在ex4-mat文件夹中

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

**该例程创建了一个三对角矩阵A，并与单位向量u相乘，得到b，然后我们希望通过PETSc线性求解器ksp求解Ax=b的数值解（x的解析解即为b）,查看求解器每次迭代的误差范数，可以通过代码或命令行设置求解器的属性。**

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

```bash
# 线性求解器迭代过程中的残差变化
  0 KSP Residual norm 1.27475 
  1 KSP Residual norm 0.655856 
  2 KSP Residual norm 0.499797 
  3 KSP Residual norm 0.356929 
  4 KSP Residual norm 0.132518 
  5 KSP Residual norm < 1.e-11
# KSP object
KSP Object: 5 MPI processes
  type: gmres
    restart=30, using Modified Gram-Schmidt Orthogonalization
    happy breakdown tolerance 1e-30
  maximum iterations=1500, initial guess is zero
  tolerances:  relative=1e-10, absolute=1e-50, divergence=10000.
  left preconditioning
  using PRECONDITIONED norm type for convergence test
# PC object
PC Object: 5 MPI processes
  type: asm
    total subdomain blocks = 5, amount of overlap = 1
    restriction/interpolation type - RESTRICT
    Local solver information for first block is in the following KSP and PC objects on rank 0:
    Use -ksp_view ::ascii_info_detail to display information for all blocks

# ......
-- PETSc Performance Summary: ----
# ......
```

所有的输入输出详见太乙脚本及.log文件

**该例程中部分函数**

| 函数名                      | 功能                                                         |
| --------------------------- | ------------------------------------------------------------ |
| **MatSetValues()**          | 注意与MatSetValue()的区别，该函数可以指定矩阵哪几行哪几列设置为特定的值，可以选择是INSER还是ADD |
| **VecSet()**                | 将一个向量的所有元素设置为给定值                             |
| **MatMult()**               | 矩阵向量乘法                                                 |
| **KSPCreate()**             | 创建一个线性求解器对象                                       |
| **KSPSetOperators()**       | 设置线性求解器操作的矩阵，后面两个参数一般情况相同           |
| **KSPGetPC()**              | 获得求解器对应的预处理器                                     |
| **PCSetType()**             | 设置预处理器的类型                                           |
| **KSPSetTolerances()**      | 设置线性求解器的容许误差最大迭代次数等，可以设置为相对或者绝对误差 |
| **KSPSolve()**              | 通过求解器和右值向量，求解矩阵向量方程                       |
| **VecAXPY()**               | 执行特定的向量加法，程序中是 x = -1.0*u+x 即求残差           |
| **VecNorm()**               | 求向量的范数，可以设置为1范数或者2范数等                     |
| **KSPGetIterationNumber()** | 获得求解器的迭代次数                                         |

------

### 1.1

**幂迭代求三对角矩阵的最大特征向量和对应的特征值**，源代码见`q1.c`

**输入的解释（通过太乙脚本）**

```bash
# ......
# 编译链接
make q1.out

mkdir $LSB_JOBID.log
# 分配np个进程， 使用命令行 -n 指定矩阵的维度，通过-log_view查看程序表现
mpirun -np 1 ./q1.out -n 1024 -log_view> $LSB_JOBID.log/np1_n1024.log 2>&1
mpirun -np 1 ./q1.out -n 2048 -log_view> $LSB_JOBID.log/np1_n2048.log 2>&1
mpirun -np 1 ./q1.out -n 4096 -log_view> $LSB_JOBID.log/np1_n4096.log 2>&1

mpirun -np 2 ./q1.out -n 1024 -log_view> $LSB_JOBID.log/np2_n1024.log 2>&1
mpirun -np 2 ./q1.out -n 2048 -log_view> $LSB_JOBID.log/np2_n2048.log 2>&1
mpirun -np 2 ./q1.out -n 4096 -log_view> $LSB_JOBID.log/np2_n4096.log 2>&1

mpirun -np 4 ./q1.out -n 1024 -log_view> $LSB_JOBID.log/np4_n1024.log 2>&1
mpirun -np 4 ./q1.out -n 2048 -log_view> $LSB_JOBID.log/np4_n2048.log 2>&1
mpirun -np 4 ./q1.out -n 4096 -log_view> $LSB_JOBID.log/np4_n4096.log 2>&1

mpirun -np 8 ./q1.out -n 1024 -log_view> $LSB_JOBID.log/np8_n1024.log 2>&1
mpirun -np 8 ./q1.out -n 2048 -log_view> $LSB_JOBID.log/np8_n2048.log 2>&1
mpirun -np 8 ./q1.out -n 4096 -log_view> $LSB_JOBID.log/np8_n4096.log 2>&1

mpirun -np 16 ./q1.out -n 1024 -log_view> $LSB_JOBID.log/np16_n1024.log 2>&1
mpirun -np 16 ./q1.out -n 2048 -log_view> $LSB_JOBID.log/np16_n2048.log 2>&1
mpirun -np 16 ./q1.out -n 4096 -log_view> $LSB_JOBID.log/np16_n4096.log 2>&1

mpirun -np 32 ./q1.out -n 1024 -log_view> $LSB_JOBID.log/np32_n1024.log 2>&1
mpirun -np 32 ./q1.out -n 2048 -log_view> $LSB_JOBID.log/np32_n2048.log 2>&1
mpirun -np 32 ./q1.out -n 4096 -log_view> $LSB_JOBID.log/np32_n4096.log 2>&1
```

**输出的解释**

```bash
# 迭代求解的过程
iteration : 0	Residual: 2.23607
iteration : 10000	Residual: 2.99985e-08
iteration : 20000	Residual: 7.49981e-09
iteration : 30000	Residual: 3.33328e-09

......省略......
iteration : 3410000	Residual: 2.37588e-13
iteration : 3420000	Residual: 2.36255e-13
iteration : 3430000	Residual: 2.34035e-13
iteration : 3440000	Residual: 2.33591e-13
iteration : 3450000	Residual: 2.30926e-13
iteration : 3460000	Residual: 2.29594e-13
iteration : 3470000	Residual: 2.27374e-13
iteration : 3480000	Residual: 2.26485e-13
iteration : 3490000	Residual: 2.23821e-13

# 矩阵信息及迭代相关信息
*****The size of matrix is : (4096, 4096) 	#矩阵维度
*****The iteration times: 3495141			#迭代次数
*****Absolute tolerance: 2.22045e-13	#tolerance
*****break residual: 2.21601e-13		#最后退出迭代时的残差

# 矩阵的最大特征值和对应特征向量
*****The max eigen value of Matrix: 4
*****and the coressponding eigen vector:
Vec Object: 8 MPI processes
  type: mpi
Process [0]
3.12932e-05
-6.25863e-05
9.38793e-05
-0.000125172
0.000156465
......
Process [1]
......
......
Process [7]
......

# 通过 -log_view 得到的程序表现
......
```

**通过-log_view查看不同的矩阵规模和处理器个数的性能，如下表所示，单位GFlop/sec**

| 矩阵规模 | 1核   | 2核   | 4核   | 8核   | 16核  | 32核  |
| -------- | ----- | ----- | ----- | ----- | ----- | ----- |
| 1024     | 1.203 | 0.927 | 0.758 | 0.869 | 0.865 | 0.796 |
| 2048     | 1.309 | 1.409 | 1.326 | 1.540 | 1.637 | 1.610 |
| 4096     | 1.350 | 1.820 | 2.184 | 2.697 | 2.596 | 3.024 |

可以发现当矩阵规模较大时，MPI并行能有效提高计算效率

------

### 1.2



