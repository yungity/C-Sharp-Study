# 12장 예외 처리하기
## 12.1 예외에 대하여
- 예외 : 프로그래머가 생각하는 시나리오에서 벗어나는 사건
- 예외 처리(Exception Handling) : 예외가 프로그램의 오류나 다운으로 이어지지 않도록 적절하게 처리하는 것
- 처리되지 않은 예외 : CLR에게 전달된 예외, 프로그램은 강제로 종료됨
<br><br>
## 12.2 try~catch로 예외 받기
- 프로그램에서 Main() 메소드가 예외를 받으면 문제 해결 가능. (CLR이 아니라)
### -try ~ catch 구문
```C#
try
{
    // 실행할 코드
}
catch(예외객체1)
{
    // 예외가 발생했을 때 처리
}
catch(예외객체2)
{
    // 예외가 발생했을 때 처리
}
````
- catch절 : try블록에서 던질 예외 객체와 형식이 같아야 함
- 한 종류 이상 예외를 던질 가능성이 있다면, 이를 받아낼 catch 블록도 여러 개 두기 가능.
```C#
using System;

namespace TryCatch
{
    class MainApp
    {
        static void Main(string[] args)
        {
            int[] arr = { 1, 2, 3 };

            try
            {
                for (int i = 0; i < 5; i++)
                {
                    Console.WriteLine(arr[i]);
                }
            }
            catch( IndexOutOfRangeException e )
            {
                Console.WriteLine($"예외가 발생했습니다 : {e.Message}");
            }

            Console.WriteLine("종료");
        }
    }
}

```
<br><br>
## 12.3 System.Exception 클래스
```C#
try
{
}
catch( IndexOutOfRangeException e )
{
    // ....
)
catch( DivideByZeroException e )
{
    // ...
}
```

```C#
try
{
}
catch (Exception e ) // 하나의 catch 절로 처리 가능
{
    //...
}
```
- System.Exception : 모든 예외의 조상, 모든 예외 클래스는 이 클래스에서 상속받아야 함.
- 상속관계로 인해 모든 예외 클래스는 System.Exception 형식으로 간주 가능함 
- 그렇지만 다른 예외까지 받아내므로 예외 처리가 아닌, 버그를 만들어 내는 역할이 되므로 사용 자제

<br><br>
## 12.4 예외 던지기 (throw문)
```C#
using System;

namespace Throw
{
    class MainApp
    {
        static void DoSomething(int arg)
        {
            if (arg < 10)
                Console.WriteLine($"arg : {arg}");
            else
                throw new Exception("arg가 10보다 큽니다.");  // throw new 해당예외클래스
                // DoSomething 메소드 안에는 예외를 처리할 수 있는 코드가 없음 -> DoSomething() 메소드의 호출자에게 예외가 던져짐
                // Exception 매개변수로 "string" 작성 시 이후 .Message로 읽을 수 있음
                // 매개변수 작성하지 않을 시 해당_예외_클래스.Message 출력     
        }
                
        static void Main(string[] args)
        {
            try
            {
                DoSomething(1);
                DoSomething(3);
                DoSomething(5);
                DoSomething(9);
                DoSomething(11); //예외 발생
                DoSomething(13); //실행되지 않음
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message); 
            }
        }
    }
}
```

```C#
using System;

namespace ThrowExpression
{
    class MainApp
    {
        static void Main(string[] args)
        {
            try
            {
                int? a = null;
                int b = a ?? throw new ArgumentNullException();
            }
            catch (ArgumentNullException e)
            {
                Console.WriteLine(e); // ★ e 단독 사용 시 e.Message + e.StackTrace 출력★
            }

            try
            {
                int[] array = new[] { 1, 2, 3 };
                int index = 4;
                int value = array[
                    index >= 0 && index < 3
                    ? index : throw new IndexOutOfRangeException()
                    ];
            }
            catch (IndexOutOfRangeException e)
            {
                Console.WriteLine(e);
            }
        }
    }
}
```
출력
```cmd
System.ArgumentNullException: 값은 null일 수 없습니다.
   위치: ThrowExpression.MainApp.Main(String[] args) 파일 C:\Users\Affinity\source\repos\ConsoleApp4\ConsoleApp4\Program.cs:줄 12
System.IndexOutOfRangeException: 인덱스가 배열 범위를 벗어났습니다.
   위치: ThrowExpression.MainApp.Main(String[] args) 파일 C:\Users\Affinity\source\repos\ConsoleApp4\ConsoleApp4\Program.cs:줄 23
```


<br><br>
## 12.5 try~catch와 finally
- try 블록에서 catch로 넘어가면, 이후 코드는 실행할 수 없음. 이는 곧 버그의 원인
- finally절을 try ~ catch 문 가장 마지막에 연결하여 사용하면 어떤 경우라도 finally절이 실행됨. 
- try 내에 return / throw문이 사용되더라도 finally문 사용됨
```C#
using System;

namespace Finally
{
    class MainApp
    {
        static int Divide(int divisor, int dividend)
        {
            try
            {
                Console.WriteLine("Divide() 시작");
                return divisor / dividend;
            }
            catch (DivideByZeroException e)
            {
                Console.WriteLine("Divide() 예외 발생");
                throw e;
            }
            finally
            {
                Console.WriteLine("Divide()  끝");
            }
        }

