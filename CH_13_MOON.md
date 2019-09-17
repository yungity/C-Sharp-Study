# Chap 13. 대리자와 이벤트

***

## 대리자? Delegate
- 콜백(Callback)을 구현하기 위해 사용
- 대리자는 메소드에 의한 참조
- 따라서, 자신이 참조할 메소드의 반환 형식과 매개 변수를 명시해줘야 함

#### 대리자 선언
```
delegate int MyDelegate(int a, int b);
```

#### 중요한 점
- 대리자는 인스턴스가 아닌 형식(Type) 임 
- 다시 말해, int, string과 같은 Type이며, "메소드를 참조하는 그 무엇"을 만들려면 MyDelegate의 인스턴스를 따로 만들어야 함!
 
### 대리자가 참조할 메소드 선언 & 참조 
```C#
int Plus(int a, int b)
{
    return a+b;
}

int Minus(int a, int b)
{
    return a-b;
}

delegate int MyDelegate(int a, int b);

MyDelegate Callback;

Callback = new MyDelegate(Plus);          // 대리자의 인스턴스를 만들 때도 new 연산자 사용
Console.WriteLine(Callback(3, 4));        // 7 출력

Callback = new MyDelegate(Minus);
Console.WriteLine(Callback(7, 5));        // 2 출력

```
### 대리자를 이용하여 콜백을 구현하는 과정 
<img src="https://github.com/bluein/C-Sharp-Study/blob/master/OOP/pic/dele.JPG" width=450 height=150 />

1) 대리자 선언
2) 대리자의 인스턴스를 생성. 인스턴스를 생성할 때는 대리자가 참조할 메소드를 매개 변수로 넘김
3) 대리자를 호출

***
 
### Delegate Example
```C#
using System;

namespace Delegate
{
    delegate int MyDelegate( int a, int b);     // 대리자 선언

    class Calculator
    {
        public int Plus(int a, int b)           // 대리자는 인스턴스 메소드도 참조할 수 있고 
        {
            return a + b;
        }

        public static int Minus(int a, int b)       // 대리자는 정적 메소드도 참조할 수 있음
        {
            return a - b;
        }
    }

    class MainApp
    {
        static void Main(string[] args)
        {
            Calculator Calc = new Calculator();
            MyDelegate Callback;

            Callback = new MyDelegate( Calc.Plus );   // 대리자를 메소드를 호출하듯 사용하면, 참조하고 있는 메소드가 실행 
            Console.WriteLine(Callback(3, 4));

            Callback = new MyDelegate(Calculator.Minus);
            Console.WriteLine(Callback(7, 5));
        }
    }
}

```

***

## 대리자는 왜 사용하는가?
- '값'이 아닌 '코드'자체를 매개 변수로 넘기고 싶을 때 사용
- 예를 들어, 배열을 정렬하는 메소드를 만들 때, 오름 차순으로 정렬할 것인지 내림 차순으로 정렬할 것인지 프로그래머가 아닌 '대리자'가 알아서 판단

### Example
```C#

// Step 1 - 대리자 선언
delegate int Compare(int a, int b);

// Step 2 - 대리자가 참조할 메소드 선언
static int AscendCompare(int a, int b)
{
    if (a>b)
        return 1;
    else if (a==b)
        return 0;
    else 
        return -1;
}

// Step 3 - 정렬할 배열과 비교 메소드를 참조할 대리자를 매개 변수로 받는 정렬 메소드 작성
static void BubbleSort(int[] DataSet, Compare Com)
{
    int i=0;
    int j=0;
    int temp=0;
    
    for (i=0; i<DataSet.Length-1; i++)
    {
        for (j=0; j<DataSet.Length-(i+1); j++)
        {
            if (Com(DataSet[j], DataSet[j+1]) > 0)
            {
                temp = DataSet[j+1];
                DataSet[j+1] = DataSet[j];
                DataSet[j] = temp;
            }
        }
    }
}

// 위 코드는 오름차순으로 정렬하도록 되어 있음.  
                                
```

***

