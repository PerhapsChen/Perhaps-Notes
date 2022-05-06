# Homework5

> **12132191 陈鹏翰**

### 1.

**太乙可执行文件位置 `/work/ese-chenph/MAE_HW5/MAE5032-2022-spring/hdf5-tutorial-code`/**

**在对应的build文件夹的main文件。** **可能需要连接动态库**

```bash
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/work/ese-chenph/lib/hdf5-1.12.1/lib/
```

#### (a)

**使用CMake&&Makefile生成可执行文件**

```cmake
cmake_minimum_required (VERSION 3.5)
project (EX01)
add_executable (main h5file.c)
target_include_directories(main PUBLIC /usr/local/include)
target_link_libraries(main PUBLIC /usr/local/lib/libhdf5.so)
```

**h5dump**

```bash
(base) cph@CPHdell:~/GitHub_PROJ/MAE5032-2022-spring/hdf5-tutorial-code/ex-01-file/build$ /usr/local/bin/h5dump file.h5 
HDF5 "file.h5" {
GROUP "/" {
}
}
```

**HDFView**

![image-20220430162541489](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220430162541489.png)

**文件访问模式**

- **H5F_ACC_TRUNC** 如果文件已经存在，则删除它，以便应用程序重写文件 

- **H5F_ACC_EXCL** 如果文件已存在，则打开失败

  ```
   unable to open file: name = 'file.h5', errno = 17, error message = 'File exists',
  ```

- **H5F_ACC_RDONLY** 指定程序只有读权限（read only）

- **H5F_ACC_RDWR** 指定程序具有读写权限 （read and write）

#### (b)

**h5dump**

```bash
(base) cph@CPHdell:~/GitHub_PROJ/MAE5032-2022-spring/hdf5-tutorial-code/ex-02-dataset/build$ /usr/local/bin/h5dump dset.h5 
HDF5 "dset.h5" {
GROUP "/" {
   DATASET "dset" {
      DATATYPE  H5T_STD_I32BE
      DATASPACE  SIMPLE { ( 4, 6 ) / ( 4, 6 ) }
      DATA {
      (0,0): 0, 0, 0, 0, 0, 0,
      (1,0): 0, 0, 0, 0, 0, 0,
      (2,0): 0, 0, 0, 0, 0, 0,
      (3,0): 0, 0, 0, 0, 0, 0
      }
   }
}
}
```

**HDFView**

![image-20220430165046876](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220430165046876.png)



**HDFcreate()**

```c
hid_t H5Fcreate(cosnt char* filename,
				unsigned flags,
				hid_t fipl_id,
				hid_t fapl_id
				)
```

创建一个HDF5文件

参数：

- **filename**： 要创建的文件名
- **flags**：文件访问控制 
  - **H5F_ACC_TRUNC** 删除文件如果文件已经存在的话
  - **H5F_ACC_EXCL** 文件存在则无法访问
- **fcpl_id** 文件创建属性列表标识符，支持更复杂的文件操作，如无需要可以使用H5P_DEFAULT
- **fapl_id** 文件访问属性列表标识符，支持更复杂的文件操作，如无需要可以使用H5P_DEFAULT



**H5Screate_simple**

```c
hid_t H5Screate_simple( int rank,
						const hsize_t dims[],
						const hsize_t maxdims[]
						)
```

创建一个简单的数据空间并提供访问

参数：

- **rank**：数据空间的维度
- **dims**：指定每个维度的大小
- **maxdims**：指定最大的每个维度大小



#### (c)

**似乎没有SDS.h文件，导致程序无法运行**

创建SDS.h文件

```c
#define FILE "SDS.h5"

hid_t file_id, dataset_id; /* identifiers */
herr_t status;
hid_t dataspace_id;

hsize_t dims[3];
file_id = H5Fcreate(FILE, H5F_ACC_TRUNC, H5P_DEFAULT, H5P_DEFAULT);
dims[0] = 5; 
dims[1] = 6;
dims[2] = 4; 
dataspace_id = H5Screate_simple(3, dims, NULL);
dataset_id = H5Dcreate2(file_id, "/IntArray", H5T_STD_I32BE, dataspace_id, 
                      H5P_DEFAULT, H5P_DEFAULT, H5P_DEFAULT);
