# Homework3

#### 陈鹏翰 12132191

**先将`hw3_ChenPenghan_12132191.tar`解开，目录如下**

### **其中HW3.X对应第X题的作业，`mat.h` 定义了一些要用到的函数。**

![image-20220411222746963](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220411222746963.png)

#### **每个HW3.X文件夹下都对应有源代码、Makefile和太乙任务脚本及运行结果**

![image-20220411222805203](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220411222805203.png)



#### (1)

- 进入HW3.1文件夹

  ```
  cd ~/MAE_HW3/HW3.1
  ```

- 方式一：手动make执行

  执行 `make` 然后通过命令行参数输入，例如m=3,q=4,n=5

  ```
  ./matmult 3 4 5
  ```

- 方式二：递交太乙任务`mae_hw3.sh`

  等待任务计算完毕后前往.log文件查看结果

  ```
  bsub<mae_hw3.sh
  ```

- 对比Matlab，结果正确

  ![image-20220411142135032](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220411142135032.png)

#### (2)

- 进入HW3.2文件夹

  ```
  cd ~/MAE_HW3/HW3.2
  ```

- 手动make执行 该问题不设命令行参数

  ```
  ./matmult2
  ```

  或者提交太乙任务

  ```
  bsub<mae_hw3.sh
  ```

  **当n>4096时，该方法似乎难以处理**

  ```
  n=              :128  
  Time last(s)    :0.00171 
  GFlop           :0.00421 
  GFlops/sec      :2.46672 
  
  n=              :256  
  Time last(s)    :0.00635 
  GFlop           :0.03362 
  GFlops/sec      :5.29115 
  
  n=              :512  
  Time last(s)    :0.04722 
  GFlop           :0.26870 
  GFlops/sec      :5.69070 
  
  n=              :1024  
  Time last(s)    :0.38633 
  GFlop           :2.14853 
  GFlops/sec      :5.56138 
  
  n=              :2048  
  Time last(s)    :5.82528 
  GFlop           :17.18406 
  GFlops/sec      :2.94991 
  
  n=              :4096  
  Time last(s)    :61.57987 
  GFlop           :137.45573 
  GFlops/sec      :2.23215 
  ```

- **baseline performance 如图所示**

![下载 (1)](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/%E4%B8%8B%E8%BD%BD%20(1)-16495900667111.png)

#### (3)

- 进入HW3.3文件夹

  ```
  cd cd ~/MAE_HW3/HW3.3
  ```

- 手动make执行 该问题不设命令行参数

  ```
  ./bmatmult
  ```

  或者提交太乙任务

  ```
  bsub<mae_hw3.sh
  ```

- **矩阵分块乘法实现 见mat.h中的 MyBlockMM函数**

- 该方法性能如图所示，单位为GFlops/sec

  **可见分块计算能有效提高计算效率。**

  ![下载](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/%E4%B8%8B%E8%BD%BD-16495903491812.png)

#### (4) 

learn ```dgemm``` from [HERE]([dgemm (oracle.com)](https://www.intel.com/content/www/us/en/develop/documentation/mkl-tutorial-c/top/multiplying-matrices-using-dgemm.html))

dgemm performs one of  the  matrix-matrix  operations  

**C:=alpha\*op( A )\*op( B ) + beta\*C** 


   ```
void dgemm(char transa, char transb, int m, int  n,  int  k,
               double  alpha,  double *A, int LDA, double *B, int
               LDB, double beta, double *C, int LDC);
   ```

   参数：

   - transa（char*）：是否对A进行转置，'N' 代表不转置，'T' or 'C'代表转置
   - transb（char*）：是否对A进行转置，'N' 代表不转置，'T' or 'C'代表转置
   - m (int*)：代表矩阵A和矩阵C的行数
   - n (int*)：代表矩阵B和矩阵C的列数
   - k (int*)：代表矩阵A的列数和矩阵B的行数
   - alpha, beta (double*)：代表倍数标量参与上述运算
   - A, B, C (double*): 矩阵A  B C
   - LDA (int*) ：LDA指定A的第一个维度大小
   - LDB (int*) ：LDB指定B的第一个维度大小
   - LDC (int*) ：LDC指定C的第一个维度大小

#### (5)

- 进入HW3.5文件夹

  ```
  cd cd ~/MAE_HW3/HW3.5
  ```

- 手动make后执行

  ```
  ./dgemm
  ```

  Makefile中的编译指令为

  ```
  icc -o dgemm hw3_5.c -lmkl_intel_lp64 -lmkl_intel_thread -lmkl_core -liomp5 -lpthread -lm -ldl
  ```

  或者提交太乙任务

  ```
  bsub<mae_hw3.sh
  ```

- 查看结果

  ```
  n           :128
  Time last   :0.00077 
  GFlops      :0.04211 
  GFlops/sec  :54.82667 
  
  n           :256
  Time last   :0.00662 
  GFlops      :0.33620 
  GFlops/sec  :50.75478 
  
  n           :512
  Time last   :0.04818 
  GFlops      :2.68698 
  GFlops/sec  :55.76837 
  
  n           :1024
  Time last   :0.35181 
  GFlops      :21.48532 
  GFlops/sec  :61.06994 
  
  n           :2048
  Time last   :2.79628 
  GFlops      :171.84063 
  GFlops/sec  :61.45337 
  
  n           :4096
  Time last   :22.16884 
  GFlops      :1374.55731 
  GFlops/sec  :62.00401 
  ```

  **发现使用该函数可以极大提高矩阵乘法的计算效率**

#### (6)

##### Makefile:

- 在每个作业文件夹HW3.X中都有一个Makefile文件和太乙任务的shell脚本

- 对于每一个题目，进入该文件夹后执行

  ```
  make
  ```

- 然后运行可执行文件即可，例如对于题目3.1

  ```
  ./matmult 3 4 5
  ```

##### TaiYi:

- 在每个作业文件夹HW3.X中都有一个太乙任务的shell脚本

- 在该文件夹下执行命令提交太乙任务

  ```
  bsub<mae_hw3.sh
  ```

- 等待执行完毕后查看在.log文件中查看结果。

##### tar

- 在第三次作业总文件夹下，执行

  ```
   tar cvf hw3_ChenPenghan_12132191.tar *
  ```

  即可将所有作业打包至hw3.tar文件中。

#### (7)

- 当使用mkl_malloc 和 mkl_free代替 malloc 和 free 来分配和释放内存时，可以达到65.01423GFlops/sec

  ```
  n           :1024
  Time last   :0.35301 
  GFlops      :21.48532 
  GFlops/sec  :60.86321 
  
  n           :2048
  Time last   :2.66868 
  GFlops      :171.84063 
  GFlops/sec  :64.39165 
  
  n           :4096
  Time last   :21.14241 
  GFlops      :1374.55731 
  GFlops/sec  :65.01423 
  ```

  

