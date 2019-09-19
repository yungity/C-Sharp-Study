'이것이 C#이다'를 공부하면서 개인적으로 정리한 자료입니다.
--------------------------------------

## 일반화 프로그래밍
특수한 개념(사람,돼지,오리,너구리)으로부터 공통된 개념을 찾아 묶는 것을 **일반화(Generalization)** 라고 한다. 프로그래밍에서 일반화 하는 대상은
**"데이터 형식"** 을 대상으로 한다.

### 일반화 메소드
일반화 메소드(Generic Method)는 데이터 형식을 일반화한 메소드이다. 일반화 메소드는 일반화할 형식이 들어갈 자리에 구체적인 형식의 이름 대신 형식 매개변수(Type Parameter)가 들어간다는 사실이 다르다.

```
한정자 반환 형식 메소드이름<형식 매개 변수> ( 매개 변수 목록)
{
  //...
}
```
```c#
using System;

namespace CopyingArray
{
    class MainApp
    {
        static void CopyArray<T>(T[] source, T[] target)
        // "T"는 형식(Type)을 뜻하며, <T>을 넣어주면 T는 "형식 매개 변수(Type Parameter)"가 된다.
        // CopyArray()를 호출할 때 <>사이에 T대신 형식의 이름을 입력해주면 컴파일러는 메소드의 나머지 부분에        
        //  대해서도 T를 형식 매개변수 값으로 치환 해준다.
        {
            for(int i=0;i<source.Length;i++)
            {
                target[i] = source[i];
            }
        }
   
       static void Main(string[] args)
       {
            int[] source = { 1, 2, 3, 4, 5 };
            int[] target = new int[source.Length];

            CopyArray<int>(source,target); =

            foreach (int element in target)
                Console.WriteLine(element);

            string[] source2 = { "하나", "둘", "셋", "넷", "다섯" };
            string[] target2 = new string[source2.Length];

            CopyArray<string>(source2, target2);

            foreach (string element in target2)
                Console.WriteLine(element);
       }
    }
}
```

### 일반화 클래스
일반화 클래스는 데이터 형식을 일반화한 클래스다. 일반화 메소드와 방식이 크게 다르지 않다.

```c#
    class Array_Generic<T>
    {
        private T[] array;
        //..
        public T GetElement(int index)
        {
            return array[index];
        }
    }
```
위의 코드는 배열의 값을 입력받는 클래스와 메소드이다. 사용은 어떻게 할까?

```C#
  Array_Generic<int> intArr = new Array_Generic<int>();
```
위와 같이 생성할 경우 
```c#
    class Array_Generic
    {
        private int[] array;
        //..
        public int GetElement(int index)
        {
            return array[index];
        }
    }
```
와 같이 T의 위치에 int로 치환되어서 컴파일 된다.

### 형식 매개 변수 제약시키기
일반화 메소드나 일반화 클래스가 입력받는 형식 매개 변수 T는 **"모든"** 데이터 형식을 대신할 수 있다. 하지만 가끔은 받을 수 있는 매개 변수를 제약시켜야 할떄가 있다. 이때 방법을 알아보자.

```
  where 형식 매개 변수 : 제약조건
```
```c#
    void CopyArray<T>(T[] source, T[]target) where T : struct
    {
        for(int i = 0;i<source.Length;i++)
        {
            target[i] = source[i];
        }
    }
```
**CopyArray<"T">()의 형식 매개 변수 T에 "값 형식이어야 할 것"** 이라는 제약을 주는 방법이다.
  
  
> 제약조건
* where T : struct = T는 값 형식으로 제한
* where T : class = T는 참조 형식으로 제한
* where T : new() = T는 반드시 매개 변수가 없는 생성자가 있어야 한다
* where T : 기반_클래스_이름 = T는 명시간 기반 클래스의 파생 클래스여야 한다.
* where T : 인터페이스_이름 = T는 명시한 인터페이스를 반드시 구현해야 한다. 인터페이스_이름에는 여러 개의 인터페이스도 가능하다.
* where T : U = T는 또 다른 형식 매개 변수 U로부터 상속받은 클래스여야 한다.

