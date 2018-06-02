# C variable

[Global variable全域變數](https://en.wikipedia.org/wiki/Global_variable)

```
#include <stdio.h>

static int shared = 3;     /* This is the file-scope variable (with 
                            * internal linkage), visible only in 
                            * this compilation unit. 
                            */
extern int overShared = 1; /* This one has external linkage (not
                            * limited to this compilation unit). 
                            */
int overSharedToo = 2;      /* Also external linkage
                            */

static void changeShared(void)
{
    shared = 5; /* Reference to the file-scope 
                 * variable in a function.
                 */
}

static void localShadow(void)
{
    int shared; /* local variable that will hide 
                 * the global of the same name
                 */

    shared = 1000; /* This will affect only the local variable and will
                    * have no effect on the file-scope variable of the
                    * same name. 
                    */
}

static void paramShadow(int shared)
{
    shared = -shared; /* This will affect only the parameter and will have no
                       * effect on the file-scope variable of the same name. 
                       */
}

int main(void)
{

    printf("%d\n", shared); /* Reference to the file-scope 
                             * variable.
                             */
    changeShared();
    printf("%d\n", shared);

    localShadow();
    printf("%d\n", shared);

    paramShadow(1);
    printf("%d\n", shared);

    return 0;
}

```


# heap vs stack


https://www.gribblelab.org/CBootCamp/7_Memory_Stack_vs_Heap.html

```
#include <stdio.h>

double multiplyByTwo (double input) {
  double twice = input * 2.0; //twice是一個double資料型態的區域變數,會被放在Stack上
  return twice;
}

int main (int argc, char *argv[])
{
  int age = 30;//age是一個int資料型態的區域變數,會被放在Stack上
  double salary = 12345.67;//salary是一個double資料型態的區域變數,會被放在Stack上
  double myList[3] = {1.2, 2.3, 3.4};//myList是一個double資料型態的區域變數,會被放在Stack上

//These three variables are pushed onto the stack as soon as the main() function allocates them. 
//When the main() function exits (and the program stops) these variables are popped off of the stack.

  printf("double your salary is %.3f\n", multiplyByTwo(salary));

  return 0;
}

```

there is a way to tell C to keep a stack variable around, even after its creator function exits, and that is to use the static keyword when declaring the variable. A variable declared with the static keyword thus becomes something like a global variable, but one that is only visible inside the function that created it. It's a strange construction, one that you probably won't need except under very specific circumstances.

```
#include <stdio.h>
#include <stdlib.h>

double *multiplyByTwo (double *input) {
  double *twice = malloc(sizeof(double));//twice是double型態的一個指標變數(pointer)
  *twice = *input * 2.0;
  return twice;
}

int main (int argc, char *argv[])
{
  int *age = malloc(sizeof(int));
  *age = 30;
  double *salary = malloc(sizeof(double));
  *salary = 12345.67;
  double *myList = malloc(3 * sizeof(double));
  myList[0] = 1.2;
  myList[1] = 2.3;
  myList[2] = 3.4;

  double *twiceSalary = multiplyByTwo(salary);

  printf("double your salary is %.3f\n", *twiceSalary);

  free(age);
  free(salary);
  free(myList);
  free(twiceSalary);

  return 0;
}
```

### heap overflow

>* https://medium.com/@ktecv2000/heap-exploit-%E5%AD%B8%E7%BF%92%E7%AD%86%E8%A8%98-d724d0afa59b
>* malloc所分配的記憶體單位為chunk
>* 要求系統動態配置記憶體,很小的記憶體 vs 普通的記憶體 vs 很大的記憶體  會怎麼分配??

chunk定義如下：
```
struct chunk {
    int prev_size;
    int size;
    struct chunk *fd;  // forward pointer
    struct chunk *bk;  // backward pointer
};
```
>* 未分配的free chunks是用double linked-list串起來的
>* 已被分配的chunk不會用到後兩個pointer，而是直接用來存資料


### Exploiting the heap

>* https://www.win.tue.nl/~aeb/linux/hh/hh-11.html

heapbug.c
```
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

int main(int argc, char **argv) {
        char *p, *q;

        p = malloc(1024);
        q = malloc(1024);
        if (argc >= 2)
                strcpy(p, argv[1]);
        free(q);
        free(p);
        return 0;
}
```

```
% ./heapbug `perl -e 'print "A"x5000'`
Segmentation fault
```

We would like to spawn a shell from this buggy program

ltrace ./heapbug `perl -e 'print "A"x2368'`

ltrace ./heapbug `perl -e 'print "A"x2367'`

### Memory Layout of C Programs

>* https://www.geeksforgeeks.org/?p=14268

##### DEMO1:
```
#include <stdio.h>
 
int main(void)
{
    return 0;
}
```

```
gcc memory-layout.c -o memory-layout

size memory-layout
```
```
text       data        bss        dec        hex    filename
960        248          8       1216        4c0    memory-layout
```
##### DEMO2:
```
#include <stdio.h>
 
int global; /* Uninitialized variable stored in bss*/
 
int main(void)
{
    return 0;
}
```
```
text       data        bss        dec        hex    filename
 960        248         12       1220        4c4    memory-layout
```
##### DEMO3:

```
#include <stdio.h>
 
int global; /* Uninitialized variable stored in bss*/
 
int main(void)
{
    static int i; /* Uninitialized static variable stored in bss */
    return 0;
}
```

```
text       data        bss        dec        hex    filename
 960        248         16       1224        4c8    memory-layout
```
##### DEMO4:

```
#include <stdio.h>
 
int global; /* Uninitialized variable stored in bss*/
 
int main(void)
{
    static int i = 100; /* Initialized static variable stored in DS[Data Segment (DS)]*/
    return 0;
}

```
```
text       data        bss        dec        hex    filename
960         252         12       1224        4c8    memory-layout
```

##### DEMO5:

```
#include <stdio.h>
 
int global = 10; /* initialized global variable stored in DS*/
 
int main(void)
{
    static int i = 100; /* Initialized static variable stored in DS*/
    return 0;
}

```

```
text       data        bss        dec        hex    filename
960         256          8       1224        4c8    memory-layout
```

```
http://en.wikipedia.org/wiki/Data_segment
http://en.wikipedia.org/wiki/Code_segment
http://en.wikipedia.org/wiki/.bss
http://www.amazon.com/Advanced-Programming-UNIX-Environment-2nd/dp/0201433079
```

# CPP

#### 三種記憶體區間: global、stack、heap

>* http://wp.mlab.tw/?p=312
>* stack與heap在記憶體中的配置狀況可以參考這張圖(http://www.geeksforgeeks.org/archives/14268):
```
#include<iostream>
using namespace std;
int main(){
    int a1,a2,a3,a4,a5;
    
    int *b1=new int;
    int *b2=new int;
    int *b3=new int;
    int *b4=new int;
    
    cout << "address of a1 is " << &a1 << endl;
    cout << "address of a2 is " << &a2 << endl;
    cout << "address of a3 is " << &a3 << endl;
    cout << "address of a4 is " << &a4 << endl;
    cout << "address of a5 is " << &a5 << endl;
    
    cout << "address of b1 is " << &b1 << endl;
    cout << "  value of b1 is " << b1 << endl;
    cout << "address of b2 is " << &b2 << endl;
    cout << "  value of b2 is " << b2 << endl;
    cout << "address of b3 is " << &b3 << endl;
    cout << "  value of b3 is " << b3 << endl;
    cout << "address of b4 is " << &b4 << endl;
    cout << "  value of b4 is " << b4 << endl;
 
    delete b1,b2,b3,b4;
}
```

```

a1到a5的記憶體位址是由大而小，也就是由高而低。
b1到b4的所指的位址(在heap)是由小而大，也就是由低而高，b1到b4本身的位址(在stack)則是由高而低。

```

參考資料來源：

```

http://blog.pfan.cn/xman/38791.html

http://antrash.pixnet.net/blog/post/70456505-stack-vs-heap%EF%BC%9A%E5%9F%B7%E8%A1%8C%E6%99%82%E6%9C%9F%E5%84%B2%E5%AD%98%E5%85%A9%E5%A4%A7%E8%A6%81%E8%A7%92

http://www.geeksforgeeks.org/archives/14268

```

3.27:10隻程式c  vs c++  vs Java

# 4.10:要教array(陣列)與矩陣(matrix)運算

矩陣運算:相加/相乘/反矩陣?/

應用:

# 4.17:要教結構體struct+指標

# 4.24:物件導向

# template(範本)與泛型程式開發

# C++ STL
