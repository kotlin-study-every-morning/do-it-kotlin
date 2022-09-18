### DSL(domain-specific language)이란?

- 도메인을 위해 특화된 언어
    - sql

### DSL 경험해보기

```kotlin
fun main() {
    person { 
        name = "tnswh";
    }
}

data class Person(
    var name: String? = null,
    var age: Int? = null,
    var job: Job? = null
)

data class Job(
    var category: String? = null,
    var position: String? = null,
    var extension: Int? = null
)

// 이런경우 this를 써야함
//fun person(block: (Person) -> Unit): Person {
//    val p = Person()
//    block(p)
//    return p
//}

fun person(block: Person.() -> Unit): Person {
    val p = Person()
    block(p)
    return p
}
```

### DSL 활용한 사례

- Spring
- Ktor