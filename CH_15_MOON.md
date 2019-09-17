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

### Example : 키가 175 미만인 데이터만 추출
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
























