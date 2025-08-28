# 스위즐링

Swift에서 스위즐링은 메서드나 속성을 변경하는 기술

주로 런타임 시점에 메소드나 속성을 변경하여 동작을 수정하거나 보완하는데 사용되고, 이미 정해진 특정 메소드가 실행될 때 또는 해당 메소드 대신 다른 매소드가 실행되도록 바꿀 때 사용합니다.

### 사용 방법

UIViewController의 ‘viewDidLoad’ 메서드를 변경하여 원래의 동작에 추가적인 기능을 수행하도록 

```swift
extension UIViewController {
    // Method Swizzling을 위한 메서드
    @objc func myViewDidLoad() {
        // 기존의 viewDidLoad 메서드를 호출합니다.
        self.myViewDidLoad()
        
        // 추가 기능을 수행합니다.
        print("View did load: \(self)")
    }
    
    // Swizzling을 위한 메서드들을 교환합니다.
    class func swizzleViewDidLoad() {
        let originalSelector = #selector(UIViewController.viewDidLoad)
        let swizzledSelector = #selector(UIViewController.myViewDidLoad)
        
        let originalMethod = class_getInstanceMethod(self, originalSelector)
        let swizzledMethod = class_getInstanceMethod(self, swizzledSelector)
        
        // 원래의 메서드가 존재하지 않을 때 새로운 메서드를 클래스에 추가,
        let didAddMethod = class_addMethod(self, originalSelector, method_getImplementation(swizzledMethod!), method_getTypeEncoding(swizzledMethod!))
        
        if didAddMethod {
						// 이미 메서드가 존재할 경우 새로운 메서드로 대체
            class_replaceMethod(self, swizzledSelector, method_getImplementation(originalMethod!), method_getTypeEncoding(originalMethod!))
        } else {
						// 두 메서드를 서로 교환
            method_exchangeImplementations(originalMethod!, swizzledMethod!)
        }
    }
}

// Swizzling을 적용할 ViewController 예제
class MyViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        print("Original viewDidLoad")
    }
}

// Swizzling을 적용합니다.
MyViewController.swizzleViewDidLoad()

// 테스트를 위해 ViewController 인스턴스를 생성하고 viewDidLoad를 호출합니다.
let viewController = MyViewController()
viewController.viewDidLoad()
```

이 예제는 ‘UIViewController’의 ‘viewDidLoad’메서드를 변경하여 기존의 동작에 print문을 추가했습니다.

주의해야 할 점은 스위즐링은 코드를 복잡하게 만들 수 있고, 잘못 사용할 경우 예상치 못한 버그를 발생시킬 수 있습니다.