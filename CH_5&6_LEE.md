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
