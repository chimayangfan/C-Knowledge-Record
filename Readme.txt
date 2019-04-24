1.	虚函数（virturl）实现机制
C++通过虚函数（virtual function）机制来支持动态联编（dynamic binding），并实现了多态机制。多态是面向对象程序设计语言的基本特征之一。在C++中，多态就是利用基类指针指向子类实例，然后通过基类指针调用子类（虚）函数从而实现“一个接口，多种形态”的效果。
C++利用基类指针和虚函数来实现动态多态，虚函数的定义很简单，只要在成员函数原型前加上关键字virtual即可，并且，virtual关键字只要声明一次，其派生类中的相应函数仍为虚函数，可以省略virtual关键字。
class Base1 
{
public:
    virtual void func1() { cout << "Base1::func1()" << endl; }
    void func2() { cout << "Base1::func2()" << endl; }
};

class Base2 : public Base1
{
    void func1() { cout << "Base2::func1()" << endl; }
};

int main()
{
    cout << "virtual function testing:" << endl;
    Base2 b;
    Base1 *ptr = &b;//Base1 基类指针指向子类对象
    ptr->func1();//对于virtual函数，具体调用哪个版本的函数取决于指针所指向对象类型。
    ptr->func2();//对于非virtual函数，具体调用哪个版本的函数取决于指针本身的类型
}
输出：
virtual function testing:
Base2::func1()
Base1::func2()
 
基类虚函数实现
 
单继承子类虚函数实现
 
多继承子类实现
总结：
1.	当创建一个包含虚函数的类对象时，会在对象内存的首位置生成一个虚函数表指针。
2.	这个虚函数表指针指向一张虚函数表，表里包含了类对象中的所有虚函数指针。
3.	当创建单继承的子类对象时，重载函数的虚函数指针会覆盖基类被重载函数的虚函数指针；当创建多继承的子类对象时，会生成多个虚函数表指针，分别指向继承的虚函数表。

2.	extern关键字作用
1.	变量或函数的向外声明。
2.	在多个文件中共享const对象。默认情况下，一个const对象仅在本文件内有效，如果多个文件中出现了同名的const变量时，其实等同于在不同的文件中分别定义了独立的变量。
3.	模板的控制实例化：当两个或者多个独立编译的源文件中使用了相同的模板并且提供了相同的模板参数时，每个文件中都会有该模板的一个实例。编译器遇到extern模板声明时，它不会在本文件中生成实例化代码，将一个实例化声明为extern就表示承诺在程序的其他位置有该实例化的一个非extern定义。
总结：extern一般是使用在多文件之间需要共享某些代码时。
3.	static关键字作用
1.	隐藏。未加static前缀的全局变量和函数都具有全局可见性。加上static后，就会对其它源文件隐藏。利用这一特性可以在不同的文件中定义同名函数和同名变量，而不必担心命名冲突。static可以用作函数和变量的前缀，对于函数来讲，static的作用仅限于隐藏。
2.	保持变量内容的持久。存储在静态数据区的变量会在程序刚开始运行时就完成初始化，也是唯一的一次初始化。
3.	在类中声明static变量或者函数时，初始化时使用作用域运算符来标明它所属类，因此，静态数据成员是类的成员，而不是对象的成员，这样就出现以下作用：
(1)类的静态成员函数是属于整个类而非类的对象，所以它没有this指针，这就导致 了它仅能访问类的静态数据和静态成员函数。
(2)不能将静态成员函数定义为虚函数。
(3)由于静态成员声明于类中，操作于其外，所以对其取地址操作，就多少有些特殊 ，变量地址是指向其数据类型的指针 ，函数地址类型是一个“非成员函数指针”。
(4)由于静态成员函数没有this指针，所以就差不多等同于非成员函数，结果就产生了一个意想不到的好处：成为一个callback函数，使得我们得以将C++和C-based X Window系统结合，同时也成功的应用于线程函数身上。 （这条没遇见过）  
(5)static并没有增加程序的时空开销，相反她还缩短了子类对父类静态成员的访问 时间，节省了子类的内存空间。      
(6)静态数据成员在<定义或说明>时前面加关键字static。      
(7)静态数据成员是静态存储的，所以必须对它进行初始化。 （程序员手动初始化，否则编译时一般不会报错，但是在Link时会报错误） 
(8)静态成员初始化与一般数据成员初始化不同:
初始化在类体外进行，而前面不加static，以免与一般静态变量或对象相混淆；
初始化时不加该成员的访问权限控制符private，public等；        
初始化时使用作用域运算符来标明它所属类；
           所以我们得出静态数据成员初始化的格式：
<数据类型><类名>::<静态数据成员名>=<值>
(9)为了防止父类的影响，可以在子类定义一个与父类相同的静态变量，以屏蔽父类的影响。这里有一点需要注意：我们说静态成员为父类和子类共享，但我们有重复定义了静态成员，这会不会引起错误呢？不会，我们的编译器采用了一种绝妙的手法：name-mangling 用以生成唯一的标志。
4.	Volatile的作用
volatile类似于大家所熟知的const也是一个类型修饰符。volatile是给编译器的指示来说明对它所修饰的对象不应该执行优化。volatile的作用就是用来进行多线程编程。如果一个基本变量被volatile修饰，编译器将不会把它保存到寄存器中，而是每一次都去访问内存中实际保存该变量的位置上。
5.	const的作用
const是一个C++语言的限定符，它限定一个变量不允许被改变。使用const在一定程度上可以提高程序的安全性和可靠性。另外，在观看别人代码的时候，清晰理解const所起的作用，对理解对方的程序也有一些帮助。
1. 修饰常量
用const修饰的变量是不可变的，以下两种定义形式在本质上是一样的：
const int a = 10;
int const a = 10;
2. 修饰指针
如果const位于*的左侧，则const就是用来修饰指针所指向的变量，即指针指向为常量；
如果const位于*的右侧，const就是修饰指针本身，即指针本身是常量。
因此，推荐使用int const* p，而不是使用const int* p（虽然两者意义完全一样），这样更容易理解。
int a = 10;
const int* p = &a;            // 指针指向的内容不能变
int const* p = &a;            // 同上
int* const p = &a;            // 指针本身不能变
const int* const p = &a;      // 两者都不能变
int const* const p = &a;      // 同上
3. 修饰引用
以下两种定义形式在本质上是一样的：
int a = 10;
const int& b = a;
int const& b = a;
4. 修饰函数参数
用const修饰函数参数，传递过来的参数在函数内不可以改变。
void func (const int& n)
{
     n = 10;        // 编译错误 
}
5. 修饰函数返回值
用const修饰函数返回值的含义和用const修饰普通变量以及指针的含义基本相同。
const int* func()   // 返回的指针所指向的内容不能修改
{
    // return p;
}
6. 修饰类成员变量
用const修饰的类成员变量，只能在类的构造函数初始化列表中赋值，不能在类构造函数体内赋值。
 
class A
{
public：
    A(int x) : a(x)  // 正确
    {
         //a = x;    // 错误
    }

private：
    const int a;
};
 
7. 修饰类成员函数
用const修饰的类成员函数，在该函数体内不能改变该类对象的任何成员变量, 也不能调用类中任何非const成员函数。
 
