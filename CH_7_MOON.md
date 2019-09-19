출처: 이것이 C#이다 (저자: 박상현 , 출판사: 한빛미디어) 

***
# Chap 7. 클래스
***

## What is Class? 
- 데이터와 메소드를 묶는 집합

```C#
class Cat
{
  public string Name;
  public string Color;
  
  public void Meow(){
    Console.WriteLine("{0}: 야옹", Name);
  }
}
```

## What is Member?
- Class 안에 선언 된 변수들을 Field 라고 함
- 클래스 내에 선언되어 있는 요소(Field, Method ...)들을 Member 라고 함

*** 

## 객체를 생성하는 코드
```C#
Cat kitty = new Cat();
```
- Heap 영역에 객체를 생성, kitty는 생성한 객체를 가리킴 
- Cat( )은 Constructor(생성자)라고 하는 특별한 Method
- Constructor는 Class의 이름과 동일한 이름을 가지며, 객체를 생성하는 역할
- new와 Constructor는 항상 붙어서 선언

```
int a = new int();
```
- C#에서는 모든 형식이 Class이기 때문에, 다음과 같이 모든 데이터 형식에 new 연산자가 사용이 가능하지만
- 굳이 이렇게 할 필요성은? X
***

## Constructor & Finalizen 
- 생성자 & 종료자(=소멸자)
- 객체는 생성자에 의해 만들어지고 종료자에 의해 파괴
- 생성자는 Class와 이름이 같고, 반환 형식이 없음 -> 오직 객체 생성만을 위해 존재
- 생성자도 여느 메소드와 마찬가지로 오버로딩이 가능 -> 다양한 버전의 생성자 

### Constructor
```C#
class Cat
{
  public Cat(){                                   // Default Constructor 
    Name = "";
    Color = "";
    }
  public Cat(String _Name, string _Color){        // Constructor - Method Overroading
    Name = _Name;
    Color = _Color;
    }
  
  public string Name;
  public string Color;
  
  '''
}
```
- Overroading 된 생성자가 없을 때는 기본 생성자가 Default로 호출
- 매개변수를 주어서 생성자를 호출하게 되면, Overroading 된 생성자가 호출
- 그러나, Overroading 된 생성자가 있을 경우 C# 컴파일러는 Default 생성자를 자동으로 호출하지 않음
- 이는 프로그래머의 의도와 다르게 동작하는 코드가 제공되는 것을 방지하기 위한 것

***

### Finalizen
```C#
namespace Constructor
{
    class Cat
    {
        public Cat()
        {
            Name = "";
            Color = "";
        }

        public Cat(string _Name, string _Color)
        {
            Name = _Name;
            Color = _Color;
        }

        ~Cat()            // Finalizen - 종료자
        {
            Console.WriteLine($"{Name} : 잘가");
        }

        public string Name;
        public string Color;

        public void Meow()
        {
            Console.WriteLine($"{Name} : 야옹");
        }
    }

```

- Class 이름 앞에 ~를 붙인 형태
- 생성자와 달리, 매개변수도 없고, 한정자(ex. Public..)도 사용하지 않음
- Overroading도 불가능하며 직접 호출도 불가
- 그럼 언제 호출? -> CLR의 Garbage Collector가 객체가 소멸되는 시점을 판단해서 종료자를 호출!
- Garbage Collector가 알아서 객체를 소멸 해주기 때문에 종료자를 굳이 사용하지 않는 것을 권장 

***

## What is Static?
- C#에서의 static은 Field와 Method가 Class의 Instance가 아닌, Class 자체에 소속되도록 지정하는 한정자 
- static으로 한정하지 않은 Field는 자동으로 Instance에 소속되며, static으로 한정한 Field는 Class에 소속

<img src="https://github.com/bluein/C-Sharp-Study/blob/master/OOP/pic/moon_static.png" width="600" height="350" />

- static field를 사용하는 때는? 
-> 프로그램 전체에 걸쳐 공유해야 하는 변수가 있을 때

## Static Field 
```C#
using System;

class Global
{
    public static int Count = 0;
}

class ClassA
{
    public ClassA()
    {
        Global.Count++;
    }
}

class ClassB
{
    public ClassB()
    {
        Global.Count++;
    }
}

class MainApp
{
    static void Main()
    {
        Console.WriteLine($"Global.Count : {Global.Count}");
        
        new ClassA();
        new ClassA();
        new ClassB();
        new ClassB();

        Console.WriteLine($"Global.Count : {Global.Count}");
    }
}

```

