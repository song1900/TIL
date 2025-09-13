# Deadlock 교착상태

## 정의
- 여러 프로세스 또는 스레드가 자원을 얻기 위해 서로 기다리는 상태가 되어 무한정 대기에 빠지는 상황
- 더 이상 진행되지 않으며, 외부 개입 없이는 풀리지 않는다.

</br>


## 발생 조건 Coffman's Confitions
👉 4가지 모두 만족해야 Deadlick 발생

1. 상호 배제
    - 자원은 한 번에 하나의 프로세스만 사용할 수 있다.
2. 점유와 대기
    - 이미 자원을 가진 프로세스가 다른 자원을 요청하면서 대기한다.
3. 비선점
    - 자원을 강제로 빼앗을 수 없다. (반드시 자발적으로 해제해야 함)
4. 순환 대기
    - 프로세스들이 원형으로 자원을 기다힌다.</br>
    (P1 -> R1 -> P2 -> R2 -> P1 형태)

</br>

## 예시
- 멀티스레드에서 Lock 두 개를 교차로 획득하려고 할 때
```swift
let lockA = NSLock()
let lockB = NSLock()

func task1() {
    lockA.lock()
    sleep(1)
    lockB.lock()

    lockB.unlock()
    lockA.unlock()
}

func task2() {
    lockB.lock()
    sleep(1)
    lockA.lock()

    lockA.unlock()
    lockB.unlock()
}

task1()
task2()
```
- task1은 A ➡️ B 순서로 Lock을 획득하려 하고
- task2는 B ➡️ A 순서로 Lock을 획득하려 하면, 서로가 상대방의 Lock을 기다리며 Deadlock 발생.


</br>

## 해결/예방 방법
- Deadlock 방지
    - Coffman 조건 중 하나를 깨트리기
        - 자원에 대한 순서(Orderling) 강제 
        - 자원 요청 시 한 번에 모두 요청하게 설계 
        - 자원 선점 가능하게 만들기
- Deadlock 회피
    - Banker's Algorithm 사용 (안전 상태일 때만 자원 할당)
- Deadlock 탐지 및 복구 
    - Deadlock 발생 가능성을 허용하고, 주기적으로 그래프/테이블 검사
    - 교착 상태가 탐지되면 프로세스를 중단하거나 자원을 회수해서 복구
- iOS 개발에서
    - GCD, OperationQueue, async/await 활용
    - Sync 호출 안에서 또 다른 동기 호출을 하지 않기

</br>

## iOS에서 흔한 케이스
- 메인 큐에서 sync 호출
```swift
DispatchQueue.main.sync {
    print("이 코드는 절대 실행되지 않음")
}
```    
➡️ 메인 큐에서 실행 중인데 다시 메인 큐에 동기 호출을 하면 서로 기다리며 Deadlock 발생
➡️ UI 관련 코드는 항상 메인 스레드에서 실행되므로, sync 대신 async 사용