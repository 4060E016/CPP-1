# 檔案的輸入與輸出
```
13.2.1把資料寫到檔案
13.2.2從檔案裡讀出資料
13.2.3
13.2.4
13.2.5

13.3  格式化輸出

13.4 getline(),get(),put()
```

## NEXT
```
本課程主要教一般文字檔案的讀寫
```
```
各種類型的檔案讀寫:
binary 檔案的讀寫
CSV 檔案的讀寫
jpg 檔案的讀寫
mp3 檔案的讀寫
.......................
```

## 使用的類別

>* 使用 ofstream 將資料寫入於檔案
>* 使用 ifstream 從檔案讀取資料
>* 使用 fstream 建立輸入與輸出串流。
>* 若程式在輸入與輸出需要使用相同的串流物件，則使用 fstream 較為方便。
>* 打開 fstream 檔案，必須指定檔案開啟模式(file open mode)，以告訴 C++ 編譯器檔案是如何使用的。

### 13.2.1把資料寫到檔案
```
#include <iostream>
#include <fstream>
using namespace std;

int main()
{
  ofstream output;

 // Create a file
  output.open("d:///scores.txt");
  //output.open("scores.txt");

  // Write lines
  output << "John" << " " << "T" << " " << "Smith" 
    << " " << 90 << endl;
  output << "Eric" << " " << "K" << " " << "Jones" 
    << " " << 85 << endl;
  output << "天王" << " " << "G" << " " << "大大" 
    << " " << 95 << endl;
    
  output.close();

  cout << "Done" << endl;

  return 0;
}
```

### 13.2.2從檔案裡讀出資料
```
#include <iostream>
#include <fstream>
#include <string>
using namespace std;

int main()
{
  ifstream input("scores.txt");

  // Read data
  string firstName;
  char mi;
  string lastName;
  int score;
  input >> firstName >> mi >> lastName >> score;
  cout << firstName << " " << mi << " " << lastName << " "
    << score << endl;

  input >> firstName >> mi >> lastName >> score;
  cout << firstName << " " << mi << " " << lastName << " "
    << score << endl;

  input.close();

  cout << "Done" << endl;

  return 0;
}

```

### 13.2.3 檢查檔案是否存在==>使用fail()

若讀取的檔案不存在，程式可執行但會產生不正確的結果。

程式如何檢查檔案是否存在嗎? 
>* 呼叫 open 函式後立即呼叫 fail() 函式。
>* 若 fail() 函式回傳 true，則表示此檔案不存在。

### 13.2.4

```
#include <iostream>
#include <fstream>
using namespace std;

int main()
{
  // Open a file
  ifstream input("score.txt");
  
  // 檢查檔案是否存在==>使用fail()
  if (input.fail())
  {
    cout << "File does not exist" << endl;
    cout << "Exit program" << endl;
    return 0;
  }

  double sum = 0;
  double number;
  
  //參看課本不同做法與錯誤分析
  while (input >> number) // Read data to the end of file
  {
    cout << number << " "; // Display data
    sum += number;
  }
  //
  input.close();

  cout << "\nTotal is " << sum << endl;

  return 0;
}
```

### 13.2.5

```
#include <iostream>
#include <fstream>
#include <string>
using namespace std;

int main()
{
  string filename;
  cout << "Enter a file name: ";
  cin >> filename;

  ifstream input(filename.c_str());
  
  if (input.fail())
    cout << filename << " does not exist" << endl;
  else
    cout << filename << " exists" << endl;

  return 0;
}
```

## 13.3  格式化輸出
  
```
  John T Smith   90
  Eric K Jones   85
   
```

```
#include <iostream>
#include <iomanip>
#include <fstream>
using namespace std;

int main()
{
  ofstream output;

  // Create a file
  output.open("formattedscores.txt");

  // Write two lines
  output << setw(6) << "John" << setw(2) << "T" << setw(6) << "Smith"
    << " " << setw(4) << 90 << endl;
  output << setw(6) << "Eric" << setw(2) << "K" << setw(6) << "Jones"
    << " " << setw(4) << 85;

  output.close();

  cout << "Done" << endl;

  return 0;
}
```


## 13.4 getline(),get(),put()

## 13.
```
include <iostream>
#include <fstream>
#include <string>
using namespace std;

int main()
{
  //使用fstream 類別
  fstream inout;

  // Create a file==>檔案模式 ios::out參看課本說明
  inout.open("city.txt", ios::out);

  // Write cities
  inout << "Dallas" << " " << "Houston" << " " << "Atlanta" << " ";

  inout.close();

  // Append to the file==>檔案模式 ios::app參看課本說明
  inout.open("city.txt", ios::out | ios::app);

  // Write cities
  inout << "Savannah" << " " << "Austin" << " " << "Chicago";

  inout.close();

  string city;

  // Open the file
  inout.open("city.txt", ios::in);
  while (!inout.eof()) // Continue if not end of file
  {
    inout >> city;
    cout << city << " ";
  }

  inout.close();

  return 0;
} 

```
