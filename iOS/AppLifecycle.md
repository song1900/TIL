# App Lifecycle
## 1) 공통 개념
- 주요 상태
    - Not Running ➡️ Inactive(실행 중이지만 이벤트 미수신) ➡️ Active(이벤트 수신) ➡️ Background(코드 실행 가능) ➡️ Suspended(메모리만 유지, 코드 실행 X)
- 전형적 흐름
    - Launch(Cold/Warm) ➡️ Inactive ➡️ Active
    - 홈으로 나감: Active ➡️ Inactive ➡️ Background ➡️ Suspended
    - 복귀: Suspended/Background ➡️ Inactive ➡️ Active
    - 종료: Background/Suspended 중 OS 결정(메모리 압박, 크래시, 사용자 스와이프 종료 등)
- 멀티 윈도우(iPad, 일부 iPhone): iOS 13+에서 Scene(윈도우 단위 라이프사이클)이 도입됨.

</br>

## 2) UIKit Lifecycle
**핵심 개체**
- UIApplicationDelegate: 앱 전역 이벤트(푸시 토큰, 백그라운드 작업, 라우팅 진입점 등)
- UIScene/UISceneDelegate: 각 윈도우(장면)의 화면/포커스 전이

</br>

**UIApplicationDelegate 대표 메서드**
```swift
@main
class AppDelegate: UIResponder, UIApplicationDelegate {
    func application(
        _ application: UIApplication,
        didFinishLaunchingWithOptions lauchOptions: [UIApplication.LaunchOptionsKey: Any]?
    ) -> Bool {
        // 앱 첫 초기화: DI 구성, SDK 설정, Analytics 시작 등
        return true
    }

    func applicationDidEnterBackground(_ application: UIApplication) {
        // 백그라운드 진입(전역): 저장/정리 작업 트리거
    }

    func applicationWillEnterForeground(_ application: UIApplication) { }

    func applicationDidBecomeActive(_ application: UIApplication) {
        // 포그라운드 활성화(전역): 세션 재개, 타이머 재시작 등
    }

    func applicationWillTerminate(_ application: UIApplication) {
        // 드물게 호출(백업): 즉시종료 상황 대비 저장 로직 넣지 말기
    }
}
```
iOS 13+에서는 화면 수명주기는 Scene으로 분리되었고, AppDelegate는 프로세스급 이벤트에 집중.

</br>

**UISceneDelegate 대표 메서드**
```swift
class SceneDelegate: UIResponder, UIWindowSceneDelegate {
    var window: UIWindow?

    func scene(_ scene: UIScene,
               willConnectTo session: UISceneSession,
               options connectionOptions: UIScene.ConnectionOptions) {
        // 윈도우/루트VC 구성, 딥링크 처리 초기 진입점
    }

    func sceneDidBecomeActive(_ scene: UIScene) { /* 화면이 활성화됨 */ }
    func sceneWillResignActive(_ scene: UIScene) { /* 비활성화 직전 */ }
    func sceneWillEnterForeground(_ scene: UIScene) { /* 포그라운드 복귀 직전 */ }
    func sceneDidEnterBackground(_ scene: UIScene) { /* BG 진입: 상태 저장 */ }
}
```

</br>

**딥링크 & 알림 진입**
- URL Schemes / Universal Links
    - application(_:open:options:) (iOS12, AppDelegate)
    - scene(_:openURLContexts:) (iOS13+, SceneDelegate)

- 푸시 알림 탭으로 진입
    - userNotificationCenter(_:didReceive:withCompletionHandler:)
    - 백그라운드 수신 처리: didReceiveRemoteNotification:fetchCompletionHandler:


</br>

**백그라운드 실행 유형**
- Background Modes(Capabilities): audio, location, VoIP, external accessory, bg fetch/processing 등
- Background Fetch: 시스템이 주기적으로 깨워 데이터 갱신
- BGTaskScheduler(iOS13+)
    - Processing(긴 작업), App Refresh(짧은 네트워킹) 예약
```swift
BGTaskScheduler.shared.register(forTaskWithIdentifier: "com.foo.refresh",
                                using: nil) { task in
    // 작업 실행
    task.setTaskCompleted(success: true)
}
```


</br>

**상태 복원(State Restoration)**
- 사용자 흐름/화면 위치 복원: scene 기반에서 UISceneSession role과 state 저장을 활용
- 메모리 절약과 보안 고려(민감정보는 Keychain/서버)


## 3) SwiftUI Lifecycle 
**APP 구조**
- @main struct MyApp: App: SwiftUI 앱의 진입점
- 기존 AppDelegate/SceneDelegate 없이도 동작하지만, 필요 시 Adaptor로 연결 가능
```swift
@main
struct MyApp: App {
    @UIApplicationDelegateAdaptor(AppDelegate.self) var appDelegate
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
    }
}
```

</br>

**라이프사이클 감지: scenePhase**
- SwiftUI는 전역 환경값으로 앱/씬 상태를 제공
```swift
struct ContentView: View {
    @Environment(\.scenePhase) private var scenePhase

    var body: some View {
        Text("Hello")
        .onChange(of: scenePhase) { _, newPhase in
            switch newPhase {
            case .active:
                // 화면 활성화: 타이머/세션 재개
                break
            case .inactive:
                // 일시 비활성
                break
            case .background:
                // 백그라운드 진입: 저장/정리
                break
            @unknown default: break
            }
        }
    }
}
```

</br>

**App/Scene 구성 확장**
- WindowGroup, DocumentGroup 등 멀티 윈도우/플랫폼 구성
- 딥링크/단축어/푸시 등은 App의 modifiers 또는 DelegateAdaptor로 처리

</br>

**푸시/딥링크 in SwiftUI**
- 푸시 등록/수신: UNUserNotificationCenter + AppDelegateAdaptor
- 유니버셜 링크: onOpenuRL { url in ... } 혹은 Scene phase/connectionOption 활용
```swift
@main
struct MyApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
                .onOpenURL { url in
                    // 라우팅 처리
                }
        }
    }
}
```

</br>

**백그라운드 작업 in SwiftUI**
- UI 코드는 SwiftUI, 스케줄링은 BGTaskScheduler(UIKit API) 혼용
- 데이터 저장은 @SceneStorage, @AppStorage, Core Data/Realm 등으로 상태 유지