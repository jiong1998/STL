 现在开始进入C++STL学习

# 第一章 STL概论

STL 从广义上分为: 
- 容器(container) 
- 算法(algorithm) 
- 迭代器(iterator)（可以理解成指针）：功能就是**访问容器中的各个元素用的**（也就是**遍历**用的），即提供一种方法，使之能够依序寻访某个容器所含的各个元素，而又无需暴露该容器的内部表示方式。

 STL 几乎所有的代码都采用了模板类或者模板函数，这相比传统的由函数和类组成的库来说提供了更好的代码重用机会。

STL的六大组件：
- 容器：各种数据结构，如vector、list、deque、set、map等,用来存放数据，从实现角度来看，STL容器是一种class template。分为**序列式容器**和**关联式容器**两种。
- 算法：各种常用的算法，如sort、find、copy、for_each。从实现的角度来看，STL算法是一种function tempalte.
- 迭代器（可以理解成指针）：**扮演了容器与算法之间的胶合剂**，共有五种类型，从实现角度来看，迭代器是一种将operator* , operator-> , operator++,operator--等**指针相关操作予以重载的class template.** 所有STL容器都附带有自己专属的迭代器，只有容器的设计者才知道如何遍历自己的元素。原生指针(native pointer)也是一种迭代器。
- 仿函数：行为类似函数，可作为算法的某种策略。从实现角度来看，仿函数是一种重载了operator()的class 或者class template
- 适配器：一种用来修饰容器或者仿函数或迭代器接口的东西。
- 空间配置器：给容器开辟和释放内存。

STL六大组件的交互关系：容器通过空间配置器取得数据存储空间，算法通过迭代器操作容器中的内容，仿函数可以协助算法完成不同的策略的变化，适配器可以修饰仿函数。

## 1. 创建容器、使用迭代器
容器其实是使用类模板的形式构建的。

每个类型的容器都有属于自己的迭代器

容器：
- vector\<int>v

迭代器：
- vector\<int>::iterator itbegin = v.begin(); //v.begin() 起始迭代器，指向容器中第一个数据。
- vector\<int>::iterator itend = v.end(); //v.end()；结束迭代器，指向的是容器中最后一个元素的下一个位置。


代码案例：
```cpp
vector<int>v;

//插入元素
v.push_back(10);
v.push_back(20);
v.push_back(30);
v.push_back(40);

//遍历元素
vector<int>::iterator itbegin = v.begin();//v.begin() 起始迭代器，指向容器中第一个数据

vector<int>::iterator itend = v.end();//v.end()；结束迭代器，指向的是容器中最后一个元素的下一个位置

//第一种遍历方式
while(itbegin!=itend)
{
    cout<<*itbegin<<endl;
    itbegin++;
}
```
## 2. 遍历容器

注意： 上一节中的第一种遍历方式不太常用，只是作为演示迭代器，更常用的是第二种遍历方式，如下所示：
```cpp
//第二种遍历方式
for(vector<int>::iterator it = v.begin();it!=v.end();it++)
{
    cout<<*it<<endl;
}
```

还有第三种遍历方式----利用for_each():
```cpp
//第三种遍历方式
#include <algorithm>
void Myprint(int val)
{
    cout<<val<<endl;
}
for_each(v.begin(),v.end(), Myprint); //Myprint为自己设定的回调函数
```

**注意：如果不知道迭代器 *it是什么,就看\<xx>里是什么，*it就是什么**
&emsp;&emsp;比如：vector\<int>::iterator it，那么*it就是int
## 3. 容器中存放自定义数据类型
```cpp
class Person
{
public:
    Person(string name, int age)
    {
        this->m_age = age;
        this->m_name = name;
    }
    int m_age;
    string m_name;
};

void test2()
{
    vector<Person> v;

    Person p1("aaa", 11);
    Person p2("bbb", 22);
    Person p3("ccc", 33);

    v.push_back(p1);
    v.push_back(p2);
    v.push_back(p3);

    for(vector<Person>::iterator it = v.begin(); it!=v.end();it++)
    {
        //*it 为对象，it为指向指针的指针
        cout<<"年龄："<<(*it).m_age<<"姓名："<<it->m_name<<endl;
    }
}
```
**注意：如果不知道迭代器 *it是什么,就看\<xx>里是什么，*it就是什么**
&emsp;&emsp;比如：vector\<Person>::iterator it，那么*it就是Person的对象。
## 4. 容器嵌套
```cpp
//容器嵌套
void test3()
{
    vector<vector<int>>v;
    vector<int>v1;
    vector<int>v2;
    vector<int>v3;

    for(int i=0;i<5;++i)
    {
        v1.push_back(i);
        v2.push_back(i+10);
        v3.push_back(i+100);
    }

    v.push_back(v1);
    v.push_back(v2);
    v.push_back(v3);

    for(vector<vector<int>>::iterator it=v.begin();it!=v.end();++it)
    {
        for(vector<int>::iterator it1 = (*it).begin();it1!=(*it).end();++it1)
            cout<<*it1<<endl;
    }
}
```
# 第二章 string容器
## string原理

