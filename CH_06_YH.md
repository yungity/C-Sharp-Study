# 6. 메소드로 코드 간추리기
---------------


## 6.1 메소드란?

- 일련의 코드를 하나의 이름 아래 묶은 것으로 객체의 일을 처리하는 방법. 
- 한 덩어리를 실행하는 기능.
- 함수랑 비슷하지만 차이점은 클래스 안에 존재한다는 것. 
+ +객체 지향 프로그래밍에서 클래스를 구체화한 객체는 자신만의 속성(데이터)와 기능(메소드)를 가짐.

### __메소드의 선언__

```
한정자 반환형식 메소드이름 (매개변수
{
    코드
    return문
}
```

__한정자__ : 메소드의 속성을 수식

- '+ __반환 형식__' : 반환될 형식

- '+ __메소드 이름__'

- '+ __(매개 변수 목록)__'

__중괄호__ 안에 코드를 작성
  
- '+ __return__' : 메소드 결과의 데이터 형식은 메소드의 지정된 반환형식과 일치해야 함.


### __메소드의 호출__

- __호출__ : 메소드의 이름을 불러주어 실행

- __반환__ : 호출자로부터 받은 입력의 결과를 돌려줌

- __매개 변수와 반환 형식__ 

    : 호출자가 매개변수를 넘기면서 메소드를 호출하면 메소드는 매개 변수를 가공한 후 메소드의 결과를 호출자에게 반환.


### __메소드 호출의 동작__

```
CLR은 코드를 실행하다가 메소드 호출을 만나면 호출자가 매개변수를 넘기면서 흐름이 호출된 메소드로 넘어감. 

메소드를 차례대로 수행하고 끝이나 return문을 만나게 되면 결과를 반환하면서 다시 호출자에게 다시 흐름이 넘어옴.

```

__** void 반환 형식__

: 모든 메소드가 결과를 반환하지는 않고 실행 후 종료하는 경우 void를 반환 형식으로 줌. 
 
__** static 한정자__

: 메소드나 필드가 클래스의 인스턴스가 아닌 클래스 자체에 소속되도록 지정하는 한정자, 인스턴스를 만들지 않고 호출됨.


## 6.2 return에 대하여

### __return문__
: 점프문의 한 종류로 메소드를 종결시키고 흐름을 갑자기 호출자에게 돌려놓음. 
메소드의 끝에서만 return하지 않고 언제든지 호출될 수 있다


### __** 재귀 호출__

: 메소드가 자기 자신을 스스로 호출하는 것. 

  - 코드를 단순하게 구성할 수 있지만 성능에 나쁜 영향을 주기 때문에 주의해서 사용.

## 6.3 매개 변수에 대하여 , 6.4 참조에 의한 매개 변수 전달

### __매개 변수가 전달되는 과정__

: 매개 변수 a, b 는 메소드 외부에서 내부로 전달하는 매개체 역할. 

- 근본적으로는 변수여서 값, 참조 데이터 상관 없이 메소드를 호출하면서 
매개변수로 x, y를 넘겨주면 각각 메소드의 매개변수 a, b 에 _복사_ 되서 전달됨. 
복사되었기 때문에 별개의 메모리 공간을 사용. __메소드에 의해 수정되는 것은 a, b.__

__1. 값에 의한 전달__
데이터 x, y라는 변수의 값을 매개 변수 a, b에게 전달하는 것 
: 매개 변수 a, b도 변수이므로 하나의 변수를 복사해 다른 변수에 할당하는 것과 같다. 
따라서 메소드를 호출할 때 넘긴 데이터 x, y와 실제 메소드에서 동일한 메모리 공간에 
존재하지 않고 다른 메모리 공간 __a, b에 복사__되어 존재함.

-- 메소드(x, y) 해도 원래의 값이 복사된 a, b(메모리상의 a,b)가 처리된 것이어서 원본값(매개변수로 넘긴) x, y에는 변화가 없다.

- 매개 변수로 직접 원본 변수의 값을 바꾸려면 = 원본 변수의 값을 __참조__하는 매개 변수를 사용하기.

__2. 참조에 의한 전달__
: 참조 매개 변수a, b가 메소드에 넘겨진 원본 변수 x, y를 직접 참조함.

__ref 키워드__ 사용 :
원본 변수를 ref로 넘기고 메소드의 매개 변수를 ref 로 참조 매개 변수로 지정해서 넘겨 주면 
매개변수 a, b 가 직접 원본 x, y 를 참조하므로 원본이 수정됨.
- 원본 변수를 ref로 넘기고 메소드의 매개 변수를 ref 로 참조 매개 변수로 지정해줘야함.
    - swap( ref x, ref y)  메소드(ref int a, ref int b)를 참조 매개 변수로 선언

- __Q1.__  :  c언어 처럼 포인터에 포인터를 넘겨주는 거 대신 배열의 이름을 넘기는 것 처럼 C#에서도 포인터같은 ref에 그냥 ref 없이 배열의 이름을 넘길 수 있는지?
    - 확인 --> 안됨. (배열의 이름이 ref로 명시가 안되나봄)


## 6.5 메소드의 결과를 참조로 반환하기

__참조 반환값(ref return)__

: 메소드 결과를 참조로 반환한다.

__참조로 반환하는 메소드 선언__

1.ref 한정자로 메소드를 선언하고 2.return문이 반환하는 변수에 ref 키워드를 명시
```
한정자 ref 반환형식 메소드이름(매개변수) { 코드 + return ref 결과변수}
```

__참조로 반환하는 함수 호출__

__1. 값으로 반환__ 

: 한정자 ref로 선언한 메소드도 ref키워드 없으면 평범하게 값으로도 반환. 
-- 반환받는 변수 = 메소드 ()

