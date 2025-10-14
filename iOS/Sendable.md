# Sendable Protocol

## 개념
- Swift에서 값이 여러 스레드(Task) 에 안전하게 전달될 수 있음을 보장하는 프로토콜
- Swift 5.5에서 Concurrency과 함께 도입
- 동시성 환경에서 Data Race를 방지
- 대부분의 값 타입은 자동으로 Sendable을 채택함 (Struct, Enum)


## 왜 필요할까?
```swift
Task {
    await someActor.update(data)
}
```
data가 여러 Task 간에 안전하게 공유될 수 있는지 Swift가 컴파일 시점에 검사해야 한다.
그 기준이 바로 **Sendable 준수 여부**

만약 data가 안전하지 않다면 Swift는 컴파일러 경고 또는 에러가 발생한다.
(ex: Class 인스턴스가 여러 스레드에서 동시에 수정된다면)


## 어떤 타입이 Sendable인가
- Int, String, Bool, Double 등 값 타입
- Array, Dictionary, Set 등도 내부 요소가 Sendable이면 Sendable
```swift
struct User: Sendable {
    let name: String
    let age: Int
}
```
User의 모든 프로퍼티가 Sendable이므로 자동으로 Sendable로 추론된다.

👉 Class는 기본적으로 Sendable이 아님
- class는 참조 타입 이라서 여러 스레드가 동시에 접근하면 안전하지 않을 수 있다.
```swift
final class MyClass {
    var name: String = ""
}
```
이 타입을 actor나 Task간에 넘기면 경고가 발생한다.


## Class에서 Sendable
1. @unchecked Sendable
    ➡️ Swift에게 "내가 책임질게, 이건 안전해"라고 명시적으로 약속하는 방법
    ```swift
    final class TreadSafeCounter: @unchecked Sendable {
        private var valeu = 0
        private let queue = DispatchQueue(label: "counter.queue")

        func increment() {
            queue.sync {
                value += 1
            }
        }
    }
    ```
    - @unchecked를 붙이면 컴파일러가 안정성을 검증하지 않는다.
    - 대신 개발자가 직접 스레드 안정성을 보장해야 한다. (위험)

2. 불변 클래스(Immutable Class)로 만들기
    - 모든 프로퍼티를 let으로 선언하고 수정 불가하게 만들어 안전하게
    ```swift
    final class Config: Sendable {
        let name: String
        let version: Int

        init(name: String, version: Int) {
            self.name = name
            self.version = version
        }
    }
    ```
    Swift 5.10 이후로는 이런 "불변 클래스"는 Sendable로 자동 추론된다.





## @Sendable 클로저
- 클로저도 스레드 간에 전달될 수 있으므로, Sendable 클로저를 지원한다.
```swift
func performAction(_ work: @Sendable @escaping () -> Void) {
    Task {
        await work()
    }
}
```
@Sendable이 붙은 클로저는 캡처한 값도 모두 Sendable이어야 한다.