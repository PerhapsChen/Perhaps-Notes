# Homework7

> 12132191 cph 

### 1.

下载paraview，进入bin目录执行paraview可执行文件

![image-20220531224447038](https://perhaps-1306015279.cos.ap-guangzhou.myqcloud.com/image-20220531224447038.png)

-

![image-20220531225443634](https://perhaps-1306015279.cos.ap-guangzhou.myqcloud.com/image-20220531225443634.png)

### 2.

进入vtk_tutorial-code文件夹，新建build文件夹

修改CMakeLists.txt 中VTK_DIR变量为vtk的build目录。

```cmake
PROJECT(vtk-demo)
CMAKE_MINIMUM_REQUIRED(VERSION 3.10)

SET(VTK_DIR /home/cph/Software/vtk_build)
FIND_PACKAGE(VTK COMPONENTS vtkCommonCore vtkCommonSystem
  vtkCommonMisc vtkCommonMath vtkIOCore vtkIOLegacy vtkIOXML REQUIRED)

INCLUDE_DIRECTORIES(${VTK_INCLUDE_DIRS})
ADD_EXECUTABLE( vtkdemo main.cpp )
TARGET_LINK_LIBRARIES( vtkdemo ${VTK_LIBRARIES} )
```

进入build文件夹，执行 `cmake .. && make`

![](https://perhaps-1306015279.cos.ap-guangzhou.myqcloud.com/image-20220531224553707.png)

执行 `vtkdemo`可执行文件，生成`te-linear.vtk`文件，在paraview中打开，如题目1中所示。

**代码主要做的事情是：根据5个点的坐标及其索引，生成2个四面体，然后将这2个四面体附加到grid中，并输出为vtk文件**

以下是主要.cpp文件中的相关解释

![code1](https://perhaps-1306015279.cos.ap-guangzhou.myqcloud.com/code1.png)

### 3.

针对.vtk文件每一行的解释

```python
# vtk DataFile Version 4.2	# vtk数据文件版本
vtk output					# 来源是vtk程序的输出
ASCII						# 文件类型为ASCII
DATASET UNSTRUCTURED_GRID	# 数据集结构是 非结构化网格
POINTS 5 double				# 5个点的坐标
0 0 0 1 0 0 0 1 0 			# 每3个数为一个点的坐标	
0 0 1 1 1 1 				
CELLS 2 10					# 有2个cell，占用10个int空间
4 0 1 2 3 					# 第一个cell的点个数及点索引
4 1 2 3 4 					# 第二个cell的点个数及点索引

CELL_TYPES 2				# 有2个cell 与第8行相同
10							# VTK_TETRA的类型编号
10							# VTK_TETRA的类型编号

# 数据集的其他属性数据
# 例如全局多面体的id，点的id，点的颜色等
CELL_DATA 2					# cell数据 2个
FIELD FieldData 1			# 字段数据 1个
GlobalCellID 1 2 int		# 字段名，范围，数据类型
1 2 						# 数据
POINT_DATA 5				# 点数据 5个
FIELD FieldData 1			# 字段数据 1个
GlobalNodeID 1 5 int		# 字段名，范围，数据类型
1 2 3 4 5 					# 数据
```

### 4.

生成的.vtk文件用paraview打开 (n=7)

![image-20220531224937536](https://perhaps-1306015279.cos.ap-guangzhou.myqcloud.com/image-20220531224937536.png)

源码如下，相关解释见注释





