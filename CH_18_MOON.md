# Chap 18. 파일 다루기

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

***

#### Touch Example 
- 매개 변수로 입력받은 경로에 새 디렉토리나 파일을 생성
- 이미 존재한다면, 최종 수정 시간 업데이트

```C#
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
            if (args.Length == 0)
            {
                Console.WriteLine(
                    "Usage : Touch.exe <Path> [Type:File/Directory]");
                return;
            }

            string path = args[0];
            string type = "File";
            if (args.Length > 1)
                type = args[1];

            if (File.Exists(path) || Directory.Exists(path))        // 이미 존재할 때
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
            else        // 아닐 때
            {
                if (type == "File")
                    File.Create(path).Close();
                else if (type == "Directory")
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

***

## 파일을 읽고 쓰기 위해 알아야 할 내용


#### 스트림 (Stream)? 
- 데이터가 흐르는 통로  
- 메모리에서 하드디스크로 데이터를 옮길 때, 먼저 스트림을 만들어 둘 사이를 연결한 뒤에 메모리의 데이터를 바이트 단위로 하드디스크로 이동

#### 순차 접근 (Sequential Access)
- 스트림은 데이터의 '흐름'이기 때문에, 스트림을 이용하여 파일을 다룰 때는 처음부터 끝까지 순서대로 읽고 쓰는 것이 보통
- 네트워크, 데이터 백업 장치의 데이터 입/출력 구조와 동일

#### 임의 접근 (Random Access)
- 하드디스크에서 사용하는 방식
- Arm과 Head를 움직여 디스크의 어떤 위치에 기록된 데이터라도 즉시 찾아갈 수 있음


***

#### C#의 Stream 클래스
- 그 자체로 입/출력 스트림 역할을 모두할 수 있음
- 순차/임의 접근 방식 둘 다 가능
- 단, Stream 클래스는 추상 클래스이기 때문에 인스턴스를 만들 수 없고, 상속 받은 파생 클래스에서만 인스턴스를 만들 수 있음
- 파생 클래스 -> FileStream, NetworkStream, GZipStream, ButteredStream ...

#### FileStream 클래스의 인스턴스를 생성하는 방법
```C#
Stream stream1 = new FileStream("a.dat", FileMode.Create);                // 새 파일 생성
Stream stream2 = new FileStream("b.dat", FileMode.Open);                  // 파일 열기
Stream stream3 = new FileStream("c.dat", FileMode.OpenOrCreate);          // 파일을 열거나, 파일이 없으면 생성
Stream stream4 = new FileStream("d.dat", FileMode.Truncate);              // 파일을 비워서 열기
Stream stream5 = new FileStream("e.dat", FileMode.Append);                // 덧붙이기 모드로 열기
```

***

### FileStream 클래스 (For Write)
- 파일에 데이터를 기록하기 위해 Stream 클래스로부터 물려받은 다음 <b>두 가지 메소드를 오버라이딩</b> 하고 있음
```C#
public override void Write(
    byte[] array,   // 쓸 데이터가 담긴 byte 배열
    int offset,     // byte 배열 내의 시작 오프셋
    int count       // 기록할 데이터의 총 길이 (단위는 바이트)
);

public override void WriteByte( byte value );
```
- byte 혹은 byte 배열만 입력이 가능
- 이를 위해 각종 데이터 형식을 byte 배열로 바꿔주는 <b>BitConverter 클래스를 제공</b>


#### long 형식의 데이터를 파일에 기록하는 예제
```C#
long someValue = 0x123456789ABCDEF0;

// 파일 스트림 생성
Stream outStream = new FileStream("a.dat", FileMode.Create);

// long -> byte 배열
byte[] wBytes = BitConverter.GetBytes(someValue);

// 변환한 byte 배열을 파일 스트림을 통해 파일에 기록
outStream.Write(wBytes, 0, wBytes.Length);

// 파일 스트림 닫기
outStream.Close();
```

***

### FileStream 클래스 (For Read)
- Write와 같이 Read에서도 두 가지 메소드를 Override

```C#
public override int Read(
    byte[] array,   // 읽을 데이터가 담긴 byte 배열
    int offset,     // byte 배열 내의 시작 오프셋
    int count       // 읽을 데이터의 총 길이 (단위는 바이트)
);

public override int ReadByte();
```

#### File에서 데이터를 long 형식으로 읽는 예제 
```C#
byte[] rBytes = new byte[8];

