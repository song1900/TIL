# Opaque Type vs Existential Type

## Opaque Type (some)
- 구체적인 타입을 숨기지만 **컴파일 시점**에 타입이 고정됨
- 도입시기: Swift 5.1
- 컴파일러가 구체 타입을 알기 때문에 최적화 가능
```swift
func makeView() -> some View
// 이 함수는 특정 View 타입을 리턴하지만, 어떤 타입인지는 숨긴다.
```

## Existential Type (any)
- 여러 타입을 허용하는 **런타임** 다형성 컨테이너
- 도입시기: Swif 5.6 (any 키워드 명시적 도입)
- 런타임 디스패치로 오버헤드가 존재
```swift
func makeView() -> any View
// 이 함수는 View 프로토콜을 따르는 어떤 타입이든 리턴할 수 있다.
```

## 예시 코드
### some (Opaque Type)
```swift
protocol Shape {
    func area() -> Double
}

struct Circle: Shape {
    let radius: Double
    func area() -> Double { .pi * radius * radius }
}

func makeShape() -> some Shape {
    Circle(radius: 10)
}

let shape = makeShape()
// ✅ 컴파일러는 내부적으로 Circle 타입임을 알고 있음
print(shape.area())  // OK
```

### any (Existential Type)
```swift
func makeRandomShape() -> any Shape {
    Bool.random() ? Circle(radius: 5) : Rectangle(width: 3, height: 4)
}

let randomShape = makeRandomShape()
// ⚠️ 실제 타입은 런타임에 결정됨
print(randomShape.area()) // OK
```

## SwiftUI에서의 활용
- body는 some View 로 선언되어 있음
- 이유:
    - SwiftUI는 View 트리 구조를 정적 타입으로 분석해야 "diffing" 및 "랜더링 최적화"가 가능하기 때문
    - some View를 쓰면 컴파일러가 정확한 View 타비을 추론하고, View Identity와 State 관리를 효율적으로 처리할 수 있음
    - 만약 any View였다면 모든 View가 동적 박스 형태(Existential Container)로 감싸져 성능이 떨어지고, SwiftUI의 type-safe 구조가 꺼짐

## 정리
- some
    - 컴파일 타임 고정, 제네릭에 가까움
    - SwiftUI의 핵심 (정적 View 트리 보장)
- any
    - 런타임 다형성, 타입 정보 제거
    - 필요 시 Type Erasure (AnyView) 로 사용
- SwiftUI
    - some View는 성능 + 타입 안정성
    - any View는 유연하지만 비용이 큼
