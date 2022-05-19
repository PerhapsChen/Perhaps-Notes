# Homework6

> 12132191 陈鹏翰

### 1.

**本次作业目录为**

**`/work/ese-chenph/MAE_HW6/MAE5032-2022-spring/petsc-tutorial-code/`**

**其中ex1~5为tutorial中的例程，太乙任务脚本为对应练习文件夹中的`ty_script`，的输出均在对应的`$LSB_JOBID.out`文件中** 



#### **ex1-demo**

相关参数解释

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

输出中使用`XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX`区分不同的调试

