### Dispatcher와 threads

- 코루틴 context에는 해당 코루틴의 실행에 사용하는 스레드 또는 스레드를 결정하는 코루틴 dispatcher가 있음 ⇒ dispatcher가 스레드를 제한, 스레드 풀로 보냄, 실행 등을 수행하게 됨

newSingleThreadContext

- 코루틴을 실행할 때 새로운 스레드를 생성
- 스레드는 비싼 자원이기 떄문에 → close()를 사용해서 해제 or 변수에 저장한 후 전체에 걸쳐 사용

### Unconfied vs confied dispatcher

Unconfied

- Dispatchers.Unconfied 라는 코루틴 dispather는 호출한 스레드에서 코루틴을 시작하지만 첫 번째 suspension 지점까지만 시작
- suspension이 끝나면 호출된 suspension 함수에 의해 다른 스레드로 코루틴을 재개
- CPU의 시간을 소비하지도 않고 특정 스레드에 제한된 공유 데이터(UI 같은)를 업데이트하지도 않는 코루틴에는 unconfined dispatcher가 적합

confied

- 외부 CoroutineScope로부터 dispather를 상속
- runBlocking 코루틴의 기본 dispather는 호출한 스레드에 제한되므로 이를 상속받으면 예측 가능한 FIFO 스케줄링을 통해 이 스레드의 실행을 제한할 수 있는 효과

```kotlin
fun main() = runBlocking<Unit> {
    launch { // 부모의 context. main runblocking context입니다.
        println("main runBlocking      : I'm working in thread ${Thread.currentThread().name}")
    }
    launch(Dispatchers.Unconfined) { // not confined -- 메인 스레드에서 동작합니다.
        println("Unconfined            : I'm working in thread ${Thread.currentThread().name}")
    }
    launch(Dispatchers.Default) { // DefaultDispatcher로 작업이 보내집니다.
        println("Default               : I'm working in thread ${Thread.currentThread().name}")
    }
    launch(newSingleThreadContext("MyOwnThread")) { // 새로운 스레드로 작업이 전달됩니다.
        println("newSingleThreadContext: I'm working in thread ${Thread.currentThread().name}")
    }
}

// Unconfined            : I'm working in thread main
// Default               : I'm working in thread DefaultDispatcher-worker-1
// main runBlocking      : I'm working in thread main
// newSingleThreadContext: I'm working in thread MyOwnThread
```

- unconfined 스레드는 함수가 사용 중인 기본 executor 스레드에서 다시 실행되는 것을 확인

```kotlin
unconfined dispatcher는 코루틴의 일부 연산을 즉시 수행해야하기 때문에 
나중에 실행할 필요가 없거나 side-effect가 발생하는 특정 사례에서 도움이 될 수 있는 메커니즘입니다. 
unconfined dispatcher는 일반 코드에서 사용해서는 안 됩니다.
```

### coroutine의 자식

- CoroutineScope에서 실행되면 CoroutineScope.context를 통해서 context를 이어받으면 새로운 코루틴의 Job은 부모 코루틴의 Job 됨
- 부모 코루틴이 취소되면 모든 하위 자식 코루틴들도 재귀적으로 취소
- 그러나 GlobalScope 가 코루틴에서 launch될 때 새로운 코루틴의 작업에 대한 부모는 없습니다. 따라서 이것은 그것이 launched된 범위에 얽매이지 않고 독립적으로 실행

```kotlin
fun main() = runBlocking<Unit> {
    // 어떤 종류의 요청을 처리하기 위해 코루틴 launch
    val request = launch {
        // GlobalScope와 함께 두 개의 다른 job을 생성합니다.
        GlobalScope.launch {
            println("job1: I run in GlobalScope and execute independently!")
            delay(1000)
            println("job1: I am not affected by cancellation of the request")
        }
        // 그리고 다른 하나는 상위 context를 상속받습니다.
        launch {
            delay(100)
            println("job2: I am a child of the request coroutine")
            delay(1000)
            println("job2: I will not execute this line if my parent request is cancelled")
        }
    }
    delay(500)
    request.cancel() // request 취소 처리.
    delay(1000) // 어떤 일이 발생하는지 보려고 딜레이
    println("main: Who has survived request cancellation?")
}

// job1: I run in GlobalScope and execute independently!
// job2: I am a child of the request coroutine
// job1: I am not affected by cancellation of the request
// main: Who has survived request cancellation?
```

- 부모 코루틴은 항상 모든 자식 코루틴들의 완료를 기다림 - Job.join()

### 코루틴 scope

```kotlin
class Activity {
    private val mainScope = CoroutineScope(Dispatchers.Default) // 테스트를 위해 Default 사용
    
    fun destroy() {
        mainScope.cancel()
    }

    fun doSomething() {
        repeat(10) { i ->
            mainScope.launch {
                delay((i + 1) * 200L) 
                println("Coroutine $i is done")
            }
        }
    }
} // class Activity 끝

fun main() = runBlocking<Unit> {
    val activity = Activity()
    activity.doSomething() // 테스트 함수 실행
    println("Launched coroutines")
    delay(500L) 
    println("Destroying activity!")
    activity.destroy() 
    delay(1000) // 더 이상 작동하지 않는다.
}

// Launched coroutines
// Coroutine 0 is done
// Coroutine 1 is done
// Destroying activity!
```

- job.cancel() 한 번의 호출로 취소

출처 - https://medium.com/hongbeomi-dev/코틀린의-코루틴-4-coroutine-context-and-dispatchers-1eab8f175428