```C#
Global.Count : 0
Global.Count : 4
```


- static method는 Class의 Instance를 생성하지 않아도 호출이 가능한 Method 라는 점을 기억!
- 그럼 Static Method의 반대는? -> Instance Method -> Class의 Instance를 생성해야만 호출이 가능

***

## 객체 복사 
### 얕은 복사 & 깊은 복사
### Shallow Copy & Deep Copy

### 얕은 복사

```C#
class MyClass{
  public int MyField1;
  public int MyField2;
}

MyClass source = new MyClass();
source.MyField1 = 10;
source.MyField2 = 20;

MyClass target = source;
target.MyField2 = 30;

Consol.WriteLine("{0} {1}", source.MyField1, source.MyField2);
Consol.WriteLine("{0} {1}", target.MyField1, target.MyField2);
  
```

```C#
10 30 10 30
```

- 10 20 10 30 결과가 나올 것이라 생각하겠지만, 아님!
- Class는 태생이 <b>참조 형식</b>이기 때문임!
- 객체는 Heap 영역에 참조 형식으로 할당되기 때문에, 값이 변경되는 것
- 이런 방식처럼, 객체를 복사할 때 참조만 살짝 복사하는 것을 <b>얕은 복사</b>라고 함


### 깊은 복사 
- Heap에 보관되어 있는 객체의 내용을 복사하여 별도의 Heap 공간에 복사 된 객체를 할당하는 것

```C#
using System;

namespace DeepCopy
{
    class MyClass
    {
        public int MyField1;
        public int MyField2;

        public MyClass DeepCopy()
        {
            MyClass newCopy = new MyClass();          // 객체를 Heap에 새로 할당해서
            newCopy.MyField1 = this.MyField1;         // 자신의 Member를 그곳에 일일이 복사
            newCopy.MyField2 = this.MyField2;

            return newCopy;
        }
    }

    class MainApp
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Shallow Copy");

            {
                MyClass source = new MyClass();     
                source.MyField1 = 10;               
                source.MyField2 = 20;

                MyClass target = source;
                target.MyField2 = 30;

                Console.WriteLine($"{source.MyField1} {source.MyField2}");
                Console.WriteLine($"{target.MyField1} {target.MyField2}");
            }

            Console.WriteLine("Deep Copy");

            {
                MyClass source = new MyClass();
                source.MyField1 = 10;
                source.MyField2 = 20;

                MyClass target = source.DeepCopy();
                target.MyField2 = 30;

                Console.WriteLine($"{source.MyField1} {source.MyField2}");
                Console.WriteLine($"{target.MyField1} {target.MyField2}");
            }
        }
    }
}
```

```C#
Shallow Copy
10 30
10 30
Deep Copy
10 20
10 30
```

***

## this 키워드

- this는 객체가 자신을 지칭할 때 사용하는 키워드
- 객체 외부에서는 객체의 Field나 Method에 접근할 때 객체의 이름을 사용
- 객체 내부에서는 자신의 Field나 Method에 접근할 때 this 키워드를 사용

<img src="https://github.com/bluein/C-Sharp-Study/blob/master/OOP/pic/this.JPG" width=300 height=200 />

- SetName()의 매개 변수도 Name이고, Employee Class의 Field도 Name 
- 어떤 Name에 접근하는 것인지? this 키워드를 통해 이러한 모호성을 해결

### this? Example
```C#
using System;

namespace This
{
    class Employee
    {
        private string Name;
        private string Position;

        public void SetName(string Name)
        {
            this.Name = Name;
        }

        public string GetName()
        {
            return Name;
        }

        public void SetPosition(string Position)
        {
            this.Position = Position;
        }

        public string GetPosition()
        {
            return this.Position;
        }
    }

    class MainApp
    {
        static void Main(string[] args)
        {
            Employee pooh = new Employee();
            pooh.SetName("Pooh");
            pooh.SetPosition("Waiter");
            Console.WriteLine($"{pooh.GetName()} {pooh.GetPosition()}");

            Employee tigger = new Employee();
            tigger.SetName("Tigger");
            tigger.SetPosition("Cleaner");
            Console.WriteLine($"{tigger.GetName()} {tigger.GetPosition()}");
        }
    }
}
```

