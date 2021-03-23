## 设计模式的六大原则 

### 开闭原则（Open Close Principle）

开闭原则就是说**对扩展开放，对修改关闭**。在程序需要进行拓展的时候，不能去修改原有的代码，实现一个热插拔的效果。所以一句话概括就是：为了使程序的扩展性好，易于维护和升级。想要达到这样的效果，我们需要使用接口和抽象类，后面的具体设计中我们会提到这点。

### 里氏代换原则（`Liskov Substitution Principle`）

里氏代换原则(`Liskov Substitution Principle LSP`)面向对象设计的基本原则之一。 里氏代换原则中说，任何基类可以出现的地方，子类一定可以出现。` LSP`是继承复用的基石，只有当衍生类可以替换掉基类，软件单位的功能不受到影响时，基类才能真正被复用，而衍生类也能够在基类的基础上增加新的行为。里氏代换原则是对“开-闭”原则的补充。实现“开-闭”原则的关键步骤就是抽象化。而基类与子类的继承关系就是抽象化的具体实现，所以里氏代换原则是对实现抽象化的具体步骤的规范。

### 依赖倒转原则（Dependence Inversion Principle）

这个是开闭原则的基础，具体内容：真对接口编程，依赖于抽象而不依赖于具体

### 接口隔离原则（Interface Segregation Principle）

这个原则的意思是：使用多个隔离的接口，比使用单个接口要好。还是一个降低类之间的耦合度的意思，从这儿我们看出，其实设计模式就是一个软件的设计思想，从大型软件架构出发，为了升级和维护方便。所以上文中多次出现：降低依赖，降低耦合。

### 迪米特法则（最少知道原则）（Demeter Principle）

为什么叫最少知道原则，就是说：一个实体应当尽量少的与其他实体之间发生相互作用，使得系统功能模块相对独立。

### 合成复用原则（Composite Reuse Principle）

原则是尽量使用合成/聚合的方式，而不是使用继承。

## 单例模式

整个程序运行中只存在唯一的一个全局对象。

```
游戏设计中的单例：比如一个全局的NPC管理，事件记录器，控制台管理等等。
```

单例模式
要求: 通过一个类只能生成一个对象

优点：

1、在内存里只有一个实例，减少了内存的开销，尤其是频繁的创建和销毁实例（比如管理学院首页页面缓存）。

 2、避免对资源的多重占用（比如写文件操作）。

缺点：没有接口，不能继承，与单一职责原则冲突，一个类应该只关心内部逻辑，而不关心外面怎么样来实例化。

应用场景:

1. 全局对象的地方，都可以考虑使用单例模式，全局唯一
2. 读取配置文件, 配置文件的内容就可以使用单例对象存放
3. 词典库、网页库
4. 要求生产唯一序列号
5. WEB 中的计数器，不用每次刷新都在数据库里加一次，用单例先缓存起来
6. 创建的一个对象需要消耗的资源过多，比如 I/O 与数据库的连接等。

单例模式是23种 `GOF `模式中最简单的设计模式之一，属于创建型模式，它提供了一种创建对象的方式，确保只有单个对象被创建。这个设计模式主要目的是想在整个系统中只能出现类的一个实例，即一个类只有一个对象。根据我们已经学过的知识，其实现步骤有如下三步: 

1. 将构造函数私有化

2. 在类中定义一个静态的指向本类型的指针变量

3. 定义一个返回值为类指针的静态成员函数