// 파일 스트림 생성
Stream inStream = new FileStream("a.dat", FileMode.Open);

// rBytes의 길이만큼(8바이트) 데이터를 읽어 rBytes에 저장
inStream.Read(rBytes, 0, rBytes.Length);

// BitConverter를 이용하여 rBytes에 담겨있는 값을 long 형식으로 변환
long readValue = BitConverter.ToInt64(rbytes, 0);

// 파일 스트림 닫기
inStream.Close();
```

***

#### Basic IO Example : Write & Read 

```C#
using System;
using System.IO;

namespace BasicIO
{
    class MainApp
    {
        static void Main(string[] args)
        {
            // Write
            long someValue = 0x123456789ABCDEF0;
            Console.WriteLine("{0,-1} : 0x{1:X16}", "Original Data", someValue);

            Stream outStream = new FileStream("a.dat", FileMode.Create);
            byte[] wBytes = BitConverter.GetBytes(someValue);

            Console.Write("{0,-13} : ", "Byte array");

            foreach (byte b in wBytes)
                Console.Write("{0:X2} ", b);
            Console.WriteLine();

            outStream.Write(wBytes, 0, wBytes.Length);
            outStream.Close();
            
            
            // Read
            Stream inStream = new FileStream("a.dat", FileMode.Open);
            byte[] rbytes = new byte[8];
            
            int i = 0;
            while (inStream.Position < inStream.Length)
                rbytes[i++] = (byte)inStream.ReadByte();
            
            long readValue = BitConverter.ToInt64(rbytes, 0);

            Console.WriteLine("{0,-13} : 0x{1:X16} ", "Read Data", readValue);
            inStream.Close();
        }
    }
}

```

***

## 여러 개의 파일을 Write & Read 하기

#### Position 프로퍼티 
- Stream 클래스에는 Position 이라는 프로퍼티가 있음
- Position 프로퍼티는 현재 스트림의 읽는 위치 또는 쓰는 위치를 나타냄
- FileStream 객체를 생성할 때 Position은 0이 되고 호출 때마다 1씩 증가하는 형태

#### Sequential Access
- 따라서 여러 개의 데이터를 기록하는 일은 Write()나 WriteByte()를 이용하여 순차적으로 호출하는 것만으로도 충분 -> 순차 접근

#### Random Access
- 파일 내의 임의의 위치에 Position이 위치하도록 할 수도 있음
- Seek() 메소드를 호출하거나, Position 프로퍼티에 직접 원하는 값을 넣으면 지정한 위치로 점프하여 읽기/쓰기가 가능


```C#
Stream outStream = new FileStream("a.dat", FileMode.Create);
// ...

OutStream.Seek(5, SeekOrigin.Current);      // 현재 위치에서 5바이트 뒤로 이동
OutStream.WriteByte(0x04);
```

#### Seq & Rand Example
```C#
using System;
using System.IO;

namespace SeqNRand
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

            outStream.Seek(5, SeekOrigin.Current);
            Console.WriteLine($"Position : {outStream.Position}");

            outStream.WriteByte(0x04);
            Console.WriteLine($"Position : {outStream.Position}");

            outStream.Close();
        }
    }
}

```


```
Position : 0
Position : 1
Position : 2
Position : 3
Position : 8
Position : 9
```


***

## 이진 데이터 처리 : BinaryWriter / BinaryReader
- BinaryWriter / BinaryReader ?
- 특정 데이터를 저장, 읽을 때 Byte 형식으로 변환해야 하는 불편함을 해소하기 위한 도우미 클래스
- 이 두 클래스는 파일 처리의 도우미 역할만 하기 때문에, 이들 클래스를 이용하려면 <b>Stream으로부터 파생된 클래스의 인스턴스가 있어야 함</b>


#### BinaryWriter
```C#
BinaryWriter bw = new BinaryWriter(new FileStream("a.dat", FileMode.Create));

// Write()는 C#이 제공하는 모든 기본 데이터 형식에 대해 오버로딩되어 있음
bw.Write(32);
bw.Write("Good Morning!");
bw.Write(3.14);

bw.Close()
```


#### BinaryReader
```C#
BinaryReader br = new BinaryReader(new FileStream("a.dat", FileMode.Open));

// ReadXXX() 메소드 
int a = br.ReadInt32();
string b = br.ReadString();
double c = br.ReadDouble();