status = H5Dclose(dataset_id);
status = H5Sclose(dataspace_id);
status = H5Fclose(file_id);
```

对上述生成的文件执行写操作和读操作

```c
status = H5Dwrite(dataset_id, H5T_NATIVE_INT, H5S_ALL, H5S_ALL, H5P_DEFAULT, dset_data);

status = H5Dread(dataset_id, H5T_NATIVE_INT, H5S_ALL, H5S_ALL, H5P_DEFAULT, dset_data);
printf("dset_data[3][2][1]:%2d\n", dset_data[3][1][2]);

// output: dset_data[3][2][1]:512
```

**h5dump**

```bash
(base) cph@CPHdell:~/GitHub_PROJ/MAE5032-2022-spring/hdf5-tutorial-code/ex-03-rw$ h5dump SDS.h5 
HDF5 "SDS.h5" {
GROUP "/" {
   DATASET "IntArray" {
      DATATYPE  H5T_STD_I32BE
      DATASPACE  SIMPLE { ( 5, 6, 4 ) / ( 5, 6, 4 ) }
      DATA {
      (0,0,0): 200, 201, 202, 203,
      (0,1,0): 210, 211, 212, 213,
      (0,2,0): 220, 221, 222, 223,
      (0,3,0): 230, 231, 232, 233,
      (0,4,0): 240, 241, 242, 243,
      (0,5,0): 250, 251, 252, 253,
      (1,0,0): 300, 301, 302, 303,
      (1,1,0): 310, 311, 312, 313,
      (1,2,0): 320, 321, 322, 323,
      (1,3,0): 330, 331, 332, 333,
      (1,4,0): 340, 341, 342, 343,
      (1,5,0): 350, 351, 352, 353,
      (2,0,0): 400, 401, 402, 403,
      (2,1,0): 410, 411, 412, 413,
      (2,2,0): 420, 421, 422, 423,
      (2,3,0): 430, 431, 432, 433,
      (2,4,0): 440, 441, 442, 443,
      (2,5,0): 450, 451, 452, 453,
      (3,0,0): 500, 501, 502, 503,
      (3,1,0): 510, 511, 512, 513,
      (3,2,0): 520, 521, 522, 523,
      (3,3,0): 530, 531, 532, 533,
      (3,4,0): 540, 541, 542, 543,
      (3,5,0): 550, 551, 552, 553,
      (4,0,0): 600, 601, 602, 603,
      (4,1,0): 610, 611, 612, 613,
      (4,2,0): 620, 621, 622, 623,
      (4,3,0): 630, 631, 632, 633,
      (4,4,0): 640, 641, 642, 643,
      (4,5,0): 650, 651, 652, 653
      }
   }
}
}
```

**HDFView**

![image-20220430191928983](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220430191928983.png)

**H5Dread**

```c
herr_t H5Dread	(	hid_t 	dset_id,
					hid_t 	mem_type_id,
					hid_t 	mem_space_id,
					hid_t 	file_space_id,
					hid_t 	dxpl_id,
					void * 	buf 
				)	
```

读取一个dataset中的原始数据到buffer中

参数：

- **dset_id**：数据集标识符
- **mem_type_id**：存储数据类型标识符
- **mem_space_id**：存储数据空间标识符
- **file_space_id**：文件中数据集空间标识符
- **dxpl_id**：数据传输属性
- **buf**：用于存储读取数的缓冲区



**H5Dwrite**

```c
herr_t H5Dwrite	(	hid_t 	dset_id,
					hid_t 	mem_type_id,
					hid_t 	mem_space_id,
					hid_t 	file_space_id,
					hid_t 	dxpl_id,
					const void * 	buf 
				)	
