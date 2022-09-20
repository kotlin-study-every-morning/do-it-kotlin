### Flow

- 채널은 우리가 수신을 하기 전에도 데이터를 보내기 시작한다. 이런 특성 때문에 채널을 **Hot**
  이라고 부른다.
- 반면에 우리가 원하는 시점에 데이터를 가져오는 도구는 **Cold** 라고 말한다. Flow는 cold 도구이다.

```kotlin
fun main() {
    val flow: Flow<Int> = flow {
        repeat(5) {
            emit(20 + it)
        }
    }

    GlobalScope.launch {
        flow.collect {
            t -> println("t $t")
        }
    }
}
```

- flow는 emit을 수행
- collect를 호출하기 전까지 수행되지 않음 ⇒ cold

```kotlin
fun main() {
    val flow: Flow<Int> = flow {
        repeat(5) {
            emit(20 + it)
        }
    }.flowOn(Dispatchers.IO)

    GlobalScope.launch {
        flow.map { it * 2}.collect {
            t -> println("t $t")
        }
    }
}
```

- flow 안에서 suspend나 delay 등을 호출 가능하지만 코루틴 빌더를 호출해서 디스패처를 변경 불가능
- 스레드를 변경하고 싶다면 flowOn을 사용
- flow를 맵을 사용하여 값을 변경 가능

```kotlin
fun main() {
    val flow: Flow<Int> = flow {
        repeat(5) {
            emit(20 + it)
        }
    }.catch { 
        ...
    }
}
```

- catch 오퍼레이터를 사용 가능