```
Pooh Waiter
Tigger Cleaner
```

***

### Constructor Overroading 생성자 오버로딩

```C#
using System;

namespace ThisConstructor
{
    class MyClass
    {
        int a, b, c;

        public MyClass()
        {
            this.a = 5425;
            Console.WriteLine("MyClass()");
        }

        public MyClass(int b) : this()
        {
            this.b = b;
            Console.WriteLine($"MyClass({b})");
        }

        public MyClass(int b, int c) : this(b)
        {
            this.c = c;
            Console.WriteLine($"MyClass({b}. {c})");
        }

        public void PrintFields()
        {
            Console.WriteLine($"a:{a}, b:{b}, c:{c}");
        }
    }

    class MainApp
    {
        static void Main(string[] args)
        {
            MyClass a = new MyClass();
            a.PrintFields();
            Console.WriteLine();

            MyClass b = new MyClass(1);
            b.PrintFields();
            Console.WriteLine();

            MyClass c = new MyClass(10, 20);
            c.PrintFields();
        }
    }
}
```

```
MyClass()
a:5425, b:0, c:0

MyClass()
MyClass(1)
a:5425, b:1, c:0

MyClass()
MyClass(10)
MyClass(10, 20)
a:5425, b:10, c:20
```

### 객체 지향 프로그래밍의 3대 특성
1. Encapsulation 은닉성 (= 캡슐화)
2. Inheritance 상속성
3. Polymorphism 다형성


***

## Access Modifier 접근 한정자
- 감추고 싶은 것은 감추고, 보여주고 싶은 것은 보여줄 수 있도록 지정 (Encapsulation)
- Field, Method, Property 등 모든 요소에 사용 가능
- 예를 들어, Class에 선언되어 있는 Field와 Method 중에 어떤 것들은 사용자가 볼 수 있도록 하는 것이 있고, 아닌 것이 있음
- 특히 Field는 상수를 제외하고 <b>무조건</b> 감추는 것이 좋음!

#### 여섯 가지 접근 한정자
<img src="https://github.com/bluein/C-Sharp-Study/blob/master/OOP/pic/access.JPG" width=700 height=270 />

#### 파생 클래스(Derived Class) 란? 
- 기초 클래스의 모든 특성을 물려받아 새롭게 작성된 클래스

### Example 1
<img src="https://github.com/bluein/C-Sharp-Study/blob/master/OOP/pic/access2.JPG" width=600 height=350 />

#### 중요한 사실
- 접근 한정자로 수식하지 않은 클래스의 멤버는 무조건 private로 접근 수준이 자동 설정된다는 점! 

### Example 2
```C#
using System;

namespace AccessModifier
{
    class WaterHeater
    {
        protected int temperature;    // protected로 지정

        public void SetTemperature(int temperature)   // -5~42 값이 아닌 경우에 대한 예외 처리 
        {
            if (temperature < -5 || temperature > 42)
            {
                throw new Exception("Out of temperature range");
            }

            this.temperature = temperature;   // 외부에서 접근이 불가능. 이렇게 public 메소드를 통해 접근해야 함
        }

        internal void TurnOnWater( )
        {
            Console.WriteLine($"Turn on water : {temperature}");
        }
    }

    class MainApp
    {
        static void Main(string[] args)
        {
            try
            {
                WaterHeater heater = new WaterHeater();
                heater.SetTemperature(20);
                heater.TurnOnWater();

                heater.SetTemperature(-2);
                heater.TurnOnWater();

                heater.SetTemperature(50);      // 예외 발생 -> catch 블록으로 이동
                heater.TurnOnWater();
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
            }            
        }
    }
}
```

***

## 상속 (Inheritance) ?
- Base Class로 부터 Field, Method, Property 같은 Member들을 그대로 물려 받은 Derived Class를 만드는 것
- (부모 클래스 - 자식 클래스) (기반 클래스 - 파생 클래스) 라고도 함

### 형태
```C#
class 기반 클래스
{
    // 멤버 선언
}

class 파생 클래스 : 기반 클래스
{
    // 아무 멤버를 선언하지 않아도, 기반 클래스의 모든 것을 물려 받아 갖게 됨
    // <b>단, private로 선언된 멤버는 예외!</b>
}
```

