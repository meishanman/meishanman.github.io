# 使用enable_shared_from_this遇到的问题

如下情况会产生编译出错:
``` c++
#include <iostream>
#include <memory>
using namespace std;
class A: private enable_shared_from_this<A> {

};

class B:public A{

};

int main() {
    auto p = make_shared<B>();
    return 0;
}
```
编译结果：
``` bash
In file included from /usr/include/c++/5/bits/shared_ptr.h:52:0,
                 from /usr/include/c++/5/memory:82,
                 from /home/chenjunjun/sources/cpp_test/main.cpp:2:
/usr/include/c++/5/bits/shared_ptr_base.h: In instantiation of ‘std::__shared_ptr<_Tp, _Lp>::__shared_ptr(std::_Sp_make_shared_tag, const _Alloc&, _Args&& ...) [with _Alloc = std::allocator<B>; _Args = {}; _Tp = B; __gnu_cxx::_Lock_policy _Lp = (__gnu_cxx::_Lock_policy)2u]’:
/usr/include/c++/5/bits/shared_ptr.h:319:64:   required from ‘std::shared_ptr<_Tp>::shared_ptr(std::_Sp_make_shared_tag, const _Alloc&, _Args&& ...) [with _Alloc = std::allocator<B>; _Args = {}; _Tp = B]’
/usr/include/c++/5/bits/shared_ptr.h:620:39:   required from ‘std::shared_ptr<_Tp1> std::allocate_shared(const _Alloc&, _Args&& ...) [with _Tp = B; _Alloc = std::allocator<B>; _Args = {}]’
/usr/include/c++/5/bits/shared_ptr.h:635:39:   required from ‘std::shared_ptr<_Tp1> std::make_shared(_Args&& ...) [with _Tp = B; _Args = {}]’
/home/chenjunjun/sources/cpp_test/main.cpp:13:29:   required from here
/usr/include/c++/5/bits/shared_ptr_base.h:1103:36: error: ‘std::enable_shared_from_this<A>’ is an inaccessible base of ‘B’
    __enable_shared_from_this_helper(_M_refcount, _M_ptr, _M_ptr);
                                    ^
CMakeFiles/cpp_test.dir/build.make:62: recipe for target 'CMakeFiles/cpp_test.dir/main.cpp.o' failed
make[3]: *** [CMakeFiles/cpp_test.dir/main.cpp.o] Error 1
CMakeFiles/Makefile2:67: recipe for target 'CMakeFiles/cpp_test.dir/all' failed
make[2]: *** [CMakeFiles/cpp_test.dir/all] Error 2
CMakeFiles/Makefile2:79: recipe for target 'CMakeFiles/cpp_test.dir/rule' failed
make[1]: *** [CMakeFiles/cpp_test.dir/rule] Error 2
Makefile:118: recipe for target 'cpp_test' failed
make: *** [cpp_test] Error 2
```

如下情况会产生运行时错误
``` c++
#include <iostream>
#include <memory>
using namespace std;
class A: private enable_shared_from_this<A> {
public:
    void f(){
        shared_from_this();
    }
};

class B:public A, private enable_shared_from_this<B>{

};

int main() {
    auto p = make_shared<B>();
    p->f();
    return 0;
}
```
运行结果:
``` bash
terminate called after throwing an instance of 'std::bad_weak_ptr'
  what():  bad_weak_ptr
```

造成这种问题的情况还不清楚，应该跟智能指针的实现有关，以后有时间可以分析一下智能指针的实现。
