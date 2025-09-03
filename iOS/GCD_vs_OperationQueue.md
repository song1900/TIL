# GCD vs OperationQueue

## GCD (Grand Central Dispatch)
- 저수준 API ➡️ C 기반의 라이브러리
- 큐(DispatchQueue) 기반으로 동작
- 가볍고 빠름: 시스템 레벨에서 최적화
- 주요 사용처
    - 단순히 특정 작업을 비동기로 실행할 때
    - 메인 스레드(UI 업데이트)와 백그라운드 스레드(네트워킹, 파일I/O) 전환이 필요할 때
- 장점
    - 간단한 문법 (DispatchQueue.main.async, DispatchQueue.global().async)
    - 오버헤드가 적고 성능 최적화
    - Swift의 클로저와 잘 어울림
- 단점
    - 작업 취소, 우선순위 조정 등 고급 제어 기능 부족
    - 복잡한 동기화나 의존 관계 관리 어려움

</br>

## OperationQueue
- 상위 수준 API -> GCD를 기반으로 만들어진 추상화 계층
- 작업(Operation) 단위로 실행
- Operation 객체를 만들어 OperationQueue에 추가
- 주요 사용처
    - 작업 간 의존성 관리가 필요할 때
    - 특정 작업을 취소할 수 있어야 할 때
    - 여러 작업을 재사용하거나 재시도할 때
- 장점
    - 작업 간 의존성 설정 가능 (addDependency)
    - 우선순위 부여 (queuePriority, qualityOfService)
    - 작업 취소 가능 (cancel())
    - KVO(Key-Value Operation)로 상태 관찰 가능 (isReady, isExecuting, isFinished)
- 단점
    - 오버헤드가 조금 있음
    - 문법이 GCD보다 상대적으로 복잡함


</br>

## 정리
- 빠르고 간단한 비동기 처리 ➡️ GCD
- 복잡한 작업 관리(의존성, 취소, 우선순위 등) ➡️ OperationQueue

