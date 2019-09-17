'이것이 C#이다'를 공부하면서 개인적으로 정리한 자료입니다.
--------------------------------------
### System.Array

```c#
namespace UsingHashTable
{

    class MainApp
    {
        private static bool CheckPassed(int score)
        {
            if (score >= 60)
                return true;
            else
                return false;
        }

        private static void Print(int value)
        {
            Write($"{value} ");
        }
        static void Main(string[] args)
        {
            int[] scores = new int[] { 80, 74, 81, 90, 34 };

            foreach (int score in scores)
                Write($"{score} ");
            WriteLine();

            Array.Sort(scores); // 오름차순 정렬
            Array.ForEach<int>(scores, new Action<int>(Print)); // ForEach()는 배열의 모든 요소에 대해 동일한 작업 수행
            WriteLine();

            WriteLine($"Number of dimensions : {scores.Rank}"); // 배열의 차원 
            WriteLine("Binary Search: 81 is at {0}", Array.BinarySearch<int>(scores, 81)); // 이진 탐색 실행 
            WriteLine("Linear Search : 90 is at {0}", Array.IndexOf(scores, 90)); // 90은 몇번째 인덱스에 있나요
            WriteLine("EveryOne passed ? : {0}", Array.TrueForAll<int>(scores, CheckPassed)); // 배열의 모든 요소가 지정한 조건에 부합하는지 여부 반환

            int index = Array.FindIndex<int>(scores,
                delegate (int score)
                {
                    if (score < 60)
                        return true;
                    else
                        return false;
                });

            scores[index] = 61;
            WriteLine("Everyone passed ? : {0}",
                Array.TrueForAll<int>(scores, CheckPassed));
            
            WriteLine($"Old length of scores : {scores.GetLength(0)}"); // 0번째 차원의 길이는 ?

            Array.Resize<int>(ref scores, 10); // 배열의 인덱스 크기 조절과 0으로 초기화
            
            WriteLine($"New length of scores : {scores.Length}"); // 전체 배열의 길이는 ?

            Array.ForEach<int>(scores, new Action<int>(Print));
            WriteLine();

            Array.Clear(scores, 3, 7); // 3~7 사이에 값을 0으로 만들어라.

            Array.ForEach<int>(scores, new Action<int>(Print));
            WriteLine();
        }
    }
}
```

### 2차원 배열
2차원 배열을 선언하는 방법은

```
데이터형식[,] 배열이름 = new 데이터형식[2차원길이,1차원길이];
```

2차원 배열을 선언과 동시에 초기화 하는 경우
```c#
int[,] arr = new int[2,3]{{1,2,3},{4,5,6}}; // 배열의 형식과 길이를 명시
int[,] arr2 = new int[,]{{1,2,3},{4,5,6}}; // 배열의 길이를 생략
int[,] arr3 = {{1,2,3},{4,5,6}}; // 형식과 길이 모두 생략
```

```c#
using System;

namespace _2Darray
{
    class MainApp
    {
        static void Main(string[] args)
        {
            int[,] arr = new int[2, 3] { { 1, 2, 3 }, { 4, 5, 6 } }; // 배열의 형식과 길이를 명시
            for (int i = 0; i < arr.GetLength(0); i++)
            {
                for (int j = 0; j < arr.GetLength(1); j++)
                {
                    Console.Write($"[{i},{j} : {arr[i, j]}");

                }
                Console.WriteLine();
            }
            Console.WriteLine();
            
            foreach (int i in arr) // foreach를 사용하면 배열의 순서대로 출력된다.
            {
                Console.WriteLine(i);
            }
        }
    }
}
```

### 가변 배열
진정한 의미에서의 배열을 요소로 같는 배열로, 들쭉날쭉(Jagged)한 배열이다.
```
데이터형식[][] 배열이름 = new 데이터형식[가변 배열의 용량][];
```
2차원 배열과 비슷하나 2차원 배열은 아니다. 가변 배열의 요소로 입력되는 배열은 그 길이가 모두 같을 필요는 없다. 