```c#
using System;
using System.Collections;
using static System.Console;

namespace ConstraintsOnTypeParameters
{
    class StructArray<T> where T : struct // T는 값 형식만 가능
    {
        public T[] Array { get; set; } // 인덱서
        public StructArray(int size) // 생성자
        {
            Array = new T[size];
        }
        public int Length
        {
            get { return Array.Length; }
        }
    }

    class RefArray<T> where T : class // T는 참조 형식만 가능
    {
        public T[] Array { get; set; } // 인덱서
        public RefArray(int size) // 생성자
        {
            Array = new T[size];
        }
        public int Length
        {
            get { return Array.Length; }
        }
    }

    class Base { }
    class Drived : Base { }
    class BaseArray<U> where U : Base
    {
        public U[] Array { get; set; }
        public BaseArray(int size)
        {
            Array = new U[size];
        }

        public void CopyArray<T>(T[] Source) where T : U
        {
            Source.CopyTo(Array, 0);
        }
        public int Length
        {
            get { return Array.Length; }
        }
    }

    class MainApp
    {
        public static T CreateInstance<T>() where T : new() // T 는 반드시 매개변수가 없는 생성자가 있어야 한다.
        {
            return new T();
        }
        static void Main(string[] args)
        {
            StructArray<int> a = new StructArray<int>(3); // Array 생성
            a.Array[0] = 0;
            a.Array[1] = 1;
            a.Array[2] = 2;

            for (int i = 0; i < a.Length; i++)
                WriteLine(a.Array[i]);

            RefArray<StructArray<double>> b = new RefArray<StructArray<double>>(3);
            b.Array[0] = new StructArray<double>(5);
            b.Array[1] = new StructArray<double>(10);
            b.Array[2] = new StructArray<double>(1005);

            WriteLine("RefArray<StructArray<double>>");

            for (int i = 0; i < b.Length; i++)
                WriteLine(b.Array[i].Array.Length);

            BaseArray<Base> c = new BaseArray<Base>(3);
            c.Array[0] = new Base();
            c.Array[1] = new Drived();
            c.Array[2] = CreateInstance<Base>();

            BaseArray<Drived> d = new BaseArray<Drived>(3);
            d.Array[0] = new Drived(); // Base 형식 여기에 할당 할 수 없다.
            d.Array[1] = CreateInstance<Drived>();
            d.Array[2] = CreateInstance<Drived>();

            BaseArray<Drived> e = new BaseArray<Drived>(3);
            e.CopyArray<Drived>(d.Array);
        }
```

상위 코드에서 사용되던 형식 매개 변수 U로부터 상속받는 형식으로 제약 조건을 주는 예이다. 
다음 코드의 CopyArray<"T">()는 소속 클래스인 BaseArray<U>의 형식 매개변수 U로부터 T가 상속받아야 할 것을 강제한다.

### 일반화 컬렉션
지금까지 사용한 컬렉션 클래스들은 모두 Object 형식으로 기반으로 하였기에, 어떤 형식이든 간에 Object 형식을
상속받고 있으므로 Object 형식으로 형식 변환이 가능하다.

#### System.Collections.Generic 
일반화 컬렉션들을 모아놓은 네임스페이스이다. 일반화 컬렉션은 object 형식 기반의 컬렉션이 갖고 있던 문제를(형식 변환의 비용) 일반화에 기반해서
만들기 때문에 컴파일할 때 컬렉션에서 사용할 형식이 결정되고, 쓸데없는 형식 변환을 일으키지 않는다. 또한 잘못된 형식의 객체를 담게 될 위험도 피할
수 있다.

### List<"T">
List<"T">클래스는 비일반화 클래스인 ArrayList와 같은 기능을 하며 사용법도 동일하다. 차이점은 List<"T">클래스는 인스턴스를 만들때 형식 매개 변수로 입력한 형식 외에는 입력을 허용하지 않는다.

```c#
using System;
using System.Collections.Generic;

namespace UsingGenericList
{
    class MainApp
    {
        static void Main(string[] args)
        {
            List<int> list = new List<int>();
            for(int i =0;i<5;i++)
            {
                list.Add(i);
            }

            foreach(int element in list)
            {
                Console.Write($"{element}");
            }
            Console.WriteLine();
        }
    }
}
```

