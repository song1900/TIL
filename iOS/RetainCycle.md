# Retain Cycle: Closure vs Delegate

## Retain Cycle 기본 개념
- ARC는 객체가 더 이상 참조되지 않을 때 메모리를 해제합니다.
- 하지만 서로가 서로를 강하게 참조(Strong) 하면, ARC가 두 객체를 해제하지 못하고 메모리 누수가 생깁니다. 
-> 이것이 Retain Cycle.

## Closure와 Retain Cycle
### 문제 발생 원리
- 클래스 내부에서 self를 캡처하는 Closure를 프로퍼티로 저장하면, </br> 클래스가 클로저를 강하게 잡고, 클로저가 다시 self를 강하게 잡는 구조가 됩니다.
```swift
class MyViewController: UIViewController {
    var onButtonTap: (() -> Void)?

    override func viewDidLoad() {
        super.viewDidLoad()
        onButtonTap = {
            print(self.title ?? "") // self를 캡처
        }
    }
}
```
- self가 해제되지 못하는 Retain Cycle 발생

### 해결 방법
- Capture List 사용 -> [weak self] 또는 [unowned self]
- 보통은 weak를 선호 (self가 먼저 해제될 수 있으므로 nil 체크 필요)
```swift
onButtonTap = { [weak self] in
    print(self?.title ?? "")
}
```

</br>

## Delegate와 Retain Cycle
### 문제 발생 원리
- Delegate 패턴에서, delegate 프로퍼티를 강한 참조로 선언하면 Retain Cycle이 생깁니다.
```swift
protocol SomeDelegate: AnyObject {
    func didSomething()
}

class A {
    var delegate: SomeDelegate? // ❌ strong 참조 (기본값)
}

class B: SomeDelegate {
    var a: A?
    func didSomething() {}
}
```
- B가 A를 강하게 잡고, A가 다시 B를 delegate로 강하게 잡으면 순환.

### 해결 방법
- Delegate는 항상 weak으로 선언해야 합니다.
```swift
class A {
    weak var delegate: SomeDelegate? // ✅ weak 참조
}
```

## Closure vs Delegate 차이점
- Retain Cycle 원인 
    - Closure: 클로저가 self를 캡처
    - Delegate: delegate 프로퍼티를 strong으로 선언
- 해결 방법
    - Closure: [weak self], [unowned self]
    - Delegate: delegate를 weak 선언
- 용도
    - Closure: 특정 이벤트 시 실행할 코드 블록 전달
    - Delegate: 객체 간 위임 패턴(1:1 관계 유지)
- 주의점
    - Closure: self가 nil 될 수 있어 옵셔널 처리 필요
    - Delegate: 프로토콜은 AnyObject로 제한 + weak
