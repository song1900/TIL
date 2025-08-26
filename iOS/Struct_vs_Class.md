# Struct vs Class 차이

## 값 타입(Value Type) vs 참조 타입(Reference Type)
- Struct
    - 값 타입(Value Type) -> 변수나 상수에 할당하거나 함수에 전달할 때 복사(copy)가 일어남.
    - 즉, 원본과 복사본은 완전히 독립적.
- Class
    - 참조 타입(Reference Type) -> 변수나 상수에 할당하거나 함수에 전달할 때 참조(reference) 가 전달됨.
    - 여러 변수가 같은 인스턴스를 참조할 수 있음.

## 상속 (Inheritance)
- Struct
    - 상속 불가능 (대신 Protocol 채택으로 기능 확장)
        - 👉 왜 상속이 없을까?
            - struct는 값 타입 이라 "공유된 상위 타입의 상태를 물려받아 확장"하는 상속 모델과 잘 안 맞는다.
            - 대신 Swift는 구성(composition)을 권장
                - "상속해서 늘리는"대신 "여러 프로토콜을 채택해서 조립"
            - POP의 장점 (상속 대비)
                - 값 의미론: 복사 시 독립. 스레드 안정성 ⬆️
                - 다중 조합: 필요 기능만 프로토콜로 조합 -> 구성(Composition).
                - 테스트/모듈화 용이: 레트로액티브 모델링, 조건부 적합으로 범용성 ⬆️
                - 경량/예측 가능: 복잡한 상속 트리, 다이아몬드 문제 회피.
            - 언제 상속이 더 맞나?
                - 참조 의미론이 필요한 경우 (공유 상태/아이덴티티, ===)
                - 상속으로 생명주기/행동을 공통화하고 싶을 때, (ex, UIKit의 ViewController)
            
- Class
    - 상속 가능. 부모 클래스의 속성과 메서드를 물려받고 오버라이딩 가능.

## 메모리 관리
- Struct
    - 스택(Stack)에 저장되는 경우가 많음. (작고 간단한 데이터에 적합)
    - ARC의 영향을 받지 않음.
