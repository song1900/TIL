# Context Switching

## 기본 개념
- CPU가 현재 실행 중인 프로세스/스레드의 상태(Context)를 저장하고, 다른 프로세스/스레드의 상태를 복원하여 실행을 전환하는 과정.
- 운영체제(OS)의 멀티태스킬 핵심 기술.
- 단일 CPU 환경에서도 여러 작업이 동시에 실행되는 것처럼 보이게 함.

</br>

## Context란?
- CPU가 작업을 이어가기 위해 필요한 정보
    - 레지스터 값 (Program Counter, Stack Pointer, CPU 레지스터)
    - 프로세스 제어 블록(PCB): 프로세스 ID, 상태, 우선순위, 메모리 관리 정보 등
    - 스레드 정보: 스택, 스케줄러 상태 등

</br>

## Context Switching 과정
1. 실행 중인 프로세스/스레드의 상태 저장 (PCB에 기록)
2. 스케줄러가 다음 실행할 프로세스/스레드를 선택
3. 선택된 프로세스/스레드의 상태 복원 (레지스터/메모리/스택 등)
4. CPU 제어를 새 프로세스에 넘김

</br>

## Context Switching 비용
- 장점: 
    - 여러 프로세스가 동시에 실행되는 것처럼 보임
- 단점: 
    - 저장/복원 작업에 따른 시간 소모
    - Cache Miss 증가 ➡️ 성능 저하
    - 빈번한 스위칭은 CPU 효율 저하

</br>

## Context Switching 필요한 상황
- 시분할(Time-sharing): CPU 시간을 분배하기 위해
- I/O 요청: 한 프로세스가 I/O 대기 ➡️ 다른 프로세스 실행
- 우선순위: 높은 우선순위 프로세스가 도착
- 멀티스레딩: 여러 스레드 사이 전환

</br>

## iOS 관점에서
- iOS는 Darwing 커널 위에서 동작하며, GCD와 OperationQueue 등 을 통해 스레드 풀 + 큐 기반 스케줄링 수행
- 상황  
    - 메인 스레드 ↔️ 백그라운드 스레드 전환
        - UI는 반드시 메인 스레드에서 처리해야 하므로 전환이 자주 발생
    - async/await, DispatchQueue.main.async 호출 시 스위칭 발생
    - OperationQueue
        - 작업 취소/우선순위 변경 ➡️ 다른 스레드로 스위칭
- 너무 잦은 스레드 전환은 오히려 CPU 오버헤드와 배터리 소모로 이어짐

</br>

## Context Switching vs Function Call
- Function Call: 같은 스택 내에서 이동 (저렴)
- Context Switching: CPU 전체 상태를 바꿔야 함 (비쌈)
- 따라서 멀티스레드 환경에서는 스레드 수를 적절히 제한하는 것이 중요

</br>

