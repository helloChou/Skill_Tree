<<<<<<< HEAD
# C++面试常见问题

## C++基础

***extern关键字的作用**

1.extern C，为了能够正确实现C++代码调研其它C语言的代码。使用了extern C之后，会指示编译器以下部分的代码按C语言进行编译。

2.extern修饰一个变量， 用来做全局变量的外部声明，表示该变量的定义在另一个源文件中。

3.extern 修饰一个函数，作用与修饰变量类似。



***static关键字的作用**

1.修饰局部变量，扩展该局部变量的生命周期，使之存在于整个程序周期中。

2.修饰全局变量，表示该全局变量只能在本源文件中使用，不能在别的源文件中使用。

3.修饰类的成员函数，表示该成员函数只能调用类的静态成员变量和静态成员函数。

4.修饰普通成员函数，表示该函数被限定在本源文件中，不能被别的源文件调用，与修饰全局变量类似。



***volatile的作用**

volatile是易变的，CPU访问寄存器的速度要比访问内存快，因此一般CPU会先访问该数据在寄存器中的存储结果，但是有些数据可能在内存中已经发生了变化，而寄存器中仍保留着原来的结果。为了防止这种情况，就需要将变量声明为 volatile，表示每次用到这个变量的时候直接去内存读取而不从寄存器读。

volatile主要是用在多线程编程的时候，线程1和线程2都在使用该变量，为了防止数据出错，因为要使用volatile。



***一个变量可以即用const又用volatile修饰吗**

可以，const只是表示不能修改其值，而volatile是读取操作，不冲突。



***指针和引用的区别**

1.指针是一个变量类型，是占用了内存空间的，而引用本身只是个变量别名，不重新占用空间。

2.指针在定义的时候可以不用初始化，并且在使用过程中可以更改指针的指向；引用在定义的时候一定要初始化，并一旦初始化完成后就不能再修改其引用。

3.存在指向指针的指针，即二次指针，但是不存在引用的应用。

4.指针需要解引用间接访问，引用就是直接访问。



***new和malloc的区别**

1.在使用时，new不需要程序员自己计算输入开辟空间的大小，编译器会自动的根据对象类型计算所需要的空间大小，而malloc需要程序员自己计算输入所需字节数。

2.返回值不相同，new返回的是指向对象类型的指针，而malloc返回的是一个空类型指针（void *）；

3.类类型对象可用new来开辟动态空间，而无法用malloc来开辟。

4.new本身是一个操作符，可以重载，而malloc是一个库函数。

5.new开辟的空间用delete来销毁，malloc开辟的空间用free来销毁。6.

6.new可用来直接开辟一个数组空间（new [ ]），而malloc不行。

7.在实现上new和malloc也不相同。new会调用构造函数，销毁时delete会调用析构函数；malloc和free不会。



***new和delete的实现原理**

使用new来开辟空间有三个步骤：(1)调用operator new来开辟一块未使用的内存空间；(2)调用对象类型的构造函数来完成初始化操作；(3)返回指向该空间的对象类型的指针。

使用delete来销毁空间也有两个步骤：(1)首先调用对象的析构函数；(2)编译器用operator delete函数来释放内存空间。



***什么是C++的多态？**

我的理解就是基类和派生类有某些相似却不完全相同的特性。多态有静态多态和动态多态，静态多态是通过重载和模板来实现的，在编译时就完全确定下来了；动态多态是通过在类中用继承和虚函数实现的，在基类的指针或引用指向派生类的对象时，实现动态绑定，运行时确实需要执行的函数方法。

```c++
class A{
    ...
       virtual void func()；
    ...
};
class B:public A{
    ...
	void func() override；
};
B b;
A aa;
A *a1 = &b;
A *a2 = &aa;
a1.func();  //这里调用的是B::func()
a2.func();  //这里调用的是A::func()
```

***C++虚函数是如何实现的？**

采用虚函数指针和虚函数表实现，每一个类对象实例的内存空间起始位置都会有一个虚函数指针，其指向一个虚函数表，虚函数表中每一个位置记录着其基类的虚函数地址，如果该对象实例中覆盖了基类的某个虚函数，则在虚函数表中相应基类虚函数的位置替代掉基类的虚函数地址，换为该对象实例覆盖后的函数地址。



***什么是纯虚函数？**

