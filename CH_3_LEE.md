```cs
int a = 100; // 변수 a, 리터럴 : 100
float c = 3.14f; // 변수c, 리터럴 : 3.14f
string s = "가나다라마바사"; // 변수 s, 리터럴 : "가나다라마바사"
```

>리터럴(Literal) : 고정값을 나타내는 표기법


### 값 형식(Value Types)과 참조 형식(Reference Types)
> 값 형식

변수가 값을 담는 데이터 형식, **스택** 메모리영역에 생성된다. 이 types은 코드블록이 시작됨과 끝남에 따라서 메모리에 생성되었다가 제거된다.
```cs
{ //코드 블록 시작
    int a = 100;
    int b = 200;
    int c = 300;
}//코드 블록 끝
```
선언된 세 변수 a,b,c는 차례대로 스택에 쌓였다가 코드 블록이 끝나면서 스택에서 제거된다. 스택에 쌓인 데이터들은 
코드 블록이 끝나는(사라지는)순간 함께 제거가 되는데 코드블록이 끝났어도 계속 메모리에 저장하고 싶으면 어떻게 해야 할까? 이때 참조 형식을 사용해야
한다.

>참조 방식

변수가 값대신 값이 있는 곳의 위치(참조,주소)를 담는 데이터 형식, **힙** 메모리영역에 생성된다. 프로그래머가 힙에 데이터를 올려 놓으면, 코드블록이 종료되는지점과는 
상관 없이 데이터를 유지 할 수 있다. 하지만 프로그래머가 더 이상 사용하지 않을떄(참조되지 않을때) 가비지 콜렉터가 제거해준다. 이때서야 메모리에서 삭제된다.

#### Object 형식
참조 방식의 형식으로 구성되어 있다. 어떠한 형식이든지 다룰 수 있는 데이터 형식으로 **"상속"** 을 통해서 다른 데이터들의 형식을 받을 수 있다.object 형식은 부동 소수점 형식, decimal 형식과 같이 소수를 처리하는 방식이 달라도 어떻게 처리할까? 박싱과 언박싱을 통해서 해결한다.

> 박싱과 언박싱
Object는 참조 형식이므로 힙에 데이터를 할당한다. 
```c#
object a = 20;
```
위 코드에서 20은 박스에 담겨 힙에 할당되고 a는 그 주소를 참조하고 있다.(a는 스택에 저장) 이 과정을 **박싱** 이라고 한다.

힙에 있던 값 형식 데이터를 값 형식 객체에 다시 할당해야 하는 경우가 있다. 

```c#
object a = 20;
int b = (int)a;
```
위 코드에서 a는 20이 박싱되어 저장되어 있는 힙을 참조하고 있다. b는 a가 참조하고 있는 메모리로부터 값을 복사하려고 하는 중이다. 이 떄 박싱되어 있는 값을 꺼내 값 형식 변수에 저장하는 과정을 **언박싱** 이라고 한다.

#### 문자열을 숫자로, 숫자를 문자열로

```C#
string a = "12345";
int b = (int)a; // b는 이제 12345? 문자열이냐?!
```
응 안됩니다. (int)형식 변환 연산자는 숫자 형식 데이터를 int 형식으로만 변환하는 방법(object 형식을 언박싱하는 방법)을 갖고 있지만 string 과 같은 여타 다른 형식으로 변환은 하지 못한다.

> 문자열 - 숫자 형식 변환 방법
```c#
int a = int.Parse("12345");
float b = float.Parse("123.45");
```
C#은 정수 계열 형식, 부동 소수점 형식 모두 Parse 메소드를 갖고 있다.

> 숫자 - 문자열 형식 변환 방법
```c#
int c = 12345;
string d = c.Tostring();

float e = 123.45;
string f = e.Tostring();
```
object로 부터 물려받은 Tostring()을 재정의(오버라이드)하여 사용한다.

```c#
using System;


namespace StringNumberConversion
{
    class MainAPP
    {
        static void Main(string[] args)
        {
            int a = 123;
            string b = a.ToString();
            Console.WriteLine(b);

            string c = "12345.65";
            float d = float.Parse(c);
            Console.WriteLine(d);


        }
    }
}
```

### 열거 형식 - 여러 개의 상수를 정리합시다

열거 형식은 같은 범주에 속하는 여러개의 상수를 선언 할 때 아주 유용하다.

enum 열거 형식명 : 기반자료형 {상수1, 상수2, 상수3, ...}

```c#
 enum DiaglogResult {YES,NO,CANCEL,CONFIRM,OK}
```
열거 형식 안에 선언된 상수들이 어떤 값도 할당받지 않았다. 그러나 컴파일러가 자동으로 첫번째는 0으로 시작하여 1씩증가한 값을 할당한다.

```c#
using System;

namespace Enum
{ 
    class MainApp
    {
        enum DiaglogResult { YES, NO, CANCEL, CONFIRM, OK };
        static void Main(string[] args)
        {
            Console.WriteLine((int)DiaglogResult.YES);
            Console.WriteLine((int)DiaglogResult.NO);
            Console.WriteLine((int)DiaglogResult.CANCEL);
            Console.WriteLine((int)DiaglogResult.CONFIRM);
            Console.WriteLine((int)DiaglogResult.OK);
        }
    }
}
```
를 실행하면 0,1,2,3,4 가 출력된다.
또한 DialogResult 라는 열거 형식으로 만들어지는데, 새로운 형식이다.

```c#
DialogResult result = DialogResult.YES; // DialogResult result = 0; 과 같은 코드
```