```c++
//懒汉式
class Singleton { 
public: 
	static Singleton * getInstance() //提供一个可访问类自定义对象的类成员方法（对外提供该对象的访问方式）
	{ 		/*通过类提道供的方法访问类中的那个自定义对象。使用类中方法只有两种方式，
			1.创建类的一个对象，用对象去调用方法；
			2.使用类名直接调用类中方法。而想要使用类名直接调用类中方法，类中方法必须是静态的，而静态方法不能访问非静态成员变量
			所以成员对象是静态的*/
		if(nullptr == _pInstance) 
		{  // 被动创建，在真正需要使用时才去创建
            _pInstance = new Singleton();
         } 
		return _pInstance; 
	} 
private: 
	Singleton() { cout << "Singleton()" << endl; } 
private: 
	Static Singleton * _pInstance; 
}; 
Singleton * Singleton::_pInstance = nullptr;
/*我们从懒汉式单例可以看到，单例实例被延迟加载，即只有在真正使用的时候才会实例化一个对象并交给自己的引用。
这种写法起到了Lazy Loading的效果，但是只能在单线程下使用。如果在多线程下，一个线程进入了if (nullptr == _pInstance)判断语句块，还未来得及往下执行，另一个线程也通过了这个判断语句，这时便会产生多个实例。所以在多线程环境下不可使用这种方式。
如果使用懒汉模式需要使用锁机制，防止多次访问,可以这样，第一次判断为空不加锁，若为空，再进行加锁判断是否为空，若为空则生成对象。*/
```

```c++
//饿汉式
class Singleton { 
public: 
	static Singleton * getInstance() 
	{ 
		return _pInstance; 
	} 
private: 
	Singleton() { cout << "Singleton()" << endl; } 
private: 
	Static Singleton * _pInstance; 
}; 
Singleton * Singleton::_pInstance = new Singleton();//直接初始化实例对象

/*我们知道，类加载的方式是按需加载，且加载一次。。因此，在上述单例类被加载时，就会实例化一个对象并交给自己的引用，供系统使用；而且，由于这个类在整个生命周期中只会被加载一次，因此只会创建一个实例，即能够充分保证单例。

优点：这种写法比较简单，就是在类装载的时候就完成实例化。避免了线程同步问题。

缺点：在类装载的时候就完成实例化，没有达到Lazy Loading的效果。如果从始至终从未使用过这个实例，则会造成内存的浪费。*/
```

## 工厂模式

所谓工厂，目的就是生产产品。所以，我们至少封装一个工厂类和一个产品类。一般来说呢，一个工厂又不会只生产一种产品。所以呢，我们把产品抽象一下，可以基于这个产品派生出多种类似的产品。这样就是一种**简单工厂**了，该工厂可以生产一个类型不同系列的多种产品。举例来说，一个制造厂生产民用飞机，战斗机，无人机。那么这三种飞机就是同一个类型（都是飞机），不过系列不同（应用场景不同）。

当然呢，有的时候为了分工明确，我不想一个工厂生产所有类型的产品，所以我把工厂抽象一下，不同类型的工厂生产相同类型不同系列的产品。这也是一种工厂模式，我们就称他为普通工厂吧。举例：我不能让战斗机与民用飞机在同一个厂子生产，所以呢，再弄一个新的战斗机制造厂专门生产战斗机，而民用机弄一个新的民用制造厂来制作。

**那么什么是抽象工厂？**
上面简单工厂不管怎么抽象，一个具体的工厂只生产一种特定的产品。抽象工厂的每个具体的工厂可以生产多种产品。举例：制造厂发现平时生产飞机多出来很多材料，扔了太浪费了，所以研究一下生产点小型汽车。这样，一个民用飞机制造厂又可以生产汽车了，然后改名为民用制造厂。战斗机工厂生产防弹车，改名叫军用制造厂。

**对比一下简单工厂，普通工厂与抽象工厂：**
简单工厂只有一种工厂，负责生产同一类型的所有产品，所以可以简单的都在一个工厂处理，工厂不需要抽象，但是由于产品可能有多种，所以产品有必要抽象。
普通工厂有多种工厂但是每个工厂只生产同一类型同一系列的一种产品，考虑到同一类型的产品可能有多种而且一个系列的产品对应一个工厂，所以产品与工厂都有必要抽象。
抽象工厂有多种工厂，每种工厂都可能生产多种类型的产品。考虑到同一类型的产品可能有多种，所以也有必要抽象。

```
游戏设计中的工厂模式： 游戏GamePlay逻辑游戏世界里面需要频繁生成与创建的对象都可以通过工厂模式来管理。如刷怪，然后可以根据怪的种类再进一步抽象成普通工厂。
```



### 简单工厂模式