#### 중요한 점
- 파생 클래스는 객체를 생성할 때 내부적으로 기반 클래스의 생성자를 호출한 뒤 자신의 생성자를 호출
- 객체가 소멸할 때는 반대의 순서로 (파생 클래스 -> 기반 클래스) 종료자를 호출


### Example 1

```C#
class Base
{
    public Base()
    {
        Console.WriteLine("Base()");
    }
    
    ~Base()
    {
        Console.WriteLine("~Base()");
    }
}

class Derived : Base
{
    public Derived()
    {
        Console.WriteLine("Derived()");
    }
    
    ~Derived()
    {
        Console.WriteLine("~Derived()");
    }
}
```

```
Base()
Derived()
~Derived()
~Base()
```

#### 호출 순서
1. 기반 클래스의 생성자 호출
2. 파생 클래스의 생성자 호출
3. 파생 클래스의 종료자 호출
4. 기반 클래스의 종료자 호출


***

#### Q. 만약 기반 클래스의 생성자가 매개 변수를 입력받도록 선언되어 있다면, 파생 클래스의 인스턴스를 생성할 때 호출되는 기반 클래스의 생성자에게는 어떻게 매개 변수를 잘 전달해줄 수 있을까?
A. base()를 이용 -> this()가 자기 자신의 생성자인것처럼, base()는 기반 클래스의 생성자임. 따라서 base()에 매개 변수를 넘겨 호출하면 우리가 원했던 것처럼 base() 생성자를 통해 Name Field를 초기화할 수 있음

### Example 1 - base 키워드
```C#
class Base
{
    public void BaseMethod()
    { /* ... */ }
}

class Derived : Base
{
    public void DerivedMethod()
    {
        base.BaseMethod();      // base 키워드를 통해 기반 클래스에 접근 가능
    }
}
```


### Example 2 - base 키워드 (매개 변수 전달)
```C#
class Base
{
    protected string Name;
    
    public Base(string Name)
    { 
        this.Name = Name;
    }
}

class Derived : Base
{
    public void Derived(string Name) : base(Name)       // Base(string Name)을 호출
    {
        Console.WriteLine("{0}.Derived()", this.Name);      
    }
}

```

### Example 3
```C#
using System;

namespace Inheritance
{
    class Base
    {
        protected string Name;
        public Base(string Name)
        { 
            this.Name = Name;
            Console.WriteLine($"{this.Name}.Base()");
        }

        ~Base()
        {
            Console.WriteLine($"{this.Name}.~Base()");
        }

        public void BaseMethod()
        {
            Console.WriteLine($"{Name}.BaseMethod()");
        }     
    }

    class Derived : Base
    {
        public Derived(string Name) : base(Name)
        {
            Console.WriteLine($"{this.Name}.Derived()");
        }
        
        ~Derived()
        {
            Console.WriteLine($"{this.Name}.~Derived()");
        }

        public void DerivedMethod()
        {
            Console.WriteLine($"{Name}.DerivedMethod()");
        }
    }

    class MainApp
    {
        static void Main(string[] args)
        {
            Base a = new Base("a");
            a.BaseMethod();

            Derived b = new Derived("b");
            b.BaseMethod();
            b.DerivedMethod();
        }
    }
}
```

```
a.Base()
a.BaseMethod()
b.Base()
b.Derived()
b.BaseMethod()
b.DerivedMethod()
b.~Derived()
b.~Base()
a.~Base()
```

#### Q. 자식이 달라고 하면 부모는 무조건 물려줘야 하나요?
- 그렇지 않음. 의도하지 않은 상속을 막기 위해, 상속이 불가능하도록 기반 클래스에 선언할 수 있음
- sealed 키워드 이용

```C#
sealed class Base
{
    // ...
}

class Derived : Base        // compile error
{

}

```

***
## 기반-파생 클래스 사이의 형식 변환
- 기반 클래스와 파생 클래스 사이에서는 형식 변환이 가능
- 파생 클래스의 인스턴스는 기반 클래스의 인스턴스로서도 사용 가능
- 이는 <b>코드의 생산성 향상</b>과 관련이 있음


### 형변환을 위한 두 개의 연산자
#### is 
- 객체가 해당 형식에 해당하는지를 검사하여 그 결과를 bool 값으로 반환
#### as 
- 형식 변환 연산자와 같은 역할을 함 
- 다만 형변환 연산자가 변환에 실패하는 경우 예외를 던지는 반면, as 연산자는 객체 참조를 null로 만든다는 점이 다름


