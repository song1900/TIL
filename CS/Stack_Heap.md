# Heap vs Stack

## Stack
- 함수 호출 시 생기는 "초고속 작업대".
- LIFO로 쌓였다가 함수가 끝나면 한 번에 치움.
- 스레드마다 하나씩, 용량은 작지만 빠름.

## Heap
- 오래 두고 쓰는 "창고".
- 크고 유연하지만, 할당/해제가 상대적으로 느리고 파편화 가능.
- ARC가 관리.

</br>

## Swift에서의 배치 규칙
### Class 인스턴스
- Heap. 변수는 참조(포인터)

### Struct/Enum
- 값 타입.
- 보톤 Stack에 "직접" 놓이지만,
    - 컬렉션 요소/프로퍼티로 Heap 객체에 포함되면 그 객체의 힙 블록 안에 inline으로 들어감

### Array / String / Dictionary 등 컬렉션
- 값 타입이지만 요소 저장용 버퍼는 Heap.
- Copy-on-Write(CoW)로 실제 복사는 수정 시에만.

### Closure
- 캡처가 있고 escaping이면 보통 Heap에 저장. (non-escaping은 최적화 시 Stack 가능)

### ARC
- Heap 객체의 참조 수를 관리.
- 순환참조는 weak/unowned로 끊어야 함.



</br>

## 비교
- 관리 주제
    - Stack: 컴파일러/런타임이 자동 push/pop
    - Heap: ARC가 참조 카운팅으로 해제

- 할당/해제 속도
    - Stack: 매우 빠름 (포인터 증감)
    - Heap: 느림

- 수명
    - Stack: 스코프/함수 종료 시 자동 소멸
    - Heap: 참조가 0이 될 때까지 유지

- 메모리 배치
    - Stack: 연속적, 스레드별 고정 크기
    - Heap: 비연속적, 프로세스 전체에서 공유

- 용량/성능
    - Stack: 작지만 예측 가능, 캐시 친화적
    - Heap: 크고 유연하지만 파편화/오버헤드 가능

- 대표 데이터
    - Stack: 지역 변수, 파라미터, 값 타입(대체로)
    - Heap: 클래스 인스턴스, 컬렉션 버퍼, escaping 클로저

- 장점
    - Stack: 관리 비용 적고 초고속, 해제 시점 예측
    - Heap: 큰 객체/가변 객체에 유리, 수명 유연

- 위험/이슈
    - Stack: 깊은 재귀 시 stack overflow
    - Heap: 순환 참조, 메모리 누수, 단편화