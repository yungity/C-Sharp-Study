# Chap 15. 파일 다루기

***

#### 파일과 디렉토리 다루기?
- .NET 프레임워크에서는 파일과 디렉토리 정보를 손쉽게 다룰 수 있도록 System.IO 네임스페이스 아래에 다음과 같은 클래스들을 제공

|클래스|설명|
|:---:|:---:|
|File|파일의 생성, 복사, 삭제, 이동, 조회를 처리하는 <b>정적 메소드</b>를 제공|
|FileInfo|File 클래스와 기능은 동일, <b>인스턴스 메소드</b>를 제공|
|Directory|디렉토리의 생성, 삭제, 이동, 조회를 처리하는 <b>정적 메소드</b>를 제공|
|DirectoryInfo|Directory 클래스와 기능은 동일, <b>인스턴스 메소드</b>를 제공|

***


#### dir Example : 디렉토리, 파일 정보 조회
```C#
using System;
using System.Linq;
using System.IO;

namespace Dir
{
    class MainApp
    {
        static void Main(string[] args)
        {
            string directory;
            if (args.Length < 1)
                directory = ".";
            else
                directory = args[0];

            Console.WriteLine($"{directory} directory Info");
            Console.WriteLine("- Directories :");
            var directories = (from dir in Directory.GetDirectories(directory)
                               let info = new DirectoryInfo(dir)
                               select new
                               {
                                   Name = info.Name,
                                   Attributes = info.Attributes
                               }).ToList();

            foreach (var d in directories)
                Console.WriteLine($"{d.Name} : {d.Attributes}");

            Console.WriteLine("- Files :");
            var files = (from file in Directory.GetFiles(directory)
                         let info = new FileInfo(file)
                         select new
                         {
                             Name = info.Name,
                             FileSize = info.Length,
                             Attributes = info.Attributes
                         }).ToList();
            foreach (var f in files)
                Console.WriteLine(
                    $"{f.Name} : {f.FileSize}, {f.Attributes}");
        }
    }
}
```

```
C:\Users\Affinity\source\repos\HelloWorld\Project1\bin\Debug>Project1.exe "c:\Users"
c:\Users directory Info
- Directories :
Affinity : Directory
All Users : Hidden, System, Directory, ReparsePoint, NotContentIndexed
Default : ReadOnly, Hidden, Directory
Default User : Hidden, System, Directory, ReparsePoint, NotContentIndexed
Public : ReadOnly, Directory
- Files :
desktop.ini : 174, Hidden, System, Archive
```

 