就是在定义虚函数的时候不写函数体，并在函数名后添加 =0；有纯虚函数的类叫抽象基类，抽象基类是不能实例化的，必须要在其派生类中对纯虚函数进行了覆盖之后才可实例化。



***析构函数可以抛出异常吗**

C++标准指明了析构函数不能抛出异常。

原因：(1)如果析构函数会抛出异常的话，那么异常点之后的程序就不会执行了，如果异常点之后的程序有释放内存的操作，那么这些操作不执行就会造成内存泄漏的问题。

(2)本身在程序发生异常的时候，c++机制会调用已经构造的对象的析构函数来释放资源，如果这个析构函数又抛出异常，就会出现前一个异常未处理，又有新的异常会导致程序崩溃。



***构造函数和析构函数应该为虚函数吗？**

**构造函数不能为虚函数**，(1)因为虚函数的实现需要虚函数指针和虚函数表，这些都需要构造函数来创建它的值，如果构造函数本身就是虚函数，那无法实现创建虚函数指针和虚函数表了。

(2)因为虚函数是由虚函数指针和虚函数表来实现的，必须是构造函数执行完之后，即构造好了之后才开始有虚函数指针和虚函数表。



**析构函数在存在派生类时应该设置为虚函数**，这是为了防止内存泄漏。因为当基类的指针或引用指向的一个派生类对象需要销毁时，如果析构函数不是虚函数的话，就会执行基类的析构函数，而派生类中的那一部分就无法被释放掉，就产生了内存泄露。

```c++
class A{
    ...
       virtual void func()；
    ...
};
class B:public A{
    ...
	void func() override；
};
B b;
A *a1 = &b;//这里采用的是指针形式，a1指向b的地址。
//如果不将基类的析构函数声明为虚函数的话，这里销毁a1时，就会执行A：：~A(),而导致b派生类中的资源无法释放掉，产生内存泄露。

```



***智能指针是怎么实现的？什么时候改变引用计数？**

智能指针本身是一个模板类，其中包含了一个指针和计数器，并且对这个模板类重载了解引用等一些指针的操作，使其看起来就像是一个指针。

当发生拷贝和赋值的时增加引用计数，当一个智能指针被赋予新值或者被销毁的时候，递减引用计数。

当引用计数递减为0时，释放指向的内存空间。

> 简单的智能指针demo

```c++
#include <iostream>
#include <memory>
using namespace std;

template<typename T>
class SmartPointer{
private:
    T* _ptr;
    size_t* _count;
public:
    
    //定义 带默认值的构造函数
	SmartPointer(T* ptr = nullptr ):_ptr(ptr){
        if(_ptr){
            _count = new size_t(1);
        }else{
            _count = new size_t(0);
        }  
    }    
    
    //定义拷贝构造函数
    SmartPointer(const SmartPointer& ptr){
        if(this!=&ptr){
            this->_ptr = ptr._ptr;
            this->_count = ptr._count;
            (*this->_count)++;
        }
    }
    //定义析构函数
    ~SmartPointer(){
        (*this->_count)--;
        if(*this->_count==0){
            delete this->_ptr;
            delete this->_count;
        }
    }
    
    //重载指针赋值
    SmartPointer& operator =(const SmartPointer& ptr){
        if(this->_ptr==ptr._ptr){
           return *this;
        }
        
        if(this->_ptr!=nullptr){
            (*this->_count)--;
            if(*this->_count==0){
                delete this->_ptr;
                delete this->_count;
            }
        }
        this->_ptr = ptr._ptr;
        this->_count = ptr._count;
        (*this->_count)++;
        return *this;    
    }
    
    //重载指针的解引用
    T& operator*(){
        assert(this->_ptr == nullptr);
        return *(this->_ptr);
    }
    
    //这个不太明白？ 只有当指针是指向类的指针时，->才有意义。
    T* operator ->(){             
        assert(this->_ptr == nullptr);
        return this->_ptr;
    }
    
    size_t use_count(){
        return *this->_count;
    }    
};

int main(){
    SmartPointer<int> sp(new int(10)); //sp._count==1
    SmartPointer<int> sp2(sp);         //sp._count==sp2._count==2
    SmartPointer<int> sp3(new int(20));//sp3._count==1
    sp2 = sp3;                         //sp._count=1; sp2._count = sp3._count==2
    
    cout << sp.use_count()<<endl;
    cout << sp3.use_count()<<endl;
    
    return 0;
}

```