C风格字符串(以空字符结尾的字符数组)太过复杂难于掌握，不适合大程序的开发，所以C++标准库定义了一种string类，定义在头文件<string>。

String和c风格字符串对比：
- Char*是一个指针，**string是一个类**
	- string封装了char*，管理这个字符串，是一个char*型的容器。
- string封装了很多实用的成员方法
	- 查找find，拷贝copy，删除delete 替换replace，插入insert
- 不用考虑内存释放和越界
	-  string管理char*所分配的内存。每一次string的复制，取值都由string类负责维护，不用担心复制越界和取值越界等。
## 1. 常用操作
### 1.1 string 构造函数
```cpp
string();//创建一个空的字符串 例如: string str;      
string(const string& str);//使用一个string对象初始化另一个string对象
string(const char* s);//使用字符串s初始化
string(int n, char c);//使用n个字符c初始化 
```
### 1.2 string基本赋值操作
```cpp
string& operator=(const char* s);//char*类型字符串 赋值给当前的字符串
string& operator=(const string &s);//把字符串s赋给当前的字符串
string& operator=(char c);//字符赋值给当前的字符串
string& assign(const char *s);//把字符串s赋给当前的字符串
string& assign(const char *s, int n);//把字符串s的前n个字符赋给当前的字符串
string& assign(const string &s);//把字符串s赋给当前字符串
string& assign(int n, char c);//用n个字符c赋给当前字符串
string& assign(const string &s, int start, int n);//将s从start开始n个字符赋值给字符串
```
### 1.3 string存取字符操作
```cpp
char& operator[](int n);//通过[]方式取字符
char& at(int n);//通过at方法获取字符
```
注意：[]访问越界会直接挂掉，但是at会抛出 out_of_range异常
### 1.4 string拼接操作 
```cpp
string& operator+=(const string& str);//重载+=操作符
string& operator+=(const char* str);//重载+=操作符
string& operator+=(const char c);//重载+=操作符
string& append(const char *s);//把字符串s连接到当前字符串结尾
string& append(const char *s, int n);//把字符串s的前n个字符连接到当前字符串结尾
string& append(const string &s);//同operator+=()
string& append(const string &s, int pos, int n);//把字符串s中从pos开始的n个字符连接到当前字符串结尾
string& append(int n, char c);//在当前字符串结尾添加n个字符c
```
### 1.5 string查找和替换
```cpp
int find(const string& str, int pos = 0) const; //查找str第一次出现位置,从pos开始查找
int find(const char* s, int pos = 0) const;  //查找s第一次出现位置,从pos开始查找
int find(const char* s, int pos, int n) const;  //从pos位置查找s的前n个字符第一次位置
int find(const char c, int pos = 0) const;  //查找字符c第一次出现位置
int rfind(const string& str, int pos = npos) const;//查找str最后一次位置,从pos开始查找
int rfind(const char* s, int pos = npos) const;//查找s最后一次出现位置,从pos开始查找
int rfind(const char* s, int pos, int n) const;//从pos查找s的前n个字符最后一次位置
int rfind(const char c, int pos = 0) const; //查找字符c最后一次出现位置
string& replace(int pos, int n, const string& str); //替换从pos开始n个字符为字符串str
string& replace(int pos, int n, const char* s); //替换从pos开始的n个字符为字符串s
```
### 1.6 string比较操作
```cpp
/*
compare函数在>时返回 1，<时返回 -1，==时返回 0。
比较区分大小写，比较时参考字典顺序，排越前面的越小。
大写的A比小写的a小。
*/
int compare(const string &s) const;//与字符串s比较
int compare(const char *s) const;//与字符串s比较
```
### 1.7 string获取子串
```cpp
string substr(int pos = 0, int n = npos) const;//返回由pos开始的n个字符组成的字符串
```
### 1.8 string插入和删除操作
```cpp
string& insert(int pos, const char* s); //插入字符串
string& insert(int pos, const string& str); //插入字符串
string& insert(int pos, int n, char c);//在指定位置插入n个字符c
string& erase(int pos, int n = npos);//删除从Pos开始的n个字符
```
### 1.9 string和c-style字符串转换
```cpp
//string 转 char*
string str = "itcast";
const char* cstr = str.c_str();
//char* 转 string 
char* s = "itcast";
string str(s);//string类的有参构造
```

