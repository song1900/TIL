# async vs sync


## Asynchronous 비동기
- 작업의 완료를 기다리지 않고 바로 다음 작업 실행
- 호출한 함수는 즉시 반환하고, 작업이 끝나면 콜백/이벤트/Completion Handler 등을 통해 결과 전달
- 예) 키오스크 주문 -> 음식 나오면 알림 받는 것

**특징**
- 프로그램의 흐름을 막지 않음 -> Non-Blocking
- 여러 작업을 동시에 진행할 수 있어 효율적
- 흐름이 복잡해질 수 있음 (콜백 지옥 문제 -> async/await로 개선)

</br>

## Synchronous 동기
- 작업을 순서대로 실행하는 방식
- 한 작업이 끝나야 다음 작업이 실행됨
- 호출한 함수의 결과를 즉시 반환하거나 끝날 때까지 기다림
- 예) 줄 서서 계산대 기다리는 것

**특징**
- 흐름이 단순하고 예측하기 쉬움
- 하지만 작업이 오래 걸리면 전체 프로그램이 블로킹 됨



</br>


## iOS에서 
### 1. async 실행
- 오래 걸리는 작업은 보통 Background Trhead 에서 비동기로 실행
- 완료 후 UI 업데이트는 반드시 Main Thread 로 돌아와야 함
- GCD, OperationQeue 등 활용

</br>

### 2. sync 실행
- Main Thread 에서 실행되는 경우가 많음
- 작업이 끝날 때까지 UI 업데이트나 사용자 입력이 멈춤 -> 앱이 멈춘 것으로 보임
- 네트워크, 파일 I/O 같은 시간이 오래 걸리는 작업은 절대 메인 스레드에서 동기 실행하면 안 됨
```swift
DispatchQueue.global().async {
    // 오래 걸리는 작업 (네트워크, 파일 처리 등)
    let data = try? Data(contentsOf: URL(string: "https://example.com")!)

    DispatchQueue.main.async {
        // UI 업데이트는 메인 스레드에서
        self.imageView.image = UIImage(data: data!)
    }
}
```

</br>

### 3. async/await (Swift 5.5+ / iOS 15+)
- 비동기 코드 흐름을 동기 코드처럼 깔끔하게 작성 가능
- 가독성이 좋음
```swift
func fetchImage() async throws -> UIImage {
    let url = URL(string: "https://example.com")!
    let (data, _) = try await URLSession.shared.data(from: url)
    return UIImage(data: data)!
}

Task {
    do {
        let image = try await fetchImage()
        self.imageView.image = image
    } catch {
        print("에러:", error)
    }
}
```
