# Homework6

> 12132191 陈鹏翰

## 1.

**本次作业目录为**

**`/work/ese-chenph/MAE_HW6/MAE5032-2022-spring/petsc-tutorial-code/`**

**其中ex1~5为tutorial中的例程，太乙任务脚本为对应练习中的`ty_script`，输出均在对应的`$LSB_JOBID.log`文件中** 

**Q1,Q2 为两个题目的解答，源代码为q1.c和q2.c ，太乙脚本输出为各自文件夹内的.log文件(夹)**

------

### 1.1

**幂迭代求三对角矩阵的最大特征向量和对应的特征值**

**源码**(相关解释见注释)

![code](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/code.png)

**输入的解释（通过太乙脚本）**

```bash
#!/bin/bash
#BSUB -J petsc-test
#BSUB -q short
#BSUB -n 40
#BSUB -e %J-petsc.err
#BSUB -o %J-petsc.out

module purge
module load intel/2018.4
module load mpi/intel/2018.4

# 编译并创建存放结果日志的文件夹
make q1.out
mkdir $LSB_JOBID.log

# 对于不同的处理器个数k=1,5,10,20,40 和 矩阵规模 DIM=1000,2000,4000分别进行计算
# 不同组合的输出的结果存放在$LSF_JOBID.log文件夹里面的不同日志文件中
for k in {1,5,10,20,40}
do
    for DIM in {1000,2000,4000}
    do
    mpirun -np $k ./q1.out -n $DIM -log_view -npv 1> $LSB_JOBID.log/np${k}_n${DIM}.log 2>&1
    done
done
```

**输出的解释**

```bash
# 迭代求解的过程 显示每1万次迭代的迭代次数和残差
iteration : 0	Residual: 2.23607
iteration : 10000	Residual: 2.99985e-08
iteration : 20000	Residual: 7.49981e-09
iteration : 30000	Residual: 3.33328e-09

......省略......
iteration : 550000	Residual: 5.15588e-13
iteration : 560000	Residual: 4.44533e-13
iteration : 570000	Residual: 3.82805e-13
iteration : 580000	Residual: 3.30402e-13
iteration : 590000	Residual: 2.85105e-13
iteration : 600000	Residual: 2.45581e-13

# 矩阵信息及迭代收敛相关信息
*****The size of matrix is : (1000, 1000)	#矩阵维度
*****The iteration times: 606833			#迭代次数
*****Absolute tolerance: 2.22045e-13		#tolerance
*****break residual: 2.21601e-13			#最后退出迭代时的残差

# 矩阵的最大特征值和对应特征向量 
# 由于特征向量太长，故默认不输出，可以指定-npv 0 输出特征向量
*****The max eigen value of Matrix: 3.99999
*****and the coressponding eigen vector:
Vec Object: 5 MPI processes
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

通过-log_view查看不同的矩阵规模和处理器个数下的**执行时间**，如下表所示，单位为**秒**

| 矩阵规模 | 1核    | 5核    | 10核   | 20核   | 40核   |
| -------- | ------ | ------ | ------ | ------ | ------ |
| 1000     | 3.9208 | 3.4061 | 3.2807 | 3.8512 | 3.6489 |
| 2000     | 20.273 | 12.454 | 9.9536 | 10.346 | 10.182 |
| 4000     | 82.116 | 33.261 | 26.304 | 24.327 | 21.043 |

**可以发现当矩阵规模较大时，并行能有效提高幂迭代计算效率**

------

### 1.2

**逆幂迭代求矩阵最小特征值（模最小），由于逆矩阵和原矩阵的特征值互为倒数，故用A的逆代替在1.1中幂迭代中的A即可求得最小特征值。代码见`q2.c`**

**源码（相关解释见注释）**

![code2](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/code2.png)

**输入的解释（太乙脚本）**

```bash
#!/bin/bash
#BSUB -J petsc-test
#BSUB -q debug
#BSUB -n 40
#BSUB -e %J-petsc.err
#BSUB -o %J-petsc.out

