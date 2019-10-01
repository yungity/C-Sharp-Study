# 1강 C#언어의 개요

## 1. C언어의 개요

C언어(함수 단위) -> C++(클래스 개념 도입)-> C#(클래스 라이브러리, CLR-언어를 실행하는 모듈)

__C# = C++언어 기반(C언어가 모태) + Java의 장점 결합.__

c#의 장점 : 운영체제를 잘 운용할 수 있다,

### C#언어 사용범위
: XML, Web, Network, 데이터베이스, 게임, IoT(4차 산업혁명)-로봇 등…

### C#은 포인터를 사용하지 않는다.
: C/C++의 경우 하드웨어를 다루기 위해(CPU 등) 메모리 안에 데이터를 저장/불러오기를 포인터를 통해 했다. 하지만 new로 메모리를 할당한 후 delete나 free()로 프로그래머가 직접 메모리를 해제시켜야 메모리 누수가 발생하지 않았다.
C#의 경우, 포인터는 더 이상 쓰지 않고 unsafe 코드로 지정했다. 또 메모리 관리를 가비지 컬렉터가 대신해줘 자동으로 메모리를 해제해준다.

## 2. .NET 프레임워크와 운영체제
: 프로그램들이 운영체제에서 .NET 프레임워크의 도움을 받아 실행됨.

window 7 프레임워크를 넣다 뺐다 했는데 window 10부터 운영체제 안으로 프레임워크가 들어가서 자리잡았다.

## 2.1 NET 프레임 워크의 구성

### 클래스 라이브러리란
- BCL(Basic class library) : 기본 클래스 라이브러리
- WindowsForm : 윈도우 응용 프로그램 제작을 위한 클래스라이브러리
- ASP.NET : 웹 클래스라이브러리
- ADO.NET : 데이터베이스 클래스라이브러리


### CLR(common language runtime)
- 역할 : 컴파일된 C#코드를 visual studio를 통해 중간언어인 IL(intermediate language) 코드로 compile시켜 .exe나 .dll 확장자를 가진 파일이 만든다. 이를 CLR이 모든 운영체제에 맞게 새롭게 기계어 코드로 재컴파일시켜 실행할 수 있다.
- MSIL/IL (intermediate language) : 중간언어 – visual studio에서 C#코드를 중간언어로 컴파일해 .exe나 .dll의 확장자를 가진 어셈블리를 만든다(실행파일이라고 안함).
: 중간언어로 작성된 것은 (visual basic, C# 등) 공통언어로 실행이 가능하게 됨.

## 3. CTS와 CLS

### CTS(common Type system)
 : 공통데이터형, 객체로 메소드나 멤버변수를 갖는다.
(ex. int 데이터형은 CTS에서는 System.Int32의 객체)

### CLS(Common Language Specification)
: .NET언어가 지켜야할 최소 코드 규칙, 공통 언어 사항들 정의

