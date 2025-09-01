# Protocol & POP

## Protocol(프로토콜)
Swift에서 **프로토콜은 일종의 "약속(Interface)"**입니다.<br>
구현이 아닌 무엇을 할 수 있어야 하는지에 대한 청사진만 정의합니다.

### 특징
- Class, Struct, Enum 모두 채택 가능
- 메서드, 프로퍼티, 이니셜라이저 요구사항을 정리할 수 있음
- 다중 채택 가능 (Swift는 다중 상속은 불가하지만 프로토콜은 다중 태책이 가능)
- optional 키워드는 @objc 프로토콜에서만 사용 가능 (Objective-C 호환 필요)
- AssociatedType을 이용해 제네릭처럼 추상화 가능
```swift
protocol Flyable {
    func fly()
}

struct Bird: Flyable {
    func fly() {
        print("새가 날아간다")
    }
}
```

<br>

## POP(Protocol Oriented Programming, 프로토콜 지향 프로그래밍)
Swift가 강조하는 프로그래밍 패러다임. <br>
객체 지향(OOP)의 단점을 보완하고, 프로토콜을 중심으로 추상화 + 기능 확장을 지향함.

### 핵심 아이디어
1. 값 타입 중심(Value Type)
    - Struct, Enum 활용 ➡️ Copy-on-Write로 메모리 안정성 + 성능 최적화
2. 프로토콜 확장(Protocol Extension)
    - 공통 기능을 프로토콜의 Extension에 기본 구현(Default Implementation)으로 제공
3. 조합 기반 설계(Composition)
    - 상속(Inheritance) 대신 여러 프로토콜을 조합해서 유연하게 기능 확장
4. Generic & AssociatedType 적극 활용
    - 코드 재사용성과 타입 안정성을 동시에 확보
```swift
protocol Runnable {
    func run()
}

extension Runnable {
    func run() {
        print("기본 달리기")
    }
}

struct Person: Runnable {}
struct Dog: Runnable {
    func run() {
        print("강아지가 달린다")
    }
}

let person = Person()
person.run() // 기본 달리기
let dog = Dog()
dog.run() // 강아지가 달린다
```

<br>

## Protocol vs Class 상속
| 구분              | 프로토콜(Protocol)                           | 클래스 상속(Class Inheritance)   |
|-------------------|----------------------------------------------|----------------------------------|
| 다중 적용 가능 여부 | ✅ 여러 개 채택 가능                           | ❌ 단일 상속만 가능               |
| 구현 유무          | ❌ 구현 없음 (청사진만)                        | ✅ 구현까지 포함됨                |
| 확장성             | ✅ Extension으로 기본 구현                   | 🔸 기본 구현 상속, 오버라이드 필요 |
| 적용 대상          | 클래스, 구조체, 열거형 모두 가능                | 클래스만 가능                     |
| 메모리 모델        | 값 타입(Value Type)에도 적용 가능               | 참조 타입(Reference Type)만 적용 |


<br><br><br>

👉 Q.
- Swift가 왜 POP를 지향할까?
    - 값 타입 중심, 다중 상속의 불가능함을 보완하고 유연한 코드 재사용이 가능하다는 장점 때문에
- Protocol Extension과 Default Implementation의 장점은?
    - 공통 코드를 중복 없이 제공하고 필요 시 개별 타입에서 커스터마이징이 가능하다.
- OOP와 POP의 차이점은?
    - OOP는 클래스와 상속 중심이고, POP는 프로토콜과 조합 중심이다.