```c#
int[][] jagged = new int[3][];
jagged[0] = new int[5]{1,2,3,4,5};
jagged[1] = new int[]{10,20,30};
jagged[2] = new int[]{100,200};
```
위와 같이 0행,1행,2행 모두 각자 길이가 다르다. 들쭉날쭉하다는 것은 이런 의미인 것이다. 가변 배열의 요소는 "배열"이다.
```c#
using System;

namespace _2Darray
{
    class MainApp
    {
        static void Main(string[] args)
        {
            int[][] jagged = new int[3][];
            jagged[0] = new int[5] { 1, 2, 3, 4, 5 };
            jagged[1] = new int[] { 10, 20, 30 };
            jagged[2] = new int[] { 100, 200 };

            foreach(int[] arr in jagged)
            {
                Console.WriteLine($"Length : {arr.Length},");
                foreach(int e in arr)
                {
                    Console.Write($"{e}");
                }
                Console.WriteLine("");
            }
            Console.WriteLine("");

            /*foreach(int e in jagged)
            {
                Console.WriteLine(e);
            }*/ int e으로는 int[](jagged의 형식)을 출력할 수 없다.
        }
    }
}
```


### 컬렉션
컬렉션(Collection)이란 같은 성격을 띄는 데이터의 모음을 담는 **자료구조** 이다. 배열또한 컬렉션의 일부이다.

>컬렉션의 종류
* ArrayList
* Queue
* Stack
* Hashtable

### ArrayList
가장 배열과 닮은 컬렉션이지만, 배열과는 달리 컬렉션을 생성할 때 용량을 미리 지정할 필요가 없이 필요에 따라 자동으로 그 용량이 늘어나거나 줄어든다. 
> ArrayList의 메소드
* Add() : 컬렉션의 가장 마지막에 있는 요소 뒤에 새 요소를 추가
* RemoveAt() : 특정 인덱스에 있는 요소를 제거
* Insert() : 원하는 위치에 새 요소를 삽입

```c#
ArrayList list = new ArrayList();
list.Add(10);
list.Add(20);

list.RemoveAt(1); // 20을 삭제
list.Insert(1,25); // 25를 1번 인덱스에 삽입. 즉, 10과 30 사이에 25를 삽입
```
> ArrayList가 다양한 형식의 객체를 담을 수 있는 이유

Add(),Insert() 메소드의 선언을 보면 Object 형식으로 매개 변수를 받고 있다. 즉 메소드에 int 형식의 데이터를 넣더라도 정수 형식 그대로
입력되는 것이 아니라 Object형식으로 박싱되어 입력되는 것이다. 반대로 요소에 접근해서 사용할 떄는 언박싱이 이루어진다.
박싱과 언박싱은 작지 않은 **오버헤드**를 요구하는 작업이므로, ArrayList가 다루는 데이터가 많아지면 성능의 저하가 더욱 늘어난다. 이는 Stack,Queue,Hashtable 등의 컬렉션도 갖고 있다. 해결방법은 일반화컬렉션(Generic Collection)이 있다.

> 오버헤드란?

어떤 처리를 하기 위해 들어가는 간접적인 처리 시간 · 메모리 등을 말한다. 예를 들어 A라는 처리를 단순하게 실행한다면 10초 걸리는데, 안전성을 고려하고 부가적인 B라는 처리를 추가한 결과 처리시간이 15초 걸렸다면, 오버헤드는 5초가 된다.

### Queue
큐(Queue)는 대기열, 즉 기다리는 줄이라는 뜻이다. 큐는 데이터 작업을 차례대로 입력해뒀다가 입력된 순서대로 하나씩 꺼내 처리하기 위해 사용된다. 
큐의 입력은 오직 뒤에서, 출력은 앞에서만 이루어진다(**FIFO**, 선입선출, First-In-First-Out)

* Enqueue(): 데이터 입력 메소드
* Dequeue() : 데이터를 꺼낼때 사용하는 메소드

```c#
using System;
using System.Collections;

namespace UsingQueue
{
    class MainApp
    {
        static void Main(string[] args)
        {
            Queue que = new Queue();
            que.Enqueue(1); // Queue에 입력하고
            que.Enqueue(2);
            que.Enqueue(3);
            que.Enqueue(4);
            que.Enqueue(5);

            while (que.Count > 0)
                Console.WriteLine(que.Dequeue()); // FIFO방식으로 출력한다.
        }
    }
}
```
#### output
```
1
2
3
4
5
```
### Stack
스택은 큐와는 다르게 반대로 먼저 들어온 데이터가 나중에 나가고(First-In - Last Out), 나중에 들어온 데이터는 먼저 나가는(Last In - First Out) 구조의 컬렉션이다.(**LIFO**,후입선출,Last-In-First-Out)

