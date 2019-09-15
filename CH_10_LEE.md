'이것이 C#이다'를 공부하면서 개인적으로 정리한 자료입니다.
--------------------------------------

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
