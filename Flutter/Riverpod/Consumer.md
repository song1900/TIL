# Riverpod의 Consumer 정리

- Consumer는 Riverpod에서 특정 Provider가 변경될 때 **필요한 위젯만 빌드**하기 위해 사용되는 위젯
- 전체 위젯 트리를 rebuild하지 않고, 정확히 필요한 부분만 다시 그리는 최적화된 방식


<br>

### Consumer 사용하는 이유
일반적으로 ref.watch(provider)를 사용하면 해당 위젯 전체가 리빌드된다.

하지만 다음과 같은 경우라면?
- 전체 페이지는 리빌드될 필요 없고
- 버튼, 텍스트 등 일부만 provider 변경에 반응해야 함
- 무거운 화면에서 성능 최적화를 하고 싶음

이때 Consumer(or ConsumerWidget)를 사용하면 원하는 영역만 적은 비용으로 갱신된다.

<br>

### 기본 구조
```dart
Consumer(
    builder: (context, ref, child) {
        final count = ref.watch(counterProvider);
        return Text('$count');
    }
)
```
- context: Build Context
- ref: Provider 읽기/구독용
- child: Consumer 내부에서 변하지 않는 위젯(성능 최적화용)


<br>

### 예제
**1. 기본 Counter**
```dart
final counterProvider = StateProvider<int>((ref) => 0);

class HomePage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Consumer(
          builder: (context, ref, _) {
            final count = ref.watch(counterProvider);
            return Text('$count');
          },
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () {
          context.read(counterProvider.notifier).state++;
        },
        child: Icon(Icons.add),
      ),
    );
  }
}
```
결과 -> Provider가 변경될 때 텍스트 위젯만 리빌드


**2. child를 활용한 성능 최적화**
child는 리빌드되지 않는 위젯을 전달할 때 사용
```dart
Consumer(
  builder: (context, ref, child) {
    final value = ref.watch(counterProvider);
    return Row(
      children: [
        child!, // 리빌드 X
        Text('$value'), // 리빌드 O
      ],
    );
  },
  child: const Text("변하지 않는 텍스트"),
)
```
- "변하지 않는 텍스트" -> 절대 리빌드 안ㄷㅗ미
- Provider 값이 바뀌면 오른쪽 Text만 리빌드됨


**3. ConsumerWidget 사용하기**
ConsumerWidget은 StatelessWidget과 Consumer를 합친 형태
```dart
class HomePage extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final count = ref.watch(counterProvider);

    return Scaffold(
      body: Center(child: Text('$count')),
    );
  }
}
```
- 전체 구조가 깔끔해짐
- ref가 바로 주입됨
- 대부분의 경우 Consumer보다 더 많이 사용됨


<br>

### Consumer vs ConsumerWidget 차이

| 구분 | **Consumer** | **ConsumerWidget** |
|------|--------------|---------------------|
| **사용 목적** | 특정 작은 영역만 부분적으로 리빌드 | 전체 위젯을 Consumer 기반으로 설계 |
| **빌드 방식** | `builder` 내부에서 ref.watch 사용 | build 메서드에 `WidgetRef` 바로 제공 |
| **코드 구조** | 중첩되면 복잡해질 수 있음 | 코드가 더 깔끔하고 구조적 |
| **성능 최적화** | `child` 파라미터로 리빌드 방지 가능 | 세밀한 리빌드를 위한 child 기능 없음 |
| **권장 사용 케이스** | 화면 일부만 Provider 변화에 반응해야 할 때 | 화면 전체가 Provider에 강하게 의존할 때 |
| **장점** | 원하는 부분만 감싸는 유연함 | StatelessWidget처럼 직관적이고 깔끔함 |
| **단점** | builder 중첩 시 가독성 저하 | 부분 최적화(child) 불가 |
| **실무 사용 빈도** | 특정 영역 최적화 시 사용 | 대부분의 화면 기본 방식으로 더 많이 사용됨 |


<br>

### 주의할 점
1. 너무 많이 중첩 사용하면 코드가 난잡해짐 -> 구조적 설계가 더 중요
2. ref.watch를 build 밖에서 사용 금지 -> watch는 항상 build 내부에서만 가능해야 함.
3. 가능한 경우 ConsumerWidget 사용 -> 위젯 구조가 깔끔해지고 테스트하기도 편해짐

<br>

### 요약
- Consumer = 필요한 부분만 build -> 성능 최적화
- ConsumerWidget = Consumer + StatelessWidget
- child 파라미터로 리빌드 방지 가능
- Riverpod의 핵심 개념은 정확히 필요한 곳만 watch하는 것