class A
{
public:
    int& getValue() const
    {
        // a = 10;    // 错误
        return a;
    }

private:
    int a;            // 非const成员变量
};
 
8. 修饰类对象
用const修饰的类对象，该对象内的任何成员变量都不能被修改。
因此不能调用该对象的任何非const成员函数，因为对非const成员函数的调用会有修改成员变量的企图。
 
class A
{
 public:
    void funcA() {}
    void funcB() const {}
};

int main
{
    const A a;
    a.funcB();    // 可以
    a.funcA();    // 错误

    const A* b = new A();
    b->funcB();    // 可以
    b->funcA();    // 错误
}
 
9. 在类内重载成员函数
class A
{
public:
    void func() {}
    void func() const {}   // 重载
};
10. const与宏定义的区别
const常量有数据类型，而宏常量没有数据类型。编译器可以对前者进行类型安全检查，而对后者只进行字符替换，没有类型安全检查，并且在字符替换时可能会产生意料不到的错误。
const常量从汇编的角度来看，只是给出了对应的内存地址，而不是象#define一样给出的是立即数，所以，const定义的常量在程序运行过程中只有一份拷贝，而#define定义的常量在内存中有若干个拷贝。
另外，编译器通常不为const常量分配存储空间，而是将它们保存在符号表中，这使得它成为一个编译期间的常量，没有了存储与读内存的操作，使得它的效率也很高。
#define PI 3.1415926535
const double PI = 3.1415926535;
11. const_cast
const_cast运算符用来修改类型的const或volatile属性。
一、常量指针被转化成非常量的指针，并且仍然指向原来的对象；
二、常量引用被转换成非常量的引用，并且仍然指向原来的对象。
 
void func()
{
    const int a = 10;
    int* p = const_cast<int*> (&a);
    *p = 20;
    std::cout<<*p;    // 20
    std::cout<<a;     // 10, 原因见第10部分
}

6.	new与malloc区别
6.1 属性
    new和delete是C++关键字，需要编译器支持；malloc和free是库函数，需要头文件支持。
6.2 参数
    使用new操作符申请内存分配时无须指定内存块的大小，编译器会根据类型信息自行计算。而malloc则需要显式地指出所需内存的尺寸。
6.3 返回类型
    new操作符内存分配成功时，返回的是对象类型的指针，类型严格与对象匹配，无须进行类型转换，故new是符合类型安全性的操作符。而malloc内存分配成功则是返回void * ，需要通过强制类型转换将void*指针转换成我们需要的类型。
6.4 自定义类型
    new会先调用operator new函数，申请足够的内存（通常底层使用malloc实现）。然后调用类型的构造函数，初始化成员变量，最后返回自定义类型指针。delete先调用析构函数，然后调用operator delete函数释放内存（通常底层使用free实现）。
    malloc/free是库函数，只能动态的申请和释放内存，无法强制要求其做自定义类型对象构造和析构工作。
6.5 重载
    C++允许重载new/delete操作符，malloc不允许重载。
6.6 内存区域
    new做两件事：分配内存和调用类的构造函数，delete是：调用类的析构函数和释放内存。而malloc和free只是分配和释放内存。
    new操作符从自由存储区（free store）上为对象动态分配内存空间，而malloc函数从堆上动态分配内存。自由存储区是C++基于new操作符的一个抽象概念，凡是通过new操作符进行内存申请，该内存即为自由存储区。而堆是操作系统中的术语，是操作系统所维护的一块特殊内存，用于程序的内存动态分配，C语言使用malloc从堆上分配内存，使用free释放已分配的对应内存。自由存储区不等于堆，如上所述，布局new就可以不位于堆中。
6.7 分配失败
    new内存分配失败时，会抛出bac_alloc异常。malloc分配内存失败时返回NULL。
6.8 内存泄漏
    内存泄漏对于new和malloc都能检测出来，而new可以指明是哪个文件的哪一行，malloc确不可以。
7.	类成员函数的重写、重载与隐藏的区别
重写和重载主要有以下几点不同。
范围的区别：被重写的和重写的函数在两个类中，而重载和被重载的函数在同一个类中。
参数的区别：被重写函数和重写函数的参数列表一定相同，而被重载函数和重载函数的参数列表一
定不同。
virtual 的区别：重写的基类中被重写的函数必须要有virtual 修饰，而重载函数和被重载函数可以被virtual 修饰，也可以没有。
（2）隐藏和重写、重载有以下几点不同。
与重载的范围不同：和重写一样，隐藏函数和被隐藏函数不在同一个类中。
参数的区别：隐藏函数和被隐藏的函数的参数列表可以相同，也可不同，但是函数名肯定要相同。
当参数不相同时，无论基类中的参数是否被virtual 修饰，基类的函数都是被隐藏，而不是被重写。
说明：虽然重载和覆盖都是实现多态的基础，但是两者实现的技术完全不相同，达到的目的也是完
全不同的，覆盖是动态态绑定的多态，而重载是静态绑定的多态。

C++ primer5 语言学习记录
1、复合类型
1.1、引用 &：左值引用lvalue reference  别名; int val; &refval = val; 右值引用 rvalue reference
int val = 1024;//整形变量
int &refVal = val;// 整形引用 refVal 指向 val ，是val的另一个名字，
                 // 引用必须被初始化，引用不是对象，只是为所存在的对象起的一个别名。
refVal = 2;       // 把2 赋值给refVal 也就是 赋值给了 val
int i = refVal;   // 相当于 i = val; 
int &refVal2 = 10;// 错误，非常量引用只能绑定(bind)在对象向,
                  // 不能与字面值或某个表达式的计算结果绑定在一起, 且引用的类型 必须和 对象的类型一致
const int &rci = 10;// 常量引用可以绑定到 字面值 非常量 一般表达式
double dval = 3.14;// 浮点数
int &refVal3 = dval; // 错误，引用的类型 和 对象 必须一致
1.2、指针 pointer * 指向 point to 另外一个对象（其存储地址）
int ival = 42;  // 整数
int *p = &ival; // 指针定义 p存放 ival对象存放的地址，也即是 p 指向 变量 ival的指针   &ival 为取的 ival变量的 存储地址
double dval = 3.14; // 浮点数
double *pd = &dval; // double 类型 指针
double *pd2 = pd;   // pd存放 的是 dval存放的地址

int *pi = pd;     // 错误， 指针pi 的类型 和 右边指针对象 类型 不匹配
pi = &dval;   // 错误， 视图把 double 类型对象的地址 给 int类型的指针 错误  左右两边类型 必须匹配
利用指针访问对象 取地址内存储的值 解引用符 来访问向
int ival = 42; //整形 变量
int *p = &ival;//指针变量定义初始化， p 存放着 变量ival 在内存中的地址
std::cout << *p;// 表达式里 *p 为 解引用 取的p存放的地址 指向的值
int &ref_i = ival; // 引用声明定义， ref_i 是一个引用
int *p; //指针声明定义，p是一个指针
p = &ival;// 表达式 p为指针（地址），&val 为取的 val 地址
*p = ival;   //表达式  *p 相当于 ival *是一个解引用
int &ref_i2 = *p;// &ref_i2 为引用定义 *p 相当于 ival， *是一个解引用
   