```

把缓冲区buf中的数据写入数据集

参数同H5Dread



#### (d)

**h5dump**

可以看出生成了含有MyGroup组的h5文件

```bash
(base) cph@CPHdell:~/GitHub_PROJ/MAE5032-2022-spring/hdf5-tutorial-code/ex-04-group$ h5dump group.h5 
HDF5 "group.h5" {
GROUP "/" {
   GROUP "MyGroup" {
   }
}
}
```

**HDFView**

![image-20220430193323873](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220430193323873.png)

**H5Gcreate**

```c
hid_t H5Gcreate2(	hid_t 	loc_id,
					const char* name,
                    hid_t 	lcpl_id,
                    hid_t 	gcpl_id,
                    hid_t 	gapl_id 
				)	
```

创建一个组并放到指定文件中去

参数

- **loc_id**：位置标识符，可以是文件、组、dataset等
- **name**：组的名字
- **lcpl_id**：连接创建形式
- **gcpl_id**：组创建属性标识符
- **gapl_id**：组访问属性标识符



#### (e)

**code and compiling instructions**

```c
// code
#include "stdio.h"
#include "stdlib.h"
#include "hdf5.h"
#define FILE "combine.h5"

int main()
{
    hid_t file_id, group_id, dataset_id, dataspace_id;
    herr_t status;
    hsize_t dims[3];

    dims[0] = 5; 
    dims[1] = 6;
    dims[2] = 4; 
    dataspace_id = H5Screate_simple(3, dims, NULL);

    file_id = H5Fcreate(FILE, H5F_ACC_TRUNC, H5P_DEFAULT, H5P_DEFAULT);
    group_id = H5Gcreate2(file_id, "/MyNewGroup", H5P_DEFAULT, H5P_DEFAULT, H5P_DEFAULT);
    dataset_id = H5Dcreate2(file_id, "/MyNewGroup/IntArray", H5T_STD_I32BE, dataspace_id, 
                          H5P_DEFAULT, H5P_DEFAULT, H5P_DEFAULT);
    
    int i, j, k, dset_data[5][6][4];
    for (j = 0; j < 5; j++)
        for (i = 0; i < 6; i++)
            for (k = 0; k < 4; k++)
                dset_data[j][i][k] = 200 + 100 * j + 10 * i + k;
    
    status = H5Dwrite(dataset_id, H5T_NATIVE_INT, H5S_ALL, H5S_ALL, H5P_DEFAULT, dset_data);
    status = H5Dread(dataset_id, H5T_NATIVE_INT, H5S_ALL, H5S_ALL, H5P_DEFAULT, dset_data);
    printf("dset_data[3][2][1]:%2d\n", dset_data[3][1][2]);

    status = H5Dclose(dataset_id);
    status = H5Sclose(dataspace_id);
    status = H5Fclose(file_id);
    status = H5Gclose(group_id);
}
```

```bash
# compiling instructions
gcc -g -pg -Wall main.c -lhdf5
```

**h5dump**

h5dump和HDFView可以看出已经成功在group内创建dataset

```bash
(base) cph@CPHdell:~/GitHub_PROJ/MAE5032-2022-spring/hdf5-tutorial-code/ex-05-combine$ h5dump combine.h5 
HDF5 "combine.h5" {
GROUP "/" {
   GROUP "MyNewGroup" {
      DATASET "IntArray" {
         DATATYPE  H5T_STD_I32BE
         DATASPACE  SIMPLE { ( 5, 6, 4 ) / ( 5, 6, 4 ) }
         DATA {
         (0,0,0): 200, 201, 202, 203,
         (0,1,0): 210, 211, 212, 213,
         (0,2,0): 220, 221, 222, 223,
         (0,3,0): 230, 231, 232, 233,
		 # 此处省略很多行...
         (4,1,0): 610, 611, 612, 613,
         (4,2,0): 620, 621, 622, 623,
         (4,3,0): 630, 631, 632, 633,
         (4,4,0): 640, 641, 642, 643,
         (4,5,0): 650, 651, 652, 653
         }
      }
   }
}
}
```

**HDFView**

![image-20220430195442633](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220430195442633.png)

**valgrind内存检查**

valgrind检查发现不存在内存泄漏

```bash
(base) cph@CPHdell:~/GitHub_PROJ/MAE5032-2022-spring/hdf5-tutorial-code/ex-05-combine$ valgrind ./a.out 
==211882== Memcheck, a memory error detector
==211882== Copyright (C) 2002-2017, and GNU GPL'd, by Julian Seward et al.
==211882== Using Valgrind-3.15.0 and LibVEX; rerun with -h for copyright info
==211882== Command: ./a.out
==211882== 
dset_data[3][2][1]:512
==211882== 
==211882== HEAP SUMMARY:
==211882==     in use at exit: 0 bytes in 0 blocks
==211882==   total heap usage: 2,599 allocs, 2,599 frees, 1,886,590 bytes allocated
==211882== 
==211882== All heap blocks were freed -- no leaks are possible
==211882== 
==211882== For lists of detected and suppressed errors, rerun with: -s
==211882== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 0 from 0)
```

**Gprof and Valgrind callgrind**

使用 `gcc -g -pg -Wall main.c -lhdf5`编译后，执行

```bash
./a.out
gprof ./a.out gmon.out > prifile.txt
```

prifile.txt显示未累计时间，说明该程序运行时间极短，gprof可能无法统计。

使用valgrind callgrind显示出以下内容

```bash
valgrind --tool=callgrind ./a.out
==215180== Callgrind, a call-graph generating cache profiler
==215180== Copyright (C) 2002-2017, and GNU GPL'd, by Josef Weidendorfer et al.
==215180== Using Valgrind-3.15.0 and LibVEX; rerun with -h for copyright info
==215180== Command: ./a.out
==215180== 
==215180== For interactive control, run 'callgrind_control -h'.
dset_data[3][2][1]:512
==215180== 
==215180== Process terminating with default action of signal 27 (SIGPROF)
==215180==    at 0x4DAE52A: __open_nocancel (open64_nocancel.c:45)
==215180==    by 0x4DBC30F: write_gmon (gmon.c:370)
==215180==    by 0x4DBCB6E: _mcleanup (gmon.c:444)
==215180==    by 0x4CE200D: __cxa_finalize (cxa_finalize.c:83)
==215180==    by 0x109366: ??? (in /home/cph/GitHub_PROJ/MAE5032-2022-spring/hdf5-tutorial-code/ex-05-combine/a.out)
==215180==    by 0x4011F5A: _dl_fini (dl-fini.c:138)
==215180==    by 0x4CE18D6: __run_exit_handlers (exit.c:108)
==215180==    by 0x4CE1A8F: exit (exit.c:139)
==215180==    by 0x4CBF0B9: (below main) (libc-start.c:342)
==215180== 
==215180== Events    : Ir
==215180== Collected : 12728180
==215180== 
==215180== I   refs:      12,728,180
Profiling timer expired
```

说明程序搜集到约1200多万个事件，并写入了callgrind.out.215180，执行

```bash
callgrind_annotate --auto=yes callgrind.out.215180 
```

可以看到程序调用函数的热点情况

```bash
12,727,938  PROGRAM TOTALS

