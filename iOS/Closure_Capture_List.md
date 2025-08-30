# Swift Closure Capture List 정리

## 개념
- Closure Capture List는 클로저가 외부의 변수를 어떻게 capture 할지 명시적으로 제어하는 문법.
- 기본적으로 클로저는 **외부 변수(상수 포함)**를 참조(reference) 또는 복사(copy)하여 캡처함.
- 이 과정에서 **강한 참조(Strong Reference)**로 캡처할 경우 강한 참조 순환(Retain Cycle) 문제가 발생할 수 있음.
- 따라서 [weak self], [unowned self] 같은 capture list를 사용하여 메모리 관리를 안전하게 할 수 있음.

## 문법
```swift
{ [capture list] (매개변수) -> 반환타입 in

}

// 예시
myFunction { [weak self, unowned delegate] (value: Int) -> Void in
    self?.doSomething()
    delegate.doWork()
}
```

## 기본 동작
- 기본 캡처: 클로저는 외부 변수를 참조 타입은 참조, 값 타입은 값 복사로 캡처한다.
```swift
var number = 10
let closure = { print(number) } // number 캡처됨
number = 20
closure() // 20 출력 (참조가 아닌 "값"을 캡처하는 시점이 있음 -> Swift 최적화에 따라 값이 아닌 참조로 동작할 수 있음)
```

## Capture List 주요 키워드
### strong (기본값)
- 아무것도 안 쓰면 strong capture
- 외부 인스턴스를 강하게 잡아 메모리 해제를 막을 수 있음 ➡️ 순환 참조 위험

### weak
- 약한 참조로 캡처
- 인스턴스가 해제되면 자동으로 nil이 됨 ➡️ Optional 타입 필요

### unowned
- 약한 참조지만 Optional 아님
- 해제된 후 접근 시 런타임 에러 발생
- self가 반드시 살아있다고 확신할 때 사용


## 사용 예
### (1) 순환 참조 발생 예시
```swift
class ViewController {
    var name = "Manbo"
    var closure: (() -> Void)?

    func setup() {
        closure = {
            print(self.name) // self를 strong으로 캡처
        }
    }
}
```
➡️ closure가 self를 잡고, self가 closure를 프로퍼티로 갖기 때문에 강한 순환 참조 발생.

### (2) [weak self]로 해결
```swift
func setup() {
    closure = { [weak self] in
        print(self?.name ?? "null")
    }
}
```
➡️ self는 해제될 수 있음. 해제 후에는 nil.

### (3) [unowned self]로 해결
```swift
func setup() {
    closure = { [unowned self] in
        print(self.name)
    }
}
```
➡️ self가 해제되면 crash. 하지만 ViewController가 클로저보다 오래 살아있을 경우 안전.

