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
### 12.17
- (a) 不合法，必须绑定到一个new返回的指针上
- (b) 能通过编译，但可能出错。因为pi不是new出来的，因此在离开作用域后指针调用delete时会报错。
- (c) 能通过编译，但可能出错。因为当p2离开作用域后会delete掉动态内存，此时pi2就是空置指针了。
- (d) 能通过编译，但可能出错。因为&ix不是new出来的，因此在离开作用域后指针调用delete时会报错。
- (e) 合法。
- (f) 能通过编译，但可能出错。当P2或P5delete动态内存后，另一个指针就是空置指针了。还会引发二次delete的问题。

### 12.18
> elease操作将释放指向的对象并返回指针，而share_ptr可能拥有多个指向同一个对象的指针，此时就会导致其他指针成为空置指针。

### 12.19
```c++
#include <string>
#include <vector>
#include <memory>
#include <exception>
class StrBlobPtr;

class StrBlob {
	friend class StrBlobPtr;
public:
	typedef std::vector<std::string>::size_type size_type;
	StrBlob();
	StrBlob(std::initializer_list<std::string> il);
	size_type size() const { return data->size(); }
	bool empty() const { return data->empty(); }
	// 添加删减元素
	void push_back(const std::string &t) { data->push_back(t); }
	void pop_back() 
	{check(0, "pop_back on empty StrBlob");
		data->pop_back();}	
	// 元素访问
	std::string& front();
	const std::string& front() const;
	std::string& back();
	const std::string& back() const;
	StrBlobPtr begin();
	StrBlobPtr end();
private:
	std::shared_ptr < std::vector<std::string>> data;
	// 如果data[i]不合法，抛出一个异常
	void check(size_type i, const std::string &msg) const;
};

StrBlob::StrBlob() : data(std::make_shared<std::vector<std::string>>()) { }
StrBlob::StrBlob(std::initializer_list<std::string> il) : data(std::make_shared<std::vector<std::string>>(il)) { }

void StrBlob::check(size_type i, const std::string &msg) const {
	if (i >= data->size())
		throw std::out_of_range(msg);
}

std::string& StrBlob::front(){
	// 如果vector为空， check会抛出一个异常
	check(0, "front on empty StrBolb");
	return data->front();
}

std::string& StrBlob::back() {
	check(0, "back on empty StrBlob");
	return data->back();
}

const std::string& StrBlob::front() const {
	// 如果vector为空， check会抛出一个异常
	check(0, "front on empty StrBolb");
	return data->front();
}

const std::string& StrBlob::back() const {
	check(0, "back on empty StrBlob");
	return data->back();
}


class StrBlobPtr {
public:
	StrBlobPtr() : curr(0) {}
	StrBlobPtr(StrBlob &a, std::size_t sz = 0) : wptr(a.data), curr(sz) {}
	std::string& deref() const;
	StrBlobPtr& incr(); //前缀递增
	bool operator!=(const StrBlobPtr &a) { return a.curr != curr; }
private:
	std::shared_ptr<std::vector<std::string>> check(std::size_t, const std::string&) const;
	std::weak_ptr<std::vector<std::string>> wptr;
	std::size_t curr;
};

std::shared_ptr<std::vector<std::string>> StrBlobPtr::check(std::size_t i, const std::string &msg) const {
	auto ret = wptr.lock();    // vector还存在吗
	if (!ret)
		throw std::runtime_error("unbound StrBlobPtr");
	if (i >= ret->size())
		throw std::out_of_range(msg);
	return ret;    // 一切正常，返回指向vector的shared_ptr
}

std::string& StrBlobPtr::deref() const {
	auto p = check(curr, "dereference past end");
	return (*p)[curr];
}

StrBlobPtr& StrBlobPtr::incr() {    // 前缀递增，返回递增后对象的引用
	check(curr, "increment past end of StrBlobPtr");
	++curr;
	return *this;
}

StrBlobPtr StrBlob::begin(){
	return StrBlobPtr(*this); 
}

StrBlobPtr StrBlob::end() {
	return StrBlobPtr(*this, data->size()); 
}
```
### 12.30
```c++
#ifndef TEXTQUERY_H_INCLUDED
#define TEXTQUERY_H_INCLUDED
 
#include<vector>
#include<fstream>
#include<memory>
#include<sstream>
#include<string>
#include<map>
#include<set>
 
using namespace std;
 
class QueryResult; //前向声明
 
class TextQuery{
public:
    //类型别名
    typedef vector<string>::size_type line_no;
    //构造函数。
    TextQuery( ifstream& );
    QueryResult query( const string& ) const;
private:
    shared_ptr<vector<string>> file;
    map<string, shared_ptr<set<line_no>>> wordMap;
 
};
 
//构造函数定义。读取文件按行存入vector，并且建立单词与行号映射。
TextQuery::TextQuery( ifstream &fin ): file( new vector<string> )
{
    string line;
    //为了使得行号从1开始对应。我把vector的位置0先设为空串。
    file->push_back( "" );
    while( getline( fin, line ) ){
        file->push_back( line );
        unsigned lineN = file->size() - 1; //lineN用来保存行号。
        istringstream sin( line );
        string word;
        while( sin >> word ){
            auto &lines = wordMap[ word ]; //lines是一个shared_ptr
            if( !lines )
                lines.reset(  new set<line_no> ); //如果lines为空指针（set没有元素）,分配一个新的set
            lines->insert( lineN );
        }
    }
}
 
 
class QueryResult{
    friend ostream& print( ostream&, const QueryResult& );
public:
    QueryResult( const string& str, shared_ptr<set<TextQuery::line_no>> l,
                shared_ptr<vector<string>> f ): sought( str ), lines( l ), file( f ) { }
private:
    string sought;
    shared_ptr<set<TextQuery::line_no>> lines;
    shared_ptr<vector<string>> file;
 
};
 
QueryResult TextQuery::query( const string &s ) const
{
    static shared_ptr<set<line_no>> nodata( new set<line_no> );
 
    auto loc = wordMap.find( s );
    if( loc == wordMap.end() )
        return QueryResult( s, nodata, file );
    else
        return QueryResult( s, loc->second, file );
}
 
ostream& print( ostream &os, const QueryResult &qr )
{
    os << qr.sought << " occurs " << qr.lines->size()
        << ( ( qr.lines->size() > 1 ) ? " times" : " time" )
        << endl;
    for( auto num : *( qr.lines ) )
        os << "\t(line " << num << ") " <<  *( qr.file->begin() + num ) << endl;
    return os;
}
 
#endif // TEXTQUERY_H_INCLUDED
#include<iostream>
#include"TextQuery.h"
#include<fstream>
 
using namespace std;
 
int main()
{
    ifstream fin;
    fin.open( "T11_9_data.txt" );
    if( !fin ){
        cerr << "cant open file!";
        return -1;
    }
    TextQuery tq( fin );
    print( cout, tq.query( "me" ) );
 
    return 0;
}
```

---

