# 람다식
---
람다식은 영어로 Lambda Expression 이라고 한다. 람다식은 **익명 메소드** 를 만들기 위해 사용한다. 다만 람다식으로 만드는 익명 메소드는 
**무명 함수(Anonymous Function)** 라는 이름으로 부른다. 람다식 또한 메소드처럼 입력(매개 변수)과 출력(반환 값)을 가지고 있다.

결국 람다식을 사용하는 이유는 무엇일까? 대리자 처럼 상수,변수 와 같은 데이터 뿐만 아니라 **메소드 조차도 매개 변수로 보내버리고 싶다**는 의도로 사용 하는 것이다.
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
식 트리는 자료구조의 트리와 동일한 구조이지만 이진트리(Binary Tree)로 구성되어 있다.
식 트리란, 식을 트리로 표현한 자료 구조를 말하며 식 트리로 표현된 식은 트리의 잎 노드부터 계산해서 루트까지 올라가면 전체 식의 결과를
얻을 수 있다.

식 트리를 다루는데 필요한 클래스들은 System.Linq.Expressions 네임스페이스 안에 있는 **Expression 클래스와 그의 파생클래스들**이다.

> Expression 클래스의 역할
* 식 트리를 구성하는 노드를 표현
* 파생클래스들의 객체를 생성하는 역할(팩토리 메소드)
하지만 Expression 클래스 자신은 abstarct로 선언되어 있어 자신의 인스턴스를 만들 수는 없지만 파생 클래스의 인스턴스를 생성하는 정적 팩토리 메소드를 제공하고 있다.

#### 팩토리 메소드
팩토리 메소드(Factory Method)는 클래스의 인스턴스를 생성하는 일을 담당하는 메소드다. 객체를 생성하는 생성자 메소드가 있지만 이것만으로 충분하지 않을 떄가 있다. 객체 생성에 복잡한 논리가 필요할 때인데, 이때 객체 생성과정을 별도의 메소드에 구현해 놓으면 코드의 복잡도를 상당히 줄일 수 있다.

여기선 Expression 클래스의 정적 팩토리 메소드들은 Expression 클래스의 파생 클래스인 ConstatntExpression, BinaryExpression 클래스 등의
인스턴트를 생성하는 기능을 제공한다.

```
Expression const1 = Expression.Constatnt(1); // 상수 1
Expression param1 = Expression.Parameter(typeof(int),"x"); // 매개변수 x

Expression exp = Expression.Add(const1,param1); // 1+x
```
팩토리 메소드인 Expression.Constatnt()로 Expression 형식으로 선언한 Const1 는 **ConstantExpression** 형식이 된다.

ConstantExpression 은 Expression 을 상속하기 때문에 ConstantExpression 객체는 Expression 형식의 참조를 통해 가리 킬 수 있다. param1 과 exp도 마찬가지이다. 각 노드가 어떤 타입인지 신경 쓰지 않고 Expression 형식의 참조를 선언에서 사용하면 된다.

그러나 식 트리는 "식"을 트리로 표현한 것 뿐이라 exp는 실행하지 못하고 "데이터"의 상테에 머물러 있을 뿐이다. 이를 실행하기 위해선 람다식으로
컴파일 되어야 한다. 람다식으로 컴파일은 Expression<TDelegate> 클래스를 이용한다.
  
<details>
<summary>예시 코드</summary>
<div markdown="1">

```c#
using System;
using System.Linq.Expressions;

namespace UsingExpressionTree
{
    class MainApp
    {
        static void Main(string[] args)
        {
            // 1*2+(x-y)
            Expression const1 = Expression.Constant(1);
            Expression const2 = Expression.Constant(2);

            Expression leftExp = Expression.Multiply(const1, const2); // 1*2

            Expression param1 =
                Expression.Parameter(typeof(int)); //x 를 위한 변수
            Expression param2 =
                Expression.Parameter(typeof(int)); //y 를 위한 변수

            Expression rightExp = Expression.Subtract(param1, param2); // x-y

            Expression exp = Expression.Add(leftExp, rightExp);

            Expression<Func<int, int, int>> expression =
                Expression<Func<int, int, int>>.Lambda<Func<int, int, int>>(
                    exp, new ParameterExpression[]{
                        (ParameterExpression)param1,
                        (ParameterExpression)param2});

            Func<int, int, int> func = expression.Compile(); // .Compile(): 실행 가능한 코드로 컴파일
            // x = 7, y = 8
            Console.WriteLine($"1*2+({7}-{8}) ={func(7, 8)}"); // func(7, 8) : 컴파일한 무명함수를 실행
        }
    }
}

람다식을 이용한 식 트리 예제
using System;
using System.Linq.Expressions;

namespace ExpressionTreeViaLambda
{
    class MainApp
    {
        static void Main(string[] args)
        {
            // 1*2+(x-y)
            Expression<Func<int, int, int>> expression =
                (a, b) => 1 * 2 + (a - b);
            Func<int, int, int> func = expression.Compile();

            //x =7, y =8
            Console.WriteLine($"1*2 + ({7}-{8}) = {func(7, 8)}");
        }
    }
}
```

</div>
</details>

### 식으로 이루어지는 멤버
메소드,속성(인덱서),생성자,종료자의 공통된 특징은 모두 클래스의 멤버로서 본문이 중괄호{}로 만들어져 있다. 이러한 멤버의 본문을 식(Expression)만으로 구현이 가능하다. 이러한 멤버를 **"식 본문 멤버(Expression-Bodied Member)"** 라고 한다.

```
멤버 => 식;
```

<details>
<summary>예시 코드</summary>
<div markdown="1">

```c#
using System;
using System.Collections.Generic;

namespace ExpressionBodiedMember
{ 
    class FriendList
    {
        private List<string> list = new List<string>();

        
        public void Add(string name) => list.Add(name); // list 의 Add 메서드를 추가
        public void Remove(string name) => list.Remove(name);
        public void PrintAll()
        {
             foreach (var s in list)
                Console.WriteLine(s);
        }

        public FriendList() => Console.WriteLine("FrindList()"); // 생성자 식으로 선언
        ~FriendList() => Console.WriteLine("~FrindList()"); // 소멸자 식선언

        // public int Capacity => list.Capacity; // 읽기 전용으로 한다면 get 도 생략할 수 있다.

        public int Capacity // 속성
        {
            get => list.Capacity;
            set => list.Capacity = value;
        }

        //pubilc string this[int index] => list[index]; // 읽기 전용
        public string this[int index]
        {
            get => list[index];
            set => list[index] = value;
        }

    }
    class MainApp
    {
        static void Main(string[] args)
        {
            FriendList obj = new FriendList();
            obj.Add("Eeny");
            obj.Add("Meeny");
            obj.Add("Miny");
            obj.Remove("Eeny");
            obj.PrintAll();

            Console.WriteLine($"{obj.Capacity}");
            obj.Capacity = 10;
            Console.WriteLine($"{obj.Capacity}");

            Console.WriteLine($"{obj[0]}");
            obj[0] = "Moe";
            obj.PrintAll();
        }
    }
}
```

</div>
</details>
