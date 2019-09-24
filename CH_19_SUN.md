# Chapter 19. 스레드와 태스크

# 19.1 프로세스와 스레드
### 기본 개념
- <b>프로세스</b> : 실행 파일이 실행되어 메모리에 적재된 인스턴스, 하나 이상의 스레드(Thread)로 구성됨.
- <b>스레드</b> : 운영체제가 CPU시간을 할당하는 기본 단위
<br>

### 멀티 스레드 구조 프로그램 장점
1. 사용자 대화형 프로그램에서 어떤 작업을 실행하는 동시에 사용자로부터 명령을 입력받도록 함. <b>즉, 응답성을 높일 수 있음</b>
2. <b>자원 공유가 쉬움.</b> 코드 내 변수를 같이 사용하며 데이터 교환 가능. (vs 멀티 프로세스 : 소켓, IPC(Inter Process Communication) 필요)
3. <b>"경제성", 이미</b> 프로세스에 할당된 메모리와 자원을 사용하므로 메모리와 자원을 할당하는 비용을 지불하지 않아도 됨.
<br>
<img src = "./OOP/pic/picture1.png" width = 540 height = 380>
참조 : 멀티 프로세스 / 멀티 스레드 구조 프로그램 자원 공유


### 멀티 스레드 구조 프로그램 단점
1. <b>구현이 까다로움, 디버깅도 쉽지 않음</b>
2. 자식 스레드 중 하나에 문제가 생기면 <b>전체</b> 프로세스가 영향을 받음
3. 스레드를 너무 많이 사용하면 오히려 성능이 저하됨. 실제 일을 하는 시간에 비해 <b>작업간 전환 Context Switching</b>으로 인한 시간이 커져 성능이 저하.
<br>
<img src = "./OOP/pic/picture2.png" width = 540 height = 380>
참조 : 멀티 프로세스 / 멀티 스레드 구조 자식 문제


### 정리
- <b>멀티 프로세스</b> ~ 소프트웨어의 안정성
- <b>멀티 스레드</b> ~ 초고성능의 사양

<br><br>
## 19.1.1 스레드 시작하기
- System.Threading.Thread 클래스 사용
```C#
static void DoSomething() // 스레드가 실행할 메소드
{
    for (int i = 0; i < 5; i++)
    {
        Console.WriteLine("DoSomething : {0}", i);
    }
}

static void Main(string[] args)
{
    Thread t1 = new Thread(new ThreadStart(DoSomething)); 1. Thread의 인스턴스 생성
    
    t1.Start(); // 2. 스레드 시작
    
    t1.Join(); // 3. 스레드의 종료 대기
}
```
<img src = "./OOP/pic/picture3.png" width = 600 height = 250>

- t1.Start()를 호출하는 시점 : CLR이 실제 스레드를 생성, 즉 메모리에 적재되는 시점

<summary> 예제 : BasicThread </summary>
<div>

```C#
using System;
using System.Threading;

namespace BasicThread
{
    class MainApp
    {
        static void DoSomething()
        {
            for (int i = 0; i < 5; i++)
            {
                Console.WriteLine($"DoSomething : {i}");
                Thread.Sleep(10);
            }
        }

        static void Main(string[] args)
        {            
            Thread t1 = new Thread(new ThreadStart(DoSomething));

            Console.WriteLine("Starting thread...");
            t1.Start();

            for (int i = 0; i < 5; i++)
            {
                Console.WriteLine($"Main : {i}");
                Thread.Sleep(10); // ★ 다른 스레드도 CPU를 사용할 수 있도록 CPU 점유를 내려놓음. 밀리초 단위★
            }
            
            Console.WriteLine("Wating until thread stops...");
            t1.Join();

            Console.WriteLine("Finished");
        }
    }
}

```
결과
```cmd
Starting thread...
Main : 0
DoSomething : 0
DoSomething : 1
Main : 1
Main : 2
DoSomething : 2
DoSomething : 3
Main : 3
Main : 4
DoSomething : 4
Wating until thread stops...
Finished
```

</div></details>