空指针
int *p1 = nullptr;// 等价于 int *p1 = 0; 空指针  p1 不指向 任何 对象
int *p2 = 0; // 空指针     等价于 int *p1 = 0;
int *p3 = NULL;// 空指针   等价于 int *p1 = 0;
int zero = 0;
int *p4 = zero;// 错误，不能把 int 变量直接赋值 给指针

int i = 42;
int *pi1 = 0; // pi1 被初始化为空指针，但没有指向 任何对象
int *pi2 = &i;// pi2 被初始化，存有 i 的地址
int *pi3;// 定义 未初始化
pi3 = pi2;// pi3 和 pi2指向同一个 对象 i
pi2 = 0; // 现在 pi2 被赋值为 空指针 不指向任何对象  指针被改变， 指向的对象不变
pi2 = &i;// 现在 pi2 又指向 i
*pi2 = 0;// 指针pi2 没变 , *pi2 被改变 即pi2指向的对象 i 发生了变化
使用指针作为条件 为空指针时，逻辑值为 False，其他指向一个对象的指针(非0指针) 逻辑值 均为 true
相等 == 操作符  不等 != 操作符 
void* 类型的指针  可一指向 任意类型 的 地址 ， 可一存放任意类型的 地址，
用作函数参数，函数体内使用时需要，转化成实际传入类型的指针
double obj = 3.14, *pd = &obj; // double 类型 的变量 double类型的指针
void *pv = &obj; // obj 可以是任意类型的对象
pv = pd; // pv 可一存放任意类型的指针（地址）
double *pd = (double*)(pv);// void* 类型的指针 强制转化成  double* 类型的指针
定义多个变量
int i = 1024， *p = &i, &r = i;//i是整形变量 p是一个整形指针 r是一个整形引用
int* p1, p2;// * 仅仅修饰 p1 ，即p1是 指向整形的指针 p2是整形变量
int *p1, *p2;// p1 和 p2 都是指向 整形的指针
指向指针的指针
int ival = 1024;
int *pi = &ival; //指向整形变量的指针
int **ppi = &pi; //指向整形指针的指针  ppi ---> pi ------> ival
std::cout << ival << "\n"// 直接输出 变量值
      << *pi  << "\n"// 1次解引用 得到变量值
      << **ppi<< "\n";//2次解引用 的到变量值
指向指针的 引用 指针对象的别名  引用不是对象 不存在 指向引用的指针
int i = 42;//整形对象
int *p;//指向整形类型的指针
int *&r_p = p;//引用r_p的类型为 整形的指针引用int*&，r_p 是指针p的一个别名 
           //从右向左 离变量最近的符号为 & 即为 引用，* 引用的为 指针类型
r_p = &i;// 相当于 p = &i p 指向 i
*r_p = 0;// 相当于 *p = 0 即 i =0 改变了指针指向对象的值  
         // 这里 *p *解引用指针的类型其实相当于 原变量的别名 引用
1.3、const 限定符
修饰变量后，可以防止变量被修改
const int bufSize = 512;//初始化常量 一旦创建后就不能被修改
bufSize = 1024;// 错误，常量不能被赋值
const int i = get_size();// 一个函数赋值 运行时初始化
const int j = 42;//编译时初始化
const int k;//错误，常量定义式必须初始化
double dvel = 3.14;//  也可以由其他类型变量 强制转换成 常量
const int ci = dvel;// 由double类型变量 强制转换成 整形常量 3
extern const int bufSize = 1024;//定义了一个变量，想在其他文件也使用 bufSize 必须在定义之前 加extern
extern const int bufSize;// 另一个文件中 声明 bufSize 后 就可以使用了
绑定到常量的引用 const 的引用 即对常量的引用 reference to const 常量 的 别名 不能修改 不存在常量引用 &const 引用不是对象
        const int c_i = 1024;   // 常整数
        const int &r_c_i = c_i; // 常整数 c_i 的 引用（别名）
        r_c_i = 42;// 错误r_c_i 是常量引用 不能被修改
        int &r_c_i2 = c_i;//错误 常整数 不能赋值给 int变量 左右两边类型必须一样
允许将一个常量引用绑定到 非常量对象、字面值，甚至是个 一般表达式
        int i = 52;
        const int &r1 = i; // 允许将 常量引用 const int & 绑定到 int变量上
        const int &r2 = 42;// 绑定到 字面值上
        const int &r3 = r1 * 2;// 绑定到一个 表达式上
        int &r4 = r1 * 2;// 错误，r4是非常量 引用，只能绑定到 其对应类型的对象上

        double dval = 3.14;//浮点数
        const int &ri = dval;//常量引用 绑定到 非常量上 
        //相当于 先把 非常量转化成常量 强制类型转换 常量引用实际上绑定了一个临时变量
        const int temp = dval;//

        int i = 42;// int 变量
        int &r1 = i;// 整数变量引用 r1为 i 的别名
        const int &r2 = i;//常量 引用 r2 绑定到 变量 i上
        r1 = 0;// 相当于 i =0
        r2 = 0;// 错误 r2 时常量引用 不允许改变
指向常量的指针 const int *   指针指向的值不能变  也就是 *p 不能被赋值 不能改变
const double pi = 3.14;//双精度 浮点型 常量
double *ptr_d = &pi;// 错误，浮点型变量指针 不能绑定一个 常量的存储地址
const double *ptr_d_c = &pi;// 双精度常量 针 ptr_d_c  指向一个 双精度常量
*ptr_d_c = 42;// 不能给 pi 赋值 指向常量的指针的解引用相当于 绑定的常量 ，因此不能赋值
doubel dvel = 3.14;//双精度变量
ptr_d_c = &dvel;//允许 常量指针ptr_d_c 指向一个变量dvel 但是不能通过 常量指针ptr_d_c 修改变量dvel
常量指针 *const 指针本身不能改变 也就是指向不能改变  p不能改变 但是其指向的对象 无影响
int errNumber = 0;//
int *const conpErr = &errNumber;// * 可变指针  *const 常量指针不可变 
                               // 指向整形的 常量指针，conpErr 一直指向 errNumber
*conpErr = 3;//相当于 errNumber = 3
const double pi = 3.14;//
const double *const cd_cp = &pi;//指向 常量的常量指针，
                               // 即 指针本身cd_cp不能变， 其指向的值 *cd_cp也不能变
常量表达式 编译时就能确定的量/式子 constexpr int ce = 20;
const int *p = nullptr;// 指向整形常量 的指针
constexpr int *p = nullptr;// 指向整形变量的 常量指针 
                           // constexpr 声明中出现 指针 仅仅说明 指针为常量指针
int *const p =nullptr;//指向 整形变量的 常量指针
const int *const p = nullptr;// 指向 整形常量 的常量指针
constexpr const int *p = nullptr;// 指向 整形常量 的常量指针

constexpr int ci = 42; // ci 是整形常量
int j = 0;
constexpr int *pci = &j; // 指向整形的 常量指针
2、处理类型
2.1类型别名  type alias
typedef double wages;   // wages 是double类型的 同义词
typedef wages base, *p; // base 也是double类型的 同义词。 p是double * 的同义词
wages d_money = 1.00;//等有价于 double d_money = 1.00
p p_dmoney =   &d_money;//等价于 double *p_dmoney =   &d_money;
cout << p_dmoney << endl; 
 // 别名声明 alias declaration
