출처: 이것이 C#이다 (저자: 박상현 , 출판사: 한빛미디어) 

***
## 정리
***

## What is Class? 
- 데이터와 메소드를 묶는 집합

```C#
class Cat
{
  public string Name;
  public string Color;
  
  public void Meow(){
    Console.WriteLine("{0}: 야옹", Name);
  }
}
```

## What is Member?
- Class 안에 선언 된 변수들을 Field 라고 함
- 클래스 내에 선언되어 있는 요소(Field, Method ...)들을 Member 라고 함

*** 

## 객체를 생성하는 코드
```C#
Cat kitty = new Cat();
```
- Heap 영역에 객체를 생성, kitty는 생성한 객체를 가리킴 
- Cat( )은 Constructor(생성자)라고 하는 특별한 Method
- Constructor는 Class의 이름과 동일한 이름을 가지며, 객체를 생성하는 역할
- new와 Constructor는 항상 붙어서 선언

```
int a = new int();
```
- C#에서는 모든 형식이 Class이기 때문에, 다음과 같이 모든 데이터 형식에 new 연산자가 사용이 가능하지만
- 굳이 이렇게 할 필요성은? X
***

## Constructor & Finalizen 
- 생성자 & 종료자(=소멸자)
- 객체는 생성자에 의해 만들어지고 종료자에 의해 파괴
- 생성자는 Class와 이름이 같고, 반환 형식이 없음 -> 오직 객체 생성만을 위해 존재
- 생성자도 여느 메소드와 마찬가지로 오버로딩이 가능 -> 다양한 버전의 생성자 

### Constructor
```C#
class Cat
{
  public Cat(){                                   // Default Constructor 
    Name = "";
    Color = "";
    }
  public Cat(String _Name, string _Color){        // Constructor - Method Overroading
    Name = _Name;
    Color = _Color;
    }
  
  public string Name;
  public string Color;
  
  '''
}
```
- Overroading 된 생성자가 없을 때는 기본 생성자가 Default로 호출
- 매개변수를 주어서 생성자를 호출하게 되면, Overroading 된 생성자가 호출
- 그러나, Overroading 된 생성자가 있을 경우 C# 컴파일러는 Default 생성자를 자동으로 호출하지 않음
- 이는 프로그래머의 의도와 다르게 동작하는 코드가 제공되는 것을 방지하기 위한 것

***

### Finalizen
```C#
namespace Constructor
{
    class Cat
    {
        public Cat()
        {
            Name = "";
            Color = "";
        }

        public Cat(string _Name, string _Color)
        {
            Name = _Name;
            Color = _Color;
        }

        ~Cat()            // Finalizen - 종료자
        {
            Console.WriteLine($"{Name} : 잘가");
        }

        public string Name;
        public string Color;

        public void Meow()
        {
            Console.WriteLine($"{Name} : 야옹");
        }
    }

```

- Class 이름 앞에 ~를 붙인 형태
- 생성자와 달리, 매개변수도 없고, 한정자(ex. Public..)도 사용하지 않음
- Overroading도 불가능하며 직접 호출도 불가
- 그럼 언제 호출? -> CLR의 Garbage Collector가 객체가 소멸되는 시점을 판단해서 종료자를 호출!
- Garbage Collector가 알아서 객체를 소멸 해주기 때문에 종료자를 굳이 사용하지 않는 것을 권장 

***

## What is Static?
- C#에서의 static은 Field와 Method가 Class의 Instance가 아닌, Class 자체에 소속되도록 지정하는 한정자 
- static으로 한정하지 않은 Field는 자동으로 Instance에 소속되며, static으로 한정한 Field는 Class에 소속

<img src="https://github.com/bluein/C-Sharp-Study/blob/master/OOP/pic/moon_static.png" 
data-canonical-src="https://github.com/bluein/C-Sharp-Study/blob/master/OOP/pic/moon_static.png" width="600" height="350" />

# hi
