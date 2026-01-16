# Today I Learned (TIL)

[![iOS](https://img.shields.io/badge/iOS-000000?style=flat-square&logo=apple&logoColor=white)](https://developer.apple.com/ios/)
[![Swift](https://img.shields.io/badge/Swift-FA7343?style=flat-square&logo=swift&logoColor=white)](https://swift.org/)
[![Flutter](https://img.shields.io/badge/Flutter-02569B?style=flat-square&logo=flutter&logoColor=white)](https://flutter.dev/)

> 개발하면서 배운 것들을 기록하는 공간

---

## About

CS 기초부터 iOS/Flutter 심화 개념까지, 학습한 내용을 정리합니다.

---

## Contents

### CS (11)
| 주제 | 설명 |
|:-----|:-----|
| [CPU, RAM, 저장장치](CS/CPU_RAM_저장장치.md) | 컴퓨터 구조의 핵심 구성 요소 |
| [Stack & Heap](CS/Stack_Heap.md) | 메모리 구조 |
| [Process vs Thread](CS/Process_vs_Thread.md) | 프로세스와 스레드 비교 |
| [Context Switching](CS/Context_Switching.md) | 문맥 교환 메커니즘 |
| [Deadlock](CS/Deadlock.md) | 교착 상태 발생 조건과 해결 |
| [Cache Memory & Locality](CS/CacheMemory_Locality.md) | 캐시와 지역성 원리 |
| [Hash](CS/Hash.md) | 해시 테이블과 해시 함수 |
| [컴파일러](CS/컴파일러.md) | 컴파일 과정 |
| [인터프리터](CS/인터프리터.md) | 인터프리터 동작 방식 |
| [재귀](CS/재귀.md) | 재귀 함수 |
| [시뮬레이터 vs 에뮬레이터](CS/시뮬레이터_에뮬레이터.md) | 개발 환경 차이 |

### iOS (18)
| 주제 | 설명 |
|:-----|:-----|
| [ARC 동작원리](iOS/ARC동작원리.md) | 메모리 관리 메커니즘 |
| [Retain Cycle](iOS/RetainCycle.md) | 순환 참조 문제와 해결 |
| [Copy-on-Write](iOS/Copy-on-Write(CoW).md) | 값 타입 최적화 |
| [Closure Capture List](iOS/Closure_Capture_List.md) | 클로저 캡처 |
| [이스케이핑 클로저](iOS/이스케이핑.md) | @escaping 키워드 |
| [Struct vs Class](iOS/Struct_vs_Class.md) | 값 타입 vs 참조 타입 |
| [Protocol & POP](iOS/Ptotocol&POP.md) | 프로토콜 지향 프로그래밍 |
| [Opaque vs Existential Type](iOS/Opaque_Type_vs_Existential_Type.md) | some vs any |
| [GCD vs OperationQueue](iOS/GCD_vs_OperationQueue.md) | 동시성 처리 |
| [async vs sync](iOS/async_vs_sync.md) | 비동기 프로그래밍 |
| [RunLoop](iOS/RunLoop.md) | 이벤트 루프 |
| [Combine Subject](iOS/Combine_Subject.md) | 반응형 프로그래밍 |
| [Sendable](iOS/Sendable.md) | Swift Concurrency |
| [App Lifecycle](iOS/AppLifecycle.md) | 앱 생명주기 |
| [URLCache & NSCache](iOS/URLCache_NSCache.md) | 캐싱 전략 |
| [LLVM](iOS/LLVM.md) | 컴파일러 인프라 |
| [Swizzling](iOS/Swizzling.md) | 메서드 스위즐링 |
| [C-Style Loop & ++ 연산자](iOS/C_Style_Loop그리고++연산자.md) | Swift 문법 변경 |

### Flutter (2)
| 주제 | 설명 |
|:-----|:-----|
| [Riverpod - Consumer](Flutter/Riverpod/Consumer.md) | 상태 구독과 리빌드 최적화 |
| [Riverpod - autoDispose](Flutter/Riverpod/autoDispose.md) | Provider 자동 해제 |

### Git (3)
| 주제 | 설명 |
|:-----|:-----|
| [Chapter 01](Git/Chapter01.md) | Git 기초 - 버전 관리, 스냅샷 |
| [Chapter 02](Git/Chapter02.md) | 브랜치와 병합 |
| [Chapter 03](Git/Chapter03.md) | 리베이스와 태깅 |

### Algorithm
서브모듈로 연결된 알고리즘 풀이 저장소입니다.

| Repository | 설명 |
|:-----------|:-----|
| [LeetCode](Algorithm/LeetCode) | LeetCode 문제 풀이 |
| [Baekjoon](Algorithm/Baekjoon) | 백준 문제 풀이 |

---

## Clone

```bash
# 서브모듈 포함 클론
git clone --recursive https://github.com/song1900/TIL.git

# 이미 클론한 경우
git submodule update --init --recursive
```

---

## Author

**송우진** | iOS & Flutter Developer

[![GitHub](https://img.shields.io/badge/GitHub-181717?style=flat-square&logo=github&logoColor=white)](https://github.com/song1900)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-0A66C2?style=flat-square&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/woojin-song-86412319b)
[![Blog](https://img.shields.io/badge/Blog-FF5722?style=flat-square&logo=blogger&logoColor=white)](https://247ios.tistory.com)
[![Email](https://img.shields.io/badge/Email-EA4335?style=flat-square&logo=gmail&logoColor=white)](mailto:woojin1900@gmail.com)
