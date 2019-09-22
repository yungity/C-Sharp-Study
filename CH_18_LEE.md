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

<details>
<summary>예시 코드</summary>
<div markdown="1">

```c#
using System;
using System.IO;

namespace Touch
{
    class MainApp
    {
        static void OnWrongPathType(string type)
        {
            Console.WriteLine($"{type} is wrong type");
            return;
        }
        static void Main(string[] args)
        {
           if (args.Length ==0)
            {
                Console.WriteLine(
                    "Usage : Touch.exe <Path> [Type:File/Directory]");
                return;
            }

            string path = args[0];
            string type = "File";
            if (args.Length > 1)
                type = args[1];

            if (File.Exists(path) || Directory.Exists(path))
            {
                if (type == "File")
                    File.SetLastWriteTime(path, DateTime.Now);
                else if (type == "Directory")
                    Directory.SetLastWriteTime(path, DateTime.Now);
                else
                {
                    OnWrongPathType(path);
                    return;
                }
                Console.WriteLine($"Updated {path} {type}");
            }
            else
            {
                if (type == "File")
                    File.Create(path).Close();
                else if (type == "Directiory")
                    Directory.CreateDirectory(path);
                else
                {
                    OnWrongPathType(path);
                    return;
                }
                Console.WriteLine($"Created {path} {type}");
            }
        }
    }
}
```

</div>
</details>

### 스트림(Stream)
파일을 다룰때 말하는 스트림은 "데이터가 흐르는 통로"를 말한다. 메모리에서 하드디스크(DVD,USB등)로 데이터를 옮길때(파일을 쓸때)에는 먼저 이 스트림을 만들어 둘 사이를 연결한 뒤에 메모리에 있는 데이터를 바이트 단위로 하드디스크로 옮겨 넣는다.

#### System.IO.Stream 클래스
Stream 클래스는 그 자체로 입력 스트림,출력 스트림의 역할을 모두 할 수 있으며 파일을 읽고 쓰는 방식 역시 순차 접근 방식과 임의 접근 방식 모두를 지원하지만, Stream 클래스는 추상 클래스이기 때문에 이 클래스의 인스턴스를 직접 만들어 사용할 수는 없다. 
<details>
<summary>예시 코드</summary>
<div markdown="1">

using System;
using System.IO;

namespace BasicIO
{
    class MainApp
    {
        static void Main(string[] args)
        {
            long someValue = 0X123456789ABCDEF0; // 파일 생성
            Console.WriteLine("{0,-1} : 0x{1:X16}", "Original Data", someValue);

            Stream outStream = new FileStream("a.dat", FileMode.Create); // 파일 스트림 생성, Create
            byte[] wBytes = BitConverter.GetBytes(someValue); // long 형식을 Byte 배열로 변환

            Console.Write("{0,-13} :", "Byte array");

            foreach (byte b in wBytes)
                Console.Write("{0:X2} ", b);
            Console.WriteLine();

            outStream.Write(wBytes, 0, wBytes.Length); // 변환한 byte 배열을 파일 스트림을 통해 기록
            outStream.Close(); // 파일 스트림 닫기 

            Stream inStream = new FileStream("a.dat", FileMode.Open); // 파일 스트림 생성
            byte[] rbytes = new byte[8]; // byte 배열을 long 형식의 크기로 맞춰서 변환

            int i = 0;
            while (inStream.Position < inStream.Length)
                rbytes[i++] = (byte)inStream.ReadByte();

            long readValue = BitConverter.ToInt64(rbytes, 0); // byte 값을 long 형식으로 변환

            Console.WriteLine("{0,-13} : 0x{1:X16}", "Read Data", readValue);
            inStream.Close(); // 파일 스트림 닫기
        }
    }
}

</div>
</details>

#### Position
Stream 클래스에는 Position이라는 프로퍼티가 있다. Position 프로퍼티는 현재 스트림의 읽는 위치 또는 쓰는 위치를 나타낸다. 여러개의 데이터를 기록 하는 것은 Write()나 WriteByte()메소드를 차례대로 호출하기만 하면 된다. 이 방식은 순차 접근(Sequential Access)이라고 한다.

파일 내의 임의의 위치에 Position이 위치하도록 하는 방법은 임의 접근(Random Access)방식이라고 한다. Seek() 메소드를 호출하거나 Position 프로퍼티에 직접 원하는 값을 대입하여 지정된 위치로 점프하여 읽기/쓰기를 위한 준비를 할 수 있다.
<details>
<summary>예시 코드</summary>
<div markdown="1">