### is 연산자
```C#
Mammal mammal = new Dog();
Dog dog;

if (mammal is Dog)
{
    dog = (Dog)mammal;        // mammal 객체가 Dog 형식임을 확인했으므로 안전하게 형식 변환이 이뤄짐
    dog.Bark();
}
```

- 일반적으로 형식 변환 연산자 대신, <b>as 연산자를 사용하는 쪽이 권장</b>
-> 형식 변환에 실패하더라도 예외가 일어나 갑자기 코드의 실행이 점프하는 일이 없으므로 코드를 관리하기가 더 수월하기 때문 

- 단, as 연산자는 참조 형식에 대해서만 사용이 가능!
- 값 형식의 객체는 기존의 형식 변환 연산자를 이용해야 함


## Type Casting
```C#
using System;

namespace TypeCasting
{
    class Mammal
    {
        public void Nurse() 
        { 
            Console.WriteLine("Nurse()");
        }
    }

    class Dog : Mammal
    {
        public void Bark() 
        { 
            Console.WriteLine("Bark()");
        }
    }

    class Cat : Mammal
    {
        public void Meow()
        {
            Console.WriteLine("Meow()");
        }
    }


    class MainApp
    {
        static void Main(string[] args)
        {
            Mammal mammal = new Dog();
            Dog    dog;

            if (mammal is Dog)
            {
                dog = (Dog)mammal;
                dog.Bark();
            }

            Mammal mammal2 = new Cat();

            Cat cat = mammal2 as Cat;
            if (cat != null)
                cat.Meow();

            Cat cat2 = mammal as Cat;
            if (cat2 != null)
                cat2.Meow();
            else
                Console.WriteLine("cat2 is not a Cat");
        }
    }
}
```

```
Bark()
Meow()
cat2 is not a Cat
```

***

## Overriding & Polymorphism (오버라이딩과 다형성)
- 객체가 여러 형태를 가질 수 있음 -> 다형성
- 다시 말해, 자신으로부터 상속받아 만들어진 파생 클래스를 통해 다형성을 실현한다는 의미


```
class ArmorSuite
{
    public virtual void Initialize()
    {
        Console.WriteLine("Armored");
    }
}
```
- 예를 들어 토니 스타크가 Initialize()라는 메소드를 갖고 있는 ArmorSuite 클래스를 만들었다고 해보자
- 토니는 이 ArmorSuite를 업그레이드 하기로 함
- 수중 전투용, 비행용 등의 Variation이 나올 수 있으므로, ArmorSuite를 뜯어 고치기보다 ArmorSuite를 상속하는 파생 클래스를 만들기로 함

```C#
class IromMan : ArmorSuite
{
    // ...
}

class WarMachine : ArmorSuite
{
    // ...
}
```

- ArmorSuite의 기본 기능은 사람을 보호하는 수트를 장착하는 것
- 하지만, 새로운 Suite에서는 새로운 기능이 필요 -> 상속 받은 Initialize()를 재정의 -> <b>Overriding</b>

#### Method Overriding을 위한 한 가지 조건
- Overriding을 할 메소드가 virtual 키워드로 한정되어 있어야 함
- (메소드를 overriding 하고 있다는 사실을 컴파일러에게 알려야 하기 때문!)
- 파생 클래스에선 override 키워드 사용

```C#
class IronMan : ArmorSuite
{
    public override void Initialize()       // Initialize Overriding
    {
        base.Initialize();
        Console.WriteLine("Repulsor Rays Armed");
    }
}

class WarMachine : ArmorSuite
{
    public override void Initialize()       // Initialize Overriding
    {
        base.Initialize();
        Console.WriteLine("Double-Barrel Cannons Armed");
        Console.WriteLine("Micro-Rocket Launcher Armed");
    }
}
```


#### 여기서 잠깐
- private로 선언한 메소드는 Overriding 할 수 없음!
- private로 선언된 멤버는 어차피 파생 클래스에서 보이지 않음