### Using Callback Example
```C#
using System;

namespace UsingCallback
{
    delegate int Compare(int a, int b);

    class MainApp
    {
        static int AscendCompare(int a, int b)
        {
            if (a > b)
                return 1;
            else if( a == b )
                return 0;
            else
                return -1 ; 
        }

        static int DescendCompare(int a, int b)
        {
            if (a < b)
                return 1;
            else if (a == b)
                return 0;
            else
                return -1;
        }

        static void BubbleSort(int[] DataSet, Compare Comparer) 
        { 
            int i = 0; 
            int j = 0; 
            int temp = 0; 
 
            for ( i=0; i<DataSet.Length-1; i++ ) 
            {
                for (j = 0; j < DataSet.Length - (i + 1); j++)  
                { 
                    if ( Comparer( DataSet[j] , DataSet[j+1] ) > 0 ) 
                    { 
                        temp = DataSet[j+1]; 
                        DataSet[j+1] = DataSet[j]; 
                        DataSet[j] = temp; 
                    }
                }
            }
        }

        static void Main(string[] args)
        {
            int[] array = { 3, 7, 4, 2, 10 };

            Console.WriteLine("Sorting ascending...");
            BubbleSort(array, new Compare(AscendCompare));

            for (int i = 0; i<array.Length; i++)
                Console.Write($"{array[i]} ");

            int[] array2 = { 7, 2, 8, 10, 11 };
            Console.WriteLine("\nSorting descending...");
            BubbleSort(array2, new Compare(DescendCompare));            

            for (int i = 0; i < array2.Length; i++)
                Console.Write($"{array2[i]} ");

            Console.WriteLine();
        }
    }
}

```

```
Sorting ascending...
2 3 4 7 10
Sorting descending...
11 10 8 7 2 
```

***

## 일반화 대리자 Generic Delegate
- 대리자는 보통 메소드뿐 아니라, 일반화 메소드도 참조가 가능
- 물론 형식 매개 변수를 이용하여 선언 되어야 함

```
delegate int Compare<T>(T a, T b);
```

### Generic Delegate Example
```C#
using System;

namespace GenericDelegate
{
    delegate int Compare<T>(T a, T b);

    class MainApp
    {
        static int AscendCompare<T>(T a, T b) where T : IComparable<T>
        {
            return a.CompareTo(b);
        }

        static int DescendCompare<T>(T a, T b) where T : IComparable<T>
        {
            return a.CompareTo(b) * -1;  // -1을 곱하면 자신보다 큰 경우에는 1, 같으면 0, 작으면 -1을 반환
        }

        static void BubbleSort<T>(T[] DataSet, Compare<T> Comparer)
        {
            int i = 0;
            int j = 0;
            T temp;

            for (i = 0; i < DataSet.Length - 1; i++)
            {
                for (j = 0; j < DataSet.Length - (i + 1); j++)
                {
                    if (Comparer(DataSet[j], DataSet[j + 1]) > 0)
                    {
                        temp = DataSet[j + 1];
                        DataSet[j + 1] = DataSet[j];
                        DataSet[j] = temp;
                    }
                }
            }
        }

        static void Main(string[] args)
        {
            int[] array = { 3, 7, 4, 2, 10 };

            Console.WriteLine("Sorting ascending...");
            BubbleSort<int>(array, new Compare<int>(AscendCompare));

            for (int i = 0; i < array.Length; i++)
                Console.Write($"{array[i]} ");

            string[] array2 = { "abc", "def", "ghi", "jkl", "mno" };

            Console.WriteLine("\nSorting descending...");
            BubbleSort<string>(array2, new Compare<string>(DescendCompare));

            for (int i = 0; i < array2.Length; i++)
                Console.Write($"{array2[i]} ");

            Console.WriteLine();
        }
    }
}

```

```
Sorting ascending...
2 3 4 7 10
Sorting descending...
mno jkl ghi def abc
```

- System.Int32(int), System.Double(double)을 비롯한 모든 수치 형식과 System.String(string)은
- IComparable을 상속해서 CompareTo() 메소드를 구현하고 있기 때문

***

