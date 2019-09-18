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


***

## group by로 데이터 분류하기
- 특정 기준(조건)에 따라 데이터를 분류


```
group A by B into C
```
- A는 from 절에서 뽑아낸 범위 변수
- B는 분류 기준
- C는 그룹 변수

#### group by Example
```C#
using System;
using System.Linq;

namespace GroupBy
{
    class Profile
    {
        public string Name { get; set; }
        public int Height { get; set; }
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

            var listProfile = from profile in arrProfile
                                orderby profile.Height
                                group profile by profile.Height < 175 into g
                                select new { GroupKey = g.Key, Profiles = g };

            foreach (var Group in listProfile)  // Group은 IGrouping<T> 형식 
            {
                Console.WriteLine($"- 175cm 미만? : {Group.GroupKey}");

                foreach (var profile in Group.Profiles)
                {
                    Console.WriteLine($"    {profile.Name}, {profile.Height}");
                }                
            }   
        }
    }
}

```

```
- 175cm 미만? : True
    김태희, 158
    하하, 171
    고현정, 172
- 175cm 미만? : False
    이문세, 178
    정우성, 186
```


- 분류의 기준은 "175 미만인가? 또는 175 이상인가?"
- 이 쿼리식의 그룹 변수 g에는 Height 값이 175 미만인 객체의 컬렉션, 175 이상인 객체의 컬렉션이 입력되고, 
- select 문이 추출하는 새로운 무명 형식은 컬렉션의 컬렉션이 됨
- 그리고 무명 형식 Profiles 필드는 그룹 변수 g를 담게 됨

#### IGrouping<T> Interface ?
- 공통 키가 있는 개체의 컬렉션
    
***

## 두 데이터의 원본을 연결하는 join

- join은 두 데이터 원본을 연결하는 연산
- 막무가내로 연결하는 건 아니고, 각 데이터 원본에서 특정 필드의 값을 비교하여 일치하는 데이터끼리 연결

### 내부 조인 (Inner Join)
- 교집합과 비슷
- <b>두 데이터 원본 사이에서 일치하는 데이터들만 연결한 후 반환</b>
- join 절의 on 키워드는 조인 조건을 수반
- 이 때, <b>on 절의 조인 조건은 '동등'만 허용</b>
- equals 키워드 사용

```
from a in A
join b in B on a.XXXX equals b.YYYY
```

#### Inner Join Example
```C#
// 첫 번째 배열
class Profile
{
    public string Name {get; set;}
    public int Height {get; set;}
}

// 두 번째 배열
class Product
{
    public string Title {get; set;}
    public string Star {get; set;}
}

// Inner Join
var listProfile = 
    from profile in arrProfile
    join product in arrProduct on profile.Name equals product.Star
    select new
    {
        Name = profile.Name,
        work = product.Title,
        Height = profile.Height
    };
```

***

## 외부 조인 (Outer Join)
- 조인 결과에 데이터 원본이 모두 포함
- 연결할 데이터 원본에 기준 데이터 원본의 데이터와 일치하는 데이터가 없다면, 그 부분은 <b>빈 값으로 채움</b>
- LINQ 에서는 Left Outer Join만 지원

<img src="https://github.com/bluein/C-Sharp-Study/blob/master/OOP/pic/oj.JPG" width=400 height=250 />

#### Outer Join Example
```C#
// 첫 번째 배열
class Profile
{
    public string Name {get; set;}
    public int Height {get; set;}
}

// 두 번째 배열
class Product
{
    public string Title {get; set;}
    public string Star {get; set;}
}

// Outer Join
var listProfile = 
    from profile in arrProfile
    join product in arrProduct on profile.Name equals product.Star into ps
    from product in ps.DefaultIfEmpty(new Product(){Title="그런거 없음"})
    select new
    {
        Name = profile.Name,
        work = product.Title,
        Height = profile.Height
    };
```