        static void Main(string[] args)
        {
            try
            {
                Console.Write("제수를 입력하세요. :");
                String temp = Console.ReadLine();
                int divisor = Convert.ToInt32(temp);

                Console.Write("피제수를 입력하세요. : ");
                temp = Console.ReadLine();
                int dividend = Convert.ToInt32(temp);

                Console.WriteLine("{0}/{1} = {2}",
                    divisor, dividend, Divide(divisor, dividend));
            }
            catch (FormatException e)
            {
                Console.WriteLine("에러 : " + e.Message);
            }
            catch (DivideByZeroException e)
            {
                Console.WriteLine("에러 : " + e.Message);
            }
            finally
            {
                Console.WriteLine("프로그램을 종료합니다.");
            }
        }
    }
}
```
출력
```cmd
제수를 입력하세요. :4
피제수를 입력하세요. : 0
Divide() 시작         // Divide()의 try
Divide() 예외 발생    // Divide()의 catch
Divide()  끝         // Divide()의 Finally (반드시 실행됨)
에러 : 0으로 나누려 했습니다. // Divide()에서 throw 한 e-> Main()의 catch로 이어짐
프로그램을 종료합니다.

제수를 입력하세요. :2
피제수를 입력하세요. : 하이
에러 : 입력 문자열의 형식이 잘못되었습니다.
프로그램을 종료합니다.

제수를 입력하세요. :5
피제수를 입력하세요. : 7
Divide() 시작
Divide()  끝
5/7 = 0
프로그램을 종료합니다.
```

<br><br>
## 12.6 사용자 정의 예외 클래스 만들기
- 모든 예외 클래스는 System.Exception 클래스로부터 파생되어야 함
```C#
using System;

namespace MyException
{
    class InvalidArgumentException : Exception
    {
        public InvalidArgumentException()
        {
        }

        public InvalidArgumentException(string message)
            : base(message) // ★ 기반 클래스에 매개변수 전달 ★
        {
        }

        public object Argument
        {
            get;
            set;
        }

        public string Range
        {
            get;
            set;
        }
    }

    class MainApp
    {
        static uint MergeARGB(uint alpha, uint red, uint green, uint blue)
        {
            uint[] args = new uint[] { alpha, red, green, blue };

            foreach (uint arg in args)
            {
                if (arg > 255)
                    throw new InvalidArgumentException()
                    {
                        Argument = arg,
                        Range = "0~255"
                    };
            }

            return (alpha << 24 & 0xFF000000) | 
                   (red   << 16 & 0x00FF0000) |
                   (green << 8  & 0x0000FF00) |
                   (blue        & 0x000000FF); // 하나의 16진수로 표현, 각 두 칸마다(1byte) alpha, red, green, blue 순서
        }

        static void Main(string[] args)
        {
            try
            {
                Console.WriteLine("0x{0:X}", MergeARGB(255, 111, 111, 111));
                Console.WriteLine("0x{0:X}", MergeARGB(1, 65, 192, 128));
                Console.WriteLine("0x{0:X}", MergeARGB(0, 255, 255, 300));
            }
            catch (InvalidArgumentException e)
            {
                Console.WriteLine(e.Message);
                Console.WriteLine($"Argument:{e.Argument}, Range:{e.Range}");

            }
            
        }
    }
}
```
출력
```cmd
0xFF6F6F6F
0x141C080
'MyException.InvalidArgumentException' 형식의 예외가 Throw되었습니다.
Argument:300, Range:0~255
```

<br><br>
## 12.7 예외 필터하기 (Exception Filter)
- catch() 문 뒤에 when 키워드를 이용해서 제약 조건을 기술(if라고 생각하면 편함)
```C#
using System;

namespace ExceptionFiltering
{
    class FilterableException : Exception
    {
        public int ErrorNo {get;set;}
    }

    class MainApp
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Enter Number Between 0~10");
            string input = Console.ReadLine();
            try
            {
                int num = Int32.Parse(input);

                if (num < 0 || num > 10)
                    throw new FilterableException() { ErrorNo = num }; // ★★★객체 생성 시 프로퍼티를 이용한 초기화
                else
                    Console.WriteLine($"Output : {num}");
            }
            catch (FilterableException e) when (e.ErrorNo < 0)
            {
                Console.WriteLine("Negative input is not allowed.");
            }
            catch(FilterableException e) when (e.ErrorNo > 10)
            {
                Console.WriteLine("Too big number is not allowed.");
            }
        }
    }
}
```
출력
```cmd
Enter Number Between 0~10
7
Output : 7

Enter Number Between 0~10
11
Too big number is not allowed.

Enter Number Between 0~10
-5
Negative input is not allowed.
```


<br><br>
## 12.8 예외 처리 다시 생각해보기
- .StackTrace 프로퍼티를 통해 문제가 발생한 부분의 소스코드 위치를 알 수 있으므로 디버깅이 용이함
- 오류를 종류별로 정리해주는 효과가 있음. 오류가 발생하는 부분은 두 부분 이상일 수 있으나, 이 형식의 예외를 받는 catch 블록 하나면 처리 가능.
