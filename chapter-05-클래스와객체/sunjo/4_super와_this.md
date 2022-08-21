### super와 this

super

상위 클래스의 프로퍼티나 메서드 참조

this

현재 클래스의 프로퍼티나 메서드 참조

### super로 상위 객체 참조

```kotlin
class Parrot(name: String, wing: Int = 2, beak: String, color: String,
	var language: String = "natural") : Bird(name, wing, beak, color) {
	fun speak( ) = println("Speak! $language")
	override fun sing(vol: Int) { // 1 상위 클래스의 내용과 다르게 새로 구현된 내용을 가짐 
		super.sing(vol) // 상위 클래스의 sing( )을 먼저 수행
		println("I'm a parrot! The volume level is $vol")
		speak( )
	} 
}
```

### this로 현재 객체 참조

```kotlin
open class Person { 
	constructor(firstName: String) {
	}
	constructor(firstName: String, age: Int) { // 3

	} 
}

class Developer: Person {
	constructor(firstName: String): this(firstName, 10) { // 1
		println("[Developer] $firstName") 
	}
	constructor(firstName: String, age: Int): super(firstName, age) { // 2 
		println("[Developer] $firstName, $age")
	} 
}
fun main( ) {
	val sean = Developer("Sean")
}
```

Developer(”Sean”)을 부르면서 1 번을 부름

→ this(firstName, 10) 으로 2 번을 부름

→ super(firstName, age) 로 3번을 부름

결국 실행 순서는 3 → 2 → 1

### 주 생성자와 부 생성자 함께 사용하기

```kotlin
class Person(
    firstName: String,
    out: Unit = println("[Primary Constructor] Parameter")
) { // 2 주 생성자
    val fName = println("[Property] Person fName: $firstName") // 3 프로퍼티 할당

    init {
        println("[init] Person init block") // 4 초기화 블록
    }

    // 1 부 생성자
    constructor(
        firstName: String, age: Int,
        out: Unit = println("[Secondary Constructor] Parameter")
    ) : this(firstName) {
        println("[Secondary Constructor] Body: $firstName, $age") // 5 부 생성자 본문
    }
}

fun main() {
	val p1 = Person("hello", 10) // A
	val p2 = Person("hello2") // B
}

[Secondary Constructor] Parameter
[Primary Constructor] Parameter
[Property] Person fName: hello
[init] Person init block
[Secondary Constructor] Body: hello, 10

[Primary Constructor] Parameter
[Property] Person fName: hello2
[init] Person init block
```

### A (?? 내가 이해한게 맞나?)

1 → this(firstName)를 통해서 2를 호출 → 3 → 4 → 5 실행

```kotlin
처음 out을 호출
[Secondary Constructor] Parameter
this(firstName)을 통해 2를 호출하면서 out 호출
[Primary Constructor] Parameter
fName 할당
[Property] Person fName: hello
init 실행
[init] Person init block
[Secondary Constructor] Body: hello, 10
```

### 바깥 클래스 호출하기

- 클래스 안에 있는 클래스(이너 클래스)에서 바깥에 있는 클래스를 참조하기

```kotlin
open class Base {
    open val x: Int = 1
    open fun f( ) = println("Base Class f( )")
}
class Child : Base( ) {
    override val x: Int = super.x + 1
    override fun f( ) = println("Child Class f( )")
    inner class Inside {
        fun f() = println("inside class f()")
        fun test() {
            f()
            Child().f()
            super@Child.f()
            println("[inside] super@child.x : ${super@Child.x}")
        }
    }
}

fun main() {
	val c1 = Child()
	c1.Inside.test()
}
```

```kotlin
// 결과
Inside Class f( )
Child Class f( )
Base Class f( )
[Inside] super@Child.x: 1
```

### 인터페이스에서 참조하기

- 코틀린은 자바처럼 한 번에 2개 이상의 클래스에서 상속 받는 것이 불가능
- 하지만 여러개의 인터페이스를 상속 받는것이 가능
    - 만약 프로퍼티나 메서드의 이름이 중복될 경우 <> 를 사용하여 구분


```kotlin
open class A {
    open fun f() = println("A Class f( )")
    fun a() = println("A Class a( )")
}

interface B {
    fun f( ) = println("B Interface f( )") // 인터페이스는 기본적으로 open임
    fun b( ) = println("B Interface b( )")
}

class C : A(), B {
    override fun f() = println("C Class f( )")
    fun test() {
        f() // 2 현재 클래스의 f( )
        b() // 3 인터페이스 B의 b( )
        super<A>.f( )
        super<B>.f( )
    }
}
```