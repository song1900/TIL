# RunLoop

## RunLoop란?
- Thread의 이벤트 처리 루프를 의미합니다.
- 입력 소스(이벤트, 포트, 타이머 등)를 처리하고, 필요 없을 때는 스레드를 대기(sleep) 상태로 전환하여 CPU 자원을 효율적으로 관리합니다.
- iOS에서는 기본적으로 메인 스레드에 RunLoop가 돌아가며, UI 이벤트, 터치 이벤트, Timer, GCD에서 메인큐에 전달된 작업 등이 모두 RunLoop 위에서 처리됩니다.

</br>

## RunLoop의 동작 구조
RunLoop는 반복적인 루프 구조로 동작합니다.
1. RunLoop 진입
    - 스레드가 RunLoop를 실행하면 루프가 시작됩니다.
2. 이벤트 처리 준비
    - 타이머, 입력 소스(Event source), 관찰자(observer)를 확인합니다.
3. 이벤트 처리 or 대기
    - 이벤트가 있다면 처리합니다.
    - 없다면 스레드를 sleep 상태로 전환 ➡️ 자원 절약
    - 새로운 이벤트가 들어오면 깨워서 처리
4. 루프 반복
즉, 이벤트 확인 ➡️ 처리 ➡️ 대기를 반복하는 구조입니다.

</br>

## RunLoop의 주요 구성 요소
1. Input Source
    - 외부에서 들어오는 이벤트를 RunLoop로 전달하는 역할.
    - 예: 사용자 터치 이벤트, 포트 통신 이벤트 등
2. Timer Source
    - 지정된 시간 간격마다 이벤트를 발생시키는 소스
    - 예: Timer.scheduledTimer, CADisplayLink 등
3. Observer
    - RunLoop 상태 변화를 관찰하는 객체.
    - 진입/종료/대기 상태 등을 감지 가능.
    - 예: autoreleasepool이 RunLoop observer를 활용해 메모리 관리.
4. Mode
    - RunLoop가 처리할 이벤트 그룹
    - 대표적으로:  
        - Default Mode: 일반 이벤트 처리
        - Tracking Mode: UITracking(스크롤/드래그 중) 이벤트 전용
        - Common Modes: 여러 모드에 공통적으로 등록된 소스/타이머 처리

</br>

## RunLoop와 Thread
- Main Thread: 앱 실행 시 자동으로 RunLoop가 설정됩니다. ➡️ UI 업데이트와 이벤트 처리 담당.
- Background Thread: 기본적으로 RunLoop가 없음 ➡️ 필요하면 직접 생성 및 실행해야 함.
```swift
Thread {
    let runLoop = RunLoop.current
    runLoop.add(Port(), forMode: .default)
    runLoop.run()
}.start()
```

</br>

## RunLoop의 활용 사례
1. UI 이벤트 처리
    - 메인 스레드의 RunLoop가 사용자의 터치 이벤트를 받아 UIKit으로 전달.
2. Timer
    - Timer는 RunLoop 위에서 동작
    - RunLoop에 등록되지 않은 Timer는 작동하지 않음
3. Networking (구버전)
    - iOS 옛날 API(NSURLConnection)은 RunLoop 기반 비동기 통신을 사용
    - 현재는 URLSession이 주류지만 내부적으로 RunLoop와 GCD를 적절히 활용
4. AutoreleasePool 관리
    - RunLoop가 한 사이클을 돌 때마다 AutoreleasePool을 비워줌

</br>

### 👉 그럼 RunLoop가 없다면?
스레드가 이벤트 대기 중에도 Busy-Waiting 하여 CPU를 계속 점유하게 됨.

### 👉 Main Thread에 RunLoop가 필요한 이유는?
UI 이벤트와 입력 처리, Timer, GCD 메인큐 실행을 위해

### 👉 Background Thread에서 Timer를 사용하려면?
RunLoop를 명시적으로 실행해줘야 Timer가 동작