* Push() : 데이터를 넣을 때 사용, Push()는 데이터를 위에 "쌓고"
* Pop() : 데이터를 꺼낼 떄 사용, Pop() 메소드는 제일 위에 쌓여 있는 데이터를 "꺼낸다"

```c#
using System;
using System.Collections;

namespace UsingQueue
{
    class MainApp
    {
        static void Main(string[] args)
        {
            Stack stack = new Stack();
            stack.Push(1);
            stack.Push(2);
            stack.Push(3);
            stack.Push(4);
            stack.Push(5);

            while (stack.Count > 0)
                Console.WriteLine(stack.Pop());
        }
    }
}
```

#### output
```
5
4
3
2
1
```

### Hashtable
파이썬의 사전형 데이터와 거의 동일하다. 해쉬테이블은 배열에서 인덱스를 이용해서 배열 요소에 접근하는 것에 준하는 탐색 속도로 탐색 속도가 거의 소요되지 않는다. ArrayList에서는 원하는 데이터를 찾으려면 정렬 후 이진 탐색을 하거나 순차적으로 리스트를 탐색해야 하지만 해쉬테이블은 단번에 데이터가 저장되어 있는 컬렉션 내의 주소를 계산한다. 이를 **헤싱(Hashing)** 이라고 한다.

```c#
using System;
using System.Collections;
using static System.Console;

namespace UsingHashTable
{
    class MainApp
    {
        static void Main(string[] args)
        {
            Hashtable ht = new Hashtable();
            ht["하나"] = "one";
            ht["둘"] = "two"; 
            ht["셋"] = "three";

            WriteLine(ht["하나"]);
            WriteLine(ht["둘"]);
            WriteLine(ht["셋"]);
        }
    }
}
```

### 컬렉션을 초기화 하는 방법
* 배열을 이용해 초기화
ArrayList,Queue,Stack은 배열의 도움을 받아 간단하게 초기화를 할 수 있다. 이들 컬렉션의 생성자를 호출 할때 매개 변수로 배열 객체를 넘기면
컬렉션 객체는 해당 배열을 바탕으로 내부 데이터를 채운다.
```c#
int[] arr = {123,456,789};
ArrayList list = new ArrayList(arr); // 123, 456, 789
Stack stack = new Stack(arr); // 789, 456, 123
Queue queue = new Queue(arr); // 123, 456, 789
```

* 배열의 도움 없이 직접 컬렉션 초기자를 이용
ArrayList만 가능하고 Stack,Queue는 사용할 수 없다.
```c#
ArrayList list = new ArrayList(){11,22,33};
```

* 딕셔너리 초기자(Dictionary Initializer)
Hashtable 초기화를 할때 사용하는 방식, 컬렉션 초기자와 비슷하게 생겼다.
```c#
Hashtable ht = new Hashtable()
{
   ["하나"] = 1, // ;가 dkslfk ,를 이용하여 항목 구분
   ["둘"] = 2,
   ["셋"] = 3
};
```
물론 컬렉션 초기자를 사용하여 초기화가 가능하다.
```c#
Hashtable ht = new Hashtable()
{
   {"하나",1},
   {"둘",2},
   {"셋",3}
};
```
### 인덱서
인덱서(indexer)는 인덱스(index)를 이용해서 객체 내의 데이터에 접근하게 해주는 프로퍼티라고 생각하면 된다. 객체를 마치 배열처럼 사용 할 수 있게 해준다.

```c#
using System;
using System.Collections;
using static System.Console;

namespace UsingHashTable
{
    class MyList
    {
        private int[] arr;
        
        public MyList()
        {
            arr = new int[3]; // 배열 인스턴트 생성
        }
        public int this[int index] // 인덱서 시작
        {
            get
            {
                return arr[index];
            }

            set
            {
                if(index >= arr.Length) // 만약 인덱스의 길이가 배열보다 크다면
                {
                    Array.Resize<int>(ref arr, index + 1); //배열의 길이 증가
                    Console.WriteLine($"arr resized : {arr.Length}");
                }
                arr[index] = value;
            }
        }
        
        public int Length
        {
            get { return arr.Length; }
        }
    }
    
    class MainApp
    {
        static void Main(string[] args)
        {
            MyList list = new MyList();
            for(int i =0;i<5;i++)
            {
                list[i] = i; // 배열을 다루듯 인덱스를 통해 데이터를 입력
            }
            for (int i = 0; i < list.Length; i++)
            {
                Console.WriteLine(list[i]); // 데이터를 얻어올 때도 인덱스를 이용
            }
        }
    }
}
```
인덱서는 프로퍼티처럼 식별자를 따로 가지지 않는다. 프로퍼티는 이름을 통해 객체 내의 데이터에 접근하게 해준다면, 인덱서는 인덱스를 통해 객체내의
데이터에 접근하게 해준다.