智能指针一共有三种：`shared_ptr`；`unique_ptr`；`weak_ptr`；

`shared_ptr`：

```c++
#include <iostream>
#include <memory>
using namespace std;

int main(){
    int m = 1;
    shared_ptr<int> ptr1 = make_shared<int>(m);
    shared_ptr<int> ptr2(ptr1);//这里调用拷贝构造函数 引用计数器+1
    cout<<ptr1.use_count()<<endl;
    
    int n = 1;
    int *pb = &m;
    shared_ptr<int> ptr3=make_shared<int>(n);
    ptr2 = ptr3;//这里 ptr1的引用计数器-1 ptr3的引用计数器+1
    pb = ptr2.get();
    
    cout<<ptr1.use_count()<<endl; //1
    cout<<ptr3.use_count()<<endl; //2
    
    return 0;
}
```

​		允许多个指针指向相同的对象(内存空间)，shared_ptr使用引用计数，每一个拷贝都指向相同的内存，每拷贝一次，内部的引用计数器加1，每析构一次，内部的引用计数器减1，减为0时就自动删除所指向的堆内存。

注意：

- shared_ptr内部的引用计数是线程安全的，但是对象的读取操作需要加锁！

- 不能用同一个原始指针初始化多个shared_ptr，这样会造成二次释放同一内存。

- **避免循环引用**，shared_ptr最大的陷阱是循环引用，循环引用会导致堆内存无法正确的释放，导致内存泄露。

  > **循环指针的问题**



=======
# C++面试常见问题

## C++基础

***extern关键字的作用**

1.extern C，为了能够正确实现C++代码调研其它C语言的代码。使用了extern C之后，会指示编译器以下部分的代码按C语言进行编译。

2.extern修饰一个变量， 用来做全局变量的外部声明，表示该变量的定义在另一个源文件中。

3.extern 修饰一个函数，作用与修饰变量类似。



***static关键字的作用**

1.修饰局部变量，扩展该局部变量的生命周期，使之存在于整个程序周期中。

2.修饰全局变量，表示该全局变量只能在本源文件中使用，不能在别的源文件中使用。

3.修饰类的成员函数，表示该成员函数只能调用类的静态成员变量和静态成员函数。

4.修饰普通成员函数，表示该函数被限定在本源文件中，不能被别的源文件调用，与修饰全局变量类似。



***volatile的作用**

volatile是易变的，CPU访问寄存器的速度要比访问内存快，因此一般CPU会先访问该数据在寄存器中的存储结果，但是有些数据可能在内存中已经发生了变化，而寄存器中仍保留着原来的结果。为了防止这种情况，就需要将变量声明为 volatile，表示每次用到这个变量的时候直接去内存读取而不从寄存器读。

volatile主要是用在多线程编程的时候，线程1和线程2都在使用该变量，为了防止数据出错，因为要使用volatile。



***一个变量可以即用const又用volatile修饰吗**

可以，const只是表示不能修改其值，而volatile是读取操作，不冲突。



***指针和引用的区别**

1.指针是一个变量类型，是占用了内存空间的，而引用本身只是个变量别名，不重新占用空间。

2.指针在定义的时候可以不用初始化，并且在使用过程中可以更改指针的指向；引用在定义的时候一定要初始化，并一旦初始化完成后就不能再修改其引用。

3.存在指向指针的指针，即二次指针，但是不存在引用的应用。

4.指针需要解引用间接访问，引用就是直接访问。



***new和malloc的区别**

1.在使用时，new不需要程序员自己计算输入开辟空间的大小，编译器会自动的根据对象类型计算所需要的空间大小，而malloc需要程序员自己计算输入所需字节数。

2.返回值不相同，new返回的是指向对象类型的指针，而malloc返回的是一个空类型指针（void *）；

3.类类型对象可用new来开辟动态空间，而无法用malloc来开辟。

4.new本身是一个操作符，可以重载，而malloc是一个库函数。

5.new开辟的空间用delete来销毁，malloc开辟的空间用free来销毁。6.

6.new可用来直接开辟一个数组空间（new [ ]），而malloc不行。

7.在实现上new和malloc也不相同。new会调用构造函数，销毁时delete会调用析构函数；malloc和free不会。