module purge
module load intel/2018.4
module load mpi/intel/2018.4

# 编译，创建存放结果的文件夹
make q2.out
mkdir $LSB_JOBID.log

# 矩阵规模及线性求解器的收敛条件
DIM=2000		#矩阵规模
MaxIts=1000		#线性求解最大迭代次数
RTol=1.0e-10	#相对容差
ATol=1.0e-50	#绝对容差
MaxPIts=1000 	#最大幂迭代次数

# 针对不同核数k=1,5,10,20,40的运行 输出在.log文件夹的各个文件中
# 每次幂迭代都有一次线性求解的迭代，报告线性求解器收敛原因
# 针对不同的组合执行程序
# 通过-log_view查看performance
# 通过-ksp_max_it 设置线性求解器最大迭代次数
# 通过-ksp_rtol 设置线性求解器相对误差
# 通过-ksp_atol 设置线性求解器绝对误差
# 通过-ksp_type 设置线性求解器类型
# 通过-pc_type 设置与处理器类型
for k in {1,5,10,20,40}
do
mpirun -np $k ./q2.out -n $DIM -log_view -ksp_max_it $MaxIts -ksp_rtol $RTol -ksp_atol $ATol -ksp_converged_reason -ksptype richardson -pc_type jacobi > $LSB_JOBID.log/${k}_richardson_jacobi.log 2>&1
mpirun -np $k ./q2.out -n $DIM -log_view -ksp_max_it $MaxIts -ksp_rtol $RTol -ksp_atol $ATol -ksp_converged_reason -ksptype richardson -pc_type sor > $LSB_JOBID.log/${k}_richardson_sor.log 2>&1
mpirun -np $k ./q2.out -n $DIM -log_view -ksp_max_it $MaxIts -ksp_rtol $RTol -ksp_atol $ATol -ksp_converged_reason -ksptype richardson -pc_type sor > $LSB_JOBID.log/${k}_richardson_sor.log 2>&1

mpirun -np $k ./q2.out -n $DIM -log_view -ksp_max_it $MaxIts -ksp_rtol $RTol -ksp_atol $ATol -ksp_converged_reason -ksptype gmres -pc_type none > $LSB_JOBID.log/${k}_gmres_none.log 2>&1
mpirun -np $k ./q2.out -n $DIM -log_view -ksp_max_it $MaxIts -ksp_rtol $RTol -ksp_atol $ATol -ksp_converged_reason -ksptype gmres -pc_type jacobi > $LSB_JOBID.log/${k}_gmres_jacobi.log 2>&1
mpirun -np $k ./q2.out -n $DIM -log_view -ksp_max_it $MaxIts -ksp_rtol $RTol -ksp_atol $ATol -ksp_converged_reason -ksptype gmres -pc_type asm > $LSB_JOBID.log/${k}_gmres_asm.log 2>&1
mpirun -np $k ./q2.out -n $DIM -log_view -ksp_max_it $MaxIts -ksp_rtol $RTol -ksp_atol $ATol -ksp_converged_reason -ksptype gmres -pc_type hypre > $LSB_JOBID.log/${k}_gmres_hypre.log 2>&1

mpirun -np $k ./q2.out -n $DIM -log_view -ksp_max_it $MaxIts -ksp_rtol $RTol -ksp_atol $ATol -ksp_converged_reason -ksptype cg -pc_type none > $LSB_JOBID.log/${k}_cg_none.log 2>&1
mpirun -np $k ./q2.out -n $DIM -log_view -ksp_max_it $MaxIts -ksp_rtol $RTol -ksp_atol $ATol -ksp_converged_reason -ksptype cg -pc_type jacobi > $LSB_JOBID.log/${k}_cg_jacobi.log 2>&1
mpirun -np $k ./q2.out -n $DIM -log_view -ksp_max_it $MaxIts -ksp_rtol $RTol -ksp_atol $ATol -ksp_converged_reason -ksptype cg -pc_type asm > $LSB_JOBID.log/${k}_cg_asm.log 2>&1
mpirun -np $k ./q2.out -n $DIM -log_view -ksp_max_it $MaxIts -ksp_rtol $RTol -ksp_atol $ATol -ksp_converged_reason -ksptype cg -pc_type hypre > $LSB_JOBID.log/${k}_cg_hypre.log 2>&1

