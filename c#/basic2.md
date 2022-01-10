# 기초 문법-2

* [변수](#변수)
* [상수](#상수)
* [제어문](#제어문)
* [연산자](#연산자)
* [오버로딩과 오버라이딩](#오버로딩과-오버라이딩)
* [대리자 (Delegate)](#대리자-delegate)
* [이벤트](#이벤트)

<br>

# 변수

## 👉 정의
* 값을 다루기 위해 메모리에 임시로 저장한 공간
* 중간에 값 변경 가능

## 👉 특징
* 변수의 첫 글자는 문자
* 대/소문자 구별
* 유니코드 지원: 영어, 한자, 한글 등 가능

## 👉 리터럴
직접 정수, 문자열 등의 값을 저장할 수 있는데, `값 그대로를 의미`

```c#
bool b = true;
int num = 100;
```

## 👉 초기화
* 변수의 메모리에 저장될 값을 지정
* 기본 값으로 초기화할 때에는 `default` 키워드 사용

```c#
int v1 = default; // 0
bool v2 = default // false
string v3 = default // null
Console.WriteLine(v1); // 0
```

<br>

# 상수

## 👉 정의
* 값이 컴파일 시간에 설정되며 변경할 수 없는 필드
* 중간에 값 변경 불가능
* "const" 키워드로 생성

## 👉 선언 방법
* static 클래스에 `const` 키워드를 이용해 선언 (권장 사항)
* 클래스 내에서 필드로 선언

```c#
static class Constants {
    public const int val = 5;
}

class Program {
    const int v1 = 3;
    
    static void Main() {
        Console.WriteLine(Constants.val);
    }
}
```

## 👉 const와 readonly
||const|readonly|
|:--:|:--:|:--:|
|공통점|값이 정해지면 수정 불가|값이 정해지면 수정 불가|
|차이점|자동으로 컴파일러에 의해 static이 됨|생성자에서 값을 수정할 수 있음<br>지역 변수로는 사용 불가|

```c#
class TEST {
    public const string constVal = "string value";
    public readonly string readOnlyVal = "string value";
    
    public TEST() {
        readOnlyVal = "abc";
    }
    public TEST(string s) {
        readOnlyVal = s;
    }
}
```

<br>

# 제어문

## 👉 조건문
```c#
if (/*조건*/) { }
else if () { }
else { }
```

```c#
switch (/*조건*/) {
    case 1:
        // 내용
        break;
    case 2:
        // 내용
        break;
    default:
        // 내용
        break;
}
```

<br>

## 👉 반복문

```c#
for (; ;) {}
```

```c#
foreach (/*원소*/ in /*배열, 컬렉션 등*/) {}

int[] arr = { 1, 2, 3 };
foreach (int i in arr) {
    Console.WriteLine(i); // 1 // 2 // 3
}
```

```c#
while (/*조건*/) { }

do { }
while (/*조건*/);
```

<br>

# 연산자

## 👉 산술 연산자
* +, -, *, /, %
* 전체 오버로딩 가능

## 👉 할당 연산자
* =, +=, -=, *=, /=, %=
* 전체 오버로딩 불가능

## 👉 증감 연산자 
* ++, --

```c#
int a = 1;
++a; // 2
a = 1;
a++; // 1 
a = 1;
--a; // 0 
a = 1;
a--; // 1
```

## 👉 논리 연산자 
* &&, ||, !
* 오버로딩 가능: !

## 👉 관계/비교 
* <, >, ==, !=, >=, <=
* 전체 오버로딩 가능
  * 쌍으로 오버로드 해야 함
  * == 와 !=, < 와 >, <= 와 >=

## 👉 비트 연산자 
* &, |, ^
* 전체 오버로딩 가능

```c#
& // AND
| // OR
^ // XOR
```

## 👉 Shift 
* >>, <<
* 전체 오버로딩 가능

## 👉 조건 
* ?, ?? (C# 3.0 이상)
* 전체 오버로딩 불가능

```c#
int val = (/* 조건 */) ? a : b;
// 조건이 true이면 a, false이면 b

Object o1 = obj ?? (Object)o2
// obj가 null이면 o2 , 아니면 obj
```

<br>

# 오버로딩과 오버라이딩

## 👉오버로딩
* 메소드의 이름은 같지만 매개변수의 개수, 타입, 리턴 타입이 다른 메소드를 여러 개 정의

```c#
class Program {
    public int Plus(int x, int y) {
        return x + y;
    }
    
    public float Plus(float x, float y) {
        return x + y;
    }
}
```

## 👉 오버라이딩
* 자식 클래스가 부모 클래스를 상속받아 메소드를 재정의
* 부모 클래스의 메소드에는 `virtual` 키워드 필요
* 자식 클래스의 메소드에는 `override` 키워드 필요

```c#
class ParentClass {
    virtual public void Overriding() {
        Console.WriteLine("Parent Class");
    }
    public void NonOverriding() {
        Console.WriteLine("Parent Class");
    }
}

class ChildClass : ParentClass {
    public override void Overriding() {
        Console.WriteLine("Child Class");
    }
    public new void NonOverriding() {
        Console.WriteLine("Child Class");
    }
}
```

##### ✅ virtual
* 메소드, 속성, 인덱서, 이벤트 선언에 사용
* 자식 클래스에서 재정의 할 수 있고 하지 않아도 됨


## 👉 연산자 오버로딩
* 메소드에 public, static, operator 키워드 필요

```c#
//
public static ClassName operator [op](ClassName c) {}
//
public static ClassName operator [op](ClassName c1, ClassName c2) {}
//
public static bool operator [op](ClassName c1, ClassName c2) {}
```

```c#
class OverloadClass {
    private int n;

    public OverloadClass(int num) {
        this.n = num;
    }

    // 5 -> -5
    public static OverloadClass operator -(OverloadClass o) {
        return new OverloadClass(-o.n);
    }
    
    public static OverloadClass operator ++(OverloadClass o) {
        return new OverloadClass(++o.n);
    }
    
    public static OverloadClass operator +(OverloadClass o1, OverloadClass o2) {
        return new OverloadClass(o1.n + o2.n);
    }
    
    public static bool operator >=(OverloadClass o1, OverloadClass o2) {
        return (o1.n >= o2.n);
    }

    public static bool operator <=(OverloadClass o1, OverloadClass o2) {
        return (o1.n <= o2.n);
    }
    
    public override string ToString() {
        return $"{n}";
    }
}

public class OperatorOverloading {
    public void OperFunc() {
        OverloadClass o1 = new OverloadClass(5);
        OverloadClass o2 = new OverloadClass(4);
        Console.WriteLine(-o1); // -5
        Console.WriteLine(++o1); // 6
        Console.WriteLine(o1 + o2); // 10
        Console.WriteLine(o1 >= o2); // True
        Console.WriteLine(o1 <= o2); // False
    }
}
```

<br>

# 대리자 (Delegate)

* 메소드의 참조를 포함하는 영역
* 메소드를 매개 변수로 전달할 수 있는 것
* 델리게이트와 메소드는 원형이 일치해야 함 → 입력 파라미터 타입, 개수, 리턴 타입이 동일
* `delegate` 키워드 사용
* ✅ C# 컴파일러는 Delegate의 정의를 읽어 System.MulticastDelegate 클래스에서 생성된 Delegate를 생성 → Delegate는 메소드 메타정보를 보유한
Wrapper 클래스

## 👉 예시1: 메소드를 매개변수로 전달

```c#
class DelegateClass {
    delegate void ODelegate(int a, int b);

    static void Multiplication(int a, int b) {
        Console.WriteLine("{0} * {1} = {2}", a, b, a * b);
    }

    static void TestFunc(ODelegate d) {
        // 2가지 방법으로 호출 가능
        d.Invoke(7, 6); // 7 * 6 = 42
        d(2, 3); // 2 * 3 = 6
    }

    public void DeleFunc() {
        TestFunc(Multiplication);
    }
}
```

## 👉 객체화

```c#
class DelegateClass {
    delegate int PDelegate(int a, int b);

    static int Plus(int a, int b) {
        return a + b;
    }

    public void DeleFunc() {
        // // Plus
        PDelegate pd1 = Plus;

        //PDelegate pd1 = new PDelegate(Plus); // Plus
        Console.WriteLine(pd1(5, 10)); // 15
        // //
        PDelegate pd2 = delegate (int a, int b) {
            return a / b;
        };

        Console.WriteLine(pd2(10, 5)); //2
    }
}
```

## 👉 Delegate Chain

```c#
class DelegateClass {
    delegate void ODelegate(int a, int b);

    static void Minus(int a, int b) {
        Console.WriteLine("{0} - {1} = {2}", a, b, a - b);
    }
    
    static void Multiplication(int a, int b) {
        Console.WriteLine("{0} * {1} = {2}", a, b, a * b);
    }

    public void DeleFunc() {
        // 20 - 10 = 10
        // 20 * 10 = 200
    
        ODelegate pd3 = (ODelegate)Delegate.Combine(new ODelegate(Minus), new ODelegate(Multiplication));
        pd3(20, 10);
    }
}
```

<br>

# 이벤트

* 클래스에서 특정한 이벤트가 발생했음을 외부의 이벤트 가입자(Subscriber)에게 알리는 기능
* `event` 키워드 사용
* 특수 형태의 Delegate라 할 수 있음

## 👉 대리자 사용

```c#
namespace Basic {
    public delegate void MyEventHandler(string message);
    
    class Publisher {
        public event MyEventHandler Active;
        
        public void DoActive(int number) {
            if (number % 2 == 0)
                Active("Active!" + number);
            else
                Console.WriteLine(number);
        }
    }

    class Subscriber {
        static public void MyHandler(string message) {
            Console.WriteLine(message);
        }

        public void SubFunc() {
            Publisher publisher1 = new Publisher();
            //
            publisher1.Active += new MyEventHandler(MyHandler);
            // += // -=
            //
            for (int i = 1; i < 10; i++) {
                publisher1.DoActive(i);
            }
        }
    }
}
```

## 👉 EventHandler 사용

```c#
namespace Basic {
    public delegate void MyEventHandler(string message);

    class Publisher {
        public event EventHandler EventHandle;

        public void DoEventHandle(int number) {
            if (number % 2 == 0)
                EventHandle(this, EventArgs.Empty);
        }
    }

    class Subscriber {
        public void MyEventHandle(object sender, EventArgs e) {
            Console.WriteLine("Do EventHandle");
        }

        public void SubFunc() {
            Publisher publisher2 = new Publisher();

            publisher2.EventHandle += new EventHandler(MyEventHandle);
            for (int i = 1; i < 10; i++) {
                publisher2.DoEventHandle(i);
            }
        }
    }
}
```

<br>

# 예외 처리

## 👉 try, catch, finally

* try, catch, finally 문 사용
* try에서 예외가 발생하면 catch 실행
* finally는 무조건 실행되어야 하는 내용 작성

```c#
try {
    // 내용
}
catch (ArgumentException ex) { 
    // ArgumentException 발생 시 실행
}
catch (Exception ex) {
    // 내용
}
finally {
    // 내용
}
```

##### ✅ finally

* 할당된 외부 자원의 해제는 반드시 진행해야 하기 때문에 finally 사용
* 소켓, 파일, DB Connection, 정리되지 않은 리소스


## 👉 throw
* 새로운 Exception을 생성하여 throw: `Stack Trace 정보 유지` → 예외가 발생한 라인까지 알 수 있음 (같은/다른 메소드 모두에서 가능)
* 기존 Exception을 throw: `Stack Trace 정보를 리셋` → 사용하지 않는 것이 좋음
* 발생된 Exception을 그대로 호출자에 전달: 다른 메소드에서 같은 에러가 발생하면 발생 위치를 알 수 있지만, 같은 메소드에서 발생할 경우 어느 라인에서
발생했는지 알 수 없음

```c#
try {
    // 내용
}
catch(IndexOutOfRangeException ex) { // Exception throw
    throw new MyException("Invalid index", ex); // ex ,
}
catch(FileNotFoundException ex) { // Exception throw
    throw ex; //Stack Trace throw
}
catch(Exception ex) { // Exception
    throw;
}
```
