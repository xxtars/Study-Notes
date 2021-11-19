# const限定符

> https://zhuanlan.zhihu.com/p/161560391  
> https://blog.csdn.net/qq_19528953/article/details/50922303

## 底层const与顶层const 

### 底层const
**指向常量的指针**：**不能改变其指向内容**的指针。声明时const可以放在类型名前后都可，拿int类型来说，声明时：const int和int const 是等价的。声明指向常量的指针也就是**底层const**，下面举一个例子：

> int num_a = 1;  
> int const *p_a = &num_a; //底层const  
> *p_a = 2;  //错误，指向“常量”的指针不能改变所指的对象  
> num_a=2; // 正确，可以用过赋值语句直接改变变量内容

注意：指向“常量”的指针不代表它所指向的内容一定是常量，只是代表不能通过解引用符（操作符*）来改变它所指向的内容。上例中指针p_a指向的内容就不是常量，可以通过赋值语句：num_a=2;  来改变它所指向的内容。

### 顶层const 

**指针常量**：代表指针本身是常量，声明时必须初始化，之后它存储的地址值就不能再改变。**声明时const必须放在指针符号*后面**，即：*const 。声明常量指针就是顶层const，下面举一个例子：

> int num_b = 2;  
> int *const p_b = &num_b; //顶层const
> p_b = &num_a;  //错误，常量指针不能改变存储的地址值

## 顶层const和底层const的区别

* **执行对象拷贝时有限制**，常量的底层const不能赋值给非常量的底层const。也就是说，你只要能正确区分顶层const和底层const，你就能避免这样的赋值错误。下面举一个例子：
> int num_c = 3;  
> const int *p_c = &num_c;  //p_c为底层const的指针  
> int *p_d = p_c;  //错误，不能将底层const指针赋值给非底层const指针,不能作为初始化右值  
> const int *p_d = p_c; //正确，可以将底层const指针复制给底层const指针  

**要理解c++给const的定位，const变量就不允许再改变，否则就没有必要声明为const。就可以推出，底层const在对象拷贝时会有限制，底层const不能拷贝给非底层const，否则就允许了通过非底层const改变底层const指向对象的值。**

* **使用命名的强制类型转换函数const_cast时**，需要能够分辨底层const和顶层const，因为const_cast只能改变运算对象的底层const。下面举一个例子：
> int num_e = 4;  
> const int *p_e = &num_e;  
> *p_e = 5;  //错误，不能改变底层const指针指向的内容  
> int *p_f = const_cast<int *>(p_e);  //正确，const_cast可以改变运算对象的底层const。但是使用时一定要知道num_e不是const的类型。与作用1不同
> *p_f = 5;  //正确，非顶层const指针可以改变指向的内容  
> cout << num_e;  //输出5  

* **函数重载与顶层和底层const 形参**。顶层 const 不影响传入函数的对象，一个拥有顶层 const 的形参无法和另一个没有顶层 const 的形参区分开：
> Record lookup(Phone);  
> Record lookup(const Phone);         //重复声明了Record lookup(Phone)  
> Record lookup(Phone*);  
> Record lookup(Phone* const);        //该const是顶层，重复声明了Record lookup(Phone* const)  

另一方面，如果形参是某种类型的指针或引用，则通过区分其是否指向的是常量对象还是非常量对象可以实现函数重载。此时的const是底层的。
> Record lookup(Phone&);  
> Record lookup(const Phone&);        //正确，底层const实现了函数的重载  
> Record lookup(Phone*);  
> Record lookup(const Phone*);        //正确，底层const实现了函数的重载  

* **函数重载与强制类型转换**,以下节选自《Effective C++》，假设要重载一个函数，而重载函数的区别仅在于函数形参一个是底层const一个是非底层的，那么最佳的做法是采用代码的复用，减少这两个重载函数实现过程的相同代码段，可以通过const_cast来实现这一做法，在非const函数中调用const。

> class Textbook  
> {  
> public:  
> &ensp;&ensp;&ensp;&ensp;...  
> &ensp;&ensp;&ensp;&ensp;const char& operator[](std::size_t position) const   
> &ensp;&ensp;&ensp;&ensp;{  
> &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;...  
> &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;return text[position];  
> &ensp;&ensp;&ensp;&ensp;}  
> &ensp;&ensp;&ensp;&ensp;char& operator[](std::size_t position)  
> &ensp;&ensp;&ensp;&ensp;{  
> &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;//这里首先将op[]的返回值的const去除，因为const char&不能自动转换为char&，除此之外，由于调用以上const函数的必须是const对象，因此将调用此函数的对象(即*this)强制转化为一个(底层)const对象，再调用const op[]。  
> &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;return  
> &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;const_cast<char&>(static_cast<const TextBlock&>(*this)[position]);  
> &ensp;&ensp;&ensp;&ensp;}  
> };  

**为什么不能在一个常量对象中调用非常成员函数？**

因为在默认情况下，this的类型是指向类的非常量版本的常量指针（意思是this的值不能改变，永远指向那个对象，即“常量指针”，但是被this指向的对象本身是可以改变的，因为是非常量版本，这里this相当于是顶层const），而this尽管是隐式的，它仍然需要遵循初始化规则，普通成员函数的隐式参数之一是一个底层非const指针，在默认情况下我们无法把一个底层const的this指针转化为非const的this指针，因此我们不能在常量对象上调用普通的成员函数。因此在上例中，形参列表后的const就意味着默认this指针应该是一个底层const, 类型是 const ClassName&。而非常对象却可以调用常成员函数，因为底层非const可以默认转化为底层const。
