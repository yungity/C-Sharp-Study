# Chap 13. 대리자와 이벤트

***

## 대리자? Delegate
- 콜백(Callback)을 구현하기 위해 사용
- 대리자는 메소드에 의한 참조
- 따라서, 자신이 참조할 메소드의 반환 형식과 매개 변수를 명시해줘야 함

### 선언 Example
```
delegate int MyDelegate(int a, int b);
```

#### 중요한 점
- 대리자는 인스턴스가 아닌 형식(Type) 임 
- 다시 말해, int, string과 같은 Type이며, "메소드를 참조하는 그 무엇"을 만들려면 MyDelegate의 인스턴스를 따로 만들어야 함!
 
### 대리자가 참조할 메소드 선언 & 참조 
```C#
int Plus(int a, int b)
{
    return a+b;
}

int Minus(int a, int b)
{
    return a-b;
}

delegate int MyDelegate(int a, int b);

MyDelegate Callback;

Callback = new MyDelegate(Plus);          // 대리자의 인스턴스를 만들 때도 new 연산자 사용
Console.WriteLine(Callback(3, 4));        // 7 출력

Callback = new MyDelegate(Minus);
Console.WriteLine(Callback(7, 5));        // 2 출력

```
### 대리자를 이용하여 콜백을 구현하는 과정 
<img src="https://github.com/bluein/C-Sharp-Study/blob/master/OOP/pic/dele.JPG" width=450 height=150 />

1) 대리자 선언
2) 대리자의 인스턴스를 생성. 인스턴스를 생성할 때는 대리자가 참조할 메소드를 매개 변수로 넘김
3) 대리자를 호출

***
 
## Delegate Example
```C#
using System;

namespace Delegate
{
    delegate int MyDelegate( int a, int b);     // 대리자 선언

    class Calculator
    {
        public int Plus(int a, int b)           // 대리자는 인스턴스 메소드도 참조할 수 있고 
        {
            return a + b;
        }

        public static int Minus(int a, int b)       // 대리자는 정적 메소드도 참조할 수 있음
        {
            return a - b;
        }
    }

    class MainApp
    {
        static void Main(string[] args)
        {
            Calculator Calc = new Calculator();
            MyDelegate Callback;

            Callback = new MyDelegate( Calc.Plus );   // 대리자를 메소드를 호출하듯 사용하면, 참조하고 있는 메소드가 실행 
            Console.WriteLine(Callback(3, 4));

            Callback = new MyDelegate(Calculator.Minus);
            Console.WriteLine(Callback(7, 5));
        }
    }
}

```

***

## 대리자는 왜 사용하는가?
- '값'이 아닌 '코드'자체를 매개 변수로 넘기고 싶을 때 사용
- 예를 들어, 배열을 정렬하는 메소드를 만들 때, 오름 차순으로 정렬할 것인지 내림 차순으로 정렬할 것인지 프로그래머가 아닌 '대리자'가 알아서 판단

### Example
```C#

// Step 1 - 대리자 선언
delegate int Compare(int a, int b);

// Step 2 - 대리자가 참조할 메소드 선언
static int AscendCompare(int a, int b)
{
    if (a>b)
        return 1;
    else if (a==b)
        return 0;
    else 
        return -1;
}

// Step 3 - 정렬할 배열과 비교 메소드를 참조할 대리자를 매개 변수로 받는 정렬 메소드 작성
static void BubbleSort(int[] DataSet, Compare Com)
{
    int i=0;
    int j=0;
    int temp=0;
    
    for (i=0; i<DataSet.Length-1; i++)
    {
        for (j=0; j<DataSet.Length-(i+1); j++)
        {
            if (Com(DataSet[j], DataSet[j+1]) > 0)
            {
                temp = DataSet[j+1];
                DataSet[j+1] = DataSet[j];
                DataSet[j] = temp;
            }
        }
    }
}

// 위 코드는 오름차순으로 정렬하도록 되어 있음.  
                                
```

***

## Using Callback Example
```C#
using System;

namespace UsingCallback
{
    delegate int Compare(int a, int b);

    class MainApp
    {
        static int AscendCompare(int a, int b)
        {
            if (a > b)
                return 1;
            else if( a == b )
                return 0;
            else
                return -1 ; 
        }

        static int DescendCompare(int a, int b)
        {
            if (a < b)
                return 1;
            else if (a == b)
                return 0;
            else
                return -1;
        }

        static void BubbleSort(int[] DataSet, Compare Comparer) 
        { 
            int i = 0; 
            int j = 0; 
            int temp = 0; 
 
            for ( i=0; i<DataSet.Length-1; i++ ) 
            {
                for (j = 0; j < DataSet.Length - (i + 1); j++)  
                { 
                    if ( Comparer( DataSet[j] , DataSet[j+1] ) > 0 ) 
                    { 
                        temp = DataSet[j+1]; 
                        DataSet[j+1] = DataSet[j]; 
                        DataSet[j] = temp; 
                    }
                }
            }
        }

        static void Main(string[] args)
        {
            int[] array = { 3, 7, 4, 2, 10 };

            Console.WriteLine("Sorting ascending...");
            BubbleSort(array, new Compare(AscendCompare));

            for (int i = 0; i<array.Length; i++)
                Console.Write($"{array[i]} ");

            int[] array2 = { 7, 2, 8, 10, 11 };
            Console.WriteLine("\nSorting descending...");
            BubbleSort(array2, new Compare(DescendCompare));            

            for (int i = 0; i < array2.Length; i++)
                Console.Write($"{array2[i]} ");

            Console.WriteLine();
        }
    }
}

```

```
Sorting ascending...
2 3 4 7 10
Sorting descending...
11 10 8 7 2 
```





 