简单工厂模式又称为静态工厂模式，实质是由一个工厂类根据传入的参数，动态决定应该创建哪一个产品类（这些产品类继承自一个父类或接口）的实例。简单工厂模式的创建目标，所有创建的对象都是充当这个角色的某个具体类的实例。在简单工厂模式中，可以根据参数的不同返回不同类的实例。简单工厂模式不属于`GOF`的23种设计模式，但现实中却经常会用到，而且思想也非常简单。

简单工厂模式的结构

简单工厂模式包含如下角色：
Factory：**工厂角色**
Product：**抽象产品角色**
`ConcreteProduct`：**具体产品角色**

| 工厂角色（Creator）              | 是简单工厂模式的核心，它负责实现创建所有具体产品类的实例。工厂类可以被外界直接调用，创建所需的产品对象。 |
| -------------------------------- | ------------------------------------------------------------ |
| 抽象产品角色（Product）          | 是所有具体产品角色的父类，它负责描述所有实例所共有的公共接口。 |
| 具体产品角色（Concrete Product） | 继承自抽象产品角色，一般为多个，是简单工厂模式的创建目标。工厂类返回的都是该角色的某一具体产品。 |

```c++
class Figure
{
public:
	virtual void display() const=0;
};
class Rectangle
: public Figure
{
public:
	void display() const override
	{	cout << "rectangle";}
};
class Circle 
: public Figure
{
public:
	void display() const override
	{	cout << "circle";}
};
class Triangle 
: public Figure
{
public:
	void display() const override
	{	cout << "triangle";}
};
class FigureFactory
{
public:
	static Figure * createRectangle()
	{
		return new Rectangle();
	}
	static Figure * createCircle()
	{
		return new Circle ();
	}
	static Figure * createTriangle()
	{
		return new Triangle();
	}
	static Figure * createTrapezoid()
	{
		return 
	}

};
```

**简单工厂模式专门定义一个类来负责创建其他类的实例，被创建的实例通常都具有共同的父类。**
**简单工厂模式包含三个角色：工厂角色负责实现创建所有实例的内部逻辑；抽象产品角色是所创建的所有对象的父类，负责描述所有实例所共有的公共接口；具体产品角色是创建目标，所有创建的对象都充当这个角色的某个具体类的实例。**
简单工厂模式的**要点**在于：当你需要什么，只需要传入一个正确的参数，就可以获取你所需要的对象，而无须知道其创建细节。
简单工厂模式**最大的优点**在于实现对象的创建和对象的使用分离，将对象的创建交给专门的工厂类负责，但是其最大的缺点在于工厂类不够灵活，增加新的具体产品需要修改工厂类的判断逻辑代码，而且产品较多时，工厂方法代码将会非常复杂。
简单工厂模式**适用情况**包括：工厂类负责创建的对象比较少；客户端只知道传入工厂类的参数，对于如何创建对象不关心。

### 工厂方法模式

工厂方法模式(**又称工厂模式**)是简单工厂模式的进一步抽象和推广。由于使用了面向对象的多态性，工厂方法模式保持了简单工厂模式的优点，而且克服了它的缺点。在工厂方法模式中，核心的工厂类不再负责所有产品的创建，而是将具体创建工作交给子类去做。这个核心类仅仅负责给出具体工厂必须实现的接口，而不负责哪一个产品类被实例化这种细节，这使得工厂方法模式可以允许系统在不修改工厂角色的情况下引进新产品。

**抽象产品角色（Product）**：定义产品的接口
**具体产品角色（`ConcreteProduct`）** ：实现接口Product的具体产品类
**抽象工厂角色（Creator）** ：声明工厂方法（`FactoryMethod`），返回一个产品
**真实的工厂（`ConcreteCreator`）**：实现`FactoryMethod`工厂方法，由客户调用，返回一个产品的实例

