
# Chapter 3 데이터 보관하기
***
## 3.3 값 형식과 참조 형식
- 값 형식(Value Types) : 변수가 값을 담는 데이터 형식
- 참조 형식(Reference Types) : 변수가 값 대신 값이 있는 곳의 위치(참조)를 담는 데이터 형식

### 3.3.1 스택과 값 형식
```C#
{ //코드 블록 시작
    int a = 100;
    int b = 200;
    int c = 300;
} //코드 블록 끝
```
: 값 형식의 변수는 스택에 저장됨. 차례대로 스택에 쌓였다가(a, b, c순) 코드 블록이 끝나면서(}) 스택에서 걷혀 제거(c, b, a순)

### 3.3.2 힙과 참조 형식
```C#
{
    object a = 10;
    object b = 20;
}
```
: 참조 형식의 변수는 힙과 스택을 함께 이용. 힙 영역 - 데이터를 저장, 스택 영역 - 데이터가 저장되어 있는 힙 메모리의 주소를 저장
- a, b(각 값의 주소값을 가지고 있는 변수)가 스택에서 사라지면 힙에 남아있는 10, 20은 CLR의 가비지 콜렉터가 수거해감.

#### 스택Stack과 힙Heap 요약
- 스택 :  변수의 생명 주기가 다 하면 자동으로 데이터를 제거하는 메모리 영역
- 힙 : 더 이상 데이터를 참조하는 곳이 없을 때 CLR의 가비지 콜렉터가 데이터를 치워주는 구조의 메모리 영역
***
## 3.4 기본 데이터 형식
https://social.msdn.microsoft.com/Forums/ko-KR/3ac37ce3-535b-4aae-9acb-5bf0aafc9cc9/c-4459248376-4593651060535525480551032-5133347448?forum=visualcsharpko 참조
### 3.4.12 object 형식
- object형식 : 모든 데이터 형식의 조상(상속 개념 연관), 컴파일러는 어떤 형식의 데이터라도 object에 담아 처리할 수 있음.
```C#
using System;


namespace Object
{
    class Program
    {
        static void Main(string[] args)
        {
            object a = 123;
            object b = 3.141592653589793238462643383279m;
            object c = true;
            object d = "안녕하세요";

            Console.Write($"{a}\n{b}\n{c}\n{d}");
            Console.WriteLine();
        }
    }

}
```

### 3.4.13 박싱과 언박싱
object 형식: 참조 형식이므로 힙에 데이터를 할당


  - 박싱Boxing : 값 형식의 데이터를 힙에 할당하는 기능
```C#
object a = 20;
```
20은 박스에 담겨 힙에 할당되고, a는 그 주소를 참조함(스택)




  - 언박싱UnBoxing :  박싱되어 있는 값을 꺼내 값 형식 변수에 저장하는 과정
```C#
object a = 20;
int    b = (int)a;
```
b는 a가 참조하고 있는 메모리부터 값을 복사해서 스택에 값을 저장.

### 정리
```C#
int a = 123;
object b = (object)a; //a에 담긴 값을 박싱해서 힙에 저장
int c = (int)b;       //b에 담긴 값을 언박싱해서 스택에 저장

double x = 3.141592;
object y = x;           //x에 담긴 값을 박싱해서 힙에 저장
//(object형식에 저장할 때 형식 변환 연산자를 지정하지 않으면 암시적으로 object 형식)
double z = (double)y;   //y에 담긴 값을 언박싱해서 스택에 저장
```            
박싱과 언박싱 - 값 형식과 참조 형식 간의 형식 변환이라고 할 수 있다.

### 3.4.14 데이터 형식 바꾸기
형식변환 : 변수를 다른 데이터 형식의 변수에 옮겨 담는 것
- 크기(표현 범위)가 서로 다른 정수 형식 사이의 변환 -> 오버플로우 발생
- 크기(표현 범위)가 서로 다른 부동 소수점 형식 사이의 변환 -> 정밀성 손상 위험
- 부호 있는 정수 형식과 부호 없는 정수 형식 사이의 변환 -> 오버플로우/언더플로우 발생
- 부동 소수점 형식과 정수 형식 사이의 변환 -> 정수로 변환시 버림 (반올림 X)
- 문자열과 숫자 사이의 변환 -> .Parse, .ToString, Conver.ToInt32 
```C#
int a = int.Parse("12345");
float b = float.Parse("123.45");

int c = 12345;
string d = c.ToString(); // 오버라이딩(overriding) - 자신이 갖고 있는 숫자를 문자열로 변환하도록 재정의

float e = 123.45;
float f = e.ToString();

string g = "123456";
int h = Convert.ToInt32(e);
```
***
## 3.5 상수와 열거 형식
### 3.5.1 상수
```C#
const 자료형 상수명 = 값;
```
- 데이터 형식 앞에 const 키워드가 위치하고, 상수가 가져야 하는 데이터를 반드시 대입해줘야 함.
- 데이터를 변경할 수 없음