br.Close();
```


#### Binary File Example : Write & Read
```C#
using System;
using System.IO;

namespace BinaryFile
{
    class MainApp
    {
        static void Main(string[] args)
        {
            // 쓰기
            BinaryWriter bw =
                new BinaryWriter(
                    new FileStream("a.dat", FileMode.Create));

            bw.Write(int.MaxValue);
            bw.Write("Good morning!");
            bw.Write(uint.MaxValue);
            bw.Write("안녕하세요!");
            bw.Write(double.MaxValue);

            bw.Close();     // 내부 스트림을 닫음


            // 읽기
            BinaryReader br =
                new BinaryReader(
                    new FileStream("a.dat", FileMode.Open));

            Console.WriteLine($"File size : {br.BaseStream.Length} bytes");
            Console.WriteLine($"{br.ReadInt32()}");
            Console.WriteLine($"{br.ReadString()}");
            Console.WriteLine($"{br.ReadUInt32()}");
            Console.WriteLine($"{br.ReadString()}");
            Console.WriteLine($"{br.ReadDouble()}");

            br.Close();     // 내부 스트림을 닫음
        }
    }
}

```

```
File size : 47 bytes
2147483647
Good morning!
4294967295
안녕하세요!
1.79769313486232E+308
```

***

## 텍스트 파일 처리 : StreamWriter / StreamReader
- 텍스트 파일은 구조는 간단하지만 활용도가 높은 파일 형식
- ASCII 인코딩에서는 <b>각 바이트가 문자 하나</b>를 나타내기 때문에, 바이트 오더의 문제도 벗어날 수 있음 (플랫폼 호환성이 높음)
- .NET 프레임워크에서는 텍스트 파일 처리를 위한 StreamWriter / StreamReader 를 제공
- 이 또한 Stream의 도우미 클래스


#### Stream Writer
```C#
StreamWriter sw = new StreamWriter(new FileStream("a.dat", FileMode.Create));

sw.Write(32);
sw.WriteLine("Good Morning!");
sw.WriteLine(3.14);

sw.Close();
```

#### Stream Reader
```C#
StreamReader sr = new StreamReader(new FileStream("a.dat", FileMode.Open));

while(sr.EndOfStream == false)  // EndOfStream 프로퍼티는 스트림의 끝에 도달했는지를 알려줌
{
    Console.WriteLine(sr.ReadLine());
}

sr.Close();

```

#### Text File Example
```C#
using System;
using System.IO;

namespace TextFile
{
    class MainApp
    {
        static void Main(string[] args)
        {
            // Write
            StreamWriter sw =
                new StreamWriter(
                    new FileStream("a.txt", FileMode.Create));

            sw.WriteLine(int.MaxValue);
            sw.WriteLine("Good morning!");
            sw.WriteLine(uint.MaxValue);
            sw.WriteLine("안녕하세요!");
            sw.WriteLine(double.MaxValue);

            sw.Close();     // Close Stream
            
            // Read
            StreamReader sr =
                new StreamReader(
                    new FileStream("a.txt", FileMode.Open));

            Console.WriteLine($"File size : {sr.BaseStream.Length} bytes");

            while (sr.EndOfStream == false)
            {
                Console.WriteLine(sr.ReadLine());
            }

            sr.Close();     // Close Stream
        }
    }
}

```

***

## 객체 직렬화 : Serialization / Deserialization
- 프로그래머가 직접 정의한 클래스나 구조체 같은 <b>복합 데이터 형식</b>을 Write & Read 하기 위한 방식
- 복합 데이터 형식을 다루러면, 그 형식이 갖고 있는 Field의 값을 저장할 순서를 정한 후, 이 <b>순서대로 저장하고 읽는</b> 코드를 작성해야 함
- C# 에서는 이러한 문제를 다루기 위해 <b>직렬화(Serialization)</b>를 제공

#### .NET Serialization은 크게 세 가지 방식을 지원
1. XML Serialization
2. SoapFormatter Serialization
3. Binary Serialization

#### 이 책에서 다루는 건 Binary Serialization

#### Serialization Example 
```C#
[Serializable]      // Seializable 선언
class MyClass
{
    //...
}

Stream ws = new FileStream("a.dat", FileMode.Create);
BinaryFormatter serializer = new BinaryFormatter();

MyClass obj = new MyClass();        // 객체 생성

serializer.Serialize(ws, obj);      // 직렬화!

