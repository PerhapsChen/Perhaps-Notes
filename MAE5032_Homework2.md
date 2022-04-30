## Homework2

#### 12132191 陈鹏翰

#### 1

**(a)What are the possible ways of mapping from the main memory to the cache?**

Direct; Set associative; fully associative

**(b) Design a code example that will lead to conflicts in the direct mapping approach.**

****Assuming that cache size is 2^16 bytes, a[0] and b[0] are mapped to the same cache location, cache line is 2^5 bytes , so the number of cache lines is 2^11, the following program will lead to conflicts.**

Firstly, b[0] b[1] b[2] b[3] will be loaded in cache, when assigning values to a[0], a[0] a[1] a[2] a[3] will be loaded to the same location in cache, which will lead to conflict.

```c
#include <stdio.h>
int main()
{
    int i;
	int n = 8192;
	double a[8192], b[8192];
    //assuming a[0] and b[0] are mapped to the same cache location
	for (i=0; i<n; i++)
	{
   	 	a[i] = b[i];
	}
    return 0;
}
```

**(c) Design a code example that will lead to conflicts in the 2-way set-associative approach**

Assuming that cache size is 2^16 bytes, cache line is 2^5 bytes, 2-way set-associative results in 2^10 cache lines. 

Firstly, b[0] b[1] b[2] b[3]  and  c[0] c[1] c[2] c[3] will be loaded in a same cache set, when assigning the sum values to a[0], a[0] a[1] a[2] a[3] will be loaded to the same set in cache, but all space the set have been used, which will lead to conflict.

```C
#include <stdio.h>
int main()
{
    int i;
	int n = 8192;
	double a[8192], b[8192], c[8192];
    //assuming a[0], b[0] and c[0] are mapped to the same cache set
	for (i=0; i<n; i++)
	{
   	 	a[i] = b[i] + c[i];
	}
    return 0;
}
```



#### 2

**What are latency and bandwidth? Suppose data transfer from the main memory to the L2 cache has a latency of 80 ns and its bandwidth is 4.8 GB/s, what is the maximum amount of data it can transfer per second?**

Latency is the delay between the processor issuing a request for a memory item, and the item actually arriving.

Bandwidth is the rate at which data arrives at its destination.
$$
(1s-80ns)\times 4.8GB/s=4.79999616GB
$$
The maximum amount of data it can transfer per second is 4.79999616GB



#### 3 

**Explain the following command. ```nm -o /lib/* /usr/lib/* 2> /dev/null | grep 'cos$'```**

Redirection all file names and information of the library files  which contain field(such as function, variable, etc.) ending with string "cos" in path `/lib` and `/usr/lib`  to `/dev/null`, i.e., discard.



#### 4

**Consider the code abc.c attached in this homework. Do the following using your own machine with gcc**

**(a)Add a timing function (use functions in time.h) to calculate the time in seconds for the loops that perform operations on arrays**

```C
#include <stdio.h>
#include <sys/time.h>

#define ARRAY_SIZE 2048LL
#define NUMBER_OF_TRIALS 1000000LL

int main(int argc, char *argv[]) {
    // Declare arrays small enough to stay in L1 cache.
    // Assume the compiler aligns them correctly.
    double a[ARRAY_SIZE], b[ARRAY_SIZE], c[ARRAY_SIZE];
    const double m = 1.5;
    struct timeval tm1, tm2;
    double pass;
    int i, t;
    long long operations;
    // Initialize a, b and c arrays
    for (i = 0; i < ARRAY_SIZE; i++) {
        a[i] = 0.0;
        b[i] = i * 1.0e-9;
        c[i] = i * 0.5e-9;
    }

    gettimeofday(&tm1, NULL);
    // Perform operations with arrays many, many times
    for (t = 0; t < NUMBER_OF_TRIALS; t++) {
        for (i = 0; i < ARRAY_SIZE; i++) {
            a[i] += m * b[i] + c[i];
        }
    }
    gettimeofday(&tm2, NULL);
    // Print a result so the loops won't be optimized away
    double d = 0.0;
    for (i = 0; i < ARRAY_SIZE; i++) d += a[i];

    printf("%f\n", d / ARRAY_SIZE);
    pass = (double) tm2.tv_sec - (double) tm1.tv_sec + 1e-6 * (tm2.tv_usec - tm1.tv_usec);
    operations = (long long)(3 * NUMBER_OF_TRIALS * ARRAY_SIZE / pass);
    printf("The loops spent %f seconds.\n", pass);
    printf("There are %lld floating point operations per second.\n", operations);

    return 0;
}
```

**(b) Based on the timing, print on screen the time and floating-point operation per second on screen**

```
2.047000
The loops spent 3.823644 seconds.
There are 1606844151 floating point operations per second.
```

**(c) Invoke your compiler with no special flags and run. Report the compiler and your compiling command**