--------------------------------------------------------------------------------
Ir         file:function
--------------------------------------------------------------------------------
3,381,921  main.c:main [/home/cph/GitHub_PROJ/MAE5032-2022-spring/hdf5-tutorial-code/ex-05-combine/a.out]
3,183,693  /build/glibc-sMfBJT/glibc-2.31/string/../sysdeps/x86_64/multiarch/memset-vec-unaligned-erms.S:__memset_avx2_unaligned_erms [/usr/lib/x86_64-linux-gnu/libc-2.31.so]
1,476,285  ???:H5T__conv_order_opt [/usr/local/lib/libhdf5.so.200.2.0]
  982,850  /build/glibc-sMfBJT/glibc-2.31/string/../sysdeps/x86_64/multiarch/memmove-vec-unaligned-erms.S:__memcpy_avx_unaligned_erms [/usr/lib/x86_64-linux-gnu/libc-2.31.so]
  436,652  /build/glibc-sMfBJT/glibc-2.31/elf/dl-lookup.c:do_lookup_x [/usr/lib/x86_64-linux-gnu/ld-2.31.so]
  411,279  /build/glibc-sMfBJT/glibc-2.31/elf/dl-lookup.c:_dl_lookup_symbol_x [/usr/lib/x86_64-linux-gnu/ld-2.31.so]
  368,826  /build/glibc-sMfBJT/glibc-2.31/malloc/malloc.c:_int_malloc [/usr/lib/x86_64-linux-gnu/libc-2.31.so]
  221,393  /build/glibc-sMfBJT/glibc-2.31/malloc/malloc.c:_int_free [/usr/lib/x86_64-linux-gnu/libc-2.31.so]
  153,860  ???:H5_hash_string [/usr/local/lib/libhdf5.so.200.2.0]
  134,473  /build/glibc-sMfBJT/glibc-2.31/elf/../sysdeps/x86_64/dl-machine.h:_dl_relocate_object
  122,028  /build/glibc-sMfBJT/glibc-2.31/malloc/malloc.c:malloc [/usr/lib/x86_64-linux-gnu/libc-2.31.so]
   89,438  ???:H5I_register [/usr/local/lib/libhdf5.so.200.2.0]
   # ...................
