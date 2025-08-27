# ARC 동작원리

## ARC란?
- Swift에서 메모리 관리를 자동으로 수행하는 방식.
- 개발자가 malloc, free 같은 함수를 직접 호출하지 않아도, 객체(특히 클래스 인스턴스)의 **생명 주기**를 관리해줌
- 값 타입(struct, enum) 은 스택에 저장되며 ARC 대상이 아니고, 참조 타입(class) 만 ARC의 관리 대상임.

## 동작 원리
- Swift는 객체의 참조 카운트(reference count)를 기반으로 메모리를 관리함.
- 새로운 객체가 생성되면 참조 카운트 = 1
- 다른 변수나 상수가 그 객체를 가리킬 때마다 참조 카운트가 +1
- 해당 변수가 범위를 벗어나거나 nil을 할당하면 참조 카운트가 -1
- 참조 카운트가 0이 되는 순간, ARC가 자동으로 메모리를 해제함
```swift
class Person {
    let name: String
    init(name: String) {
        self.name = name
    }
    deinit {
        print("\(name) is being deinitialized")
    }
}

var p1: Person? = Person(name: "Manbo") // count = 1
var p2: p1                              // count = 2
p1 = nil                                // count = 1
p2 = nil                                // count = 0 -> 메모리 해제
```

## 강한 참조(Strong Reference)
- Swift에서 기본은 강한 참조
- 즉, 변수나 객체를 참조하면 그 객체의 참조 카운트가 올라감
- 순환 참조(Retain Cycle) 문제는 강한 참조만 존재할 때 발생.

## 약한 참조(Weak Reference)
- weak 키워드 사용
- 객체를 참조화되 참조 카운트를 증가시키지 않음
- 참조 대상이 해제되면 자동으로 nil이 됨 (Optional로 선언해야 함)
```swift
class Person {
    var name: String
    weak var friend: Person?
    init(name: String) {
        self.name = name
    }
}
```

## 미소유 참조(Unowned Reference)
- unowned 키워드 사용
- 약한 참조와 동일하게 참조 카운트를 증가시키지 않음
- 하지만 대상이 해제되더라도 자동으로 nil이 되지 않음
    ➡️ 해제된 메모리에 접근 시 런타임 에러 발생
- 보통 생명주기가 보장되는 경우 (ex. parent ↔️ child 관계)에서 사용.
```swift
class CreditCard {
    unowned let owner: Customer // 반드시 살아있음을 보장
    init(owner: Customer) {
        self.owner = owner
    }
}
```

## 순환 참조(Retain Cycle)
- 두 객체가 서로를 강하게 참조할 때 발생
- 참조 카운트가 0이 되지 않아 메모리가 해제되지 않는 문제
```swift
class Person {
    var apartment: Apartment?
}
class Apartment {
    var tenant: Person?
}
```
- 해결책: 
    - 한쪽을 weak 또는 unowned 로 선언

## 클로저와 ARC
- 클로저도 객체이므로 캡처 시 순환 참조가 발생할 수 있음
- self를 캡처할 때 weak 또는 unowned 를 사용해 해결
```swift
class ViewController {
    var name = "Manbo"
    lazy var printName: () -> Void = { [weak self] in
        print(self?.name ?? "null")
    }
}
```

## ARC vs GC 차이
- GC(Garbage Collector)
    - 런타임에 주기적으로 메모리를 탐색
    - 해제 시점이 비결정적
    - 메모리 누수는 줄일 수 있지만, 중간에 GC pause(Stop-the-world) 가 발생할 수 있음
    - Java, Kotlin(Android) 등에서 사용
- 👉 iOS는 왜 GC가 아니라 ARC를 선택?
    - iOS는 UI/UX 실시간 반응성이 중요 -> GC pause 허용 불가 -> ARC 선택
    - 대신 ARC는 순환 참조 문제를 직접 관리해야 함.