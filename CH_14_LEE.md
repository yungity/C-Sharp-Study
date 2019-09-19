# 람다식
---
람다식은 영어로 Lambda Expression 이라고 한다. 람다식은 **익명 메소드** 를 만들기 위해 사용한다. 다만 람다식으로 만드는 익명 메소드는 
**무명 함수(Anonymous Function)** 라는 이름으로 부른다. 람다식 또한 메소드처럼 입력(매개 변수)과 출력(반환 값)을 가지고 있다.
```
매개_변수_목록 => 식
```

```c#
delegate int Calculate(int a, int b); // 익명 메소드를 만들려면 대리자가 필요하다.

//
..
static void Main(string[] args)
{
  Calculate calc = (int a, int b) => a + b;
  // Calculate calc = (a, b) => a + b; 와 같이 매개 변수의 형식도 지정하지 않아도 된다. 
  이를 형식 유추(Type Inference)라고 한다.
}
```

<details>
<summary>예시 코드</summary>
<div markdown="1">
    
```c#
using System;

namespace SimpleLambda
{
    
    class MainApp
    {
        delegate int Calculate(int a, int b);

        static void Main(string[] args)
        {
            Calculate calc = (a, b) => a + b;
            Console.WriteLine($"3 + 4 : {calc(3, 4)}");
        }
    }
}
```

</div>
</details>

### 문 형식의 람다식
람다식은 a+b 나 a == b 말고도 if(a==b) return 0; else return 1; 같은 문장을 사용할 수 있다.
```
(매개_변수_목록) => {문장1;문장2;문장3;...}
```

<details>
<summary>예시 코드</summary>
<div markdown="1">

```c#
using System;

namespace StatementLambda
{
    class MainApp
    {
        delegate string Concatenate(string[] args);

        static void Main(string[] args)
        {
            Concatenate concat =
                (arr) =>
                {
                    string result = "";
                    foreach (string s in arr)
                        result += s;

                    return result;
                };
            Console.WriteLine(concat(args));
        }
    }
}
```

</div>
</details>

### Func & Action 으로 더 간편한 무명 함수 만들기

func,Action은 MS 에 미리 선언한 대리자이다.
* Func : 결과를 반환하는 매소드를 참조
* Action : 결과를 반환하지 않는 메소드를 참조

#### Func 대리자
Func 대리자는 결과를 반환하는 메소드를 참조하기 위해 만들어졌다. 

```
public delegate TResult Func<out TResult>();
```
입력 매개 변수가 없는 버전의 Func<Tresult>이다. 모든 Func 대리자의 형식 매개 변수 중 가장 마지막에 있는 것이 반환 형식이다. 여기선
<"out TResult">가 반환 형식이다.

<details>
<summary>예시 코드</summary>
<div markdown="1">

```c#
using System;

namespace FuncTest
{
    class MainApp
    {
        static void Main(string[] args)
        {
            Func<int> func1 = () => 10; // 매개변수0, 반환형식 1
            Console.WriteLine($"func1() : {func1()}");

            Func<int, int> func2 = (x) => x * 2; // 매개변수1, 반환형식 1
            Console.WriteLine($"func2(4) : {func2(4)}");

            Func<double, double, double> func3 = // 매개변수2, 반환형식 1
                (x, y) => x / y;
            Console.WriteLine($"func3(22,7) : {func3(22, 7)}");
        }
    }
}
```

</div>
</details>


#### Action 대리자
Action 대리자는 Func 대리자와 거의 똑같지만, 반환 형식이 없다는 차이점이 있다.

```
int result = 0;
Action<int> act2 = (x) => result = x * x; // 람다식 밖에 선언한 result에 결과 값을 저장한다.
```

<details>
<summary>예시 코드</summary>
<div markdown="1">

```c#
using System;

namespace ActionTest
{
    class MainApp
    {
        static void Main(string[] args)
        {
            Action act1 = () => Console.WriteLine("Action()");
            act1();

            int result = 0;
            Action<int> act2 = (x) => result = x * x;

            act2(3);
            Console.WriteLine($"result : {result}");

            Action<double, double> act3 = (x, y) =>
             {
                 double pi = x / y;
                 Console.WriteLine($"Action<T1,T2>({x},{y}) : {pi}");
             };
            act3(22.0, 7.0);
        }
    }
}
```

</div>
</details>

### 식 트리

<img src="https://github.com/bluein/C-Sharp-Study/blob/master/OOP/pic/ext.png">
식 트리는 자료구조의 트리와 동일한 구조이지만 이진트리로 구성되어 있다.
식 트리란, 식을 트리로 표현한 자료 구조를 말하며 식 트리로 표현된 식은 트리의 잎 노드부터 계산해서 루트까지 올라가면 전체 식의 결과를
얻을 수 있다.

