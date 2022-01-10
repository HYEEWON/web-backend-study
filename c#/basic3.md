# 기초 문법-3

* [Attribute](#attribute)
* [#region](#region)
* [비동기: async/await](#비동기-asyncawait)
* [yield 키워드](#yield-키워드)

<br>

# Attribute

* 클래스에 메타 데이터를 추가할 수 있음
* 대상 요소: 클래스, 인터페이스, 생성자, 대리자, 열거형, 필드, 메소드, 이벤트, 매개 변수, 속성, 구조체 등
* 사용 방법: [Attribute-Name (매개 변수)]

## 👉 Obsolete 사용 예시
* Attribute에 있는 문자열은 경고로 출력
* 콘솔에는 `구버전` 출력

```c#
class MyClass {
    [Obsolete("2017 OldMethod . NewMethod .")]
    public void OldMethod() {
        Console.WriteLine("");
    }
}

class AttributeClass {
    public void AttributeFunc() {
        MyClass mine = new MyClass();
        mine.OldMethod();
    }
}
```

![image](https://user-images.githubusercontent.com/38900338/148763241-88f040a0-6eb6-4864-bd85-2c9f7b6de18e.png)

<br>

# #region
* `#region [이름]`으로 시작 ~ `#endregion`으로 닫음
* +/-로 코드를 펼치고 닫을 수 있음

<br>

# 비동기: async/await 
* C# 5.0부터 지원

## 👉 async
* 반환 형 앞에 붙어 메소드를 비동기 메소드로 만듦
* 비동기 메소드의 반환 형은 void, Task, Task<T> 중 한 가지
    * Task<T>: 반환되는 데이터가 있다면 반환 형을 T에 명시 → int형을 리턴하면 Task<int>
    * Task: 반환 데이터가 없을 때 사용
    * void: 비동기 메소드가 이벤트 핸들러로 사용될 때
* 비동기 메소드 이름을 Async로 마무리하는 것이 컨벤션
* 비동기 메소드의 매개 변수는 out, ref와 같은 참조 형식 불가

## 👉 await
* 비동기 메소드 안에서(만) 사용
* 비동기 메소드 내부의 비동기 작업이 모두 끝날 때까지 해당 메소드는 중단
* await는 중단점 → 비동기 작업이 끝나기 전에는 await 이후로 진행 불가, 제어는 비동기 메소드의 호출자에게 넘어감
* 비동기 메소드 안에는 동기/비동기 메소드 모두 가능

## 👉 Task 클래스
* 쓰레드 풀에서 쓰레드를 가져와 비동기 작업을 실행
* .NET 4.0에 도입

## 👉 비동기 지원
* Web access: HttpClient , SyndicationClient
* 파일 입출력: StorageFile, StreamWriter, StreamReader, XmlReader
* 이미지 처리: MediaCapture, BitmapEncoder, BitmapDecoder

## 👉 실행 흐름

![image](https://user-images.githubusercontent.com/38900338/148763519-edd0fa1b-d541-4533-a378-9cb3183a31c0.png)

* (1) 이벤트 StartButton_Click에 의해 비동기 메소드 AccessTheWebAsync() 호출
* (2) HttpClient 객체를 생성하고 비동기 메소드 GetStringAsync() 호출
* (3) GetStringAsync() 메소드에 주어진 주소로 요청 전달 및 응답 대기, 실행 제어를 호출자에게 넘김, 현재 작업 상태를 Task<string> 형식으로 반환, getStrignT
ask 변수는 진행 중인 비동기 작업에 대한 정보를 저장
* (4) 동기 메소드인 DoIndepentWork() 실행
* (5) await를 만나면 비동기 작업인 getStringTask가 완료될 때까지 AccessTheWebAsync()의 실행을 중단, 현재 쓰레드의 제어를 호출자에게 넘김 (호출자는 다
른 작업 진행), 현재 AccessTheWebAsync의 작업 상태를 나타내는 Task<int>를 생성해 호출자에게 전달
* (6) 비동기 작업 getStringTask의 작업이 완료되면 await는 getStringTask에서 결과 값을 가져와 변수 urlContents에 저장, 중단되었던 지점부터 작업을 재개
* (7) 최종 결과가 호출자에게 반환

##  👉 코드
* 순차적으로 실행되다가 await를 만나면 비동기 작업 시작
* `awaiting`(비동기)과 `main end`가 번갈아 가면서 출력
* `awaiting`(비동기)이 끝나면 `async end`와 `main end`가 번갈아 가면서 출력

```c#
class AsyncClass {
    async static private void MyMethodAsync() {
        Console.WriteLine("async start");
        
        await Task.Run(() => {
            for (var i = 0; i < 5000; i++) {
                Console.WriteLine(i+" awaiting");
            }
        });

        for (var i = 0; i < 5000; i++) {
            Console.WriteLine(i + "async end");
        }
    }

    public void Caller() {
        Console.WriteLine("main start");
        MyMethodAsync(); // 비동기 함수 호출
        for (var i = 0; i < 7000; i++) {
            Console.WriteLine(i + "main end");
        }
    }
}
```

<br>

# yield 키워드

* 호출자(Caller)에게 컬렉션 데이터를 하나씩 리턴할 때 사용
* 집합적인 데이터 셋에서 데이터를 하나씩 호출자에게 전달
* 무제한 데이터 리턴 등 데이터의 양이 커서 조금씩 리턴하는 것이 효과적일 때 사용

## 👉 종류
* yield return: 컬렉션 데이터를 하나씩 리턴하는데 사용, 함수가 다시 호출되면 실행되던 곳부터 다시 실행
* yield break: 리턴을 중지하고 iteration 루프를 빠져나올 때 사용

```c#
using System;
using System.Collections;

class YieldClass {
    // Enumerator
    static IEnumerable GetNumber() {
        yield return 1; 
        yield return "string"; 
        yield break;
    }

    public void YieldMain() {
        foreach (var num in GetNumber()) {
            Console.WriteLine(num);
            // 1 
            // string 
            // 두번 출력된 후, 함수 종료
        }
    }
}
```