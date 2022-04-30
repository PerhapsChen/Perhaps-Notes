# Homework4

#### 12132191 陈鹏翰

#### 1.VTK

- 下载VTK-8.2.0.tar.gz到太乙，并解压

  ```bash
  wget https://www.vtk.org/files/release/8.2/VTK-8.2.0.tar.gz
  tar -zxvf ~/download/VTK-8.2.0.tar.gz
  ```

- 加载cmake模组，为out-of-source-build新建vtk_build目录并进入

  ```
  module load cmake/3.12.2 
  mkdir vtk_build
  cd vtk_build
  ```

- ccmake进入交互界面，按C准备configure

  ```bash
  ccmake ../VTK-8.2.0
  # enter C to configure
  ```

- 找到 **CMAKE_BUILD_TYPE** 更改为**Release**

- 找到 **CMAKE_INSTALL_PREFIX** 更改为 **/work/ese-chenph/lib/VTK-8.2.0/**

- 修改完成后配置，然后生成

  ![image-20220420130947341](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220420130947341.png)

- 执行cmake生成Makefile

  ```
  cmake ../VTK-8.2.0
  ```

  ![image-20220420131119904](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220420131119904.png)

- 执行make进行构建

  ```bash
  make -j
  # specify "-j" to activate multi-job
  ```

  ![image-20220420131522640](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220420131522640.png)

- 执行 make install 进行安装

  ```
  make install
  ```

  ![image-20220420131639607](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220420131639607.png)

  ![image-20220420131746593](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220420131746593.png)



#### 2.HDF5

- 下载hdf5-1.12.1.tar.gz并解压

  ```
  tar -zxvf hdf5-1.12.1.tar.gz
  ```

- 进入解压后的文件夹，查看文档

  ```
  cd hdf5-1.12.1
  cat release_docs/INSTALL
  ```

  ![image-20220420133154364](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220420133154364.png)

- 执行configure并确认配置

  ```
  ./configure --prefix=/work/ese-chenph/lib/hdf5-1.12.1/
  ```

  ![image-20220420133512496](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220420133512496.png)

- 执行make进行构建

  ```
  make -j
  ```

  ![image-20220420133631692](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220420133631692.png)

- 执行 make check 进行构建测试

  ```
  make check -j
  ```

  ![image-20220420134751067](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220420134751067.png)

- 执行 make install 安装

  ```
  make install 
  ```

  ![image-20220420134824236](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220420134824236.png)

- 执行make check-install 测试

  ```
  make check-install -j
  ```

  ![image-20220420134933157](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220420134933157.png)

- 查看安装目录

  ![image-20220420135006424](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220420135006424.png)



#### 3.GDB

- 对于第5个例子 编译添加-g 通过gdb a.out进入调试

  ```bash
  g++ -g basic5.c
  gdb a.out
  ```

  ![image-20220420165327052](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220420165327052.png)

- run: 从头运行一个待调试的命令

  ![image-20220420165424345](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220420165424345.png)

- print：打印某个变量

  ![image-20220420165533503](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220420165533503.png)

- next：运行到下一行，不进入函数内部

  可以看到直接运行了x = mystery(x) 并没有进入函数内部

  ![image-20220420165657240](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220420165657240.png)

- step：类似next 运行到下一行 会进入到函数内部

  可以看到进入了mystery函数内部

  ![image-20220420165820077](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220420165820077.png)

- break：打断点，调试时运行run会停在断点

  info break可以查看已经打的断点

  ![image-20220420170146059](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220420170146059.png)

- continue：继续运行程序到下一个断点

  ![image-20220420170243206](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220420170243206.png)

- list：打印源码，后面加数字可以查看该行附近的代码

  ![image-20220420170701241](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220420170701241.png)

- display：每一步调试后都显示某个变量的值

  ![image-20220420170746168](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220420170746168.png)

- where：查看程序运行到哪个函数、哪一行

  ![image-20220420170847510](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220420170847510.png)

- backtrace：查看函数调用的情况 编号越小越最近调用，位于函数调用栈的越上面。

  ![image-20220420171140207](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220420171140207.png)

- quit：退出调试

  ![image-20220420171200630](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220420171200630.png)

#### 4.Valgrind

##### 4.1

- 执行下列命令

  ```bash
  gcc -g -Wall one.c
  valgrind ./a.out
  ```
  
- valgrind分析显示 程序存在无效的内存写入操作（4字节），因为malloc只分配了40字节的内存而程序在40字节以外的位置进行了写的操作。堆分析显示分配了一个堆，并得到了释放，大小为40字节，不存在内存泄漏。

- ![image-20220420205512932](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220420205512932.png)

##### 4.2

- 程序在第14行进行了对未初始化值的打印操作 分析可知是a[9]未进行赋值。

  ![image-20220420204955615](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220420204955615.png)

- 堆使用情况：分配并释放了一个堆，共1024字节内存空间，不存在内存泄漏。

##### 4.3

- 程序共使用了 10个堆，共分配了4000字节内存，但只释放了一个。

  ![image-20220420205939677](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220420205939677.png)

- 分析代码发现，在每次循环中都分配了400字节的内存空间，但在循环中并未进行内存释放，只有出循环后对最后一次分配进行了释放，故存在9次共3600字节的内存泄漏。

  ![image-20220420210000112](https://raw.githubusercontent.com/PerhapsChen/picgo_pic/main/image-20220420210000112.png)