### set

```kotlin
val setOf = setOf("1", "2")  // 불변형
val setOf2 = mutableSetOf("1", "2") // 가변형
```

hashSetOf()

```kotlin
val hashSetOf = hashSetOf("1", "2")
```

- 해시 테이블에 요소를 저장할 수 있는 컬렉션
- 불변성이 없음
- 검색속도가 O(1)로 빠름

sortedSetOf()

```kotlin
val sortedSetOf = sortedSetOf(2, 1, 5)
```

- TreeSet 컬렉션을 정렬된 상태로 반환
- 검색과 정렬에 뛰어남

linkedSetOf()

```kotlin
val linkedSetOf = linkedSetOf(1, 2, 3)
```

- linkedList로 구현
- 다음 데이터를 포인터로 연결하여 저장 공간이 효율적

### Map

```kotlin
val mapOf = mapOf(11 to "java")  // 불변
val mutableMapOf = mutableMapOf(11 to "java") // 가변
```

hashmap

```kotlin
val hashMapOf = hashMapOf(11 to "java")
val sortedMapOf = sortedMapOf(11 to "java")
val linkedMapOf = linkedMapOf(11 to "java")
```