### goto
```c#
goto 레이블;

레이블 :
//이어지는 코드
```
goto 문은 레이블이 가리키는 곳으로 바로 뛰어 넘어간다. 많은 프로그래머들이 선호하는 문은 아니다. 코드의 흐름을 끊어서 코드를 읽기 어렵게 만들기 때문이다.
하지만 중첩된 반복문을 단번에 뚫고 나오는 등의 상황에선 사용된다.

```c#
using System;

namespace Goto
{ 
    class MainApp
    {
        
        static void Main(string[] args)
        {
            Console.Write("종료 조건(숫자)을 입력하세요 :");

            string input = Console.ReadLine();

            int input_number = Convert.ToInt32(input);
            int exit_number = 0;
            for(int i=0;i<2;i++)
            {
                for(int j =0; j<2;j++)
                {
                    for(int k=0;k<3;k++)
                    {
                        if (exit_number++ == input_number)
                            goto EXIT_FOR;
                        Console.WriteLine(exit_number);
                  
                    }
                }
            }
            goto EXIT_PROGRAM;

        EXIT_FOR:
            Console.WriteLine("\nEixt nested for...");

        EXIT_PROGRAM:
            Console.WriteLine("Exit program...");
        }
    }
}
```

### 메소드의 결과를 참조로 반환하기 

C#에서는 참조 반환값(ref return)이라 하여 메소드의 결과를 참조로 반환하는 방법이 있다. 이렇게 하면 다른 메소드에서 변경된 애들을 main에서 그대로 사용 할 수 있다. 이렇게 참조로 반환 은 결과를 담은 지역 변수를 참조 지역 변수(ref local)이라고 한다.
```C#
using System;

namespace RefReturn
{ 
    class Product
    {
        private int price = 100;
        public ref int GetPrice() // 반환 값을 ref int 로 
        {
            return ref price;
        }

        public void PrintPrice()
        {
            Console.WriteLine($"Price :{price}");
        }

        static void Main(string[] args)
        {
            Product carrot = new Product(); // 인스턴스 선언
            ref int ref_local_price = ref carrot.GetPrice();
            int normal_local_price = carrot.GetPrice();

            carrot.PrintPrice();
            Console.WriteLine($"Ref Local Price: {ref_local_price}");
            Console.WriteLine($"Normal Local Price : {normal_local_price}");

            ref_local_price = 200; // ref의 값을 바꾸면??

            carrot.PrintPrice();
            Console.WriteLine($"Ref Local Price : {ref_local_price}");
            Console.WriteLine($"Normal Local Price : {normal_local_price}");
        }
    }
}
```

### 출력 전용 매개 변수
ref 만으로 메소드로 부터 얻어 올 수있지만, C#에서는 out 키워드를 이용한 "출력 전용 매개 변수"로 더 안전하게 사용할 수 있다.
out 키워드를 이용해서 매개 변수를 넘길 때는 메소드가 해댕 매개 변수에 결과를 저장하지 않으면 컴파일러가 에러 메시지를 출력한다.
또한 메소드르 호출하는 쪽은 초기화를 하지 않은 지역 변수를 메소드의 out 매개 변수로 넘기는 것이 가능하다.

```c#
using System;

namespace usingout
{ 
    class MainApp
    {
        static void Divide(int a, int b, out int quotient, out int remainder)
        {
            quotient = a / b;
            remainder = a % b;
        }

        static void Main(string[] args)
        {
            int a = 20;
            int b = 3;
            // int c : 메소드에서 가져올꺼이므로 필요 x
            // int d : 위와 마찬가지

            Divide(a, b, out int c, out int d);
            Console.WriteLine($"a:{a}, b:{b}, a/b:{c}, a%b:{d}");
        }
    }
}
```
### 메소드 오버로딩
메소드 오버로딩(Metohd Overloading)이란 하나의 메소드 이름에 여러 개의 구현을 올리는 것을 뜻한다. 메소드의 이름은 하나지만, 들어오는 매개변수만을 분석하여(컴파일 과정에서) 어떤 버젼이 호출될지 찾는다. 메소드 오버로딩은 이름 짓는 고민도 줄여주며 코드를 일관성 있게 유지해준다. 일관성 있는 코드는 작성자에게도 도움을 주지만, 메소드의 사용자에게도 높은 생산성을 제공한다.(WriteLine 메소드는 19개 버전을 오버로딩하고 있는데, 19개 버전이 모두 다른 이름이였다면?..)

```c#
using System;
using static System.Console;

namespace Overloading
{ 
    class MainApp
    {
        static int Plus(int a, int b)
        {
            WriteLine("Calling int Plus(int,int)...");
            return a + b;
        }

        static int Plus(int a, int b, int c)
        {
            WriteLine("Calling int Plus(int,int,int)...");
            return a + b + c;
        }
        static double Plus(double a, double b)
        {
            WriteLine("Calling int Plus(double,double)...");
            return a + b;
        }

        static double Plus(int a, double b)
        {
            WriteLine("Calling int Plus(int,double)...");
            return a + b;
        }
        static void Main(string[] args)
        {
            WriteLine(Plus(1, 2));
            WriteLine(Plus(1, 2,3));
            WriteLine(Plus(1.0, 2.4));
            WriteLine(Plus(1, 2.4));
        }
    }
}
```

