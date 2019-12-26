# c++第三次作业

### 8.4

```
#include <fstream>
#include <string>
#include <vector>
#include <iostream>

using std::vector;
using std::string;
using std::ifstream;
using std::cout;
using std::endl;

void ReadFileToVec(const string& fileName, vector<string>& vec)
{
    ifstream ifs(fileName);
    if (ifs) {
        string buf;
        while (std::getline(ifs, buf)) vec.push_back(buf);
    }
}

int main()
{
    vector<string> vec;
    ReadFileToVec("../data/book.txt", vec);
    for (const auto& str : vec) cout << str << endl;
    return 0;
}
```

### 8.7

```
int main(int argc, char** argv)
{
    std::ifstream input(argv[1]);  
    std::ofstream output(argv[2]); 

    Sales_data total;
    if (read(input, total)) {
        Sales_data trans;
        while (read(input, trans)) {
            if (total.isbn() == trans.isbn())
                total.combine(trans);
            else {
                print(output, total) << std::endl;
                total = trans;
            }
        }
        print(output, total) << std::endl;
    }
    else {
        std::cerr << "No data?!" << std::endl;
    }
}
```

### 8.9

```
#include <iostream>
#include <sstream>
using std::istream;

istream& func(istream& is)
{
    std::string buf;
    while (is >> buf) std::cout << buf << std::endl;
    is.clear();
    return is;
}

int main()
{
    std::istringstream iss("hello");
    func(iss);
    return 0;
}
```

### 12.1

```
b1有4个

b2有0个
```



### 12.7

```
#include <iostream>
#include <vector>
#include <string>
#include <memory>

std::vector<int>* dynamic_vector_generator();

void dynamic_vector_processor(std::vector<int>* ptr_v);

void dynamic_vector_printer(std::vector<int>* ptr_v);

std::shared_ptr<std::vector<int>> dynamic_vector_generator_sptr();

void dynamic_vector_processor_sptr(std::shared_ptr<std::vector<int>> sptr_vi);

void dynamic_vector_printer_sptr(
    const std::shared_ptr<std::vector<int>> sptr_vi);

int main()
{
    auto sptr = dynamic_vector_generator_sptr();
    dynamic_vector_processor_sptr(sptr);
    dynamic_vector_printer_sptr(sptr);

    return 0;
}
```

### 12.10

```
正确，而且运行完之后会销毁
```



### 12.15

```
[](connection *p) { disconnect(*p); };//直接将删除器函数
```



### 12.17

```
a：不合法，ix不是new返回的指针

b：不合法，ix不是new返回的指针

c：合法，unique_ptr必须采用直接初始化

d：不合法，ix不是new返回的指针

e：合法

f:不合法，必须使用new返回的指针进行初始化
```



### 12.18

```
shared_ptr是多对一的关系，其他的智能指针仍然可以删除这个对象,因此无意义
```



### 12.19

```
#include <vector>
#include <string>
#include <initializer_list>
#include <memory>
#include <exception>

using std::vector;
using std::string;

class StrBlobPtr;

class StrBlob {
public:
    using size_type = vector<string>::size_type;
    friend class StrBlobPtr;

    StrBlobPtr begin();
    StrBlobPtr end();

    StrBlob() : data(std::make_shared<vector<string>>()) {}
    StrBlob(std::initializer_list<string> il)
        : data(std::make_shared<vector<string>>(il))
    {
    }

    size_type size() const { return data->size(); }
    bool empty() const { return data->empty(); }

    void push_back(const string& t) { data->push_back(t); }
    void pop_back()
    {
        check(0, "pop_back on empty StrBlob");
        data->pop_back();
    }

    std::string& front()
    {
        check(0, "front on empty StrBlob");
        return data->front();
    }

    std::string& back()
    {
        check(0, "back on empty StrBlob");
        return data->back();
    }

    const std::string& front() const
    {
        check(0, "front on empty StrBlob");
        return data->front();
    }
    const std::string& back() const
    {
        check(0, "back on empty StrBlob");
        return data->back();
    }

private:
    void check(size_type i, const string& msg) const
    {
        if (i >= data->size()) throw std::out_of_range(msg);
    }

private:
    std::shared_ptr<vector<string>> data;
};

class StrBlobPtr {
public:
    StrBlobPtr() : curr(0) {}
    StrBlobPtr(StrBlob& a, size_t sz = 0) : wptr(a.data), curr(sz) {}
    bool operator!=(const StrBlobPtr& p) { return p.curr != curr; }
    string& deref() const
    {
        auto p = check(curr, "dereference past end");
        return (*p)[curr];
    }
    StrBlobPtr& incr()
    {
        check(curr, "increment past end of StrBlobPtr");
        ++curr;
        return *this;
    }

private:
    std::shared_ptr<vector<string>> check(size_t i, const string& msg) const
    {
        auto ret = wptr.lock();
        if (!ret) throw std::runtime_error("unbound StrBlobPtr");
        if (i >= ret->size()) throw std::out_of_range(msg);
        return ret;
    }
    std::weak_ptr<vector<string>> wptr;
    size_t curr;
};

#endif
```