***new和delete的实现原理**

使用new来开辟空间有三个步骤：(1)调用operator new来开辟一块未使用的内存空间；(2)调用对象类型的构造函数来完成初始化操作；(3)返回指向该空间的对象类型的指针。

使用delete来销毁空间也有两个步骤：(1)首先调用对象的析构函数；(2)编译器用operator delete函数来释放内存空间。



***什么是C++的多态？**

我的理解就是基类和派生类有某些相似却不完全相同的特性。多态有静态多态和动态多态，静态多态是通过重载和模板来实现的，在编译时就完全确定下来了；动态多态是通过在类中用继承和虚函数实现的，在基类的指针或引用指向派生类的对象时，实现动态绑定，运行时确实需要执行的函数方法。

```c++
class A{
    ...
       virtual void func()；
    ...
};
class B:public A{
    ...
	void func() override；
};
B b;
A aa;
A *a1 = &b;
A *a2 = &aa;
a1.func();  //这里调用的是B::func()
a2.func();  //这里调用的是A::func()
```

***C++虚函数是如何实现的？**

采用虚函数指针和虚函数表实现，每一个类对象实例的内存空间起始位置都会有一个虚函数指针，其指向一个虚函数表，虚函数表中每一个位置记录着其基类的虚函数地址，如果该对象实例中覆盖了基类的某个虚函数，则在虚函数表中相应基类虚函数的位置替代掉基类的虚函数地址，换为该对象实例覆盖后的函数地址。



***什么是纯虚函数？**

就是在定义虚函数的时候不写函数体，并在函数名后添加 =0；有纯虚函数的类叫抽象基类，抽象基类是不能实例化的，必须要在其派生类中对纯虚函数进行了覆盖之后才可实例化。



***析构函数可以抛出异常吗**

C++标准指明了析构函数不能抛出异常。

原因：(1)如果析构函数会抛出异常的话，那么异常点之后的程序就不会执行了，如果异常点之后的程序有释放内存的操作，那么这些操作不执行就会造成内存泄漏的问题。

(2)本身在程序发生异常的时候，c++机制会调用已经构造的对象的析构函数来释放资源，如果这个析构函数又抛出异常，就会出现前一个异常未处理，又有新的异常会导致程序崩溃。



***构造函数和析构函数应该为虚函数吗？**

**构造函数不能为虚函数**，(1)因为虚函数的实现需要虚函数指针和虚函数表，这些都需要构造函数来创建它的值，如果构造函数本身就是虚函数，那无法实现创建虚函数指针和虚函数表了。

(2)因为虚函数是由虚函数指针和虚函数表来实现的，必须是构造函数执行完之后，即构造好了之后才开始有虚函数指针和虚函数表。



**析构函数在存在派生类时应该设置为虚函数**，这是为了防止内存泄漏。因为当基类的指针或引用指向的一个派生类对象需要销毁时，如果析构函数不是虚函数的话，就会执行基类的析构函数，而派生类中的那一部分就无法被释放掉，就产生了内存泄露。

```c++
class A{
    ...
       virtual void func()；
    ...
};
class B:public A{
    ...
	void func() override；
};
B b;
A *a1 = &b;//这里采用的是指针形式，a1指向b的地址。
//如果不将基类的析构函数声明为虚函数的话，这里销毁a1时，就会执行A：：~A(),而导致b派生类中的资源无法释放掉，产生内存泄露。

```



***智能指针是怎么实现的？什么时候改变引用计数？**

智能指针本身是一个模板类，其中包含了一个指针和计数器，并且对这个模板类重载了解引用等一些指针的操作，使其看起来就像是一个指针。

当发生拷贝和赋值的时增加引用计数，当一个智能指针被赋予新值或者被销毁的时候，递减引用计数。

当引用计数递减为0时，释放指向的内存空间。

> 简单的智能指针demo