using SI = Sales_Item; // SI 是 Sales_Item 的 同义词
SI item;//等价于 Sales_Item item;
 // 指针、常量const 类别别名
typedef char *pstring;// pstring等价于 char *  指向char 的指针（是指针）
const pstring cstr = 0;// cstr是指向 char 的 常量指针 
                       // ！！！！不是指向常量字符的 指针 不能直接替换 const修饰的主语是指针
const pstring *ps;     // ps是一个指针 它指向的对象 是 指向char的常量指针
2.2、auto 类型说明符  让 编译器根据右式 类型 自动推算左式的类型  且用表达式的值 初始化变量
auto item = val1 +val2;//item 初始化为 val1 和 val2相加的结果 类型 相同
// 一条语句定义多个变量时，各变量类型必须一致
auto i=0, *p = &i;//正确 i是整数， p是指向整形的指针
auto sz = 0, pi = 3.4;// 错误 sz 和 pi 类型不一致

// 引用 指针 常量 与 auto
int i = 0, &r = i;//r是i的别名 int类型
auto a = r;// a 是一个整形数

// auto 会忽略掉 顶层const
const int ci = i, &cir = ci;// 常整数
auto b = ci;  // b是一个整数，ci的顶层 const(最外层修饰 为顶层)特性被忽略
auto c = cir; // c是一个整数，ci的顶层 const特性被忽略
auto d = &i;  // d是一个指向整形的指针
auto e = &ci; // e是一个指向整形常量的指针 const 
              // 外又被 取地址符号修饰，所以这里的 const是 底层const 不被忽略

// 如果希望 auto 推断出的类型是 一个顶层 const，需要在其前面 明确指出
const auto f = ci; // ci的推演类型是 int ，f是 const int

// 将引用设置为 auto 还按之前的初始化规则  保留 顶层 const
auto &g = ci; // g 是一个 整形常量的 引用 （别名）
auto &h = 42; // 错误，非常量 引用 不能绑定 到 字面值
const auto &j = 42; // 正确， 常量引用可以绑定到 字面值

// 将 指针设置为 auto， 也按之前的初始化规则 保留顶层 const
auto *k = &ci; // k为指向 整形常量的 指针
auto *l = 0;   // l为空指针
const auto *m = &ci; // m 为 指向整形常量的 常量指针

int i = 1024;
const int c_i = i;
auto b = c_i; // b是一个 整数 ,c_i的 顶层 const被忽略
auto e = &c_i;// e 是一个 指向 整形常量的引用  这里的const是底层const 不被忽略
auto k = c_i, &ll = i;// k 是整数， ll 是一个整数引用
auto k = c_i, &o = c_i;// 错误，k 是整数变量 o是一个整数常量引用 类型不一致
auto &m =c_i, *p = &c_i;//正确 m是整数常量引用，p是指向整数常量的指针
auto n = &c_i, *p = &c_i;// 正确 n是整数常量 引用(底层const，不被忽略) p是指向整数常量的指针
2.3、decltype 类型指示符 用表达式推断类型 但是不用表达式的值初始化变量  
decltype(f()) sum = x; // sum 的类型为 函数 f() 的返回值类型 初始化为 x 就像 int sum = x; 一样
const int ci = 0, &cj = ci;// ci 整形常量  cj 整形常量的引用
decltype(ci) x = 0; // x的类型 同ci 是 整形常量 const int 不忽略 顶层 const
decltype(cj) y = x; // y的类型 同cj 是 整形常量的引用 const int &  y绑定到x上
decltype(cj) z;     // 错误 z是一个 引用 ，必须初始化

// decltype和引用 指针
int i =42, j=12, *p = &i, &r = i;
decltype(r) a = j;// a的类型和r的类型一致，为整数的引用 int& a绑定到j上
decltype(r + 0) b;//正确 加法的结果为 整形 int 因此 b为整形， 这里只定义 没有初始化
decltype(*p) c; // 错误 其实*p 解引用指针 相当于i的别名 也就是引用 
              //  所有这里 c的类型为 整数引用 int &，是个引用，必须初始化

// decltype 的表达式如果 加上括号，得到的结果将是引用
decltype(i) e; // e 是一个未初始化的 整形变量
decltype((i)) d = e; // d是一个 整数的引用 绑定到 整形变量 e上  双层引用括号 的结果 永远都是 

// 赋值表达式 也会产生 引用 引用的类型就是 等式左边变量的类型 如果 i是int i = x的类型是 int&
3、标准库 std
3.1字符串类 string 类  #include 存储一个可变长度的 字符串   使用内置数组类型实现
3.1.1、范围for  访问所有元素
string str("some string");
for (auto c : str)// auto自动推断类型
cout << c << endl; 
// 范围for 引用 改变 内容
string s("Hello World!!!");
for (auto &c : s)// c是 字符串中每个元素的一个别名
c = toupper(c);//变成大写
cout << s << endl;
3.1.2、下标运算符(索引)[] 和 迭代器 访问单个元素
string的下标 类型为 string::size_type 无符号数 可用 decltype(s.size())获取 s[0] 是第一个字符 s[s.size()-1]是最后一个字符
// 第一个字符改为大写
string s("some string");
if(!s.empty())
	s[0] = toupper(s[0]);//第一个字符改为大写  在 cctype头文件中
// 第一个单词改为大写
for(decltype(s.size()) index = 0;
    index != s.size() && !isspace(s[index]; ++index))
	s[index] = toupper(s[index]);
3.2、向量 模板  容器container vector 模板  #include 存储一个可变长度的 对象 集合 使用内置数组类型实现
因为 vector 可以存放任意类型 所以事先需要知道 存放的对象是什么类型  vector ivec; vector; vector<vector >;
// 初始化方式
vector<int> ivec(10,-1);// 直接初始化 10个元素 全为 -1
vector<int> ivec2 = ivec;//拷贝初始化
vector<int> ivec3{10};//一个元素 10 
vector<int> ivec3{10，1};//两个元素 10  和 1
vector<string> svec{"a","an","the"};//列表初始化 直接方式
vector<string> svec2 = {"a","an","the"};//列表初始化 拷贝方式

// 默认初始化 
vector<int> ivec(10);    // 10个元素，每个值都是0 
vector<string> svec(10); // 10个元素，每个值都是空 string 对象
vector<string> svec2{10};// 10个元素，每个值都是空 string 对象
vector<string> svec3{10, "hi"};// 10个 "hi"元素
vector<string> svec3(10, "hi");// 10个 "hi"元素

// 使用 .push_back() 添加元素
vector<int> ivec2; //空vec对象
for(int i = 0; i != 100; ++i)
ivec2.push_back(i);// 一次把整数值 放到 ivec2尾部 结束后 ivec2有100个元素 0~99

// 实时添加 string 元素
string word;
vector<string> text;//空对象
while (cin >> word) 
text.push_back(word);// 把word添加到 text 后面

//使用范围for  + 引用 访问 并改变vector元素
vector<int> iv{1,2,3,4,5,6,7,8,9};// 列表直接初始化
for (auto &i : v)// 对于v中每个 元素的 引用 需要改变其值
i *= i;      // 变成原来值 的 平方
for (auto i : v) // 仅读取其中的变量
cout << i << " ";
cout << endl;

