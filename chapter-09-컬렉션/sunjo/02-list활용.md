### 불변형 list

list 선언하기

```kotlin
val listOf = listOf<String>()
val emptyList = emptyList<String>() // 빈배열 선언
val listOf1 = listOf("h", "e")
val listOf2 = listOf("h", "e", 1) // <Any> 로 선언됨
```

indices (인덱스 지정)

```kotlin
for (index in listOf1.indices) {
        print(index)
}
```

listOfNotNull() (null 인것 제외)

```kotlin
val nonNullsList: List<Int> = listOfNotNull(2, 45, 2, null, 5, null)
```

### 가변형 list

arrayListOf

```kotlin
val arrayListOf = arrayListOf("hello", "world")
```

- 가변형은 add, remove 등 변형 가능
- 컴파일 할 때 반환되는 자료형은 ArrayList

mutableListOf

```kotlin
val mutableListOf = mutableListOf("hello", "world")
```

- 가변형은 add, remove 등 변형 가능
- List를 가변형으로 변경하려면 toMutableList를 사용하면 List 그대로 두고 새로운 공간 만듬

### List와 배열의 차이

- Array 클래스로 생성되는 객체는 고정된 메모리 크기를 가지고 있음
- List<T>와 Array<T>는 고정된 것이 아님
    - Array<T>는 상 하위 자료형 관계가 성립되지 않음 - 무변성
    - List<T>는 상 하위 관계가 성립됨 - 공변성