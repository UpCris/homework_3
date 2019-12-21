# homework_3
---
## 第 16 章
---
### 16.11

类模板的名字不是一个类型名，只有实例化后才能形成类型，而实例化是要提供模板实参的。因此，在上述代码中直接使用`ListItem`是错误的，应该使用`ListItem<elemType>`可简化为`List`。

### 16.12 

```C++  
  1 #include <iostream>
  2 #include <vector>
  3 #include <algorithm>
  4 //#include <iterator>
  5 using namespace std;
  6 
  7 template <typename Iter> typename Iter::value_type findMost(Iter first,Iter last)
  8 {
  9     std::size_t amount=0;
 10     Iter start=first;
 11     while(start != last)
 12     {
 13         amount++;
 14         start++;
 15     }
 16     typedef std::vector<typename Iter::value_type> VecType;
 17 
 18     VecType vec(amount);
 19 
 20     typename VecType::iterator newFirst=vec.begin();
 21     typename VecType::iterator newLast=vec.end();
 22 
 23     std::uninitialized_copy(first,last,newFirst);
 24     sort(newFirst,newLast);
 25 
 26     size_t maxOccu=0,occu=0;
 27 
 28     typename VecType::iterator preIter=newFirst;
 29     typename VecType::iterator maxOccuElemIt=newFirst;
 30     while(newFirst != newLast)
 31     {
 32         if(*newFirst != *preIter)
 33         {
 34             if(occu>maxOccu)
 35             {
 36                 maxOccu=occu;
 37                 maxOccuElemIt=preIter;
 38             }
 39             occu=0;
 40         }
 41         ++occu;
 42         preIter=newFirst;
 43         ++newFirst;
 44     }
 45     if(occu>maxOccu)
 46     {
 47         maxOccu=occu;
 48         maxOccuElemIt=preIter;
 49     }
 50     return *maxOccuElemIt;
 51 }
 52 
 53 int main()
 54 {
 55     int ia[]={1,5,1,2,5,2,7,9,5,6,4,6,8,1,5,6,8,3,9,8,7,5,6,1,2,4,5,6,8};
 56     int cnt=sizeof(ia)/sizeof(int);
 57     cout<<"cnt:"<<cnt<<endl;
 58     vector<int> ivec(ia,ia+cnt);
 59     cout<<"most find element is:"<<findMost(ivec.begin(),ivec.end())<<endl;
 60     return 0;
 61 }

```

### 16.19

```C++
/*
 * Copyright
 */

#include<iostream>
#include<string>
#include<vector>

template <typename C>
void print(const C &c) {
    for ( typename C::size_type i = 0; i < c.size(); i++)
        std::cout << c.at(i) << " ";
    std::cout << std::endl;
}

int main() {
    std:: string s = "hello!";
    print(s);

    std:: vector<int> vi = { 0 , 2 , 4 , 6 , 8 };
    print(vi);

    return 0;
}

```
![1619](https://github.com/UpCris/homework_3/blob/master/tst1619.png)

### 16.41
```C++
/*
 * Copyright
*/

#include<iostream>

template <typename T1 , typename T2>
auto sum(T1 a , T2 b) -> decltype(a+b) {
    return a + b;
}

int main() {
    auto a = sum(1 , 1);
    std::cout << a << " " << sizeof(a) << std::endl;
    auto b = sum(1 , 1.1);
    std::cout << b << " " << sizeof(b) << std::endl;
    auto c = sum(1, 1.1f);
    std::cout << c << " " << sizeof(c) << std::endl;

    return 0;
}
```
![1641](https://github.com/UpCris/homework_3/blob/master/1641.png)

### 16.62
```c++
/*
 *Copyright
 */

#include<cstddef>
using std::size_t;

#include<string>
using std::string;

#include<iostream>
using std::cin;
using std::cout;
using std::endl;

#include<unordered_set>
using sdt::unordered_multiset;

#include<functional>

#include "Sales_data.h"
using std::hash;

int main() {
        unordered_multiset<Sales_data> SDset;
        Sales_data item;
        while (cin >> item) {
            SDset.insert(item);
        }
        cout << SDset.size() << endl;
        for (auto sd : SDset)
            cout << sd << endl;
        return 0;
}


```


---
## 第 12 章

### 12.1

- b1有4个元素。

- b2被销毁。但是元素底层元素并没有被销毁。

### 12.10

此调用是正确的。

参数位置的shared_ptr<int>(p)，会创建一个临时的shared_ptr赋予process的参数ptr，引用计数值加1，当调用所在的表达式结束时，这个临时变量就会被销毁，引用计数减1。同时将值拷贝给ptr，计数值加1。但函数结束后ptr被销毁，引用计数减1。

最终p的引用计数还是1。内存不会被释放。

### 12.15

```C++
/*
 *Copyright
 */
 
#include<iostream>
#include<memory>
 
using namespace std;
 
struct destination{ };//表示我们正在连接什么
struct connection{ };//使用连接所需的信息
 
connection connect( destination* );//打开连接
void disconnect( connection );//关闭给定的连接
 
void f( destination& );
void f1( destination & );
 
//void end_connection( connection *p );
 
 
int main() {
    destination d;
    f1( d );
    f( d );
 
    return 0;
}
/*
void end_connection( connection *p )
{
    disconnect( *p );
}
*/
connection connect( destination *pd )
{
    cout << "打开连接" << endl;
    return connection();
}
void disconnect( connection c )
{
    cout << "连接关闭" << endl;
}
 
void f( destination &d )
{
    //获得一个连接
    connection c = connect( &d );
    cout << "用shared_ptr管理connect" << endl;
    shared_ptr<connection> sp( &c, []( connection *p ){ disconnect( *p ); } );
    /* 获得连接后的操作 */
    //忘记调用disconnect
    //当f退出时，不管是正常退出还是异常退出，connection都会被正确关闭。
}
 
void f1( destination &d )
{
    cout << "直接管理connect" << endl;
    connection c = connect( &d );
    //忘记调用disconnect
    cout << endl;
}
```

---