### 가변길이 매개 변수
가변길이 매개 변수란, 그 개수가 유연하게 변할 수 있는 매개 변수를 말한다. 메소드 오버로딩과의 차이는, 메소드 오버로딩은 변수의 수 뿐만 아니라 형식이 다른 경우에도 사용 할 수있다는 점이다. 하지만 가변길이 매개 변수는 형식이 같으나 매개 변수의 개수만 유연하게 달라지는 경우만 가능하다.
즉 매개 변수의 개수가 유안하게 정해져 있다면, 메소드 오버로딩이 적절하다. 형식은 같으나 매개 변수의 개수만 유연하게 달라지는 경우는 가변길이 매개
변수가 적합하다.

가변길이 매개변수는 params 키워드와 배열을 이용하여 선언한다.

```c#
using System;
using static System.Console;

namespace UsingParams
{ 
    class MainApp
    {
       static int Sum(params int[] args)
        {
            Write("summing...");
            int sum = 0;

            for(int i=0;i<args.Length;i++)
            {
                if(i>0)
                {
                    Write(", ");
                }
                Write(args[i]);
                sum += args[i];
            }
            WriteLine();
            return sum;
        }
        static void Main(string[] args)
        {
            int sum = Sum(3, 4, 5, 6, 7, 8, 9, 10, 11, 13);
            WriteLine($"Sum : {sum}");
        }
    }
}
```

## 명명된 매개 변수

메소드의 매개변수가 다양할때, 어느 매개변수에 할당 할 것인지를 지정하는 방법이다. 일반적으로 입력된 순서로 할당하지만, 복잡해지면 메소드를 호출할때
매개 변수의 이름 뒤에 콜론(:)을 붙이고 그 뒤에 할당할 데이터를 넣어주면 된다.

```c#
using System;
using static System.Console;

namespace NamedParameter
{
    class MainApp
    {
        static void PrintProfile(string name, string phone)
        {
            WriteLine($"Name:{name}, Phone:{phone}");
        }
        static void Main(string[] args)
        {
            PrintProfile(name: "박찬호", phone: "010-123-1234");
            PrintProfile(name: "이동석", phone: "010-2210-1212");
        }
    }
}
```

### 선택적 매개 변수
매개 변수도 초기화하듯 메소드를 선언 할 수 있다. **선택적 매개 변수는 항상 필수 매개 변수 뒤에 와야 한다.**
선택적 매개 변수는 메소드의 사용자에게 사용하지 않는 매개 변수를 염두에 두지 않도록 편의를 제공하지만, 또 한편으로는 모호함이라는 스트레스를 준다. 어느 코드에 데이터를 할당했는지 분간이 잘 안될 때도 있다. 이런 경우 명명된 매개 변수를 사용하면 문제 해결을 할 수 있다.


```c#
using System;
using static System.Console;

namespace OptionalParameter
{
    class MainApp
    {
        static void PrintProfile(string name, string phone = "") // phone =""으로 초기화 해서 사용한다.
        {
            WriteLine($"Name:{name}, Phone:{phone}");
        }
        static void Main(string[] args)
        {
            PrintProfile(name: "박찬호", phone: "010-123-1234");
            PrintProfile(name: "이동석");
        }
    }
}
```
> 메소드 오버로딩 vs 선택적 매게 변수
논리는 동일하되 매개 변수가 다른 경우는 선택적 매개 변수를 사용하고, 매개 변수에 따라 논리도 함께 달라지는 경우에는 오버로딩을 사용하는 식으로 코딩할때 메뉴얼을 정해놔야 한다.

### 로컬 함수
로컬 함수(Local Function)는 메소드 안에서 선언되고, 선언된 메소드 안에서만 사용되는 특별한 함수이다. 클래스의 멤버가 아니기 때문에 메소드가 아니라
함수(Function)라고 부른다. 선언 방법은 메소드와 다르지 않지만, 로컬 함수는 자신이 존재하는 지역에 선언되어 있는 변수를 사용할 수 있다.
로컬 함수는 메소드 밖에서는 다시 쓸일이 없는 반복적인 작업을 하나의 이름으로 묶어 놓는데 제격이다. 

```c#
using System;
using static System.Console;

namespace LocalFunction
{
    class MainApp
    {
        static string ToLowerString(String input)
        {
            var arr = input.ToCharArray();
            for(int i =0;i<arr.Length;i++)
            {
                arr[i] = ToLowerChar(i);
            }
            char ToLowerChar(int i)
            {
                if (arr[i] < 65 || arr[i] > 90) // A~Z 의 ASCII 값 :65~90
                    return arr[i];
                else // a~z ASCII 값 : 97~122
                    return (char)(arr[i] + 32); // ToLowerString 메소드의 지역변수인 arr 사용
            }
            return new string(arr);
        }
        static void Main(string[] args)
        {
            WriteLine(ToLowerString("hello!"));
            WriteLine(ToLowerString("Good Mornig."));
        }
    }
}
```