// vector 对象大小 类型为size_type
vector<int>::size_type se = iv.size();

// 使用索引[] 访问 计算vector对象元素索引   统计各个分数段上 出现的 成绩个数
// 索引不能添加元素
vector<unsigned> scores(11,0);//11个分数段， 0~9，10~19，...,90~99，100 计数值全部初始化为0
unsigned grade;
while (cin >> grade){
if(grade <= 100) ++scores[grade/10];
}    

// cin 读入一组词 改成大写 存入 vector中  #include <cctype> 使用toupper()
vector<string> sv;
string word1 = "qwe";
cout << word1 << endl; 
while(cin >> word1){
	for (auto &c : word1) c = toupper(c);
	sv.push_back(word1); 	
	cout << word1 << endl; vector  
}
3.3、迭代器 访问容器中的 元素 auto b = v.begin(), e = v.end(); b表示v的第一个元素 e表示v尾元素 的下一个位置 类似 指针
// 修改 字符串 第一个元素为大小字符
string s("some string");
if (s.begin() != s.end()){//确保 s非空
  auto it = s.begin();// it 指向 s的第一个字符 类似指针 的作用
  *it = toupper(*it);// 将当前字符改写成大写形式  *it 解引用迭代器 得到其所指向的 对象  是其指向对象的别名 引用
}

// 字符串的第一个单词 改写成大写
for (auto it = s.begin(); it != s.end() && !isspace(*it); ++it)
	*it = toupper(*it);

// 迭代器类型  iterator (具有读写功能)  const_iterator 具有 读功能  不具有写功能
// 对象为常量 只具有常量类型的迭代器 const_iterator  对象为变量具有 iterator 和 const_iterator
vector<int>::iterator it;// 迭代器 it 可以读写 vector<int> 类型容器 的元素
 string::iterator it2;   // it2 可以读写  string对象 中的字符
 vector<int>::const_iterator it3;//it3只能读元素，不能写元素
 string::const_iterator it4;     //it4只能读字符，不能写字符

 // cbegin()   cend() 返回 常量 迭代器 仅能读取 容器元素 不能修改
 vector<int> iv;        // 变量
 const vector<int> civ; // 常量
 auto it1 = iv.begin(); //  it1的类型为 vector<int>::iterator  能读写iv的元素
 auto it2 = iv.cbegin(); // it2的类型为 vector<int>::const_iterator  能读iv的元素 不能修改 iv的元素
 auto it3 = civ.begin(); // it3的类型为 vector<int>::const_iterator  能读civ的元素 不能修改 civ的元素

  // 访问 容器元素对象的 成员函数   (*it).empty  等同于 it->empty()  解引用 和成员访问
  vector<string> text;
  for (auto it = text.cbegin(); it !=text.cend() && !it->empty(); ++it)
	cout << *it << endl;

  // 迭代器 运算
  auto mid = iv.begin() + iv.size()/2; //指向容器的中间位置
  if (it < mid) // 处理前半部分元素

// 两个迭代器相减 得到的类型为 带符号整数  difference_type

// 常规二分查找算法
// 升序数组 查找的元素  范围开始  结束 
int BinarySearch(int *array, int key, int low, int high)
{
    int mid;
    while (low <= high)//  缩小范围 
    {
	mid = (low + high) / 2;//更新中间元素的 下标 
	if (key == array[mid])//中间元素是否 等于所查找的元素 
	    return mid+1;//相等 返回元素下标  
	else if (key < array[mid])//所查元素 比 中间元素小  则在 前区间查找 
	    high = mid - 1;//将区间 右侧 退后 到 中间元素下标前一个元素  搜索 范围为  low，mid-1
	else//所查元素 比 中间元素大 则 在后区间查找 
	    low = mid + 1;//将区间  左测 提至  中间元素下标后一个元素    搜索 范围 mid+1，high
    }
    return 0;
}

// 使用迭代器完成二分查找
 vector<int> text// 升序容器
 auto b = text.begin(), e = text.end();//起始 结束位置
 auto mid = b + (e - b)/2;//中间位置
 while(low <= end && *mid != key){
	if(key < *mid) e = mid - 1;//所查元素 比 中间元素小  则在 前区间查找
	else b = mid + 1;// 所查元素 比 中间元素大 则 在后区间查找
	mid =  b + (e - b)/2;//更新 中间位置
 }

// 使用索引[] 访问 计算vector对象元素索引   统计各个分数段上 出现的 成绩个数
// 索引不能添加元素
vector<unsigned> scores(11,0);//11个分数段， 0~9，10~19，...,90~99，100 计数值全部初始化为0
unsigned grade;
while (cin >> grade){
if(grade <= 100) ++scores[grade/10];
} 
// 使用迭代器 访问 计算vector对象元素索引   统计各个分数段上 出现的 成绩个数
vector<unsigned> scores(11,0);//11个分数段， 0~9，10~19，...,90~99，100 计数值全部初始化为0
unsigned grade;
auto it0 = scores.begin();
while (cin >> grade){
	auto it = it0 +  grade/10;
	if(grade <= 100) ++*it;
} 
4、数组 
数组与标准库 类型 vector 类似，都存放类型相同对象的容器。 需要通过其所在的位置访问对象。 与vector不同的是，数组大小确定不变，不能随意向数组中增加元素, 数组不允许拷贝，vector允许拷贝。 注意数组名 相当于数组首元素的地址 ia[10] ia === &ia[0]
4.1、定义
constexpr unsigned sz = 42;//constexpr修饰，常量表达式
int arr[10];  //字面值常量初始化  含有10个整数的数组
int arr2[sz]; //常量表达式初始化 含有42个个整数的数组
int *parr[sz];//常量表达式初始化 含有42个指向整数的指针的数组

定义时必须指定数组类型，不能由auto来推断
不存在引用的数组，引用不是对象！！！
string nums[] = {"one", "two", "three"};// 数组元素是string对象
string *sp = &nums[0];// p指向nums的第一个元素
string *sp2 = nums;   // 等价于 string *sp2 = &nums[0]

auto sp3(nums); //sp3 是一个string指针，指向nums的第一个元素
// 而decltype关键字 声明的 不发生上述转换
 decltype(nums) sa = {"two", "three", "four"};//sa 是一个 含有3个string对象的 数组
4.2、显式初始化数组元素
const unsigned sz = 3;//
int ia1[sz] = {0, 1, 2};//列表初始化 含有3个元素
int ia2[] = {0, 1, 2};//维度为3
int ia3[5] = {0, 1, 2};//等价于 {0, 1, 2, 0, 0}

// 字符数组
char ca1[] = {'C', 'P', 'P'};//列表初始化
char ca2[] = {'C', 'P', 'P', '\0'};//含有显式的 空字符
char ca3[] = "CPP";//字符串字面值初始化 自动添加表示字符串结束的空字符

// string 对象初始化 字符数组
string s("Hello World");
const char *str = s.c_str();// 用string对象初始化 字符数组 需要使用 c_str() 方法 最好再重新拷贝一份