ws.Close();     // 스트림 닫기

```


#### Deserialization Example 
```C#
Stream rs = new FileStream("a.dat", FileMode.Open);
BinaryFormatter deserializer = new BinaryFormatter();

MyClass obj = (MyClass)deserializer.Deserialize(rs);
rs.Close();
```

- 그냥 BinaryFormatter에게 맡기면 객체의 직렬화, 역직렬화를 알아서 해줌



#### 직렬화하고 싶지 않은 Field가 있을 때?
- 그 Field만 [NonSerialized] 애트리뷰트로 수식해주면 됨
- 이렇게 하면 이 Field의 상태는 직렬화할 때도 저장되지 않고, 역직렬화할 때도 역시 복원되지 않음

```C#
[Serializable]
class MyClass
{
    public int myField1;
    public int myField2;
    
    [NonSerialized]
    public int myField3;        // myField3 을 제외한 나머지 Field만 직렬화 수행
    
    public int MyField4;
}

```

#### 복합 데이터 형식을 직렬화할 때 주의할 점
- 직렬화하지 '않는' Field 뿐 아니라, 직렬화하지 <b>'못하는' Field 도 Nonserializable 애트리뷰트로 수식</b>해야 함!!

```C#
class NonserializableClass
{
    public int myField;
}

[Serializable]
class MyClass
{
    public int myField1;
    public int myField2;
    
    // NonserializableClass는 Serializable하지 않으므로, 직렬화를 수행할 때 오류가 발생!!
    public NonserializableClass myField3;       
    
    public int myField4;
}
```
#### 위 문제를 해결하기 위한 두 가지 해결책
1) NonserializableClass 클래스를 Serializable 애트리뷰트로 수식하는 방법
2) myField3을 Nonserializable 애트리뷰트로 수식하는 방법


#### 책 Example 
```C#
using System;
using System.IO;
using System.Runtime.Serialization.Formatters.Binary;

namespace Serialization
{
    [Serializable]
    class NameCard
    {
        public string Name;
        public string Phone;
        public int Age;
    }

    class MainApp
    {
        static void Main(string[] args)
        {
            Stream ws = new FileStream("a.dat", FileMode.Create);
            BinaryFormatter serializer = new BinaryFormatter();

            NameCard nc = new NameCard();
            nc.Name = "박상현";
            nc.Phone = "010-123-4567";
            nc.Age = 33;

            serializer.Serialize(ws, nc);
            ws.Close();

            Stream rs = new FileStream("a.dat", FileMode.Open);
            BinaryFormatter deserializer = new BinaryFormatter();

            NameCard nc2;
            nc2 = (NameCard)deserializer.Deserialize(rs);
            rs.Close();

            Console.WriteLine($"Name:  {nc2.Name}");
            Console.WriteLine($"Phone: {nc2.Phone}");
            Console.WriteLine($"Age:   {nc2.Age}");
        }
    }
}

```


***

## Collection의 직렬화
- 객체 직렬화 방식과 동일

#### Serializing Collection Example
```C#
using System;
using System.Collections.Generic;
using System.IO;
using System.Runtime.Serialization.Formatters.Binary;

namespace SerializingCollection
{
    [Serializable]
    class NameCard
    {
        public NameCard(string Name, string Phone, int Age)
        {
            this.Name = Name;
            this.Phone = Phone;
            this.Age = Age;
        }

        public string Name;
        public string Phone;
        public int Age;
    }

    class MainApp
    {
        static void Main(string[] args)
        {
            Stream ws = new FileStream("a.dat", FileMode.Create);
            BinaryFormatter serializer = new BinaryFormatter();
            
            List<NameCard> list = new List<NameCard>();
            list.Add(new NameCard("박상현", "010-123-4567", 33));
            list.Add(new NameCard("김연아", "010-323-1111", 22));
            list.Add(new NameCard("장미란", "010-555-5555", 26));

            serializer.Serialize(ws, list);
            ws.Close();

            Stream rs = new FileStream("a.dat", FileMode.Open);
            BinaryFormatter deserializer = new BinaryFormatter();

            List<NameCard> list2;
            list2 = (List<NameCard>)deserializer.Deserialize(rs);
            rs.Close();

            foreach (NameCard nc in list2)
            {
                Console.WriteLine(
                    $"Name: {nc.Name}, Phone: {nc.Phone}, Age: {nc.Age}");
            }
        }
    }
}

```