### Queue<"T">
Queue<"T">  클래스는 형식 매개 변수를 요구하는 점만 다르고, 비일반화 클래스인 Queue와 같은 기능을 하며 사용법도 동일하다.
  
```c#
 static void Main(string[] args)
        {
            Queue<int>que = new Queue<int>();
            
            que.Enqueue(1); // Queue에 입력하고
            que.Enqueue(2);
            que.Enqueue(3);
            que.Enqueue(4);
            que.Enqueue(5);

            while (que.Count > 0)
                Console.WriteLine(que.Dequeue()); // FIFO방식으로 출력한다.
        }
```
 
### Stack<"T">
Queue<"T">와 동일하다.
  
### Dictionary<TKey,TValue>
Dictionary<TKey,TValue>는 Hashtable의 일반화 버전이다. TKey는 Key,TValue는 Value를 위한 형식이다.

```c#
        static void Main(string[] args)
        {
            Dictionary<string,string> dic = new Dictionary<string,string>();
            dic["하나"] = "one";
            dic["둘"] = "two"; 
            dic["셋"] = "three";

            WriteLine(dic["하나"]);
            WriteLine(dic["둘"]);
            WriteLine(dic["셋"]);
        }
 ```
 
 ### foreach를 사용할 수 있는 일반화 클래스

```c#
using System;
using static System.Console;
using System.Collections;
using System.Collections.Generic;

namespace EnumerableGeneric
{
    class MyList<T> : IEnumerable<T>, IEnumerator<T>
    {
        private T[] array;
        int position = -1; // 컬렉션의 현재 위치를 다루는 변수, 초기값은 0이 아닌 -1

        public MyList()
        {
            array = new T[3];
        }
        public T this[int index]
        {
            get
            {
                return array[index];
            }
            set
            {
                if (index >= array.Length)
                {
                    Array.Resize<T>(ref array, index + 1);
                    WriteLine($"Array Resized : {array.Length}");
                }

                array[index] = value;
            }
        }

        public int Length
        {
            get { return array.Length; }
        }

        public IEnumerator<T> GetEnumerator()
        {
            for (int i = 0; i < array.Length; i++)
            {
                yield return (array[i]);
            }
        }

        IEnumerator IEnumerable.GetEnumerator()
        {
            for (int i = 0; i < array.Length; i++)
            {
                yield return (array[i]);
            }
        }
        //IEnumerator 멤버
        public T Current //IEnumerator로부터 상속받은 Current 프로퍼티는 현재 위치의 요소를 반환
        {
            get
            {
                return array[position];
            }
        }

        Object IEnumerator.Current
        {
            get { return array[position]; }
        }

        //IEnumerator 멤버
        public bool MoveNext()
        {
            if (position == array.Length - 1)
            {
                Reset(); // 첫번째 위치가 0이라면, Reset()을 호출해서 -1번으로 이동
                return false;
            }

            position++;
            return (position < array.Length);
        }

        //IEnumerator 멤버
        public void Reset() // 요소의 위치를 첫 요소의 "앞"으로 옮깁니다.
        {
            position = -1;
        }
        public void Dispose()
        {

        }
    }

    class MainApp
    {
        static void Main(string[] args)
        {
            MyList<string> str_list = new MyList<string>();
            str_list[0] = "abc";
            str_list[1] = "def";
            str_list[2] = "ghi";
            str_list[3] = "jkl";
            str_list[4] = "mno";

            foreach (string str in str_list)
                Console.WriteLine(str);
            Console.WriteLine();

            MyList<int> int_list = new MyList<int>();
            int_list[0] = 0;
            int_list[1] = 1;
            int_list[2] = 2;
            int_list[3] = 3;
            int_list[4] = 4;

            foreach (int no in int_list)
                Console.WriteLine(no);

        }
    }
}
```
출처 : 이것이 C#이다(저자 : 박상현 , 출판사 : 한빛미디어) CH.11 일반화 프로그래밍