프로퍼티는 객체 내의 데이터에 접근할 수 있도록 하는 통로이다. 인덱서도 동일한 기능을 하지만 차이점은 "인덱스"를 이용한다는 것이다.

### foreach가 가능한 객체 만들기
foreach 문은 Ienumerable,Ienumerator를 상속하는 형식만 지원한다. 즉 두개의 인터페이스를 상속한다면 foreach문을 사용할 수 있다는 의미이다.
우선 Ienumerable 인터페이스가 갖고 있는 메소드는 GetEnumerator() 하나 뿐이므로 이 메소드를 구현해야 한다. 메소드를 구현할 때에는 **yield return** 문의 도움을 받아야 한다. 
* yield return : 현재 메소드의 실행을 일시 정지 시켜놓고 호출자에게 결과를 반환하는 녀석, 메소드가 다시 호출되면, 일시 정지된 실행을 복구하여
yield return 또는 yield break 문을 만날 때까지 나머지 작업을 실행하게 된다.
* GetEnumerator() 메소드 : IEnumerator 인터페이스를 상속하는 클래스의 객체를 반환하면 됨

```c#
using System;
using static System.Console;
using System.Collections;

namespace Yield
{
    class MyEnumerator
    {
        int[] numbers = { 1, 2, 3, 4 };

        public IEnumerator GetEnumerator()
        {
            yield return numbers[0];
            yield return numbers[1];
            yield return numbers[2];
            yield break; // yield break 는 GetEnumerator() 메소드를 종료 시킨다.
            yield return numbers[3];  // 이 코드는 실행이 안됨
        }
    }
    class MainApp
    {
            static void Main(string[] args)
            {
                var obj = new MyEnumerator();
                foreach (int i in obj)
                {
                    WriteLine(i);
                }
            }
    }
}
```

> IEnumerator 인터페이스의 메소드 및 프로퍼티 목록
* boolean MoveNext() : 다음 요소로 이동,  컬렉션의 끝을 지난 경우에는 false, 성공한 경우에는 true 를 반환
* void Reset() : 컬렉션의 첫 번째 위치의 "앞"로 이동합니다. 첫 번쨰 위치가 0번이라면, Reset()을 호출하면 -1번으로 이동하는 한다. 첫번째 위치로의 이동은 MoveNext()를 호출한 다음 이루어진다.
* Object Current(get;) : 컬렉션의 현재 요소를 반환한다.

```c#
using System;
using static System.Console;
using System.Collections;

namespace Enumerable
{
    class MyList : IEnumerable, IEnumerator
    {
        private int[] array;
        int position = -1; // 컬렉션의 현재 위치를 다루는 변수, 초기값은 0이 아닌 -1

        public MyList()
        {
            array = new int[3];
        }
        public int this[int index]
        {
            get
            {
                return array[index];
            }
            set
            {
                if(index >= array.Length)
                {
                    Array.Resize<int>(ref array, index + 1);
                    WriteLine($"Array Resized : {array.Length}");
                }

                array[index] = value;
            }
        }

        //IEnumerator 멤버
        public object Current //IEnumerator로부터 상속받은 Current 프로퍼티는 현재 위치의 요소를 반환
        {
            get
            {
                return array[position];
            }
        }
        //IEnumerator 멤버
        public bool MoveNext()
        {
            if(position == array.Length -1)
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

        //IEnumerable 멤버
        public IEnumerator GetEnumerator()
        {
            for(int i=0;i<array.Length;i++)
            {
                yield return (array[i]);
            }
        }
    }
    class MainApp
    {
       static void Main(string[] args)
       {
            MyList list = new MyList();
            for (int i = 0; i < 5; i++)
            {
                list[i] = i;
            }
            
            foreach(int e in list)
            {
                WriteLine(e);
            }

        }
    }
}
```
출처 : 이것이 C#이다(저자 : 박상현 , 출판사 : 한빛미디어) CH.10 배열과 컬렉션, 그리고 인덱서

출처 : 위키피디아
