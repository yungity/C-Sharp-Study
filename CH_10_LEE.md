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
