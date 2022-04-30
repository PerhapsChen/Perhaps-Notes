# Homework5

> **12132191 陈鹏翰**

### 1.

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

![image-20220430162541489](D:/perhapschen.github.io/img/image-20220430162541489.png)

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

![image-20220430165046876](D:/perhapschen.github.io/img/image-20220430165046876.png)



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

