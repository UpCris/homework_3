# homework_3
---
## 第 16 章
---
### 16.11

类模板的名字不是一个类型名，只有实例化后才能形成类型，而实例化是要提供模板实参的。因此，在上述代码中直接使用`ListItem`是错误的，应该使用`ListItem<elemType>`可简化为`List`。

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
![login](https://github.com/UpCris/homework_3/blob/master/tst1619.png)