```



### 2.

**我们选择的Final Projecet是Problem A，小组成员有黄灏和陈鹏翰。**

**项目Github地址  https://github.com/PerhapsChen/MAE5032_FinalProject**

### 3.

**PC上PETSC的安装**

执行下列命令下载源码

```bash
mkdir ~/projects
cd ~/projects
git clone -b release https://gitlab.com/petsc/petsc
cd petsc
```

进行配置

```bash
./configure --download-mpich --download-fblaslapack
```

![image-20220430223754027](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220430223754027.png)

使用make构建

```bash
make PETSC_DIR=/home/cph/projects/petsc PETSC_ARCH=arch-linux-c-debug all
```

![image-20220430224217940](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220430224217940.png)

测试

```bash
make check
```

![image-20220430224228534](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220430224228534.png)



**TaiYi上PETSC的安装**

下载完petsc及其依赖项后，执行下面的命令进行配置

```bash
./configure --with-mpi-dir=/share/intel/2018u4/compilers_and_libraries_2018.5.274/linux/mpi/intel64/ --with-blaslapack-dir=/share/intel/2018u4/compilers_and_libraries_2018.5.274/linux/mkl --with-debugging=no --prefix=/work/ese-chenph/lib/petsc-3.16.6-opt --download-hypre=/work/ese-chenph/petsc-3.16.6-extlibs/hypre-2.23.0.tar.gz --download-mumps=/work/ese-chenph/petsc-3.16.6-extlibs/petsc-pkg-mumps-6d1470374d32.tar.gz --download-metis=/work/ese-chenph/petsc-3.16.6-extlibs/petsc-pkg-metis-c8d2dc1e751e.tar.gz --download-hdf5=/work/ese-chenph/petsc-3.16.6-extlibs/hdf5-1.12.1.tar.bz2 COPTFLAGS="-O3 -march=native -mtune=native" CXXOPTFLAGS="-O3 -march=native -mtune=native" FOPTFLAGS="-O3 -march=native -mtune=native" --with-scalapack-include=/share/intel/2018u4/compilers_and_libraries_2018.5.274/linux/mkl/include --with-scalapack-lib="-L/share/intel/2018u4/compilers_and_libraries_2018.5.274/linux/mkl/lib/intel64/ -lmkl_blacs_intelmpi_lp64 -lmkl_scalapack_lp64"
```

![image-20220430220843358](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220430220843358.png)

执行make进行构建

```bash
make PETSC_DIR=/work/ese-chenph/download/petsc-3.16.6 PETSC_ARCH=arch-linux2-c-opt all
```

![image-20220430221056259](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220430221056259.png)

安装

```bash
make PETSC_DIR=/work/ese-chenph/download/petsc-3.16.6 PETSC_ARCH=arch-linux2-c-opt install
```

![image-20220430221519016](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220430221519016.png)

测试

```bash
make PETSC_DIR=/work/ese-chenph/lib/petsc-3.16.6-opt PETSC_ARCH=arch-linux2-c-opt PETSC_ARCH="" check
```

![image-20220430221823595](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220430221823595.png)