## 대리자 체인 Delegate Chain
- 대리자 하나가 여러 메소드를 동시에(순차적으로) 참조하는 것

```C#
delegate void ThereIsAFire(string location);

void Call119(string location)
{
    Console.WriteLine("소방서죠? 불났어요 주소는{0}", location);
}

void ShoutOut(string location)
{
    Console.WriteLine("피하세요! {0}에 불이 났어요!", location);
}

void Escape(string location)
{
    Console.WriteLine("{0}에서 나갑시다!", location);
}

ThereIsAFire Fire = new ThereIsAFire(Call119)         // Delegate Chain
                  + new ThereIsAFire(ShoutOut)
                  + new ThereIsAFire(Escape);
                  
               
```

### Delegate Chain Example
```C#
using System;

namespace DelegateChains
{
    delegate void Notify(string message);     // Notify 대리자 선언

    class Notifier    // Notify 대리자의 인스턴스 EventOccured를 가지는 클래스 Notifier 선언
    {
        public Notify EventOccured;
    }

    class EventListener
    {
        private string name;
        public EventListener(string name)
        {
            this.name = name;
        }

        public void SomethingHappend(string message)
        {
            Console.WriteLine($"{name}.SomethingHappened : {message}");
        }
    }

    class MainApp
    {
        static void Main(string[] args)
        {
            Notifier notifier = new Notifier();
            EventListener listener1 = new EventListener("Listener1");
            EventListener listener2 = new EventListener("Listener2");
            EventListener listener3 = new EventListener("Listener3");

            notifier.EventOccured += listener1.SomethingHappend;   // Delegate Chain 만들기
            notifier.EventOccured += listener2.SomethingHappend;
            notifier.EventOccured += listener3.SomethingHappend;
            notifier.EventOccured("You've got mail.");

            Console.WriteLine();

            notifier.EventOccured -= listener2.SomethingHappend;   // Chain 끊기
            notifier.EventOccured("Download complete.");

            Console.WriteLine();

            notifier.EventOccured = new Notify(listener2.SomethingHappend)   // Delegate Chain 만들기
                                  + new Notify(listener3.SomethingHappend);
            notifier.EventOccured("Nuclear launch detected.");

            Console.WriteLine();

            Notify notify1 = new Notify(listener1.SomethingHappend);
            Notify notify2 = new Notify(listener2.SomethingHappend);

            notifier.EventOccured =
                (Notify)Delegate.Combine( notify1, notify2);   // Delegate.Combine() 메소드를 이용한 Chain 만들기
            notifier.EventOccured("Fire!!");

            Console.WriteLine();

            notifier.EventOccured = 
                (Notify)Delegate.Remove( notifier.EventOccured, notify2);   // Remove() 메소드를 이용한 Chain 끊기               
            notifier.EventOccured("RPG!");
        }
    }
}

```


***

## 익명 메소드 Anonymous Method
- 이름이 없는 메소드
- 자신을 참조할 대리자의 형식과 동일한 형식으로 선언
- 에를 들어, 대리자에서 int 형식의 매개 변수를 세 개 받도록 선언했다면, 익명 메소드도 역시 동일하게 매개 변수를 받도록 구현해야 함
```C#
delegate int Calculate(int a, int b);

public static void Main()
{
    Calculate Calc;
    
    Calc = delegate(int a, int b)       // 익명 메소드 구현
           {
                 return a+b;
           };
           
    Console.WriteLine(Calc(3, 4));      // 익명 메소드 호출
}
```

#### 익명 메소드는 언제 사용?
- 예를 들어 대리자가 참조할 메소드를 넘겨야 할 일이 생겼는데, 이 메소드가 두 번 다시 사용할 일이 없다고 판단되면 그 때 익명 메소드를 사용



