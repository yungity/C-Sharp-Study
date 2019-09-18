# Chap 15. LINQ

***

## LINQ ?
- Language INtegrated Query
- "링크"라고 읽으면 됨
- Collection을 편리하게 다루기 위한 목적으로 만들어진 질의 언어 (Query Language)
- 지루한 데이터 작업으로부터 해방
- 코드의 양을 줄일 수 있음

#### Query
- From: 어떤 데이터 집합에서 찾을 것인가?
- Where: 어떤 값의 데이터를 찾을 것인가?
- Select: 어떤 항목을 추출할 것인가?

***

#### Example : 키가 175 미만인 데이터만 추출
```C#

// 클래스 선언
class Profile
{
    public string Name { get; set; }
    public int Height { get; set; }
}

// 클래스에 대한 배열 선언
Profile[] arr = {
                  new Profile(){Name="정우성1", Height=186},
                  new Profile(){Name="정우성2", Height=181},
                  new Profile(){Name="정우성3", Height=176},
                  new Profile(){Name="정우성4", Height=182},
                  new Profile(){Name="정우성5", Height=169},
                  new Profile(){Name="정우성6", Height=170}
                };

List<Profile> proList = new List<Profile>();
```


#### LINQ를 안썼을 때
```C#

foreach (Profile pro in arr)
{
    if (pro.Height < 175)
        proList.Add(pro);
}

// 키 오름차순 정렬
proList.Sort(
    (pro1, pro2) =>
    {
        return pro1.Height - pro2.Height;
    });

foreach (var pro in proList)
    Console.WriteLine(pro.Name, pro.Height);
                      
```


#### LINQ를 썼을 때

```C#
var proList = from pro in arr
              where pro.Height < 175
              orderby pro.Height
              select pro;
              
foreach (var pro in proList)
    Console.WriteLine(pro.Name, pro.Height);

```

***


### from 절
- LINQ의 쿼리식은 반드시 from 절로 시작
- <b>from의 데이터 원본은 반드시 IEnumerable<T> 인터페이스를 상속하는 형식이어야 함!</b>

### where 절
- 필터 역할을 하는 연산자
- 조건에 부합하는 데이터만 걸러짐

### orderby 절
- 데이터의 정렬을 수행
- 오름차순이 default
- 명시적으로 ascending 키워드를 붙여도 됨
- 내림차순은 descending 키워드 사용

### select 절
- 최종 결과를 추출

***

#### Example
```C#
using System;
using System.Linq;

namespace From
{
    class MainApp
    {
        static void Main(string[] args)
        {
            int[] numbers = { 9, 2, 6, 4, 5, 3, 7, 8, 1, 10 };

                          // n은 범위 변수, numbers는 데이터 원본
            var result = from n in numbers
                         where n % 2 == 0
                         orderby n
                         select n;
            
            foreach (int n in result)
                Console.WriteLine($"짝수 : {n}");
        }
    }
}

```


```
짝수 : 2
짝수 : 4
짝수 : 6
짝수 : 8
짝수 : 10
```

***


#### Simple LINQ Example
```C#
using System;
using System.Collections.Generic;
using System.Linq;

namespace SimpleLinq
{
    class Profile
    {
        public string Name { get; set; }
        public int    Height { get; set; }
    }

    class MainApp
    {
        static void Main(string[] args)
        {
            Profile[] arrProfile = 
            {
                new Profile(){Name="정우성", Height=186},
                new Profile(){Name="김태희", Height=158},
                new Profile(){Name="고현정", Height=172},
                new Profile(){Name="이문세", Height=178},
                new Profile(){Name="하하", Height=171}
            };

            var profiles = from profile in arrProfile
                           where profile.Height < 175
                           orderby profile.Height
                           select new
                           {
                               Name = profile.Name,
                               InchHeight = profile.Height * 0.393
                           };
                           // select 절에서 최종 출력할 새로운 형식을 즉석에서 만들어 낸 경우
                           // "무명 형식"

            foreach (var profile in profiles)
                Console.WriteLine($"{profile.Name}, {profile.InchHeight}");
        }
    }
}

```

```
김태희, 62.094
하하, 67.203
고현정, 67.596
```

***


## 여러 개의 데이터 원본에 질의하기
- 여러 개의 데이터 원본에 접근하는 쿼리?
- LINQ는 기본적으로 데이터 원본에 접근하기 위해 from 절을 사용
- 여러 개의 데이터 원본에 접근하려면 <b>from 문을 중첩해서 사용하면 됨</b>

#### Example
```C#

// 클래스 선언
class Class
{
    public string Name {get; set;}
    public int[] Score {get; set;}  // 주목! 배열임
}

// 클래스를 바탕으로 배열 하나를 선언
Class[] arrClass = 
{
    new Class(){Name="연두반", Score=new int[]{99, 80, 70, 24}},
    new Class(){Name="분홍반", Score=new int[]{60, 45, 87, 72}},
    new Class(){Name="파랑반", Score=new int[]{92, 30, 85, 94}},
    new Class(){Name="노랑반", Score=new int[]{94, 88, 0, 15}}
};

// LINQ - fromfrom
var classes = from c in arrClass        // 첫 번째 데이터 원본
                  from s in c.Score     // 두 번째 데이터 원본
                  where s < 60
              select new {c.Name, Lowest=s};

```


#### From From Example
```C#
using System;
using System.Linq;

namespace FromFrom
{
    class Class
    {
        public string Name { get; set; }
        public int[]  Score { get; set; }
    }

    class MainApp
    {
        static void Main(string[] args)
        {
            Class[] arrClass = 
            {
                new Class(){Name="연두반", Score=new int[]{99, 80, 70, 24}},
                new Class(){Name="분홍반", Score=new int[]{60, 45, 87, 72}},
                new Class(){Name="파랑반", Score=new int[]{92, 30, 85, 94}},
                new Class(){Name="노랑반", Score=new int[]{90, 88, 0, 17}}
            };

            var classes = from c in arrClass
                          from s in c.Score
                          where s < 60
                          orderby s
                          select new { c.Name, Lowest = s};

            foreach (var c in classes )
                Console.WriteLine($"낙제 : {c.Name} ({c.Lowest})");
        }
    }
}

```

```
낙제 : 노랑반 (0)
낙제 : 노랑반 (17)
낙제 : 연두반 (24)
낙제 : 파랑반 (30)
낙제 : 분홍반 (45)
```




