# Riverpod autoDispose 정리

## 1. autoDispose란?
Provider가 더 이상 필요하지 않을 때 자동으로 메모리에서 제거(dispose) 해주는 옵션.

- 화면에서 벗어났을 때
- Provider를 구독하는 위젯이 없어졌을 때

Riverpod이 알아서 Provider를 정리함.

👉 목적
- 메모리 누수 방지
- 불필요한 상태 유지 방지
- 화면 이동 시 이전 상태가 남지 않도록 초기화


<br>

## 2. 사용 예시
기존 Provider
```dart
final counterProvider = StateProvider<int>((ref) => 0);
```

autoDispose 적용
```dart
// 사용법은 .autoDispose 추가
final counterProvider = StateProvider.autoDispose<int>((ref) => 0);
```

<br>

## 3. autoDispose가 필요한 상황
- 화면 이동 시 상태 초기화가 필요한 경우
    - 검색창, 입력 폼, 임시 필터값 등
    -> 다른 화면 갔다 돌아오면 초기값이면 좋음

- 무거운 리소스를 가지는 Provider
    - 예: Stream 구독, Timer, API polling, Anmiation Controller
    -> 화면 나가면 자동으로 dispose하면 좋음

- 화면 생명주기 기반으로 provider를 관리하고 싶을 때
    - Provider를 위젯의 생명과 동일하게 유지하고 싶을 때 유용

<br>


## 4.autoDispose와 상태 유지
기본적으로 autoDispose는 화면을 벗어나면 상태가 사라짐
그러나 아래처럼 ref.keepAlive() 를 사용하면 일부만 유지 가능

<br>

## 5. keepAlive()로 예외적으로 상태 유지하기
autoDispose지만 특정 로직에서 유지하고 싶은 경우:
```dart
final newsProvider = FutureProvider.autoDispose((ref) async {
    final link = ref.keepAlive(); // autoDispose지만 유지 요청
    final timer = Timer(Duration(seconds: 30), () {
        link.close(); // 30초 후에는 dispose 허용
    });

    ref.onDispose(() {
        timer.cancel();
    });

    return fetchNews();
});
```

이럴 때 유용:
- 무한 스크롤 로딩
- polling 중 일정 시간 동안 상태 유지
- API 결과 잠시 캐싱하고 싶은 경우

<br>

6. 자주 하는 실수
- 화면 전환할 때마다 FutureProvider 재실행됨
-> 무거운 API 호출이면 퍼포먼스 저하
👉 해결: keepAlive() 활용 or family + caching

<br>

## 7. autoDispose vs non-autoDispose 선택 기준
**“이 화면 벗어나면 이 상태를 버려도 되나?”** → 그렇다면 `autoDispose`
**“앱 전체에서 계속 들고 있어야 하나?”** → 그렇다면 기본 non-`autoDispose`

### 한눈에 보는 비교표

| 상황/특징                              | `autoDispose` 사용 적합 ✅                      | `non-autoDispose`(기본) 적합 ✅                    |
|----------------------------------------|-------------------------------------------------|---------------------------------------------------|
| 화면 이동 시 상태가 **초기화**되면 좋은가? | 예) 검색 폼, 임시 필터, 임시 선택값              | 아니요. 이전 상태를 그대로 유지해야 한다.         |
| 상태를 **화면 생명주기**에 묶고 싶은가?   | 예) 특정 화면 전용 상태                         | 전역으로 공유되는 상태                            |
| Provider가 **무거운 리소스**를 쓰는가?   | 예) Stream, Timer, AnimationController, polling | 가벼운 값/설정, 전역 캐시                         |
| 메모리 사용을 최소화해야 하는가?        | 사용 안 할 때 과감히 메모리에서 제거하고 싶다     | 앱 전체에서 계속 쓸 거라 메모리 상주가 괜찮다     |
| 다시 돌아왔을 때 **항상 초기 상태**여야 하나? | 폼 리셋, 임시 입력값, 일회성 화면                | 사용자 프로필, 인증 정보, 앱 설정, 캐시 데이터 등 |
| API 응답을 **잠깐만 쓰고 버려도** 되나? | 예) 일회성 조회, 상세 화면 전용 데이터           | 예전 응답도 다시 재사용하고 싶다                  |

---

### autoDispose 추천 케이스

- 검색 화면, 필터 설정 화면, 일회성 Form
- 무한 스크롤/일시적인 목록 상태
- 특정 화면에서만 사용하는 Stream / Timer / AnimationController
- 뒤로 가기 후 다시 들어오면 **매번 새로 로딩**하는 게 자연스러운 UX

---

### non-autoDispose(기본) 추천 케이스

- 로그인 상태, 사용자 정보, 토큰
- 앱 환경 설정, 다크모드/언어 설정 등 전역 상태
- 여러 화면에서 공유하는 공용 캐시 (예: 공통 코드 목록, 공통 설정 값)
- "앱을 완전히 종료하기 전까지는 유지" 가 더 자연스러운 데이터