### 3.5.2 열거 형식 - 여러 개의 상수를 정리하기
```C#
enum 열거할 형식명 : 기반자료형 {상수1, 상수2, 상수3, ...}
```

- 기반자료형 : 정수 계열(byte, sbyte, short, ushort, int, uint, long, ulong, char)만 사용 가능, 생략시 int 기본값

```C#
enum DialogResult { YES, NO, CANCEL, CONFIRM, OK }
```

- 첫 번째 열거 형식 요소에는 0, 다음 요소에는 1, ... 1씩 증가한 값을 자동으로 할당함
- 서로 중복되지 않는 값을 갖고 있다는 데에 의미가 있음
- 실수할 위험도 적고, 고치기도 쉬움.

```C#
DialogResult result = DialogResult.YES;
```


```C#
enum 열거형식명 { 상수1 = 값, 상수2 = 값, 상수3 = 값3, ... }

```

프로그래머가 원하는 값을 직접 대입할 수 있음

```C#
enum DialogResult { YES = 10, NO, CANCEL, CONFIRM = 50, OK }
```
- YES : 10, NO : 11, CANCEL : 12, CONFIRM : 50, OK : 51 
***
## 3.6 Nullable 형식
```C#
데이터형식? 변수이름;
```
- 변수에게 할당된 메모리 공간을 비워둘 수 있도록 Nullable 형식을 사용, 
- 값 형식에서만 사용 가능. 참조 형식은 사용 불가.
```C#
int? a = null;
float? b = null;
double? c = null;

Console.WriteLine(a.HasValue); // a는 null이므로 False 출력

a = 37;
Console.WriteLine(a.HasValue); // a는 37을 가지고 있으므로 True 출력
Console.WriteLine(a.Value); // 37을 출력

```
- .HasValue : 해당 변수가 값을 갖고 있는지 확인함
- .Value : 변수에 담겨 있는 값

***
## 3.7 var 
- var 키워드를 통한 약한 형식 검사 : 데이터 형식을 열거하지 않고도 컴파일러/인터프리가 알아서 형식을 지정해 줌.
- 반드시 선언과 동시에 초기화를 해줘야 함

```C#
var a = 20; // var 변수 반드시 초기화 필요
Console.WriteLine("Type: {0}, Value: {1}", a.GetType(), a);

var b = 3.1414213;
Console.WriteLine("Type: {0}, Value: {1}", b.GetType(), b);

var c = "Hello, World!";
Console.WriteLine("Type: {0}, Value: {1}", c.GetType(), c);

var d = new int[] { 10, 20, 30 }; // 배열도 가능
Console.WriteLine("Type: {0}, Value: {1}", d.GetType(), d);

foreach (var e in d)
    Console.Write("{0} ", e);
```

```cmd
Type: System.Int32, Value: 20
Type: System.Double, Value: 3.1414213
Type: System.String, Value: Hello, World!
Type: System.Int32[], Value: System.Int32[]
10 20 30
```

### object와 var의 차이
- object : CLR은 값을 박싱해서 힙에 넣어놓고 변수가 힙을 가리키게 만듦.
- var : 컴파일 시점에 컴파일러가 변수에 적합한 데이터 형식을 파악해서 int a = 20;으로 바꿔 컴파일, int 형식 객체 a에 20을 담아 스택에 올림.
***
## 3.8 공용 형식 시스템(Common Type System, CTS)
- <b>.NET 프레임워크</b> 형식 체계의 표준, "모두(.NET 언어)가 함께 사용하는 데이터 형식 체계"
- 공용 형식 시스템의 형식은 각 언어에서 코드에 그대로 사용할 수 있음.
https://apprize.info/c/net/1.html
<img src="https://apprize.info/c/net/net.files/image012.jpg" width = 640 height = 380></img>
***
### 연습 문제 - 사각형의 너비와 높이를 입력받아 넓이를 계산하고 출력하는 프로그램을 완성하세요.
```C#
using System;


namespace RectArea
{
    class MainApp
    {
        
        static void Main()

        {
            Console.WriteLine("사각형의 너비를 입력하세요.");
            string width = Console.ReadLine();

            Console.WriteLine("사각형의 높이를 입력하세요.");
            string height = Console.ReadLine();

            int result = int.Parse(width) * int.Parse(height);
            Console.WriteLine("사각형의 넓이 : {0}", result);

        }
    }

}
```