__2. 참조로 반환__

: 변수(참조 지역 변수)와 메소드 모두에 ref 붙여줘야함. 
```
ref 반환받는 변수 = ref 메소드()
```
   - 참조하는 경우 참조된 변수를 바꾸면 원본도 바뀜

<details>
<summary>예시</summary>
<div markdown="1">


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

            ref_local_price = 200; // ref의 값을 바꾸면?? 결과

            carrot.PrintPrice();
            Console.WriteLine($"Ref Local Price : {ref_local_price}");
            Console.WriteLine($"Normal Local Price : {normal_local_price}");
        }
    }
}
```
</div>
</details>



## 6.6 출력 전용 매개 변수

__두개 이상의 결과가 나오는 메소드__

: ref 키워드를 쓴 매개변수를 선언부와 호출부에 넣어줘서 두 개의 결과값을 받아주면 코드가 
실행될 때 두 개의 결과 값이 매개 변수에 저장되서 return을 받지 않고 메소드 밖에서 출력결과로 사용할 수 있음.
- C#은 출력 전용 매개 변수를 이용 

__out 키워드 :__

- ref 키워드 대신 선언부와 호출부에out 키워드를 넣어줌. 
    
__** out/ ref 키워드 차이점 :__
    
- 1. out키워드는 해당 변수에 결과가 저장되지 않으면 에러 메시지 출력하는 안전장치를 가짐. 

    - ref 키워드를 쓰면 메소드가 결과를 저장하지 않아도 경고를 주지 않지만

- 2. out 매개 변수는 호출 할 때 아직 초기화 하지 않은 지역변수를 넘길 수 있음. 
    
    - out 매개 변수는 바로 값을 저장할 수 있다는 것을 전제하기 때문ref는 지역변수를 초기화 시켜줌.
       = out 키워드를 이용하면 컴파일러가 결과를 할당하지 않은 버그를 만들 가능성을 제거해줌.


<details>
<summary>예시</summary>
<div markdown="1">


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
</div>
</details>

## 6.7 메소드 오버로딩

__오버로딩(overloading)__

: 하나의 메소드 이름에 매개변수의 수와 형식에 따라 여러 개의 구현을 올리는 것.
- 매개변수의 수와 형식만 달리한 매개변수를 넣어서 호출하면 알아서 각 경우에 따라 알아서 맞는 것을 호출해줌. 
- 이름에 대한 고민을 덜어주고 코드의 일관성 제공, 메소드 사용자에게 높은 생산성 제공

```
 int 메소드 (int a, int b) {…}
 double 메소드 (double a, double b, double c){…} ….
```

<details>
<summary>예시</summary>
<div markdown="1">

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
</div>
</details>

## 6. 8 가변 길이 매개 변수

__가변길이 매개 변수__

: 개수가 유연하게 변할 수 있는 매개 변수 
- 형식은 같으나 매개 변수의 개수만 유연하게 달라질 수 있는 경우에 적합

- __params 키워드__

    - : 매개변수에 param 키워드와 배열로 만들어서 매개 변수로 넘겨줌.

    ```메소드( params int[] args )```
    
    
<details>
<summary>예시</summary>
<div markdown="1">

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
</div>
</details>

### 6.9 명명된 매개 변수

__명명된 매개 변수__

: 매개 변수의 이름에 근거해서 데이터를 할당하는 기능. 
- 일반적으로 매개 변수 목록에 순서대로 할당하는데 순서와 상관없이 할당해도 알맞게 처리됨.
- 메소드 호출할 때

    - 매개변수를 (이름: 값 )으로 넣으면 이름에 근거해서 넣어주고 따로 메소드 수정 안해도 됨.
-- ( name : “박찬호” , 타율 : 8 )

+ 기존 방식으로도 매개 변수 넣을 수고 매개 변수들 중 몇 개만 써도 가능.


<details>
<summary>예시</summary>
<div markdown="1">



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
</div>
</details>


## 6.10 선택적 매개 변수

- __선택 매개변수__

: 기본값을 넣어서 데이터를 할당/할당하지 않을 자유를 부여할 수 있음. 
- 보통 메소드의 매개 변수는 필수 매개 변수여서 각 값을 할당해 줘야함
- 메소드 호출할 때
    - 선택 매개변수의 경우, 위치는 항상 필수 매개 변수 뒤에 두기
    - 메소드(int a, inb, int c=0, int d =20)

    - 주의!

        - 선택 매개 변수는 사용하지 않는 매개변수를 고려하지 않아도 되는 편리함을 주지만 너무 많이 쓰면 
        나중에 내가 어느 코드에 데이터를 할당했는지 모호해질 수 있음 
        
        대신 명명된 매개 변수를 활용.

- __메소드 오버로딩 Vs 선택적 매개 변수__

: 선택적 매개 변수는 메소드 오버로딩과 함께 사용될 때 혼란을 야기할 수 있음.
    - 메소드 오버로딩 : 매개 변수의 형식과 수에 따라 논리도 달라지는 경우
    - 선택적 매개변수 논리 : 동일하되 매개 변수가 달라지는 경우 

<details>
<summary>예시</summary>
<div markdown="1">


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
</div>
</details>

## 6.11 로컬 함수

__로컬 함수__

: 메소드 안에 메소드를 선언하고 그 안에서만 사용되는 함수 
- 클래스의 멤버가 아니기 때문에 (로컬)함수라고 부름.
- 자신이 존재하는 지역에 선언된 변수를 사용 가능.
- 메소드 밖에서는 __다시 쓸 일이 없는 반복적인 작업__ 을 하나의 이름 아래 묶어 놓는 데 적절.

<details>
<summary>예시</summary>
<div markdown="1">



```c#
using System;
using System.Console;

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
</div>
</details>
