现在开始进入C++STL学习

# 第一章 STL概论

STL 从广义上分为: 
- 容器(container) 
- 算法(algorithm) 
- 迭代器(iterator)（可以理解成指针）：功能就是**访问容器中的各个元素用的**（也就是**遍历**用的），即提供一种方法，使之能够依序寻访某个容器所含的各个元素，而又无需暴露该容器的内部表示方式。

 STL 几乎所有的代码都采用了模板类或者模板函数，这相比传统的由函数和类组成的库来说提供了更好的代码重用机会。

STL的六大组件：
- 容器：各种数据结构，如vector、list、deque、set、map等,用来存放数据，从实现角度来看，STL容器是一种class template。
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
### 1.7 string子串
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
string str(s);
```
# 1.10 常用操作简单总结
存取 [] at
字符串拼接   += append
查找    find   rfind(从右往左查找)
替换    replace
比较    compare
子串    substr

