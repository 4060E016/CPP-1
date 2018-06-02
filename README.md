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

# CPP

3.27:10隻程式c  vs c++  vs Java

# 4.10:要教array(陣列)與矩陣(matrix)運算

矩陣運算:相加/相乘/反矩陣?/

應用:

# 4.17:要教結構體struct+指標

# 4.24:物件導向

# template(範本)與泛型程式開發

# C++ STL