```c#
using System;
using System.IO;

namespace SeqNrand
{
    class MainApp
    {
        static void Main(string[] args)
        {
            Stream outStream = new FileStream("a.dat", FileMode.Create);
            Console.WriteLine($"Position : {outStream.Position}");

            outStream.WriteByte(0x01);
            Console.WriteLine($"Position : {outStream.Position}");

            outStream.WriteByte(0x02);
            Console.WriteLine($"Position : {outStream.Position}");

            outStream.WriteByte(0x03);
            Console.WriteLine($"Position : {outStream.Position}");

            outStream.Seek(5, SeekOrigin.Current); // 임의 접근 방식, 현재 위치에서 5바이트 뒤로 이동
            Console.WriteLine($"Position : {outStream.Position}");

            outStream.WriteByte(0x04);
            Console.WriteLine($"Position : {outStream.Position}");

            outStream.Close();
        }
    }
}
```

</div>
</details>

### 이진 데이터 처리

BinaryWriter/BinaryReader 를 사용한다면, 앞에서 사용한 byte/byte 배열 형식으로 변환하지 않아도 쉽게 읽기/쓰기를 할 수 있다. 그러나 이 두 클래스는 파일 처리의 도우미 역할일 뿐, 이들 클래스를 사용하기 위해선 Stream으로부터 파생된 클래스의 인스턴스가 있어야 한다.

```c#
using System;
using System.IO;

namespace BinaryFile
{
    class MainApp
    {
        static void Main(string[] args)
        {
            BinaryWriter bw =
                new BinaryWriter(
                    new FileStream("a.dat", FileMode.Create));
            string max255 = "12345";
            for (int i = 0; i < 100; i++)
                max255 += "Good morinig!";
            bw.Write(int.MaxValue);
            bw.Write(max255);
            bw.Write(uint.MaxValue);
            bw.Write("안녕하세요!");
            bw.Write(double.MaxValue);

            bw.Close(); // 항상 CLose 해주자.

            BinaryReader br =
                new BinaryReader(new FileStream("a.dat", FileMode.Open));

            Console.WriteLine($"File size : {br.BaseStream.Length} bytes");
            Console.WriteLine($"{br.ReadInt32()}");
            Console.WriteLine($"{br.ReadString()}");
            Console.WriteLine($"{br.ReadInt32()}");
            Console.WriteLine($"{br.ReadString()}");
            Console.WriteLine($"{br.ReadDouble()}");

            br.Close(); // 항상 CLose 해주자.
        }
    }
}
```

### 텍스트 파일 처리

StreamWriter/StreamReader 클래스들은 텍스트파일 처리를 위해 만들어진 클래스들이다. 파일로 텍스트 데이터를 내보내거나 읽어들이는 역할을 한다.

```c#
using System;
using System.IO;

namespace BinaryFile
{
    class MainApp
    {
        static void Main(string[] args)
        {
            StreamWriter sw =
                new StreamWriter(
                    new FileStream("a.txt", FileMode.Create));

            sw.WriteLine(int.MaxValue);
            sw.WriteLine("Good morinig!");
            sw.WriteLine(uint.MaxValue);
            sw.WriteLine("안녕하세요!");
            sw.WriteLine(double.MaxValue);
            sw.WriteLine(long.MaxValue);

            sw.Close();

            StreamReader sr =
                new StreamReader(
                    new FileStream("a.txt", FileMode.Open));

            Console.WriteLine($"File size : {sr.BaseStream.Length} bytes");

            while(sr.EndOfStream == false)
            {
                Console.WriteLine(sr.ReadLine());
            }

            sr.Close();

        }
    }
}
```

### 객체 직렬화하기
위에서 사용한 StreamWriter/Reader , BinaryWriter/Reader 들은 기본 데이터 형식을 스트림에 쓰고 읽을 수 있지만, **프로그래머가 직접 정의한 클래스나 구조체 같은 복합 데이터 형식은 지원하지 않는다.** 이를 해결 하기 위해선 **직렬화(Serialization)** 를 해야 한다.

```c#
using System;
using System.IO;
using System.Runtime.Serialization.Formatters.Binary;

namespace Serialization
{
    [Serializable]
    class NameCard
    {
        public string Name;
        [NonSerialized]
        public string Phone; // Phone은 직렬화/역직렬화 하지 못하게 NonSerialized로 수식해줬다.
        public int Age;
    }
    class MainApp
    {
        static void Main(string[] args)
        {
            Stream ws = new FileStream("a.txt", FileMode.Create);
            BinaryFormatter serializer = new BinaryFormatter();

            NameCard nc = new NameCard();
            nc.Name = "이동석";
            nc.Phone = "010-123-4567";
            nc.Age = 27;

            serializer.Serialize(ws, nc);
            ws.Close();

            Stream rs = new FileStream("a.txt", FileMode.Open);
            BinaryFormatter deserializer = new BinaryFormatter();

            NameCard nc2;
            nc2 = (NameCard)deserializer.Deserialize(rs);
            rs.Close();

            Console.WriteLine($"Name: {nc2.Name}");
            Console.WriteLine($"Phone: {nc2.Phone}"); // 결과값에 Phone 숫자는 나오지 않을 것
            Console.WriteLine($"Age: {nc2.Age}");


        }
    }
}
```