```c++
#include <iostream>
#include <memory>
using namespace std;

template<typename T>
class SmartPointer{
private:
    T* _ptr;
    size_t* _count;
public:
    
    //定义 带默认值的构造函数
	SmartPointer(T* ptr = nullptr ):_ptr(ptr){
        if(_ptr){
            _count = new size_t(1);
        }else{
            _count = new size_t(0);
        }  
    }    
    
    //定义拷贝构造函数
    SmartPointer(const SmartPointer& ptr){
        if(this!=&ptr){
            this->_ptr = ptr._ptr;
            this->_count = ptr._count;
            (*this->_count)++;
        }
    }
    //定义析构函数
    ~SmartPointer(){
        (*this->_count)--;
        if(*this->_count==0){
            delete this->_ptr;
            delete this->_count;
        }
    }
    
    //重载指针赋值
    SmartPointer& operator =(const SmartPointer& ptr){
        if(this->_ptr==ptr._ptr){
           return *this;
        }
        
        if(this->_ptr!=nullptr){
            (*this->_count)--;
            if(*this->_count==0){
                delete this->_ptr;
                delete this->_count;
            }
        }
        this->_ptr = ptr._ptr;
        this->_count = ptr._count;
        (*this->_count)++;
        return *this;    
    }
    
    //重载指针的解引用
    T& operator*(){
        assert(this->_ptr == nullptr);
        return *(this->_ptr);
    }
    
    //这个不太明白？ 只有当指针是指向类的指针时，->才有意义。
    T* operator ->(){             
        assert(this->_ptr == nullptr);
        return this->_ptr;
    }
    
    size_t use_count(){
        return *this->_count;
    }    
};

int main(){
    SmartPointer<int> sp(new int(10)); //sp._count==1
    SmartPointer<int> sp2(sp);         //sp._count==sp2._count==2
    SmartPointer<int> sp3(new int(20));//sp3._count==1
    sp2 = sp3;                         //sp._count=1; sp2._count = sp3._count==2
    
    cout << sp.use_count()<<endl;
    cout << sp3.use_count()<<endl;
    
    return 0;
}

```



智能指针一共有三种(c++11)：`shared_ptr`；`unique_ptr`；`weak_ptr`；



`shared_ptr`：

```c++
#include <iostream>
#include <memory>
using namespace std;

int main(){
    int m = 1;
    shared_ptr<int> ptr1 = make_shared<int>(m);
    shared_ptr<int> ptr2(ptr1);//这里调用拷贝构造函数 引用计数器+1
    cout<<ptr1.use_count()<<endl;
    
    int n = 1;
    int *pb = &m;
    shared_ptr<int> ptr3=make_shared<int>(n);
    ptr2 = ptr3;//这里 ptr1的引用计数器-1 ptr3的引用计数器+1
    pb = ptr2.get();
    
    cout<<ptr1.use_count()<<endl; //1
    cout<<ptr3.use_count()<<endl; //2
    
    return 0;
}
```

​		允许多个指针指向相同的对象(内存空间)，shared_ptr使用引用计数，每一个拷贝都指向相同的内存，每拷贝一次，内部的引用计数器加1，每析构一次，内部的引用计数器减1，减为0时就自动删除所指向的堆内存。

注意：

- shared_ptr内部的引用计数是线程安全的，但是对象的读取操作需要加锁！
- 不能用同一个原始指针初始化多个shared_ptr，这样会造成二次释放同一内存。
- **避免循环引用**，shared_ptr最大的陷阱是循环引用，循环引用会导致堆内存无法正确的释放，导致内存泄露。





`unique_ptr`

unique_ptr唯一拥有其所指的对象，**同一个时刻一个资源只能被一个unique_ptr占有**，禁止拷贝语义，只能通过移动语义。

unique_ptr的生命周期：从创建unique_ptr指针开始到离开其作用域；离开其作用域时，若指针指向对象，则会销毁改对象。

unique_ptr指针与其所指对象的关系：在unique_ptr指针的生命周期内，可以改变智能指针所指对象，比如创建智能指针时通过构造函数指针、采用reset()重新指定、通过release()释放所有权、通过移动语义转移所有权。

```c++
#include <iostream>
#include <memory>
using namespace std;

int main(){
    //定义一个作用域
    {
        unique_ptr<int> uptr(new int(10))；
       // unique_ptr<int> uptr2 = uptr;  不能拷贝赋值！！
       //unique_ptr<int> uptr2(uptr);  不能拷贝！！
        unique_ptr<int> uptr2 = std::move(uptr);//将uptr从左值转移成右值，然后实现语义所有权转移
        uptr2.release();//释放所有权；将uptr2赋值为空，但uptr2原先指向的内存不释放。
    }
    //离开uptr的作用域，内存释放
    //离开一个作用域，其作用域内分配的内存都会释放？？？
    return 0;
}
```

