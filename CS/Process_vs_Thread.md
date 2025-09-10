# Process vs Trhead

## 기본 개념
- Process
    - 실행 중인 프로그램의 인스턴스
    - 독립적인 메모리 공간(Code, Data, Heap, Stack)을 가짐
    - 운영체제에서 자원 할당의 최소 단위
- Thread
    - 프로세스 내부에서 실행되는 작업 단위
    - 같은 프로세스 내에서 메모리(Heap, Data)를 공유
    - 운영체제에서 CPU 실행의 최소 단위

</br>

## 메모리 구조
- Process
    - 프로세스마다 독립된 메모리 공간 보유 ➡️ 다른 프로세스와 직접 데이터 공유 불가
    - 프로세스 간 통신(IPC: Inter Process Communication, 예: 파이프, 소켓, Shared Memory)이 필요
- Thread
    - 동일 프로세스 내에서는 Heap, Data 공유
    - 각 스레드만의 Stack은 따로 가짐 (함수 호출, 지역 변수 관리용)
    - 공유 메모리 때문에 동기화 문제 발생 ➡️ Lock, Semaphore 필요

</br>

## 생성 및 비용
- Process
    - 생성 시 무겁다 (fork, exec 등)
    - Context Switching 비용 ⬆️ (메모리, 캐시 모두 갈아엎음)
- Thread
    - 생성 비용이 가볍다
    - Context Switching도 비교적 저렴 (메모리 공간 공유)

</br>

## 안정성
- Process
    - 독립적이므로 하나가 죽어도 다른 프로세스에는 영향 X
    - 안정성이 높음
- Thread
    - 같은 프로세스 내 스레드 공유 메모리를 망가뜨리면 전체 프로세스가 죽음
    - 안정성 ⬇️, 동기화 주의 필요

</br>

## iOS
 - Process
    - iOS에서 하나의 앱 = 하나의 프로세스
    - 앱 간에는 메모리를 공유하지 않고, App SandBox로 격리됨
- Thread
    - Main Thread: UI 업데이트 담당
    - Background Thread: 네트워킹, 파일 I/O, CoreData 처리 등에 사용
    - GCD(DispatchQueue), OperationQueue 등으로 스레드 관리