```
(base) cph@cphubuntu:~/Documents/mse$ gcc --version
gcc (Ubuntu 7.5.0-3ubuntu1~18.04) 7.5.0
Copyright (C) 2017 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

(base) cph@cphubuntu:~/Documents/mse$ gcc abc.c
(base) cph@cphubuntu:~/Documents/mse$ ./a.out 
2.047000
The loops spent 3.703910 seconds.
There are 1658787605 floating point operations per second.
```

**(d) Repeat the process with -O0, -O1, -O2, -O3, -O3 -msse3, -O3 -march=native, -O3 -mavx2, -O3 -fno-tree-vectorize and report the results.**

```
(base) cph@cphubuntu:~/Documents/mse$ gcc -O0 abc.c -o obj0
(base) cph@cphubuntu:~/Documents/mse$ ./obj0 
2.047000
The loops spent 3.674249 seconds.
There are 1672178450 floating point operations per second.
```

```
(base) cph@cphubuntu:~/Documents/mse$ gcc -O1 abc.c -o obj1
(base) cph@cphubuntu:~/Documents/mse$ ./obj1
2.047000
The loops spent 1.012445 seconds.
There are 6068477793 floating point operations per second.
```

```
(base) cph@cphubuntu:~/Documents/mse$ gcc -O2 abc.c -o obj2
(base) cph@cphubuntu:~/Documents/mse$ ./obj2
2.047000
The loops spent 1.040561 seconds.
There are 5904507280 floating point operations per second.
```

```
(base) cph@cphubuntu:~/Documents/mse$ gcc -O3 abc.c -o obj3
(base) cph@cphubuntu:~/Documents/mse$ ./obj3
2.047000
The loops spent 0.675644 seconds.
There are 9093546305 floating point operations per second.
```

```
(base) cph@cphubuntu:~/Documents/mse$ gcc -O3 -msse3 abc.c -o obj3msse3
(base) cph@cphubuntu:~/Documents/mse$ ./obj3msse3 
2.047000
The loops spent 0.681323 seconds.
There are 9017749290 floating point operations per second.
```

```
(base) cph@cphubuntu:~/Documents/mse$ gcc -O3 -march=native abc.c -o obj3native
(base) cph@cphubuntu:~/Documents/mse$ ./obj3native 
2.047000
The loops spent 0.361675 seconds.
There are 16987627013 floating point operations per second.
```

```
(base) cph@cphubuntu:~/Documents/mse$ gcc -O3 -mavx2 abc.c -o obj3mavx2
(base) cph@cphubuntu:~/Documents/mse$ ./obj3mavx2 
2.047000
The loops spent 0.349825 seconds.
There are 17563067247 floating point operations per second.
```

```
(base) cph@cphubuntu:~/Documents/mse$ gcc -O3 -fno-tree-vectorize abc.c -o obj3fno
(base) cph@cphubuntu:~/Documents/mse$ ./obj3fno 
2.047000
The loops spent 1.053310 seconds.
There are 5833040605 floating point operations per second
```

(e1) Determine the architecture of your computer and choose appropriate option for -march=

```
(base) cph@cphubuntu:~/Documents/mse$ gcc -O3 -march=corei7-avx abc.c -o obj3_core
(base) cph@cphubuntu:~/Documents/mse$ ./obj3_core 
2.047000
The loops spent 0.354073 seconds.
There are 17352353893 floating point operations per second.
```

**(e2) Recompile the code with -O2 along with optimization reporting -ftree-vectorizer-verbose to confirm the loops are vectorized.**

```
(base) cph@cphubuntu:~/Documents/mse$ gcc -O2 -ftree-vectorize -fopt-info-vec-optimized abc.c -o obj2ff
abc.c:26:9: note: loop vectorized
abc.c:17:5: note: loop vectorized
```

```
(base) cph@cphubuntu:~/Documents/mse$ ./obj2ff
2.047000
The loops spent 0.652945 seconds.
There are 9409674628 floating point operations per second.
```

**(f) Repeat (e) with -fno-tree-vectorize, confirm the loop vectorization is turned off.**

```
(base) cph@cphubuntu:~/Documents/mse$ gcc -O2 -fno-tree-vectorize -fopt-info-vec-optimized abc.c -o obj2ffno
(base) cph@cphubuntu:~/Documents/mse$ ./obj2ffno
2.047000
The loops spent 1.051814 seconds.
There are 5841336966 floating point operations per second.
```

**(g) Make a copy of abc.c to dep.c and edit it by changing the innermost loops to** 

```C
for(i=1; i<ARRAY_SIZE; ++i)
	a[i] += m*b[i] + a[i-1];
```

​	**Compile the code with vectorizatioin enabled and request a report with info on loops that missed out.**

```
(base) cph@cphubuntu:~/Documents/mse$ gcc -O2 -ftree-vectorize -fopt-info-vec-optimized dep.c -o dep
dep.c:26:9: note: loop vectorized
dep.c:17:5: note: loop vectorized

(base) cph@cphubuntu:~/Documents/mse$ ./dep
2.047500
The loops spent 0.724087 seconds.
There are 8485168218 floating point operations per second.
```