### Anonymous Method Example
```C#
using System;

namespace AnonymouseMethod
{
    delegate int Compare(int a, int b);

    class MainApp
    {
        static void BubbleSort(int[] DataSet, Compare Comparer)
        {
            int i = 0;
            int j = 0;
            int temp = 0;

            for (i = 0; i < DataSet.Length - 1; i++)
            {
                for (j = 0; j < DataSet.Length - (i + 1); j++)
                {
                    if (Comparer(DataSet[j], DataSet[j + 1]) > 0)
                    {
                        temp = DataSet[j + 1];
                        DataSet[j + 1] = DataSet[j];
                        DataSet[j] = temp;
                    }
                }
            }
        }

        static void Main(string[] args)
        {
            int[] array = { 3, 7, 4, 2, 10 };

            Console.WriteLine("Sorting ascending...");
            BubbleSort(array, delegate(int a, int b)       // Anonymous Method
                              {
                                if (a > b)
                                    return 1;
                                else if (a == b)
                                    return 0;
                                else
                                    return -1;
                              });

            for (int i = 0; i < array.Length; i++)
                Console.Write($"{array[i]} ");

            int[] array2 = { 7, 2, 8, 10, 11 };
            Console.WriteLine("\nSorting descending...");
            BubbleSort(array2, delegate(int a, int b)       // Anonymous Method
                               {
                                 if (a < b)
                                     return 1;
                                 else if (a == b)
                                     return 0;
                                 else
                                     return -1;
                               });

            for (int i = 0; i < array2.Length; i++)
                Console.Write($"{array2[i]} ");

            Console.WriteLine();
        }
    }
}

```


***

## 이벤트 : 객체에 일어난 사건 알리기
- 특정 사건이 일어났을 때 알려주는 객체를 만들 때 사용하는 것이 이벤트(Event)

```C#
// Step 1 - 대리자를 선언 (클래스 안, 밖 가능)
delegate void EventHandler(string message);


// Step 2 - 대리자의 인스턴스를 event 한정자로 수식해서 선언
class MyNotifier
{
     public event EventHandler SomethingHappened;
     public void DoSomething(int number)
     {
          int temp = number % 10;
          
          if (temp != 0 && temp % 3 ==0)
          {
               SomethingHappened(number));
          }
     }
}

// Step 3 - 이벤트 핸들러 작성, 등록, 호출
class MainApp
{
     static public void MyHandler(string message)
     {
          Console.WriteLine(message);
     }
     
     static void Main(string[] args)
     {
          MyNotifier notifier = new MyNotifier();
          notifier.SomethingHappened += new EventHandler(MyHandler);   // 이벤트 핸들러 등록
          
          for(int i=1; i<30; i++)
          {
               notifier.DoSomething(i);   // 이벤트 핸들러 호출
          }
     }
}

```

### Event Test Example
```C#
using System;

namespace EventTest
{
    delegate void EventHandler(string message);

    class MyNotifier 
    {
        public event EventHandler SomethingHappened;
        public void DoSomething(int number)
        {
            int temp = number % 10;

            if ( temp != 0 && temp % 3 == 0)
            {
                SomethingHappened(String.Format("{0} : 짝", number));
            }
        }
    }

    class MainApp
    {
        static public void MyHandler(string message)
        {
            Console.WriteLine(message);
        }

        static void Main(string[] args)
        {
            MyNotifier notifier = new MyNotifier();
            notifier.SomethingHappened += new EventHandler(MyHandler);

            for (int i = 1; i < 30; i++)
            {
                notifier.DoSomething(i);
            }
        }
    }
}

```

```
3 : 짝
6 : 짝
9 : 짝
13 : 짝
16 : 짝
19 : 짝
23 : 짝
26 : 짝
29 : 짝
```


***

#### 대리자와 이벤트의 차이?
- 이벤트는 외부에서 직접 사용할 수 없다는 점!

```C#
delegate void EventHandler(string message);

class MyNotifier
{
     public event EventHandler SomethingHappened;
     //...
}

class MainApp
{
     static void Main(string[] args)
     {
          MyNotifier noti = new MyNotifier();
          noti.SomethingHappened("테스트");      // Error! 이벤트는 객체 외부에서 직접 호출이 불가
     }
}
```

#### 대리자는 Callback 용도
#### 이벤트는 객체의 상태 변화나 사건 발생을 알리는 용도

