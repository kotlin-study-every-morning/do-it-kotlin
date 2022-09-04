코틀린에서는 stream으로 변환하지도 않고 다시 list로 변환하는 코드도 없다.
코틀린에서는 list에서 바로 filter, map 등을 쓸 수 있다. ⇒ 그냥 추가된 확장 함수들이다.
하지만 코틀린에서 collection에 추가한 확장 함수들은 기존 stream과 다른점이 있다.
⇒ 최적화 기술인 lazy, short circuit 등이 적용되지 않았다.

### stream vs collection

- 스트림은 연속된 자료들을 다루고 연산하는 API (체이닝 메서드)
- 컬렉션과 다르게 요소 추가 삭제가 불가능
- 컬렉션은 연산이 복잡해질 수록 반복문이 계속 발생, 저장 변수들이 계속 발생

**Lazy & ShortCircuit**

- stream을 최적화 해주는 것들이다.
- stream은 lazy 이다. ⇒ 최종연산이 존재하지 않으면 중간연산은 실행되지 않는다.
- shortCircuit ⇒ 모든 연산을 다 하지 않고 중간에 false가 나오면 바로 return 해버린다.

```java
List<String> list = Arrays.asList("a", "b", "c");
boolean b = list.stream().allMatch(str -> {   
	System.out.println(str);   return str.equals("d");
});
```

### 코틀린 컬렉션

```kotlin
listOf(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
            .filter {
                println(it)
                it % 2 == 0
            }
            .find { it == 2 }
```

만약 java에서 stream을 사용한다면 2만 출력하는 것이므로 shortCircuit 최적화를 이용해서 1, 2까지만 돌것이다. 하지만 컬렉션은 모두 돌게된다 ⇒ 간단한것만 사용하는게 좋을듯?

### sequence

- sequence는 java의 stream과 거의 동일하다. ⇒ 왜 따로 있냐? 코틀린은 java8이전 버전도 지원하기 때문에
- 가장 큰 차이점은 stream은 손쉬운 병렬 처리가 가능하다.