```C++
class Figure
{
public:
	virtual void display() const=0;
	virtual ~Figure() {cout << "~Figure()" << endl;	}

};
class Rectangle
: public Figure
{
public:
	Rectangle()
	{}
	void display() const override
	{	cout << "rectangle";}
	~Rectangle() {	cout << "~Rectangle()" << endl;	}
};

class Circle 
: public Figure
{
public:
	Circle()
	{}
	void display() const override
	{	cout << "circle";}
	
	~Circle() {	cout << "~Circle()" << endl;	}
};

class Triangle 
: public Figure
{
public:
	Triangle()
	{}
	void display() const override
	{	cout << "triangle";}
	~Triangle(){	cout << "~Triangle()" << endl;	}
};

class Factory
{
public:
	virtual Figure * create()=0;
	virtual ~Factory(){cout << "~Factory()" << endl;	}
};

class RectangleFactory
: public Factory
{
public:
	Figure * create()
	{
		return new Rectangle(10, 11);
	}
	~RectangleFactory() {	cout << "~RectangleFactory()" << endl;	}
};

class CircleFactory
: public Factory
{
public:
	Figure * create()
	{
		return new Circle (5);
	}

	~CircleFactory(){	cout << "~CircleFactory()" << endl;	}

};

class TriangleFactory
: public Factory
{
public:
	Figure * create()
	{
		return new Triangle(3, 4, 5);
	}
	~TriangleFactory(){ cout << "~TriangleFactory()" << endl;	}

};
```

工厂方法模式和简单工厂模式的区别

**简单工厂模式：**

（1）工厂类负责创建的对象比较少，由于创建的对象较少，不会造成工厂方法中的业务逻辑太过复杂。

（2）客户端只知道传入工厂类的参数，对于如何创建对象并不关心。

**工厂方法模式：**

（1）客户端不知道它所需要的对象的类。

（2）抽象工厂类通过其子类来指定创建哪个对象。

### 抽象工厂模式

**抽象工厂模式的定义：**为创建一组相关或相互依赖的对象提供一个接口，而且无需指定它们的具体类。
**工厂方法模式的定义：**为某个对象提供一个接口，而且无需指定它们的具体类。
都是子类实现接口的方法，并在子类写具体的代码。

工厂方法模式中也是可以有多个具体工厂，也是可以有多个抽象产品，和多个具体工厂、具体产品类。

区别是在抽象工厂接口类中，能创建几个产品对象。 

抽象工厂模式的工厂能创建多个相关的产品对象，而工厂方法模式的工厂只创建一个产品对象。

```C++
class Fruit;
class AbstractFactory{
    public:           
        virtual Fruit *CreateBanana() = 0;
        virtual Fruit *CreateApple() = 0;
    private:
};

class Fruit{
    public:
        virtual void sayname() = 0;
    private:
};

class NorthBanana : public Fruit{
    public:
        virtual void sayname(){
            cout<<"我是北方香蕉"<<endl;
        }   
};
class NorthApple : public Fruit{
    public:
        virtual void sayname(){
            cout<<"我是北方苹果"<<endl;
        }
};

class SouthBanana : public Fruit{
    public:
        virtual void sayname(){
            cout<<"我是南方香蕉"<<endl;
        }
};

class SouthApple : public Fruit{
    public:
        virtual void sayname(){
            cout<<"我是南方苹果"<<endl;
        }
};

class NorthFactory : public AbstractFactory{
    public:
        virtual Fruit *CreateBanana(){
            return new NorthBanana;
        }
        virtual Fruit *CreateApple(){
            return new NorthApple;
        }
    private:
};

class SouthFactory : public AbstractFactory{
    public:
        virtual Fruit *CreateBanana(){
            return new SouthBanana;
        }
        virtual Fruit *CreateApple(){
            return new SouthApple;
        }
    private:
};
```

**工厂模式与抽象工厂模式都属于创建型模式，在工厂模式中弥补了简单工厂模式的缺陷（不符合开闭原则），而在抽象工厂模式中弥补了工厂模式的不足（一个工厂只能生产一种产品）。**

## 观察者模式/发布订阅模式

