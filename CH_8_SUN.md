# 8장 : 인터페이스와 추상 클래스
##### cf. 인터페이스 이름 앞에 'I'를 붙이는 것이 관례
- 다형성(Polymorphism) : 하나의 형태로 여러 작업을 할 수 있음
- 다형성이 적용되는 곳 : 오버로딩(Overloading), 오버라이딩(Overrideing), 업캐스팅(Upcasting), 다운캐스팅(Downcasting), 추상클래스의 상속관계, 인터페이스의 상속관계
- 업캐스팅 : 상위클래스로 하위클래스를 캐스팅 / 다형성을 대표
http://cafe.daum.net/csharp-novice/5ijJ/74
<br><br>
## 8.1 인터페이스의 선언
### -인터페이스란 ?
```C#
interface 인터페이스 이름
{
    void Add(string s);              // 메서드
    event StringListEvent Changed;   //이벤트
    int Count{get;set;}              // 속성
    string this[int index] {get;set} //프로퍼티
    }
```
1. 메서드, 이벤트, 인덱서, 프로퍼티로만 구성할 수 있음
2. 구현부가 없음
3. default : public(멤버에 어떠한 접근자/한정자도 붙이지 않음)
4. 인스턴스를 만들 수 없음
5. ★인터페이스는 인스턴스를 못 만들지만, 참조는 만들 수 있음★

### -class로 구현

```C#
interface ILogger
{
    void WriteLog( string log );
}

class ConsoleLogger : ILogger
{
    public void WriteLog(string message)
    {
        Console.WriteLine($"{DateTime.Now.ToLocalTime()} {message}");
    }
}

ILogger logger = new ConsoleLogger(); // 업캐스팅
logger.WriteLog("Hello, World!");
```
- 인터페이스를 상속받는 클래스에 인터페이스에 선언되어 있는 모든 구성 요소를 구현해줘야 함
- ★구현 시 public 한정자로 수식해야 함 (class 기본 : private)★
- 클래스의 인스턴스화는 가능
- ★인터페이스의 메서드를 구현할 때 override 키워드는 사용하지 않음★
<br><br>
## 8.2 인터페이스는 약속이다
```C#
using System;
using System.IO;

namespace interface_
{
    interface ILogger
    {
        void WriteLog(string message);
    }

    class ConsoleLogger : ILogger // 콘솔에 로그를 출력하는 클래스
    {
        public void WriteLog(string message)
        {
            Console.WriteLine("{0},{1}", DateTime.Now.ToLocalTime(), message);
        }
    }

    class FileLogger : ILogger // 파일로 로그를 저장하는 클래스
    {
        private StreamWriter writer;

        public FileLogger(string path)
        {
            writer = File.CreateText(path);
            writer.AutoFlush = true;
        }

        public void WriteLog(string messag)
        {
            writer.WriteLine("{0} {1}", DateTime.Now.ToShortTimeString(), messag);
        }
    }

    class ClimateMonitor
    {
        private ILogger logger;
        public ClimateMonitor(ILogger logger) 
        {
            this.logger = logger;
        }

        public void start()
        {
            while (true)
            {
                Console.Write("온도를 입력하세요:");
                string temperature = Console.ReadLine();
                if (temperature == "")
                {
                    break;
                }
                
                logger.WriteLog("현재 온도 : " + temperature);
            }
        }
    }
                
    class mainapp
    {
        static void Main(string[] args)
        {
            ClimateMonitor monitor = new ClimateMonitor(new FileLogger("mylog.txt")); 
            // ClimateMonitor의 매개변수자리 : 인터페이스 -> 인터페이스를 상속한 new FileLogger 클래스 입력
            // ★-> 업캐스팅, FileLogger의 객체 가리킴 ★
            // 만일 ConsoleLogger의 객체를 가리키고 싶으면 new ClimateMonitor(new ConsoleLogger());

            monitor.start();
        }
    }
}
```
<br><br>
## 8.3 인터페이스를 상속하는 인터페이스
- 클래스, 구조체, 인터페이스 : 인터페이스를 상속할 수 있음
- 인터페이스를 수정하고 싶지만 인터페이스를 상속하는 인터페이스를 이용해야 하는 경우
  1. 상속하려는 인터페이스가 소스 코드가 아닌, 어셈블리로만 제공되는 경우
  2. 상속하려는 인터페이스의 소스 코드를 가지고 있지만, 인터페이스를 상속하는 클래스들이 '이미' 존재하는 경우 
    <br>(원 인터페이스를 고치면 모든 클래스를 고쳐야 함)

