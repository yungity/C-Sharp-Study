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

```C#
using System;

namespace RefReturn
{ 
    class Product
    {
        private int price = 100;
        public ref int GetPrice()
        {
            return ref price;
        }

        public void PrintPrice()
        {
            Console.WriteLine($"Price :{price}");
        }

        static void Main(string[] args)
        {
            Product carrot = new Product();
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