```c++
unique_ptr<int> a(new int(1));
unique_ptr<int> b = std::move(a);//这里a将被清空是指 智能指针a被清空，但原先a指向的内存不释放。
unique_ptr<int> c;
c = std::move(c);   //c将被清空
```

主要函数：

`move()`;`reset()`;`release()`

```c++
int main(){
	unique_ptr<int> ptr1(new int(1));
	unique_ptr<int> ptr2(new int(2));
    
    ptr2 =std::move(ptr1);// ptr1指针被清空，其所指的内存被ptr2指针所指，ptr2原来所指的内存被释放
    if(ptr1==nullptr){
        cout<<"ptr1 is empty!"<<endl;
    }
    int* p = ptr2.release();//释放ptr2对其所指资源的控制，将ptr2置空，返回一个指向资源的指针。
    
    ptr2.reset(p);//重置ptr2指针，用一个普通指针重置ptr2指针，则ptr2唯一指向该资源。
    
	return 0；
}
```



`weak_ptr`

> 循环引用(环形引用)的问题

由于shared_ptr是采用引用计数器的智能指针，多个shared_ptr实例可以指向同一个动态对象，并维护一个共享的引用计数器，引用计数器就可以能出现**环形引用**的问题，如下代码所示：

**exp.1**

```c++
#include <iostream>
#include <memory>
using namespace std;

class ClassB;

class ClassA{
public:
	ClassA(){ cout << "ClassA Constructor..." << endl; }
	~ClassA(){ cout << "ClassA Destructor..." << endl; }
	shared_ptr<ClassB> pb;
};

class ClassB{
public:
	ClassB(){ cout << "ClassB Constructor..." << endl; }
	~ClassB(){ cout << "ClassB Destructor..." << endl; }
};

int main(){

	{
		shared_ptr<ClassA> spa = make_shared<ClassA>();  //spa.count==1
		shared_ptr<ClassB> spb = make_shared<ClassB>();  //spb.count==1
		spa->pb = spb; //spa.count ==1; spa->pb.count==spb.count==2

		cout << "spa use_count : " << spa.use_count() << endl;
		cout << "spb use_count : " << spb.use_count() << endl;
	}
    //离开作用域后，即spa.count-1——>0 ，所以spa会被销毁，调用析构函数释放内存，因此spa->pb.count-1——>1 故spb.count-->1 
    //spb离开作用域后，spb.count-1 = 0，则spb被销毁，调用析构函数释放内存 

    return 0;
}
```

输出为：

![1556418094460](assets/1556418094460.png)

总结：

（1）spa智能指针先离开作用域后，引用计数器减1变为0，释放spa所指向的ClassA的内存，调用~ClassA()；

（2）ClassA的内存释放导致ClassA中指向ClassB的智能指针被销毁，导致spb.count-1=1;

（3）spb离开作用域，引用计数器减1变为0，释放spb指向的ClassB的内存，调用~ClassB();

ps:反过来spb先离开作用域的思路类似。

**exp.2**

```c++
#include <iostream>
#include <memory>
using namespace std;

class ClassB;
class ClassA{
public:
	ClassA(){ cout << "ClassA Constructor..." << endl; }
	~ClassA(){ cout << "ClassA Destructor..." << endl; }
	shared_ptr<ClassB> pb;
};

class ClassB{
public:
	ClassB(){ cout << "ClassB Constructor..." << endl; }
	~ClassB(){ cout << "ClassB Destructor..." << endl; }
	shared_ptr<ClassA> pa;
};

int main(){
    {
	shared_ptr<ClassA> spa = make_shared<ClassA>();//spa.count==1
	shared_ptr<ClassB> spb = make_shared<ClassB>();//spb.count==1
	spa->pb = spb; //发生赋值拷贝 spb.count==spa->pb.count==2 
	spb->pa = spa; //发生赋值拷贝 spa.count==spb->pa.count==2

	cout << "spa use_count : " << spa.use_count() << endl; //2  
	cout << "spb use_count : " << spb.use_count() << endl; //2
	}
   	//离开作用域 spa.count-1-->1  spb.count-1 -->1

    
	return 0;
}

```

输出为：

![1556419180273](assets/1556419180273.png)

总结：

