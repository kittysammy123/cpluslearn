### c++类相关

#### 1.c++虚函数

```
基类声明为虚函数，则会创建虚函数表，存储函数地址。子类继承后，虚函数表地址会更新为子类的地址。这个能实现动态绑定

基类析构函数必须声明为虚函数，若基类析构函数不为虚函数，base* p = new Son,delete p时候无法动态绑定，会只调用base的析构函数，导致资源释放失败
```

2. 虚函数和纯虚函数

   ```
   纯虚函数，virtual void xxx()=0;  =0表明是纯虚函数，用作接口，该类是抽象类，无法实例化，子类必须实现该接口。
   ```

   ```
   class Animal {
   public:
       virtual void makeSound() = 0;
   };
   
   class Cat : public Animal {
   public:
       void makeSound() override {
           std::cout << "Meow!" << std::endl;
       }
   };
   
   ```

   ```
   虚函数和纯虚函数都可以实现动态绑定，带虚函数的基类自身可以实例化。虚函数用于动态绑定。
   ```

   

3.拷贝构造函数和赋值构造函数：delete关键字和私有化（私有化只声明，不实现）

```
在基类中使用delete关键字或私有化拷贝构造函数的主要目的是为了防止派生类对象对基类对象进行拷贝构造或赋值操作，以保证派生类对象的正确性和完整性
具体来说，使用delete关键字会在编译期间报错，即使派生类中实现了对基类对象的拷贝构造或赋值操作，也无法通过编译。而私有化拷贝构造函数则可以在编译期间通过，但派生类对象无法访问基类的拷贝构造函数，从而无法进行拷贝构造或赋值操作

```

  3.1拷贝构造函数和赋值构造函数私有化后只声明，不实现

```
如果在派生类中显示声明基类的拷贝构造含糊和赋值构造函数，但是不实现，此时子类无法调用进行拷贝构造和赋值操作。
如果子类需要使用拷贝构造函数，则需要调用子类的拷贝构造函数，此时就需要提供实现。
由于基类拷贝构造函数私有化，则子类无法默认生成拷贝构造函数
```

 3.2 用户提供了构造函数，则编译器不会提供默认构造函数

3.3 基类的析构函数需要声明为虚析构函数，由于子类delete时候需要调用基类析构函数，因此虚析构函数需要空实现。

总结：基类的构造函数和析构函数要声明为protected，析构函数要为virtual，为protected是因为子类初始化的时候需要调用基类的方法，析构函数也同理。

3.4 基类拷贝构造和赋值构造全部私有，导致子类无法调用基类的，所有子类无拷贝构造和赋值构造函数

3.5由于子类继承了基础模板类，导致子类可以访问getInstance方法，但是基类中new T，无法直接调用子类的私有构造方法，所以需要声明为友元。

3.6友元：友元（friend）是一种机制，允许其他类或函数访问一个类的非公有成员。在C++中，友元可以是类、函数、甚至是整个命名空间。友元的访问权限由声明它的类控制，而不是由友元本身控制。因此，即使一个友元函数或类的访问权限为私有（private），它仍然可以访问所声明的类的私有成员。这是因为友元的访问权限不是由它本身控制，而是由所声明的类控制的。通常情况下，友元声明会在类的私有部分中进行。这样做的目的是将友元的使用限制在类的内部，而不是暴露给类的用户。这也是为什么即使友元函数或类的访问权限为私有，它仍然能够访问所声明的类的私有成员的原因。

```
总结就是友元本身不管private，protected，但是其他东西访问友元受类控制，所以为了避免外部类可以访问友元，一般将其私有化。
template<typename T>
class Singleton {
public:
    static T* getInstance() {
        call_once(flag, [&] {
            m_pInstance = new T;
            });
        return m_pInstance;
    }

    static void destoryInstance() {
        if(nullptr != m_pInstance){
            delete m_pInstance;
        }
    }


    //不需要设置为virtul,一般不会去设置为Single<Sammy>,他的子类就一个Sammy
    //virtual ~Singleton() = default;

protected:
    //必须声明为保护模式，原因是T创建构造函数是，子类访问不了基类的构造函数，只能在protected中形成
    Singleton() = default;
    virtual ~Singleton() {}
private:
    //Singleton() = default;
    Singleton(const Singleton&)=delete;
    Singleton& operator= (const Singleton&)=delete;
private:
    static once_flag flag;
    static T* m_pInstance;
};

template<typename T> 
once_flag Singleton<T>::flag;

template<typename T>
T* Singleton<T>::m_pInstance = nullptr;


class Sammy : public Singleton<Sammy> {
    friend class Singleton<Sammy>;
public:
    void printk() {
        cout << "this is sammy" << endl;
    }

private:
    Sammy() = default;
    ~Sammy() = default;
};

//此时析构的时候不是线程安全的，创建的时候是线程安全的。
对于单例子类，我们需要考虑到单例的特性，即同一时刻只能存在一个对象实例。在这种情况下，使用 unique_ptr 可能会更加合适。使用unique_ptr来释放
```

线程安全版本，实现了这个对象结束时候自动释放资源，但不提供显示释放。

```
#include <memory>
#include <mutex>

template <typename T>
class Singleton {
public:
    static T& instance() {
        std::call_once(m_onceFlag, [](){
            m_instance.reset(new T);
        });

        return *m_instance;
    }

protected:
    Singleton() {}
    virtual ~Singleton() {}

private:
    static std::once_flag m_onceFlag;
    static std::unique_ptr<T> m_instance;
};

template <typename T>
std::once_flag Singleton<T>::m_onceFlag;

template <typename T>
std::unique_ptr<T> Singleton<T>::m_instance = nullptr;
```

unique_ptr线程安全的

```
#include <iostream>
#include <mutex>
#include <memory>

template <typename T>
class Singleton {
public:
    Singleton(const Singleton&) = delete;
    Singleton& operator=(const Singleton&) = delete;

    static T& instance() {
        std::call_once(init_flag_, &Singleton::init);
        return *instance_;
    }

    static void destroy() {
        if (instance_) {
            instance_.reset();
            init_flag_ = std::once_flag();  //once_flag一旦使用后不能重置，所以需要重新生成一个flag
        }
    }

protected:
    Singleton() = default;
    virtual ~Singleton() = default;

private:
    static void init() {
        instance_.reset(new T());
    }

private:
    static std::unique_ptr<T> instance_;
    static std::once_flag init_flag_;
};

template <typename T> std::unique_ptr<T> Singleton<T>::instance_;
template <typename T> std::once_flag Singleton<T>::init_flag_;

class MyClass : public Singleton<MyClass> {
    friend Singleton<MyClass>;

public:
    void print() {
        std::cout << "MyClass" << std::endl;
    }

private:
    MyClass() {}
    ~MyClass() {}
};

int main() {
    MyClass& obj1 = MyClass::instance();
    obj1.print();

    MyClass::destroy();

    MyClass& obj2 = MyClass::instance();
    obj2.print();

    return 0;
}
```