注意：
- const char * 可以隐式类型转换为string
- string不可以隐式类型转换const char * 
### 1.10 常用操作简单总结
存取 [] at
字符串拼接   += append
查找    find   rfind(从右往左查找)
替换    replace
比较    compare
子串    substr
插入   insert
删除 erase
char*和string的转换   

# 第三章 vector容器
## vector原理
Vector容器是单向开口的连续内存空间。vector 可以看作是**动态数组**，当旧空间不够用时，会自动开辟新的更大的内存空间，然后将旧数据移动到新空间上。
![在这里插入图片描述](https://img-blog.csdnimg.cn/0e42a7d944b94b92bed8a2972897e9fc.png)
注意：
&emsp;&emsp;所谓动态增加空间大小，并不是在原空间之后续接新空间(因为无法保证原空间之后尚有可配置的空间)，而是一块更大的内存空间，然后将原数据拷贝新空间，并释放原空间。**因此，对vector的任何操作，一旦引起空间的重新配置，指向原vector的所有迭代器就都失效了**。这是程序员容易犯的一个错误，务必小心。

### vector迭代器：
Vector维护一个线性空间，所以不论元素的型别如何，普通指针都可以作为vector的迭代器，因为vector迭代器所需要的操作行为，如operaroe*, operator->, operator++, operator--, operator+, operator-, operator+=, operator-=, 普通指针天生具备。Vector支持随机存取，而普通指针正有着这样的能力。所以vector提供的是**随机访问迭代器**

注意：**对vector的任何操作，一旦引起空间的重新配置，指向原vector的所有迭代器就都失效了**
## 1. vector构造函数
```cpp
vector<T> v; //采用模板实现类实现，默认构造函数
vector(v.begin(), v.end());//将v[begin(), end())区间中的元素拷贝给本身。
vector(n, elem);//构造函数将n个elem拷贝给本身。
vector(const vector &vec);//拷贝构造函数。
```
示例
```cpp
vector<int>v2(v1.begin(),v1.end())
```
## 2. vector常用赋值操作
```cpp
assign(beg, end);//将[beg, end)区间中的数据拷贝赋值给本身。
assign(n, elem);//将n个elem拷贝赋值给本身。
vector& operator=(const vector  &vec);//重载等号操作符
swap(vec);// 将vec与本身的元素互换。
```
示例
```cpp
vector<int>v3;
v3.assign(v1.begin(),v1.end());
```
## 3. vector大小操作
```cpp
size();//返回容器中元素的个数
empty();//判断容器是否为空
resize(int num);//重新指定容器的长度为num，若容器变长，则以默认值填充新位置。如果容器变短，则末尾超出容器长度的元素被删除。
resize(int num, elem);//重新指定容器的长度为num，若容器变长，则以elem值填充新位置。如果容器变短，则末尾超出容器长>度的元素被删除。
capacity();//容器的容量
reserve(int len);//容器预留len个元素长度，预留位置不初始化，元素不可访问。
```
## 4. vector数据存取操作
```cpp
at(int idx); //返回索引idx所指的数据，如果idx越界，抛出out_of_range异常。
operator[];//返回索引idx所指的数据，越界时，运行直接报错
front();//返回容器中第一个数据元素
back();//返回容器中最后一个数据元素
```
## 5. vector插入和删除操作
```cpp
insert(const_iterator pos, int count,ele);//迭代器指向位置pos插入count个元素ele.
push_back(ele); //尾部插入元素ele
pop_back();//删除最后一个元素
erase(const_iterator start, const_iterator end);//删除迭代器从start到end之间的元素
erase(const_iterator pos);//删除迭代器指向的元素
clear();//删除容器中所有元素
```
# 第四章 deque容器----双端队列
## 1. deque概述
Vector容器是单向开口的连续内存空间，**deque则是一种双向开口的连续线性空间**。所谓的双向开口，意思是可以在头尾两端分别做元素的插入和删除操作，当然，vector容器也可以在头尾两端插入元素，但是在其头部操作效率奇差，无法被接受。
![在这里插入图片描述](https://img-blog.csdnimg.cn/f81ab86c801f4f39b575280cad5d22a1.png)
Deque容器和vector容器最大的差异：
- 一在于deque允许使用常数项时间对**头端**进行元素的插入和删除操作。
- 二在于**deque没有容量的概念**，因为它是动态的以分段连续空间组合而成，**随时可以增加一段新的空间并链接起来**，换句话说，像vector那样，”旧空间不足而重新配置一块更大空间，然后复制元素，再释放旧空间”这样的事情在deque身上是不会发生的。也因此，deque没有必须要提供所谓的空间保留(reserve)功能.

### 1.1 deque迭代器：
虽然deque容器也提供了Random Access Iterator,但是它的迭代器并不是普通的指针，其复杂度和vector不是一个量级，这当然影响各个运算的层面。因此，除非有必要，我们应该尽可能的使用vector，而不是deque。对deque进行的排序操作，为了最高效率，可将deque先完整的复制到一个vector中，对vector容器进行排序，再复制回deque.

## 2. deque原理
Deque是由一段一段的定量的连续空间构成。一旦有必要在deque前端或者尾端增加新的空间，便配置一段连续定量的空间，串接在deque的头端或者尾端。Deque最大的工作就是维护这些分段连续的内存空间的整体性的假象，并提供随机存取的接口，避开了重新配置空间，复制，释放的轮回，代价就是复杂的迭代器架构。

既然deque是分段连续内存空间，那么就必须有中央控制，维持整体连续的假象，数据结构的设计及迭代器的前进后退操作颇为繁琐。Deque代码的实现远比vector或list都多得多。

Deque采取一块所谓的**map**(注意，不是STL的map容器)作为主控，这里所谓的map是一小块连续的内存空间，其中每一个元素(此处成为一个结点)都是一个指针，指向另一段连续性内存空间，称作**缓冲区**。缓冲区才是deque的存储空间的主体。

**deque在我的理解下，就有点像链表+数组的组合，有一块连续空间的数组（称map）存储每个缓冲区的地址，缓冲区才是专门用来存元素的，如下图所示**

![在这里插入图片描述](https://img-blog.csdnimg.cn/fd8b5e40b1ec4edebd4a041a5314885c.png)

## 4. deque构造函数
```cpp
deque<T> deqT;//默认构造形式
deque(beg, end);//构造函数将[beg, end)区间中的元素拷贝给本身。
deque(n, elem);//构造函数将n个elem拷贝给本身。
deque(const deque &deq);//拷贝构造函数。
```
示例
```cpp
deque<int>v2(v1.begin(),v1.end())
```
## 5. deque赋值操作
```cpp
assign(beg, end);//将[beg, end)区间中的数据拷贝赋值给本身。
assign(n, elem);//将n个elem拷贝赋值给本身。
deque& operator=(const deque &deq); //重载等号操作符 
swap(deq);// 将deq与本身的元素互换
```
示例
```cpp
deque<int>v3;
v3.assign(v1.begin(),v1.end());
```
## 6. deque大小操作
```cpp
deque.size();//返回容器中元素的个数
deque.empty();//判断容器是否为空
deque.resize(num);//重新指定容器的长度为num,若容器变长，则以默认值填充新位置。如果容器变短，则末尾超出容器长度的元素被删除。
deque.resize(num, elem); //重新指定容器的长度为num,若容器变长，则以elem值填充新位置,如果容器变短，则末尾超出容器长度的元素被删除。
```
## 7. deque双端插入和删除操作
```cpp
push_back(elem);//在容器尾部添加一个数据
push_front(elem);//在容器头部插入一个数据
pop_back();//删除容器最后一个数据
pop_front();//删除容器第一个数据
```
## 8. deque数据存取
```cpp
at(idx);//返回索引idx所指的数据，如果idx越界，抛出out_of_range。
operator[];//返回索引idx所指的数据，如果idx越界，不抛出异常，直接出错。
front();//返回第一个数据。
back();//返回最后一个数据
```

## 9. deque插入操作
```cpp
insert(pos,elem);//在pos位置插入一个elem元素的拷贝，返回新数据的位置。
insert(pos,n,elem);//在pos位置插入n个elem数据，无返回值。
insert(pos,beg,end);//在pos位置插入[beg,end)区间的数据，无返回值。
```
## 10. deque删除操作
```cpp
clear();//移除容器的所有数据
erase(beg,end);//删除[beg,end)区间的数据，返回下一个数据的位置。
erase(pos);//删除pos位置的数据，返回下一个数据的位置。
```

# 第五章 stack容器
## 1. stack概念
stack是一种先进后出(First In Last Out,FILO)的数据结构，它只有一个出口，形式如图所示。stack容器允许新增元素，移除元素，取得栈顶元素，但是除了最顶端外，没有任何其他方法可以存取stack的其他元素。换言之，stack不允许有遍历行为。

有元素推入栈的操作称为:push,将元素推出stack的操作称为pop.


![在这里插入图片描述](https://img-blog.csdnimg.cn/dd28721917a0440385256b6b48798cc4.png)
**stack没有迭代器!**：
stack所有元素的进出都必须符合”先进后出”的条件，只有stack顶端的元素，才有机会被外界取用。Stack不提供遍历功能，也不提供迭代器。

## 2. stack构造函数
```cpp
stack<T> stkT;//stack采用模板类实现， stack对象的默认构造形式： 
stack(const stack &stk);//拷贝构造函数
```

## 3. stack赋值操作
```cpp
stack& operator=(const stack &stk);//重载等号操作符
```
## 4. stack数据存取操作
```cpp
push(elem);//向栈顶添加元素
pop();//从栈顶移除第一个元素
top();//返回栈顶元素
```
## 5. stack大小操作
```cpp
empty();//判断堆栈是否为空
size();//返回堆栈的大小
```

# 第六章 queue容器----队列
## 1. 基本概念
Queue是一种先进先出(First In First Out,FIFO)的数据结构，它有两个出口，queue容器允许从一端新增元素，从另一端移除元素。
![在这里插入图片描述](https://img-blog.csdnimg.cn/98c67e52f0bf44199b0c9ce3086b7a7b.png)
**queue没有迭代器！：**
Queue所有元素的进出都必须符合”先进先出”的条件，只有queue的顶端元素，才有机会被外界取用。Queue不提供遍历功能，也不提供迭代器。

## 2. queue构造函数
```cpp
queue<T> queT;//queue采用模板类实现，queue对象的默认构造形式：
queue(const queue &que);//拷贝构造函数
```

## 3. queue存取、插入和删除操作
```cpp
push(elem);//往队尾添加元素
pop();//从队头移除第一个元素
back();//返回最后一个元素
front();//返回第一个元素
```
## 4. queue赋值操作
```cpp
queue& operator=(const queue &que);//重载等号操作符
```
## 5. queue大小操作
```cpp
empty();//判断队列是否为空
size();//返回队列的大小
```

# 第七章 list容器----链表

## 1. list基本概念
链表是一种物理存储单元上非连续、非顺序的存储结构，数据元素的逻辑顺序是通过链表中的指针链接次序实现的。链表由一系列结点（链表中每一个元素称为结点）组成，结点可以在运行时动态生成。每个结点包括两个部分：一个是存储数据元素的数据域，另一个是存储下一个结点地址的指针域。

相较于vector的连续线性空间，list就显得负责许多，它的好处是每次插入或者删除一个元素，就是配置或者释放一个元素的空间。因此，list对于空间的运用有绝对的精准，一点也不浪费。而且，对于任何位置的元素插入或元素的移除，list永远是常数时间。

List和vector是两个最常被使用的容器。

List容器是一个**双向循环链表**。
![在这里插入图片描述](https://img-blog.csdnimg.cn/632910faa4124cff8812c962eee04727.png)

 list（双向循环链表）的优点：
- 采用动态存储分配，不会造成内存浪费和溢出
- 链表执行插入和删除操作十分方便，修改指针即可，不需要移动大量元素
- 链表灵活，但是空间和时间额外耗费较大

### 1.1list迭代器：

List容器不能像vector一样以普通指针作为迭代器，因为其节点不能保证在同一块连续的内存空间上。 **List迭代器必须有能力指向list的节点，并有能力进行正确的递增、递减、取值、成员存取操作。** 所谓“list正确的递增，递减、取值、成员取用”是指，递增时指向下一个节点，递减时指向上一个节点，取值时取的是节点的数据值，成员取用时取的是节点的成员。

由于list是一个双向链表，迭代器必须能够具备前移、后移的能力，所以list容器提供的是Bidirectional Iterators.

**List有一个重要的性质，插入操作和删除操作都不会造成原有list迭代器的失效。** 这在vector是不成立的，因为vector的插入操作可能造成记忆体重新配置，导致原有的迭代器全部失效，甚至List元素的删除，也只有被删除的那个元素的迭代器失效，其他迭代器不受任何影响。

## 2. list构造函数
```cpp
list<T> lstT;//list采用采用模板类实现,对象的默认构造形式：
list(beg,end);//构造函数将[beg, end)区间中的元素拷贝给本身。
list(n,elem);//构造函数将n个elem拷贝给本身。
list(const list &lst);//拷贝构造函数。
```

## 3. list数据元素插入和删除操作
```cpp
push_back(elem);//在容器尾部加入一个元素
pop_back();//删除容器中最后一个元素
push_front(elem);//在容器开头插入一个元素
pop_front();//从容器开头移除第一个元素
insert(pos,elem);//在pos位置插elem元素的拷贝，返回新数据的位置。
insert(pos,n,elem);//在pos位置插入n个elem数据，无返回值。
insert(pos,beg,end);//在pos位置插入[beg,end)区间的数据，无返回值。
clear();//移除容器的所有数据
erase(beg,end);//删除[beg,end)区间的数据，返回下一个数据的位置。
erase(pos);//删除pos位置的数据，返回下一个数据的位置。
remove(elem);//删除容器中所有与elem值匹配的元素。
```
## 4. list大小操作
```cpp
size();//返回容器中元素的个数
empty();//判断容器是否为空
resize(num);//重新指定容器的长度为num，
若容器变长，则以默认值填充新位置。
如果容器变短，则末尾超出容器长度的元素被删除。
resize(num, elem);//重新指定容器的长度为num，
若容器变长，则以elem值填充新位置。
如果容器变短，则末尾超出容器长度的元素被删除。

```
## 5. list赋值操作
```cpp
assign(beg, end);//将[beg, end)区间中的数据拷贝赋值给本身。
assign(n, elem);//将n个elem拷贝赋值给本身。
list& operator=(const list &lst);//重载等号操作符
swap(lst);//将lst与本身的元素互换。
```

## 6. list数据的存取
```cpp
front();//返回第一个元素。
back();//返回最后一个元素。
```
## 7. list反转排序
```cpp
reverse();//反转链表，比如lst包含1,3,5元素，运行此方法后，lst就包含5,3,1元素。
sort(); //list排序
```

# 第八章  set/multiset容器
## 1. set基本概念
set的特性是所有元素都会根据**元素的键值自动被排序**。Set的元素不像map那样可以同时拥有实值和键值，**set的元素即是键值又是实值**。**Set不允许两个元素有相同的键值**。

**我们可以通过set的迭代器改变set元素的值吗？不行**，因为set元素值就是其键值，关系到set元素的排序规则。如果任意改变set元素值，会严重破坏set组织。换句话说，set的iterator是一种只读迭代器const_iterator.

set拥有和list某些相同的性质，当对容器中的元素进行插入操作或者删除操作的时候，操作之前所有的迭代器，在操作完成之后依然有效，被删除的那个元素的迭代器必然是一个例外。

## 2. multiset基本概念
**multiset特性及用法和set完全相同**，**唯一的差别在于它允许键值重复**。set和multiset的底层实现是**红黑树**，红黑树为平衡二叉树的一种。

## 3. set构造函数
```cpp
set<T> st;//set默认构造函数：
mulitset<T> mst; //multiset默认构造函数: 
set(const set &st);//拷贝构造函数
```
## 4. set赋值操作
```cpp
set& operator=(const set &st);//重载等号操作符
swap(st);//交换两个集合容器
```
## 5. set大小操作
```cpp
size();//返回容器中元素的数目
empty();//判断容器是否为空
```

## 6. set插入和删除操作
```cpp
insert(elem);//在容器中插入元素。
clear();//清除所有元素
erase(pos);//删除pos迭代器所指的元素，返回下一个元素的迭代器。
erase(beg, end);//删除区间[beg,end)的所有元素 ，返回下一个元素的迭代器。
erase(elem);//删除容器中值为elem的元素。
```
## 7. set查找操作
```cpp
find(key);//查找键key是否存在,若存在，返回该键的元素的迭代器；若不存在，返回set.end();
count(key);//查找键key的元素个数
lower_bound(keyElem);//返回第一个key>=keyElem元素的迭代器。
upper_bound(keyElem);//返回第一个key>keyElem元素的迭代器。
equal_range(keyElem);//返回容器中key与keyElem相等的上下限的两个迭代器。
```