## Overriding Example
```C#
using System;

namespace Overriding
{
    class ArmorSuite
    {
        public virtual void Initialize()
        {
            Console.WriteLine("Armored");
        }
    }

    class IronMan : ArmorSuite
    {
        public override void Initialize()
        {
            base.Initialize();
            Console.WriteLine("Repulsor Rays Armed");
        }
    }

    class WarMachine : ArmorSuite
    {
        public override void Initialize()
        {
            base.Initialize();
            Console.WriteLine("Double-Barrel Cannons Armed");
            Console.WriteLine("Micro-Rocket Launcher Armed");
        }
    }

    class MainApp
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Creating ArmorSuite...");
            ArmorSuite armorsuite = new ArmorSuite();
            armorsuite.Initialize();

            Console.WriteLine("\nCreating IronMan...");
            ArmorSuite ironman = new IronMan();
            ironman.Initialize();

            Console.WriteLine("\nCreating WarMaching...");
            ArmorSuite warmachine = new WarMachine();
            warmachine.Initialize();
        }
    }
}
```

```
Creating ArmorSuite...
Armored

Creating IronMan
Armored
Repulsor Rays Armed

Creating WarMachine...
Armored
Double-Barrel Cannons Armed
Micro-Rocket Launcher Armed
```

***

## Method Hiding (메소드 숨기기)
- CLR에게 기반 클래스에서 구현된 버전의 메소드를 감추고, 파생 클래스에서 구현된 버전만 보여주는 것
- 파생 클래스 버전의 메소드에 <b>new 한정자</b> 사용

```C#
class Base
{
    public void MyMethod()
    {
        Console.WriteLine("Base.MyMethod()");
    }
}

class Derived : Base
{
    public new void MyMethod()
    {
        Console.WriteLine("Derived.MyMethod()");      // Base.MyMethod()를 감추고, Derived 클래스에서 구현된 MyMethod()만 노출
    }
}
```

#### Method Hiding을 한 것을 호출
```C#
Derived derived = new Derived();
derived.MyMethod();
```

#### 그냥 숨기고만 있을 뿐, Base 버전의 Method()도 살아 있음!
```C#
Base baseOrDerived = new Derived();
baseOrDerived.MyMethod();         // base 버전의 MyMethod를 실행
```

### Method Hiding Example
```C#
using System;

namespace MethodHiding
{
    class Base
    {
        public void MyMethod()
        {
            Console.WriteLine("Base.MyMethod()");
        }
    }

    class Derived : Base
    {
        public new void MyMethod()
        {
            Console.WriteLine("Derived.MyMethod()");
        }
    }
    
    class MainApp
    {
        static void Main(string[] args)
        {
            Base baseObj = new Base();
            baseObj.MyMethod();

            Derived derivedObj = new Derived();
            derivedObj.MyMethod();

            Base baseOrDerived = new Derived();
            baseOrDerived.MyMethod();
        }
    }
}
```

```
Base.MyMethod()
Derived.MyMethod()
Base.MyMethod()
```

***

## 오버라이딩 봉인하기
- 클래스가 상속이 되지 않도록 봉인하는 것처럼
- 메소드도 오버라이딩 되지 않도록 봉인할 수 있음
- sealed 한정자

```C#
class Base
{
    public virtual void SealMe()
    {
        // ...
    }
}

class Derived : Base
{
    public sealed Override Void SealMe()
    {
        //...
    }
}
```

### Sealed Method Example
```C#
using System;

class  Base
{
    public virtual void SealMe()
    {
    }
}

class Derived : Base
{
    public sealed override void SealMe()
    {
    }
}

class WantToOverride : Derived
{
    public override void SealMe()     // Sealed 된 Method는 Overriding이 불가!
    {
    }
}

class MainApp
{
    static void Main(string[] args)
    {            
    }
}
```

- 실행 결과는 Compile Error !

***

## Nested Class (중첩 클래스)
- 클래스 안에 선언되어 있는 클래스

```C#
class OuterClass
{
    class NestedClass     // 중첩 클래스
    {
    }
}
```

- 객체를 생성하거나 객체의 메소드를 호출하는 방법도 보통의 클래스와 다르지 않음
- 한 가지 다른 점이 있다면, <b>자신이 소속되어 있는 클래스의 멤버에 자유롭게 접근할 수 있다는 사실!</b>
- 심지어 private 멤버에도 접근이 가능!



