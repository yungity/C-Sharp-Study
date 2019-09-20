## 파일 다루기

### 파일 정보와 디렉토리 정보 다루기

> 파일(File) : 컴퓨터 저장 매체에 기록되는 데이터의 묶음
> 디렉토리(Directory) : 파일이 위치하는 주소(폴더 라고도 부름)
.NET Framework에선 파일과 디렉토리 정보를 손쉽게 다룰 수 잇도록 System.IO 네임스페이스 아래에 클래스들을 제공한다.

* File : 파일의 생성,복사,삭제,이동,조회를 처리하는 정적 메소드를 제공
* Directory : 디렉토리의 생성,삭제,이동,조회를 처리하는 정적 메소드 제공
* FileInfo : File 메소드와 동일, 정적 메소드 대신 동적 메소드를 제공
* DirectoryInfo : Directory클래스와 하는일은 동일, 정적 메소드 대신 동적 메소드 제공

<details>
<summary>예시 코드</summary>
<div markdown="1">

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

</div>
</details>