```C#
interface 파생 인터페이스 : 부모 인터페이스
{
    // ... 추가할 구성 요소 목록
}
```
<br><br>
## 8.4 여러 개의 인터페이스, 한꺼번에 상속하기
```C#
using System;

namespace MultiInterfaceInheritance
{
    interface IRunnable
    {
        void Run();
    }

    interface IFlyable
    {
        void Fly();
    }

    class FlyingCar : IRunnable, IFlyable
    {
        public void Run()
        {
            Console.WriteLine("Run! Run!");
        }

        public void Fly()
        {
            Console.WriteLine("Fly! Fly!");
        }
    }

    class MainApp
    {
        static void Main(string[] args)
        {
            FlyingCar car = new FlyingCar();
            car.Run();
            car.Fly();

            IRunnable runnable = car as IRunnable; 
            runnable.Run();
            
            // car : 다중 상속-> 두 인터페이스 모두 사용 가능

            IFlyable flyable = car as IFlyable;
            flyable.Fly();
        }
    }
}
```
- 인터페이스는 다중 상속을 변칙적으로 지원한다. (일반적으로는 불가능)
- ★클래스와 인터페이스를 같이 상속하는 경우, 반드시 클래스명을 제일 앞에 명시함★
- ★명시적으로 구현하는 경우(ex. IRunnable.Run() ) 접근지정자(public)를 붙이지 않음★

<br><br>
## 8.5 추상클래스 : 인터페이스와 클래스 사이
```C#
absract class 추상클래스 이름
{
    // 클래스와 동일하게 구현
    // 추상 메소드도 가질 수 있다.
}
```

### ★추상클래스와 인터페이스의 공통점★
- 인스턴스를 가질 수 없다. 
- new 키워드로 자체적으로 초기화를 할 수 없다.
- 추상 메소드를 가질 수 있다. (추상클래스에서 추상메소드 한정자는 public, protected, internal, protected internal 중 하나)
- 추상 프로퍼티를 가질 수 있다.
### ★추상클래스와 인터페이스의 차이점★
- 추상클래스는 구현된 멤버를 포함할 수 있다.
- 인터페이스는 멤버의 접근지정자를 선언할 수 없다.


```C#
using System;

namespace AbstractClass
{
    abstract class AbstractBase
    {
        protected void PrivateMethodA()
        {
            Console.WriteLine("AbstractBase.PrivateMethodA()");
        }

        public void PublicMethodA()
        {
            Console.WriteLine("AbstractBase.PublicMethodA()");
        }

        public abstract void AbstractMethodA(); // 추상메소드 / 추상 프로퍼티 : abstract 한정자를 이용해 선언
    }

    class Derived : AbstractBase
    {
        public override void AbstractMethodA() //★ 추상클래스의 추상메소드 / 추상 프로퍼티 구현 시 override로 구현 ★
        {
            Console.WriteLine("Derived.AbstractMethodA()");
            PrivateMethodA();
        }
    }

    class MainApp
    {
        static void Main(string[] args)
        {
            AbstractBase obj = new Derived();
            obj.AbstractMethodA();
            obj.PublicMethodA(); 
            // obj.PrivateMethod()는 protected로 선언되었기 때문에 하위 클래스에서만 사용 가능
        }
    }
}

```