```C#
class OuterClass
{
    private int OuterMember;
    
    class NestedClass     // 중첩 클래스
    {
        public void DoSomething()
        {
            OuterClass outer = new OuterClass();
            outer.OuterMember = 10;       // OuterClass의 private 멤버에 접근하여 값을 할당하거나 읽을 수 있음!
        }
    }
}
```

#### 중첩 클래스는 왜 쓸까?
- 클래스 외부에 공개하고 싶지 않은 형식을 만들고자 할 때
- 현재 클래스의 일부분처럼 표현할 수 있는 클래스를 만들고자 할 때 

- <b> 다른 클래스의 private 멤버에도 마구 접근할 수 있는 중첩 클래스는 은닉성을 무너뜨리기는 하지만, 보다 유연한 표현력을 프로그래머에게 가져다 준다는 장점이 있음!</b>


## Nested Class Example
```C#
using System;
using System.Collections.Generic;

namespace NestedClass
{
    class Configuration
    {
        List<ItemValue> listConfig = new List<ItemValue>();

        public void SetConfig(string item, string value)
        {
            ItemValue iv = new ItemValue();
            iv.SetValue(this, item, value);
        }

        public string GetConfig(string item)
        {
            foreach (ItemValue iv in listConfig)
            {
                if (iv.GetItem() == item)
                    return iv.GetValue();
            }

            return "";
        }
        
        // 중첩 클래스 -> private로 선언 했기 때문에 Configuration 클래스 밖에선 보이지 않음
        private class ItemValue     
        {
            private string item;
            private string value;

            public void SetValue(Configuration config, string item, string value)
            {
                this.item  = item;
                this.value = value;

                bool found = false;
                
                // 중첩 클래스는 상위 클래스의 멤버에 자유롭게 접근이 가능!
                for (int i = 0; i < config.listConfig.Count; i++)   
                {
                    if (config.listConfig[i].item == item)
                    {
                        config.listConfig[i] = this;
                        found = true;
                        break;
                    }
                }

                if (found == false)
                    config.listConfig.Add(this);
            }

            public string GetItem()
            { return item; }
            public string GetValue()
            {   return value;   }
        }        
    }

    class MainApp
    {
        static void Main(string[] args)
        {
            Configuration config = new Configuration();
            config.SetConfig("Version", "V 5.0");
            config.SetConfig("Size", "655,324 KB");

            Console.WriteLine(config.GetConfig("Version"));
            Console.WriteLine(config.GetConfig("Size"));

            config.SetConfig("Version", "V 5.0.1");
            Console.WriteLine(config.GetConfig("Version"));
        }
    }
}

```

```
V 5.0
655,324 KB
V 5.0.1
```

***

## Partial Class (분할 클래스)
- 여러 번 나눠서 구현하는 클래스
- 클래스의 구현이 길어질 경우, 여러 파일에 나눠서 구현이 가능

```C#
partial class MyClass
{
    public void Method1(){ }
    public void Method2(){ }
}

partial class MyClass     // Class 이름 동일
{
    public void Method3(){ }
}

```

### Partial Class Example
```C#
using System;

namespace PartialClass
{
    partial class MyClass
    {
        public void Method1()
        {
            Console.WriteLine("Method1");
        }

        public void Method2()
        {
            Console.WriteLine("Method2");
        }
    }

    partial class MyClass
    {
        public void Method3()
        {
            Console.WriteLine("Method3");
        }

        public void Method4()
        {
            Console.WriteLine("Method4");
        }
    }

    class MainApp
    {
        static void Main(string[] args)
        {
            MyClass obj = new MyClass();
            obj.Method1();
            obj.Method2();
            obj.Method3();
            obj.Method4();
        }
    }
}

```

```
Method1
Method2
Method3
Method4
```

***

## Extension Method (확장 메소드)
- 기존 클래스의 기능을 확장하는 기법
- 상속과는 다름
- <b>"기존 클래스"</b>의 기능을 확장!

```C#
namespace MyExtension
{
    public static class IntegerExtension
    {
        public static int Power(this int myInt, int exponent)
        {
            //
        }
    }
}
```