- Class
    - 힙(Heap)에 저장됨.
        - 👉 메모리가 힙에 저장된다?
            - 프로그램이 실행되면 운영체제는 메모리 영역을 나눠서 사용한다.
                1. Stack 
                    - 함수 호출 시 생기는 지역 변수, 매개변수 등이 저장됨
                    - LIFO 방식으로 자동 관리됨 
                    - 함수가 끝나면 자동으로 메모리에 해제됨
                    - 빠르고 효율적이지만, 공간이 제한적이고 유연성이 적음
                2. Heap
                    - 필요에 따라 동적으로 할당하는 메모리 영역 (Swift ARC)
                    - 인스턴스(객체)가 저장됨 -> 예: Class 타입
                    - 언제 해제할지 컴파일러/런타임(ARC)이 추적해서 결정해야 함
                    - 공간이 더 크고 유연하지만, 관리가 복잡하고 속도도 상대적으로 느림
            - "힙에 저장돤다"의 의미
                - Swift의 Class 인스턴스는 생성될 때 Heap 메모리 영역에 저장
                - 그리고 그 인스턴스를 가리키는 "참조(Reference)"값이 Stack에 저장됨.
                - 반대로 Struct는 Stack에 저장 (값 타입)
    - ARC가 참조 카운트를 관리해야 함 -> 순환 참조(Strong Reference Cycle) 문제 발생 가능.
        - 👉 ARC의 동작 핵심
            - 강한 참조(기본, strong): 변수가 인스턴스를 "소유"함.
                - 소유자가 하나 늘면 RC+1, 줄면 RC-1
            - 약한 참조(weak): 소유하지 않음 (RC 변화 없음)
                - 대상이 해제되면 자동으로 nil (0이 되는 약한 참조, zeroing weak). 따라서 항상 Optional이어야 함
            - 비소유 참조(unowned): 소유하지 않음 (RC 변화 없음)
                - nil이 되지 않는다고 가정하고 쓰므로 Optional이 아님. 대상이 먼저 해제되면 크러시 발생
            - **클래스 간 순환 참조 (가장 전형적인 케이스)**
                ```swift
                final class Owner {
                    var pet: Pet? // Strong
                    deinit { print("Owner deinit") }
                }

                final class Pet {
                    var owner: Owner? // strong (⚠️문제 지점)
                    deinit { print("Pet deinit") }
                }
                
                do {
                    let o = Owner()
                    let p = Pet()
                    o.pet = p
                    p.owner = 0
                    // Owner <-> Pet 서로 Strong
                    // 스코프를 벗어나도 deinit이 안 찍힘 -> 누수
                }
                ```
                해결 1. 한쪽은 weak로
                ```swift
                final class Pet {
                    weak var owner: Owner? // 약한 참조로 고리 끊기
                }
                ```
                해결 2. unowned를 쓸 때
                - 상대가 반드시 나보다 오래 살아있다는 강한 보증이 있을 때만.
                - 예: Child는 Parent보다 오래 못 산다고 보장될 대:
                ```swift
                final class Parent {
                    var child: Child!
                }
                
                final class Child {
                    unowned let parent: Parent // 절대 nil이 아니라고 보장
                    init(parent: Parent) {
                        self.parent = parent
                    }
                }
                ```
                unowned는 참조 대상이 먼저 해제되면 바로 런타임 크래시라서, 정말 생명주기가 묶여 있을 때만 써야함. 안전 우선이면 weak가 기본!
            - **클로저 <-> 객체 간 순환 참조**
                - 클로저는 참조 타입이고, 클로저가 외부의 값(특히 self) 를 캡처할 때 기본이 strong임.
                - 그 클로저를 self의 프로퍼티로 저장하면 순환이 생김.
                ```swift
                final class ViewModel {
                    var handler: (() -> Void)?
                    deinit { print("VM deinit") }

                    func bind() {
                        handler = {
                            // 기본 캡처는 strong -> self를 강하게 붙듦
                            print(self) // ⚠️ self <-> handler 순환
                        }
                    }
                }
                ```
                해결: 캡처 리스트로 약/비소유 지정
                ```swift
                func bind() {
                    handler = { [weak self] in
                        guard let self else { return }
                        print(self)
                    }
                    // 혹은 [unowned self] in (self가 반드시 생존한다고 100% 보장될 때만)
                }
                ```
                - 규칙
                    - 대부분 weak 사용 -> 안전, 해제되면 클로저 내부에서 self가 nil이라 조용히 종료.
                    - unowned는 성능/문법 간경하지만 크래시 리스크. 생명주기 보장이 확실할 때만.
                - 흔한 예: Delegate 패턴
                    - 델리게이트는 보통 강하게 잡으면 순환이 생기므로 weak 로 둔다.
                    ```swift
                    protocol MyDelegate: AnyObject { /* ... */ }
                    final class Child {
                        weak var delegate: MyDelegate? // 약한 참조
                    }
                    ```

## mutating 키워드
- Struct
    - 값 타입이므로, 인스턴스 내부 값을 변경하려면 메서드 앞에 mutating 키워드 필요
- Class
    - 참조 타입이므로 필요 없음. 속성 변경 가능.

## 타입 캐스팅
- Struct
    - 타입 캐스팅 불가
- Class
    - 런타임에 타입 캐스팅(is, as) 가능

## Deinitializer
- Struct
    - 소멸자(deinit) 없음
- Class
    - 소명자 제공 -> 인스턴스 해제 시 리소스 정리 가능.

## 식별(identity)
- Struct
    - 값 타입이라 "동등성(Equality, ==)" 비교만 가능.
- Class
    - 참조 타입이라 "동등성(==)"뿐 아니라 "동일성(identity, ===)"비교 가능.

## 언제 Struct를 쓰고, 언제 Class를 쓸까?
- Struct 사용 권장 (기본 선택)
    - 비교적 단순한 데이터 모델
    - 값이 복사되는 것이 자연스러운 상황 (예: 좌표, 크기, 날짜 등)
    - 스레드 안정성을 높이고 싶은 경우
- Class 사용
    - 상속이 필요한 경우
    - 여러 곳에서 공유해야 하는 객체 (예: ViewController, Network Manager)
    - 참조를 통해 같은 인스턴스를 다뤄야 할 때