当一个对象的状态发生改变时, 所有依赖于它的对象都得到通知并被自动更新。目标对象可以自由的绑定N个观察者，他不在乎订阅者都是谁，反正每次自己发生变化的时候就通知所有观察者，达到了降低耦合的目的。
发布订阅模式与观察者模式有区别，发布有一个统一的调度中心，订阅者（类比观察者）可以随意从调度中心订阅内容，发布者（类比目标对象）可以随时发布消息到调度中心，然后调度中心根据订阅者是否订阅来处理消息的发布。

```
游戏设计中的观察者模式：
比如任务系统中，所有接收同一个全局任务的玩家会收到任务的更新信息。该模式与C++语言中的多播代理的实现思路极为相似。
```

**观察者模式**定义了一种一对多的依赖关系，让多个观察者对象同时监听某一个主题对象。当主题对象状态发生变化时，会通知所有观察者，使观察者自己可以更新自己。

观察者模式的结构中包含四种角色：

（1）**主题（Subject）**：主题是一个接口，该接口规定了具体主题需要实现的方法，比如，添加、删除观察者以及通知观察者更新数据的方法。

（2）**观察者（Observer）**：观察者是一个接口，该接口规定了具体观察者用来更新数据的方法。

（3）**具体主题（`ConcreteSubject`）**：具体主题是实现主题接口类的一个实例，该实例包含有可以经常发生变化的数据。具体主题需使用一个集合，比如List，存放观察者的引用，以便数据变化时通知具体观察者。

（4）**具体观察者（`ConcreteObserver`）**：具体观察者是实现观察者接口类的一个实例。具体观察者包含有可以存放具体主题引用的主题接口变量，以便具体观察者让具体主题将自己的引用添加到具体主题的集合中，使自己成为它的观察者，或让这个具体主题将自己从具体主题的集合中删除，使自己不再是它的观察者。

观察者模式的使用场景

**（1）当一个对象的数据更新时需要通知其他对象，但这个对象又不希望和被通知的那些对象形成紧耦合。**

**（2）当一个对象的数据更新时，这个对象需要让其他对象也各自更新自己的数据，但这个对象不知道具体有多少对象需要更新数据。**

抽象观察者，为具体观察者定义update接口，在得到主题通知时更新自己。

```c++
class Observer
{
public:
    Observer() {}
    virtual ~Observer() {}
    virtual void update()  = 0;
};
```



抽象主题为独立模块，仅与抽象观察者耦合，维护观察者列表，向众多观察者广播事件。

```c++
class Subject
{
public:
    Subject() {}
    virtual ~Subject() {}

public:
    //增加观察者
    void attachObserver(Observer *observer) {m_observersList.push_back(observer);}
    //移除观察者
    void detachObserver(Observer *observer) {m_observersList.remove(observer);}
    //通知
    void notifyObsever()
         {
            //C++11增加了range-for用法，简化了遍历写法
            for(Observer *iter : m_observersList)  { iter->update(); }
         }

private:
    //具体观察者列表
    list<Observer *> m_observersList;
};
```



具体主题，设置相关状态，并将状态传递给具体观察者。

```c++
class ConcreteSubject : public Subject
{
public:
    ConcreteSubject() {}
    virtual ~ ConcreteSubject() {}

public:
    void setSubjectState(const string state) {m_subjectState = state;}
    string getSubjectState() {return m_subjectState;}

private:
    string m_subjectState;
};
```



具体观察者，实现抽象观察者的update接口，使自身状态与主题状态一致。具体观察者需要保存相关具体主题，也可以保存多个。类似于可以关注多个公众号。

```c++
class ConcreteObserver : public Observer
{
public:
    ConcreteObserver(ConcreteSubject *concreteSubject, string name):
        m_observerName(name),m_concreteSubject(concreteSubject) {}
    virtual ~ ConcreteObserver() {}

public:
    virtual void update()
    {
        //观察者状态由发布者提供
        m_observerState = m_concreteSubject->getSubjectState();
        std::cout << "Observer " << m_observerName << "  status is  " << m_observerState  << std::endl;
    }

private:
    string m_observerName;  //观察者名称
    string m_observerState;  //所观察的具体主题，可观察多个
    ConcreteSubject *m_concreteSubject;
};
```
