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

```C#
Cat kitty = new Cat();
```
