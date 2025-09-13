# Combine Subject

## Subject란?
- Publisher와 Subscriber의 다리 역할을 하는 객체.
- Publisher처럼 값을 방출할 수 있고, Subscriber처럼 외부 입력을 받을 수 있음.
- 즉, 외부에서 값을 주입해서 스트림에 흘려보낼 수 있는 Publisher

</br>

## Subject 특징
- 일반 Publisher는 값을 자체적으로 생성해서 방출하지만, Subject는 외부에서 send(_:) 메서드를 통해 값을 전달할 수 있음
- UI 이벤트, Notification, Delegate 콜백 등을 Combine 스트림으로 변환할 때 주로 사용됨.
- 메모리 관리 주의: Subject는 구독자가 없어도 계속 살아있을 수 있음.

</br>

## 주요 Subject 종류
1. PassthroughSubject
    - 단순하게 값을 통과시킴
    - 구독자가 붙기 전의 값은 저장하지 않음.
    - 새로운 Subscriber는 과거 값을 받을 수 없음.
    ```swift
    let subject = PassthroughSubject<String, Never>()

    let sub1 = subject.sink {
        print("sub1: \($)")
    }
    subject.send("A") // sub1: A
    subject.send("B") // sub2: B

    let sub2 = subject.sink {
        print("sub2: \($0)")
    }
    subject.sned("C") // sub1: C, sub2: C
    ```
    ➡️ 특징: 과거 값 저장 X, 현재 이후만 전달

2. CurrentValueSubject
    - 현재 값을 저장하는 Subject.
    - 초기값을 반드시 지정해야 하며, 가장 최근 값을 유지.
    - 새로운 Subscriber가 붙으면 현재 값을 즉시 전달받음.
    ```swift
    let subject = CurrentValueSubject<Int, Never>()

    let sub1 = subject.sink {
        print("sub1: \($)")
    }
    subject.send(1) // sub1: 1
    subject.send(2) // sub2: 2

    let sub2 = subject.sink {
        print("sub2: \($0)")
    }
    // sub2: 2 (현재 값 즉시 전달)

    subject.sned(3) // sub1: 3, sub2: 3
    ```
    ➡️ 특징: 가장 최근 값 저장, 새로운 구독자에게 최신 값 즉시 전달

</br>

## Subject와 @Published
- @Published 프로퍼티 래퍼는 내부적으로 CurrentValueSubject를 사용.
- $property로 접근하면 Publisher(Subject)를 가져올 수 있음
```swift
class ViewModel {
    @Published var count = 0
}

let vm = ViewModel()
let sub = vm.$count.sink {
    print("Count: \($0)")
}
vm.count = 10 // Count: 10
```


</br>

## 언제 사용?
- PassthroughSubject
    - 과거 데이터 중요하지 않을 때
    - 이벤트 스트림 (버튼 클릭, 네트워크 응답 등)
- CurrentValueSubject
    - 상태(State) 관리 필요
    - ViewModel에서 최신 상태를 구독자에게 전달할 때


</br>