mpirun -np $k ./q2.out -n $DIM -log_view -ksp_max_it $MaxIts -ksp_rtol $RTol -ksp_atol $ATol -ksp_converged_reason -ksptype preonly -pc_type lu -pc_factor_mat_solver_type mumps > $LSB_JOBID.log/${k}_preonly_lu_mumps.log 2>&1
done
```

**输出的解释，输出在.log文件夹中**

```bash
# 报告每次线性求解迭代器收敛行为 这里的显示的是由于达到相对误差而收敛
Linear solve converged due to CONVERGED_RTOL iterations 1
iteration : 1	Residual: 319140.
Linear solve converged due to CONVERGED_RTOL iterations 1
iteration : 2	Residual: 83251.3
Linear solve converged due to CONVERGED_RTOL iterations 1
iteration : 3	Residual: 3083.7
Linear solve converged due to CONVERGED_RTOL iterations 1
iteration : 4	Residual: 177.528
Linear solve converged due to CONVERGED_RTOL iterations 1
#...省略...

# 幂迭代可以收敛
iteration : 26	Residual: 1.82772e-08
Linear solve converged due to CONVERGED_RTOL iterations 1
iteration : 27	Residual: 9.02219e-09
Linear solve converged due to CONVERGED_RTOL iterations 1
iteration : 28	Residual: 0.

# 幂迭代收敛行为
*****The size of matrix is : (2000, 2000)
*****The iteration times: 29
*****Absolute tolerance: 2.22045e-13
*****break residual: 0.

*****The min eigen value of Matrix: 2.46494e-06
*****and the coressponding eigen vector:
*****You need to specify '-npv 0' to view the eigen vector.

# performance
```

**2000x2000矩阵幂迭代次数，最大迭代次数设置为1000次，* 表示达到最大次数仍未收敛 **

| ksp        | pc     | 1核  | 5核  | 10核 | 20核 | 40核 |
| ---------- | ------ | ---- | ---- | ---- | ---- | ---- |
| richardson | jacobi | 328  | 231  | *    | 484  | 563  |
|            | sor    | 245  | *    | *    | *    | *    |
| gmres      | none   | *    | 283  | 439  | 219  | 331  |
|            | jacobi | 328  | 231  | *    | 484  | 563  |
|            | asm    | 30   | *    | *    | *    | *    |
|            | hypre  | *    | *    | *    | 108  | 67   |
| cg         | none   | *    | 283  | 439  | 219  | 331  |
|            | jacobi | 328  | 231  | *    | 484  | 563  |
|            | asm    | 30   | *    | *    | *    | *    |
|            | hypre  | *    | *    | *    | 108  | 67   |
| preonly    | lu     | *    | *    | *    | 31   | 28   |

由以上结果发现，预处理器为jacobi/none时，求解器适用性较广，很少出现不收敛的情况，而预处理器为sor，asm，lu会出现较多不收敛的情况，但在收敛的情况中收敛速度极快（通常收敛情况极端，要么极快收敛，要么无法收敛）。

选取迭代速度较快的**gmres_asm, gmres_hypre, cg_asm, cg_hypre, preonly_lu**进行大规模矩阵迭代，分别选取矩阵规模为**10000x10000**和**100000x100000**，比较并行计算效率发现，各方法收敛情况似乎没有特别的规律，不合适的线性求解迭代器的容差设置可能会导致幂迭代永远无法收敛到理想的情况，而是收敛到容差外的某一个特定的值。**对于该规模的三对角矩阵的逆幂迭代问题，MPI并行似乎无法显著提高收敛效率效率。**