- 먼저, join 절을 이용해서 조인을 수행한 후 그 결과를 임시 컬렉션에 저장
- 이 임시 컬렉션에 대해 DefaultIfEmpty 연산을 수행하여 비어 있는 조인 결과에 빈 값을 채움
- DefaultIfEmpty 연산을 거친 임시 컬렉션에서 from 절을 통해 범위 변수를 뽑아 냄
- 이 범위 변수와 기준 데이터 원본에서 뽑아낸 범위 변수를 이용해서 결과를 추출

***

#### Join Example 
```C#
using System;
using System.Linq;

namespace Join
{
    class Profile
    {
        public string Name { get; set; }
        public int Height { get; set; }
    }

    class Product
    {
        public string Title { get; set; }
        public string Star { get; set; }
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

            Product[] arrProduct = 
            {
                new Product(){Title="비트",        Star="정우성"},
                new Product(){Title="CF 다수",     Star="김태희"},
                new Product(){Title="아이리스",    Star="김태희"},
                new Product(){Title="모래시계",    Star="고현정"},
                new Product(){Title="Solo 예찬",   Star="이문세"}
            };

            var listProfile = 
                from profile in arrProfile
                join product in arrProduct on profile.Name equals product.Star
                select new 
                { 
                    Name = profile.Name, 
                    Work = product.Title,
                    Height = profile.Height
                };

            Console.WriteLine("--- 내부 조인 결과 ---");
            foreach (var profile in listProfile)
            {
                Console.WriteLine("이름:{0}, 작품:{1}, 키:{2}cm", 
                    profile.Name, profile.Work, profile.Height);
            }

            listProfile = 
                from profile in arrProfile
                join product in arrProduct on profile.Name equals product.Star into ps
                from product in ps.DefaultIfEmpty(new Product(){Title="그런거 없음"})
                select new
                {
                    Name = profile.Name,
                    Work = product.Title,
                    Height = profile.Height
                };

            Console.WriteLine();
            Console.WriteLine("--- 외부 조인 결과 ---");
            foreach (var profile in listProfile)
            {
                Console.WriteLine("이름:{0}, 작품:{1}, 키:{2}cm",
                    profile.Name, profile.Work, profile.Height);
            }
        }
    }
}

```


```
--- 내부 조인 결과 ---
이름:정우성, 작품:비트, 키:186cm
이름:김태희, 작품:CF 다수, 키:158cm
이름:김태희, 작품:아이리스, 키:158cm
이름:고현정, 작품:모래시계, 키:172cm
이름:이문세, 작품:Solo 예찬, 키:178cm

--- 외부 조인 결과 ---
이름:정우성, 작품:비트, 키:186cm
이름:김태희, 작품:CF 다수, 키:158cm
이름:김태희, 작품:아이리스, 키:158cm
이름:고현정, 작품:모래시계, 키:172cm
이름:이문세, 작품:Solo 예찬, 키:178cm
이름:하하, 작품:그런거 없음, 키:171cm
```

***

#### Min Max Avg Example
```C#
using System;
using System.Linq;

namespace MinMaxAvg
{
    class Profile
    {
        public string Name { get; set; }
        public int Height { get; set; }
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

            var heightStat = from profile in arrProfile
                           group profile by profile.Height < 175 into  g
                           select new
                           {
                               Group   = g.Key==true?"175미만":"175이상",
                               Count   = g.Count(),
                               Max     = g.Max(profile => profile.Height),
                               Min     = g.Min(profile => profile.Height),
                               Average = g.Average(profile => profile.Height)
                           };

            foreach (var stat in heightStat)
            {
                Console.Write("{0} - Count:{1}, Max:{2}, ",
                    stat.Group, stat.Count, stat.Max);
                Console.WriteLine("Min:{0}, Average:{1}",
                    stat.Min, stat.Average);
            }
        }
    }
}

```

```
175이상 - Count:2, Max:186, Min:178, Average:182
175미만 - Count:3, Max:172, Min:158, Average:167
```

