### Extension Method Example
```C#
using System;
using MyExtension;

namespace MyExtension
{
    public static class IntegerExtension
    {
        public static int Square(this int myInt)
        {
            return myInt * myInt;
        }

        public static int Power(this int myInt, int exponent)
        {
            int result = myInt;
            for (int i = 1; i < exponent; i++)
                result = result * myInt;

            return result;
        }
    }
}

namespace ExtensionMethod
{
    class MainApp
    {
        static void Main(string[] args)
        {
            Console.WriteLine($"{3}^2 : {3.Square()}");
            Console.WriteLine($"{3}^{4} : {3.Power(4)}");
            Console.WriteLine($"{2}^{10} : {2.Power(10)}");
        }
    }
}
```

***

## Structure (구조체)


```C#
struct MyStruct
{
    public int MyField1
    public int MyField2
    
    public void MyMethod()
    {
        //...
    }
}
```

### C# 에서의 클래스와 구조체의 차이?
<img src="https://github.com/bluein/C-Sharp-Study/blob/master/OOP/pic/stc.JPG" width=500 height=200 />

- 가장 중요한 차이는 클래스는 참조 형식이고, 구조체는 값 형식!
- 인스턴스의 사용이 끝나면 즉시 메모리에서 제거된다는 점과 가비지 콜렉터를 덜 귀찮게 한다는 점에서 구조체가 이점을 가짐
- 예를 들어, 3차원 게임을 C#으로 만든다고 할 때 화면에 나타나야 할 수 많은 좌표들(약 1만개라고 가정)의 사용이 끝나면 즉시 메모리에서 해제되는 것과 메모리에 남아서 가비지 콜렉터를 괴롭히는 것. 이 정도만 생각해도 구조체를 사용할만한 동기를 가짐!



```
MyStruct s;
s.MyField1 = 1;
s.MyField2 = 2;

MyStruct t;
t = s;
s.MyField1 = 3;
```

- 구조체는 생성자를 호출할 때가 아니면 굳이 new 연산자를 사용하지 않아도 인스턴스를 만들 수 있음 
- 구조체는 매개 변수가 없는 생성자를 선언할 수 없음

### Structure Example
```C#
using System;

namespace Structure
{
    struct Point3D
    {
        public int X;
        public int Y;
        public int Z;

        public Point3D(int X, int Y, int Z)
        {
            this.X = X;
            this.Y = Y;
            this.Z = Z;
        }

        public override string ToString()
        {
            return string.Format($"{X}, {Y}, {Z}");
        }
    }

    class MainApp
    {
        static void Main(string[] args)
        {
            Point3D p3d1;
            p3d1.X = 10;
            p3d1.Y = 20;
            p3d1.Z = 40;

            Console.WriteLine(p3d1.ToString());

            Point3D p3d2 = new Point3D(100, 200, 300);    // 물론 생성자를 이용한 인스턴스 생성도 가능
            Point3D p3d3 = p3d2;      // 구조체의 인스턴스를 다른 인스턴스에 할당하면 깊은 복사가 이뤄짐
            p3d3.Z = 400;

            Console.WriteLine(p3d2.ToString());
            Console.WriteLine(p3d3.ToString());
        }
    }
}

```

```
10, 20, 40
100, 200, 300
100, 200, 400
```


***

## Tuple (튜플)
- 여러 필드를 담을 수 있는 구조체
- 앞선 구조체와는 달리, 튜플은 형식의 이름을 가지지 않음
- 그래서 튜플은 응용 프로그램 전체에서 사용할 형식을 선언할 때가 아닌, 임시적으로 사용할 복합 데이터 형식을 선언할 때 적합!
- 튜플은 구조체이므로, 값 형식
- 값 형식은 생성된 지역을 벗어나면 스택에서 소멸되기 때문에 프로그램에 장기적인 부담을 주지 않는다는 장점이 있음

### Tuple Example
```C#
using System;

namespace Tuple
{
    class MainApp
    {
        static void Main(string[] args)
        {
            // 명명되지 않은 튜플
            var a = ("슈퍼맨", 9999);
            Console.WriteLine($"{a.Item1}, {a.Item2}");
            
            // 명명된 튜플
            var b = (Name: "박상현", Age: 17);
            Console.WriteLine($"{b.Name}, {b.Age}");

            // 분해
            var (name, age) = b; // (var name, var age) = b;
            Console.WriteLine($"{name}, {age}");

            // 명명된 튜플 = 명명되지 않은 튜플
            b = a; 
            Console.WriteLine($"{b.Name}, {b.Age}");
        }
    }
}

```

```
슈퍼맨, 9999
박상현, 17
박상현, 17
슈퍼맨, 9999
```