（1）spa智能指针先离开作用域后，引用计数器减1变为1，不为0，因此不释放spa指向的ClassA对象的内存，因此ClassA对象中的指向ClassB类型的智能指针没有被销毁，因此引用计数器不变，保持为2；

（2）spb智能指针离开作用域后，引用计数器减1变为1，不为0，因此不释放spb指向的ClassB对象的内存，同理其ClassB对象中指向ClassA类型的智能指针没有被销毁，引用计数器不变，保持为1；

（3）导致spa.count==1;spb.count==1 内存没有被释放，且无法使用，产生**内存泄露**！

ps：**简单来说循环引用就是两个对象互相使用一个shared_ptr成员变量指向对方造成了循环引用，导致在析构时，析构A必须得先析构A中的B，但析构B又必须得先析构B中的A，产生循环，导致内存泄露。**



**引入weak_ptr来打破shared_ptr产生的循环引用的问题**

weak_ptr是弱指针，是为了配合shared_ptr而产生的一种智能指针，其特点是**它指向一个shared_ptr管理的对象，但却不影响所指对象的声明周期（通过不改变引用计数器实现）**，即将一个weak_ptr绑定到一个shared_ptr上不会改变shared_ptr的引用计数。从效果上看，一旦shared_ptr被销毁，则weak_ptr也会自动销毁。

**exp.3**

```c++
#include <iostream>
#include <memory>
using namespace std;

class ClassB;
class ClassA{
public:
	ClassA(){ cout << "ClassA Constructor..." << endl; }
	~ClassA(){ cout << "ClassA Destructor..." << endl; }
	weak_ptr<ClassB> pb; //在exp.2的基础上将其改为weak_ptr！！
};

class ClassB{
public:
	ClassB(){ cout << "ClassB Constructor..." << endl; }
	~ClassB(){ cout << "ClassB Destructor..." << endl; }
	shared_ptr<ClassA> pa;
};

int main(){
    {
	shared_ptr<ClassA> spa = make_shared<ClassA>();//spa.count==1
	shared_ptr<ClassB> spb = make_shared<ClassB>();//spb.count==1
	spa->pb = spb; //weak_ptr 不改变引用计数 spb.count==spa->pb.count==1
	spb->pa = spa; //发生赋值拷贝 spa.count==spb->pa.count==2

	cout << "spa use_count : " << spa.use_count() << endl; //2  
	cout << "spb use_count : " << spb.use_count() << endl; //1
	}
   	//离开作用域 spa.count-1-->1 spa->pb.count-1-->1
    // spb.count-1 -->0 销毁spb所指向的ClassB，调用析构函数~ClassB()； 并且spb->pa.count-1=0
    //导致spa.count==0,销毁spa所指向的ClassA，调用析构函数~ClassA();
    
	return 0;
}

```

输出为：

![1556420296133](assets/1556420296133.png)

**weak_ptr的使用**

由于weak_ptr所指的对象可能不存在，因此不能使用weak_ptr直接访问对象，应该采用`lock()`函数。

```c++
weak_ptr<T> wptr;
wptr.use_count();//返回与wptr共享对象的shared_ptr的数量
wptr.expired();//若wptr.use_count()为0，则返回True，否则返回False；
wptr.lock()；//若wptr.expired()为True，则返回nullptr，否则返回一指向wptr所指对象的shared_ptr指针
    
if(shared_ptr<T> ptr = wptr.lock()){
	//表示wptr所指对象存在时进行相关操作。
}   
```

```c++
#include <iostream>
#include <memory>
using namespace std;
int main(){

	shared_ptr<int> ptr(new int(1));
	weak_ptr<int> wptr(ptr);
	cout << ptr.use_count() << endl;
	
	shared_ptr<int> t_ptr = wptr.lock();//ptr.use_count+1-->2
	cout << ptr.use_count() << endl;

	ptr.reset();//t_ptr.use_count-1-->1
	cout << t_ptr.use_count() << endl;
    
	return 0;
}
```

输出：

![1556422659396](assets/1556422659396.png)

总结：

weak_ptr的两个作用：

（1）weak_ptr在所指内存被析构时会自动变为nullptr指针；（普通指针无法做到的）

（2）弱引用特性能使在shared_ptr发生循环引用时，解决循环引用的问题；
>>>>>>> 2019/4/28
