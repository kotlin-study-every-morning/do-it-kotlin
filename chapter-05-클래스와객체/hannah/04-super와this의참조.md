## super와 this의 참조

![img.png](static/img1.png)

- 주 생성자와 부 생성자 함께 사용하기
```kotlin
class Person(firstName: String,
             out: Unit = println("[Primary Constructor] Parameter")) { // ② 주 생성자

    val fName = println("[Property] Person fName: $firstName") // ③ 프로퍼티 할당

    init {
        println("[init] Person init block") // ④ 초기화 블록
    }

    // ① 부 생성자
    constructor(firstName: String, age: Int,
                out: Unit = println("[Secondary Constructor] Parameter")): this(firstName) {
        println("[Secondary Constructor] Body: $firstName, $age") // ⑤ 부 생성자 본문
    }
}

fun main() {
    val p1 = Person("Kildong", 30) // ① → ② 호출, ③ → ④ → ⑤ 실행
    println()
    val p2 = Person("Dooly") // ② 호출, ③ → ④ 실행
    
    // [Secondary Constructor] Parameter
    // [Primary Constructor] Parameter
    // [Property] Person fName: Kildong
    // [init] Person init block
    // [Secondary Constructor] Body: Kildong, 30
    
    // [Primary Constructor] Parameter
    // [Property] Person fName: Dooly
    // [init] Person init block
}
```

### 바깥 클래스 호출하기 (중첩 클래스)
```kotlin
open class Base {
    open val x: Int = 1
    open fun f() = println("Base Class f()")
}

class Child : Base() {

    override val x: Int = super.x + 1
    override fun f() = println("Child Class f()")

    inner class Inside {
        fun f() = println("Inside Class f()")

        fun test() {
            f() // ① 현재 이너 클래스의 f() 접근

            Child().f() // ② 바로 바깥 클래스 f() 접근

            super@Child.f() // ③ Child의 상위 클래스인 Base 클래스의 f() 접근

            println("[Inside] super@Child.x: ${super@Child.x}") // ④ Base의 x 접근
        }
    }
}

fun main() {
    val c1 = Child()
    c1.Inside().test() // 이너 클래스 Inside의 메서드 test() 실행
}
```

### 인터페이스에서 참조하기
- 코틀린은 자바처럼 한 번에 2개 이상의 클래스를 상속받는 다중 상속이 되지 않는다
```kotlin
open class A {
    open fun f() = println("A Class f()")
    fun a() = println("A Class a()")
}

interface B {
    fun f() = println("B Interface f()") // 인터페이스는 기본적으로 open임
    fun b() = println("B Interface b()")
}

class C : A(), B { // ① 쉼표(,)를 사용해 클래스와 인터페이스를 지정
    
    // 컴파일되려면 f()가 오버라이딩되어야 함
    override fun f() = println("C Class f()")
    
    fun test() {
        f() // ② 현재 클래스의 f()
        b() // ③ 인터페이스 B의 b()
        super<A>.f() // ④ A 클래스의 f()
        super<B>.f() // ⑤ B 클래스의 f()
    }
}

fun main() {
    val c = C()
    c.test()
    
    // C Class f()
    // B Interface b()
    // A Class f()
    // B Interface f()
}
```