// 数组  初始化 vector 对象
int i_arr[] = {1, 2, 3, 4, 5, 6};
vector<int> ivec(begin(i_arr), end(i_arr));//全部副本
vector<int> sub_ivec(i_arr + 1, i_arr + 4);// 包含 {2, 3, 4, 5}四个元素

// 不允许拷贝和赋值
char ca4 = ca3;// 错误

// 复杂的数组声明定义
int *parr[10];// 是数组，包含10个整形指针的数组
int &rarr[10]=?;//错误，不存在 引用数组，引用不是对象
int (*Parray)[10] = &arr;//是指针，指向一个含有10个整数的数组
int (&Rarray)[10] = arr;//是引用，引用一个含有10个整数的数组	
4.3、访问数组元素
与标准库类型vector 和 string 一样，数组元素也可以使用 范围for语句
或下标运算符 访问，元素下标从0开始，下标通常定义为 size_t类型,unsigned类型。
标准库类型vector 和 string 下标运算符索引必须为正值 unsigned类型，数组下标运算符索引 为signed类型，内置类型，可以为负值
//下标访问修改元素
unsigned score[11];//11个分数段
unsigned grade;
while(cin >> grade){
    if(grade <= 100) ++score[grade/10];//对应段 计数+1
}
// 范围for 访问修改所有元素
for( auto i:score)//可以设置为 引用就可以修改元素了
    cout << i << " ";
cout << endl;

// 指针访问数组
int iarr[] = {0,1,2,3,4};//含有5个元素
int *pi = iarr;//指向第一个元素的指针 iarr[0]
int *pi2 = iarr + 2;//指向第三个元素的指针 iarr[2]
auto num = end(iarr) - begin(iarr); // num的值是5 就是iarr包含元素的数量 
                               // ptrdiff_t 类型 是signde类型 结果可能为负
++pi;//指向第二个元素 iarr[1]
j = pi[1];   // 等价于 *(p+1),就是 iarr[2], 就是 2
k = pi[-1];  // 等价于 *(p-1),就是 iarr[0], 就是 0
pi + 3;//指向最后一个元素
*pi;//第二个元素 4
int last =  *(iarr + 4);// 等价于 last = iarr[4];
int *end = &iarr[6];//指向尾后的位置 到达不了    不能执行解引用运算！！！！！
// 使用 for 
fot(int *begin = arr; begin != end; ++begin)
	cout << *begin  << endl;//输出每一个元素
// 使用 while
while(begin<end){//指针指向相关的对象 可以比较大小（单位（间隔）一样大）
	cout << *begin  << endl;//输出每一个元素
	++begin;
}

//标准库函数 begin() end() 函数得到指针
int iarr = {0,1,2,3,4};//函数5个元素
int *beg = begin(iarr);//首元素指针
int *end = end(iarr);//尾后指针                 不能执行解引用运算！！！！！
while(beg != end && *beg >=0) ++beg;//找第一个负值元素
4.4、多维数组 数组的数组
4.4.1、初始化
constexpr size_t row =3, col = 4;
int iarr[row][col];//定义未初始化

//下标运算符（size_t 类型） for 循环初始化
for(size_t i = 0; i != row; ++i){
	for(size_t j = 0; j != col; ++j){
		iarr[i][j] = i * col + j;//元素索引为其 值
		cout << iarr[i][j] << ' '; // 访问输出  数组元素
		}
	 cout << endl;	
}

// 范围for 初始化
size_t cnt = 0;
for (auto &row1 : iarr)//每一行 引用  int (&row1)[4] 是引用 引用一个含有4个整数的数组
 {	
	for (auto &col1 : row1){// int &col1 每一行的每一个元素 引用  可以读写 除去最内层外 其它必须使用 引用
	col1 = cnt;//赋值
	cout << col1 << ' ';// 访问 输出数组元素
	++cnt;
	}
	cout << endl;
 }	
4.5、访问
// 指针访问
int (*p)[4] = iarr;//p 指向一个含有4个整数的数组 iarr的 第一行   圆括号必不可少
p = &iarr[2]; // iarr的 第3行
// for 循环访问
for( auto p = ia; p != ia + row; ++p){// 相当于定义 int (*p)[4] = iarr; p 指向 含有4个整数的数组
	 for( auto q = *p; q != *p + col; ++q) 
	     // *p 为含有4个元素的数组 q指向数组的首元素，即指向一个整数 int *q = *p
		cout << *q << ' ';
	 cout << endl;
}
// 使用 标准库函数 begin() 和 end()
for( auto p = begin(ia); p != end(ia); ++p){
               // 相当于定义 int (*p)[4] = iarr; p 指向 含有4个整数的数组
	 for( auto q = begin(*p); q != end(*p); ++q) 
	       // *p 为含有4个元素的数组 q指向数组的首元素，即指向一个整数 int *q = *p
		cout << *q << ' ';
	 cout << endl;
}	
// 使用类型别名
using int_arr_tpye = int[4];// int_arr_tpye为包含4个元素的整形数组
typedef int int_arr_tpye[4];// 效果同上
for( int_arr_tpye *p = ia; p != ia + row; ++p){// 相当于定义 int (*p)[4] = iarr; p 指向 含有4个整数的数组
	 for( int *q = *p; q != *p + col; ++q) // *p 为含有4个元素的数组 q指向数组的首元素，即指向一个整数
		cout << *q << ' ';
	 cout << endl;
}
5、函数
包括：返回类型 函数名字（形参列表） 函数体，函数的返回类型不能是数组或函数类型，但可以是 数组或函数类型的指针
5.1、函数定义 阶乘函数
int fact(int val){
	int ret = 1;
	while(val >1) ret *= val--;
	return ret;// 返回主调函数 结束函数调用
}
5.2、函数调用 分两步 1 实参初始化形参（形参为引用时，直接使用实参） 2控制权转移给被调用函数（inline 内联函数 之间在原处展开函数）
int main(){
int j = fact(5);// 实参5 初始化 形参（int val）
cout << "5! = " << j << endl;
return 0;
}
5.3、局部对象
形参和函数体内定义的变量统称为 局部变量，仅在函数的作用域内可见， 局部自动对象，只存在于函数体执行期间，而局部静态对象，可在函数调用后一直存在
// 定义
int count_call(void){
 int c =0;        //局部自动对象 每一次调用都初始化为 0  
 static int sc =0;//局部静态变量 第一次调用初始化为0 以后每次调用在前一次值上 +1 
 cout << ++sc << "   " << ++c << endl; 
}
// 调用
int main(){
 for(int i =0; i<10; ++i)  count_call(); //sc 输出 1 2,...10，c 输出一直是1
 return 0;
}
5.4、分离式编译 函数声明 与函数定义类似但是 不包括函数体，以;代替函数体 int fact(int val); 函数可以多次声明
// 源文件 fact.cc
#include "fact.h"
int fact(int val){
	int ret = 1;
	while(val >1) ret *= val--;
	return ret;// 返回主调函数 结束函数调用
}
// 函数声明头文件 fact.h
#ifndef FACT_H
#define FACT_H
int fact(int val);// 函数声明 
#endif
// 主函数调用 fact_main.c
#include <iostream>
#include "fact.h"
using namespace std;
int main(){
int j = fact(5);// 实参5 初始化 形参（int val）
cout << "5! = " << j << endl;
return 0;
}
// 编译
gcc fact_main.c fact.cc -o main
5.5、参数传递
当形参是引用类型时，为引用传递，实际传递的是实参的别名，没有进行拷贝，当实参的值被拷贝给形参时，形参和实参是两个独立的对象
5.6、值传递   函数对形参做的所有操作 都不会影响实参
int n=0;
int i = n;// n拷贝给i
i = 42;//i的值改变， n的值不变    函数对形参做的所有操作 都不会影响实参 例如 fact(i) 不会改变i的值
5.7、引用传递 函数对形参做的所有操作 都会影响实参
int n = 0;
int &r_i = n;//r_i 是 n 的引用 即别名 同一个变量
r_i = 42;//r_i 和 n 都变成 42    
5.8、指针形参
int n=0, i =42;
int *p_n = &n, *p_i = &i;// 指针
*p_n = 100;// n 的值 变为100 指针p_n（变量你存储的地址） 不变
p_n = p_i;// 现在 p_n  和  p_i 都指向了 i

// 指针形参 函数
void reset(int *pi){
 *ip = 0;//改变了指针 pi 所指向的对象的值
 ip = 0;// 值改变了 形参ip的值 实参未被改变
}

// 调用
int i = 42;
cout << "address of i =" << &i <<endl;
reset(&i);// i 的值改变为0
cout << "i = " << i << endl;// i 的值改变为0
cout << "address of i =" << &i <<endl;// i 的存储地址未改变

// C 程序中 通常使用 指针类型的形参 来访问和改变 函数外部的对象
// C++ 程序中，建议使用引用类型的形参代替 指针形参，这样会更安全，也省时间（引用 无拷贝操作）

// 使用 引用避免拷贝 拷贝大的类对象或容器 都比较低效 费时
5.9、引用形参
 void reset(int &i){
  i = 0;// 改变了i所引用的对象
 }
 // 调用
 int j = 42;
 reset(j);// 采样传 引用方式，它的值被改变 调用时 形参i 只是 实参j的一个别名，在函数reset内部对i的操作，即对j的使用
 cout << "j = " << j << endl;
5.10、常量引用形参 当函数无须 改变引用参数的值时，最好将其声明为 常量引用
// 比较两个字符串的长度
bool isShorter(const string &rs1, const string &rs2){
	return rs1.size() < rs2.size();
}
5.11、使用引用形参 返回额外信息
// 函数返回多个值 返回字符在某个字符串中第一次出现的位置，并返回出现的 总次数
string::size_type find_char(const string &crs, char c, string::size_type &occurs){
				// 字符串        查找的字符      出现的次数
	auto ret = crs.size();//初始化 第一次出现的位置
	occurs = 0;//初始化 出现的次数
	for(decltype(ret) i = 0; i != crs.size(); ++i){
	  if(crs[i] == c){// 出现字符 c
	    if (ret == crs.size())//位置 还未改变 为  第一次出现 
	    ret  = i;//记录第一次出现的位置
	    ++occurs;//出现次数 +1  通过形参引用间接返回 出现的次数
	  }
	}  
	return ret;//返回第一次出现的位置
}

// 调用
string s("some string");
string::size_type count = 0;
auto index = find_char(s, 'o', count);

// 判断 string对象是否是 一个句子
 bool is_sentence(const string &crs){
	  // 如果 find_char() 的string参数 必须为 string & 那么不能直接把 const string & 带入
	  // 需要再定义一个 string对象， 另其为 crs 的副本，再带入
	 string::size_type count = 0;
	 return (find_char(crs, '.', count) == (crs.size() - 1)) &&  count == 1;
 }
5.12、数组形参 必须确保使用数组时不会越界  通常需要给一个结束标志
1 不允许拷贝数组 
2 在使用数组时会将其转换成指针（指向数组首元素的指针）
// 三个函数等价 形参 都是 const int * 类型
void print(const int*);
void print(const int[]);
void print(const int[10]);// 这里的维度表示期望数组含有多少元素，实际不一定
//调用
int i = 0;
j[2] = {0, 1};
print(&i); //&i 是int *类型 可以赋值给 const int *类型
print(j);  //j 为 &j[0] 是 int * 类型
5.13、使用标记指定数组长度   要求数组本身包含 一个结束标志  典型 C风格字符串 带有一个空字符
// C风格字符串 带有一个空字符
void print(const char *cp){
  if(cp)// 确定 cp 不是一个空指针
    while(*cp) // 只要 指针所指的字符不是 空字符
       cout << *cp++;// 打印 字符
}
5.14、使用标准库规范 传递 数组元素的 首末地址
void print(const int *beg, const int *end){
  while(beg != end) 
    cout << *beg++ << endl;//输出当前元素 并将指针向前移动一个位置
}
// 调用
int j[2] =  {0, 1};
print(begin(j), end(j));// 使用标准库 begin() 和 end() 函数
5.15、显示传递 一个数组大小的形参
void print(const int ia[], size_t size){
  for (size_t i = 0; i != size; ++i) 
     cout << ia[i] << endl;// 等价于 cout << *(ia+i) << endl;
}
// 调用
int j[2] =  {0, 1};
print( j, end(j) - begin(j) );// 使用标准库 begin() 和 end() 函数 做差来得到数组的大小
5.16、数组引用形参 int (&r_arr)[10] 是一个含有10个元素数组的 引用  直接包含了 数组大小的信息
void print(int (&r_arr)[10]){ // 注意形式 int (&r_arr)[10]   而 int &arr[10] 成了 包含引用的数组（还不存在，引用不是对象）
  for (auto elem : r_arr)// 变量数组每个元素  范围for
    cout << elem << endl;
}
// 调用 调用时 必须是 大小为10的整形 数组作为实参才可以
int k[10] = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9}
print(k);// 大小必须为10 多一点 少一点 都不行
5.17、多维数组形参 数组指针 形参
void print(int (*p_arr)[10], int rowSize){}// 形参是 指向 一个含有10个整数的指针
5.18、main函数 主函数参数列表
// argc：argument counter 参数计数,argv：argument vector 参数 字符串 容器
int main(int argc, char *argv[]){}
int main(int argc, char **argv){}//第二个参数为 字符串数组 即数组的数组 指针的指针   argv 指向 char*
//程序执行 program -d -o outfile data0
则 argc = 5
argv[0] = "program"; // 程序名
argv[1] = "-d";      // 第一个参数 开始
argv[2] = "-o";      // 第二个参数
argv[3] = "outfile"; // 第三个参数
argv[4] = "data0";   // 第四个参数  最后一个指针
argv[5] = 0;   // 最后一个指针之后 的元素为0
5.19、含有可变形参的函数 1 传递一个 initializer_list 标准库类型，2 特殊参数类型 省略号 ...
initializer_list 形参  实参数量未知  但是全部实参类型 都 相同
// initializer_list 提供的操作
initializer_list<T> lst;// 默认初始化 T类型元素的空列表 模实际需要指定模板T的 具体类型
initializer_list<T> lst{a, b, c...};// 列表中的元素是 const 常量
lst2(lst); // 拷贝一个initializer_list对象，不会新建，原始列表 和 副本 共享元素
lst2 = lst;// 同上
lst.size();// 列表 中的元素
lst.begin();//返回指向lst中首元素的指针
lst.end();  //返回指向lst中尾元素下一个位置的指针
// 因为有 begin() 和 end()对象可以使用 范围for 遍历参数

// 具体
initializer_list<string> ls ;//initializer_list 的元素类型是 string
initializer_list<int> li ;   //initializer_list 的元素类型是 int
// 和vector不同的是 initializer_list中的元素是常量 不能被修改

// 定义函数
void error_msg(initializer_list<string> ls){
	 for(auto beg = ls.begin(); beg != ls.end(); ++beg)
	   cout << *beg << " ";
	 cout << endl;
}
//调用函数
//expected 和 actual 是string 对象
if (expected != actual)
	error_msg({"function", expected, actual});
else
	error_msg({"function", "okey"});
// 传递了一个含有不同数量元素的 initializer_list

// 定义函数 包含 ErrCode
void error_msg(ErrCode e, initializer_list<string> ls){
	 cout << e.msg() << ": "
	 for(const auto &elem : ls )//范围for 遍历
	  cout << elem << " ";
	 cout << endl;
} 
省略符 ... 形参
void foo(param_list, ...)//
void foo(param_list...)//
void foo(...)//
5.20、函数 返回类型 和 return语句
无返回值函数 void函数 无需显示的 return 语句  return 后无值
// 交换两个值的函数
void swap(int &r_i1, int &r_i2){
   if (r_i1 == r_i2)// 两个值相等 无需交换
      return;
   // 若 执行 到这里，说明还需要继续完成下面的功能 
   int temp = r_i1;//
   r_i1 = r_i2;
   r_i2 = temp;
   // 此处 无需 显示的 return 语句   会隐式指向 return
}
5.21、有返回值函数 return 后有值 且返回的对象类型 与 函数定义的 返回类型相同
// 两个 string 对象是否 最短的部分 是相同的
bool str_subrange(const string &str1, const string &str2){
 if (str1.size() == str.size())
     return str1 == str2; // 之间判断 相同长度间的部分是否相同
 auto size = (str1.size() < str2.size()) ? str1.size() : str2.size(); //得到最短 字符串的 长度
 for(decltype(size) i = 0; i != size; ++i){
   if ( str1[i] != str2[i])// 如果 有 不相等的 字符 返回 false
    return false;// 有 不相等的 字符 返回 false
  }
 return true; // 否者 相等 返回 true
}
返回类型为 引用时 不会对结果进行拷贝 节省时间  但是 不要 返回 函数内部 临时变量的 引用， 临时变量离开函数后就不存在了
建议 返回 引用对象 是一个 调用函数 之前就纯在的一个变量
同时 返回 非常量的引用 可以作为 左值 被赋值
// 不要 返回 函数内部 临时变量的 引用
string temp("glo");
const string &mainip() {
	 string ret("Empty"); // 函数内部临时变量
	 return ret; // 错误 ，不能返回 临时变量 作为 函数返回值的 引用
	 // return temp;// 返回 一个调用函数之前就出现的 变量 的引用     或者  参数 为引用类型的参数也可以
}

// 返回 两个字符串中 短的那个
const string &shortString(const string &s1, const string &s2){// 返回 s1 或者 s2的引用
	return s1.size() < s2.size() ? s1 : s2;//返回 两个字符串中 短的那个
}

// 函数返回类型 为 标准库 类类型 可以直接调用 其成员函数
auto sz = shortString(s1, s2).size()// 得到最短字符串 的长度

// 返回 非常量的引用 可以作为 左值 被赋值  
char &get_char(string &str, string::size_type id){
	return str[id];// 获取指定位置的 字符
}
// 调用
string s("a value");
cout << s << endl;// a value
get_char(s, 0) = 'A';// 将s第一个位置上的字符 替换为 大写的 A
cout << s << endl;// A value
// 而返回为 常量引用 的不能被赋值
shortString("hi", "bye") = "X"; // 错误 函数返回值是个 常量引用 不能被赋值
5.22、函数返回类型为 列表 以花括号 {} 包围
vector<string> process(){
	if(expected.empty())
	  return {};// 返回一个 空vector对象
	else if (expected == actual)
	  return {"function", "okey"};
	else 
	  return {"function", expected, actual};
}
5.23、函数递归  返回值为自身函数 的一个 式子 main 函数不能调用自己
// 计算阶乘的函数
int factorial( int val){
	if(val > 1)
	   return val * factorial(val-1);
	else 
	    return 1;
}
// 递归 返回一个vector的元素函数
void print_vec(vector<int> vi){
	auto it = vi.begin();
	if(vi.size() > 1)
	 {
	   cout << *it <<endl;
	   vi.erase(it);
	   print_vec(vi);
	}
	else 
	   cout << *it <<endl;
}
5.24、返回 数组 指针 数组不能被 拷贝 所以函数不能返回数组 但可以返回 数组的指针 或引用
// 使用类别别名
typedef int arrT[10];// arrT是一个类别别名 表示一个 含有 10个整数 的 数组
using arrT = int[10];// 同上
arrT* func(int i);   // 函数func 返回一个 指向 10个整数的数组的 指针

//直接声明
int arr[10]; //arr 是一个 含有 10个整数的数组
int *arr_p[10]; //arr_p 是一个数组   含有 10个指整形针 的 数组
int (*p2)[10] = &arr; // p2 是一个指针， 指向一个含有 10个整数的 数组 arr
// 函数声明 类似
Type (*function_name(parameter_list))[dimension]
int ( *func(int i) )[10];// 声明一个函数 其形参为 int i ,返回类型 为 指针 ，指向 一个 含有 10个整数的 数组

// 函数指针数组
多个函数可以像数组一样被调用
int (*fun_p_a[])(char *tmp); //函数指针数组

// 使用 auto 和 尾置返回类型
auto func(int i) -> int(*)[10];// 声明一个函数 其形参为 int i ,返回类型 为 指针 ，指向 一个 含有 10个整数的 数组

// 使用 decltype 知道返回数组  类似类别别名 声明 定义
int odd[] = {1, 3, 5, 7, 9};
int even[] = {0, 2, 4, 6, 8};
decltype(odd) *arrPtr(int i){ //使用decltype(odd) 表面类型与 odd类似
 return (i%2) ? &odd : &even;// 返回一个指向数组的指针
}

// 声明一个 返回 引用 一个 含有10个string对象的数组 的函数
string ( &func_r(string str) )[10];// 直接声明
  // 使用类别别名
typedef string arrS[10];// 类别别 含有10个string对象的数组
using arrS = string[10];// 同上
arrS &func(string str);   // 函数func 返回一个 指向 10个整数的数组的 指针
  // 使用 auto 和 尾置返回类型
auto func(string str) -> string(&)[10];
  // 使用 decltype 知道返回数组  类似类别别名 声明 定义
string str_arr[] = {"a string", "two"};
decltype(str_arr) &arrPtr(string str);
5.25、函数 重载  同一个作用域内 的几个函数名字相同，但形参列表不同，成为 重载 (overloaded)
// 打印 数组元素的 几个 同名函数
void print(const char *cp);//出入参数为 带有结束符的

