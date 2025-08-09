## 7. 연산자 오버로딩과 기타 관례

### 7-1. 연산자 오버로딩의 기본 개념

코틀린에서는 특정한 이름의 함수를 정의하여 연산자를 오버로딩할 수 있다. 이를 **관례(conventions)**라고 부른다.

자바에서는 특정 클래스에 제한된 언어 기능들이 있지만, 코틀린에서는 특정 이름의 함수를 정의하면 연산자를 사용할 수 있다.

```kotlin
data class Point(val x: Int, val y: Int) {
    operator fun plus(other: Point): Point {
        return Point(x + other.x, y + other.y)
    }
}

val p1 = Point(10, 20)
val p2 = Point(30, 40)
println(p1 + p2) // Point(x=40, y=60)
```

### 7-2. 산술 연산자 오버로딩

#### 이항 산술 연산자

| 식 | 함수 이름 |
|---|---|
| a + b | a.plus(b) |
| a - b | a.minus(b) |
| a * b | a.times(b) |
| a / b | a.div(b) |
| a % b | a.rem(b) |

```kotlin
data class Money(val amount: Int) {
    operator fun plus(other: Money) = Money(amount + other.amount)
    operator fun times(scale: Int) = Money(amount * scale)
}

val money1 = Money(1000)
val money2 = Money(2000)
println(money1 + money2) // Money(amount=3000)
println(money1 * 3) // Money(amount=3000)
```

#### 복합 대입 연산자

코틀린은 +=, -=, *=, /=, %= 연산자를 지원한다.

```kotlin
var point = Point(1, 2)
point += Point(3, 4) // point = point + Point(3, 4)와 같음
println(point) // Point(x=4, y=6)
```

두 가지 방식이 있다:
- `plus`와 `plusAssign`을 모두 정의하면 컴파일 오류
- 불변 클래스는 `plus` 함수만 제공
- 가변 클래스는 `plusAssign` 함수 제공

#### 단항 연산자

| 식 | 함수 이름 |
|---|---|
| +a | a.unaryPlus() |
| -a | a.unaryMinus() |
| !a | a.not() |
| ++a, a++ | a.inc() |
| --a, a-- | a.dec() |

```kotlin
operator fun Point.unaryMinus() = Point(-x, -y)
val point = Point(10, 20)
println(-point) // Point(x=-10, y=-20)
```

### 7-3. 비교 연산자 오버로딩

#### 동등성 연산자: equals

`==`와 `!=`는 내부적으로 `equals` 함수를 호출한다.

```kotlin
class Person(val firstName: String, val lastName: String) {
    override fun equals(obj: Any?): Boolean {
        val other = obj as? Person ?: return false
        return other.firstName == firstName && other.lastName == lastName
    }
    
    override fun hashCode(): Int = firstName.hashCode() * 37 + lastName.hashCode()
}
```

#### 순서 연산자: compareTo

`<`, `>`, `<=`, `>=`는 `compareTo` 함수를 호출한다.

```kotlin
class Person(val firstName: String, val lastName: String) : Comparable<Person> {
    override fun compareTo(other: Person): Int {
        return compareValuesBy(this, other, Person::lastName, Person::firstName)
    }
}

val p1 = Person("Alice", "Smith")
val p2 = Person("Bob", "Johnson")
println(p1 < p2) // compareValuesBy 결과에 따름
```

### 7-4. 컬렉션과 범위에 대한 관례

#### in 관례

`in` 연산자는 `contains` 함수를 호출한다.

```kotlin
data class Rectangle(val upperLeft: Point, val lowerRight: Point)

operator fun Rectangle.contains(p: Point): Boolean {
    return p.x in upperLeft.x until lowerRight.x &&
           p.y in upperLeft.y until lowerRight.y
}

val rect = Rectangle(Point(10, 20), Point(50, 50))
println(Point(20, 30) in rect) // true
```

#### rangeTo 관례

`..` 연산자는 `rangeTo` 함수를 호출한다.

```kotlin
val now = LocalDate.now()
val vacation = now..now.plusDays(10) // rangeTo 호출
println(now.plusWeeks(1) in vacation) // true
```

#### get과 set 관례

`[]` 연산자는 `get`과 `set` 함수를 호출한다.

```kotlin
data class MutablePoint(var x: Int, var y: Int)

operator fun MutablePoint.get(index: Int): Int {
    return when(index) {
        0 -> x
        1 -> y
        else -> throw IndexOutOfBoundsException("Invalid coordinate $index")
    }
}

operator fun MutablePoint.set(index: Int, value: Int) {
    when(index) {
        0 -> x = value
        1 -> y = value
        else -> throw IndexOutOfBoundsException("Invalid coordinate $index")
    }
}

val p = MutablePoint(10, 20)
println(p[1]) // 20
p[1] = 42
println(p) // MutablePoint(x=10, y=42)
```

### 7-5. 구조 분해 선언과 component 함수

구조 분해 선언을 사용하면 복합적인 값을 분해해서 여러 변수에 한꺼번에 초기화할 수 있다.

```kotlin
val p = Point(10, 20)
val (x, y) = p // 구조 분해 선언
println("x=$x, y=$y") // x=10, y=20
```

내부적으로 `componentN` 함수를 호출한다:

```kotlin
val (x, y) = p
// 다음과 같이 컴파일됨
val x = p.component1()
val y = p.component2()
```

#### 데이터 클래스와 구조 분해 선언

데이터 클래스는 주 생성자에 들어있는 프로퍼티에 대해 `componentN` 함수를 자동으로 생성한다.

```kotlin
data class NameComponents(val name: String, val extension: String)

fun splitFilename(fullName: String): NameComponents {
    val result = fullName.split('.', limit = 2)
    return NameComponents(result[0], result[1])
}

val (name, ext) = splitFilename("example.kt")
println("Name: $name, Extension: $ext") // Name: example, Extension: kt
```

#### 구조 분해 선언과 루프

맵의 원소에 대해 이터레이션할 때 유용하다:

```kotlin
fun printEntries(map: Map<String, String>) {
    for ((key, value) in map) {
        println("$key -> $value")
    }
}
```

### 7-6. 프로퍼티 접근자 로직 재활용: 위임 프로퍼티

위임 프로퍼티를 사용하면 값을 뒷받침하는 필드에 단순히 저장하는 것보다 더 복잡한 방식으로 작동하는 프로퍼티를 쉽게 구현할 수 있다.

#### 위임 프로퍼티의 기본 개념

```kotlin
class Foo {
    var p: Type by Delegate()
}
```

`by` 키워드는 다음 객체를 위임 객체로 사용한다는 뜻이다.

내부적으로 다음과 같이 컴파일된다:

```kotlin
class Foo {
    private val delegate = Delegate()
    var p: Type
        get() = delegate.getValue(this, ::p)
        set(value: Type) = delegate.setValue(this, ::p, value)
}
```

#### lazy 위임 프로퍼티

`lazy` 함수는 기본적으로 스레드 안전하다:

```kotlin
class Person(val name: String) {
    val emails by lazy { loadEmails(this) }
}
```

#### 위임 프로퍼티 구현

```kotlin
import kotlin.reflect.KProperty

class ObservableProperty(
    var propValue: Int, 
    val changeSupport: PropertyChangeSupport
) {
    operator fun getValue(p: Person, prop: KProperty<*>): Int = propValue
    
    operator fun setValue(p: Person, prop: KProperty<*>, newValue: Int) {
        val oldValue = propValue
        propValue = newValue
        changeSupport.firePropertyChange(prop.name, oldValue, newValue)
    }
}
```

#### 맵을 위임 프로퍼티로 사용

```kotlin
class Person {
    private val _attributes = hashMapOf<String, String>()
    
    fun setAttribute(attrName: String, value: String) {
        _attributes[attrName] = value
    }
    
    val name: String by _attributes
}
```

---

## 8. 고차 함수: 파라미터와 반환 값으로 람다 사용

### 8-1. 고차 함수 정의

고차 함수는 다른 함수를 인자로 받거나 함수를 반환하는 함수다.

#### 함수 타입

```kotlin
val sum: (Int, Int) -> Int = { x, y -> x + y }
val action: () -> Unit = { println(42) }
```

함수 타입 문법:
- `(파라미터 타입들) -> 반환 타입`
- 파라미터가 없다면 `() -> 반환 타입`
- Unit 타입도 명시해야 함

#### 인자로 받은 함수 호출

```kotlin
fun twoAndThree(operation: (Int, Int) -> Int) {
    val result = operation(2, 3)
    println("The result is $result")
}

twoAndThree { a, b -> a + b } // The result is 5
twoAndThree { a, b -> a * b } // The result is 6
```

#### 자바에서 코틀린 함수 타입 사용

코틀린의 함수 타입은 일반 인터페이스로 컴파일된다. `FunctionN` 인터페이스를 구현하는 객체를 저장한다.

```java
// Java
processTheAnswer(number -> number + 1);
```

#### 디폴트 값을 지정한 함수 타입 파라미터

```kotlin
fun <T> Collection<T>.joinToString(
    separator: String = ", ",
    prefix: String = "",
    postfix: String = "",
    transform: (T) -> String = { it.toString() }
): String {
    val result = StringBuilder(prefix)
    for ((index, element) in this.withIndex()) {
        if (index > 0) result.append(separator)
        result.append(transform(element))
    }
    result.append(postfix)
    return result.toString()
}

val letters = listOf("Alpha", "Beta")
println(letters.joinToString()) // Alpha, Beta
println(letters.joinToString { it.toLowerCase() }) // alpha, beta
```

#### 함수를 함수에서 반환

```kotlin
enum class Delivery { STANDARD, EXPEDITED }

class Order(val itemCount: Int)

fun getShippingCostCalculator(delivery: Delivery): (Order) -> Double {
    if (delivery == Delivery.EXPEDITED) {
        return { order -> 6 + 2.1 * order.itemCount }
    }
    return { order -> 1.2 * order.itemCount }
}

val calculator = getShippingCostCalculator(Delivery.EXPEDITED)
println("Shipping costs ${calculator(Order(3))}")
```

#### 람다를 활용한 중복 제거

```kotlin
data class SiteVisit(
    val path: String,
    val duration: Double,
    val os: OS
)

enum class OS { WINDOWS, LINUX, MAC, IOS, ANDROID }

val log = listOf(
    SiteVisit("/", 34.0, OS.WINDOWS),
    SiteVisit("/", 22.3, OS.MAC),
    SiteVisit("/login", 12.4, OS.WINDOWS),
    SiteVisit("/signup", 8.0, OS.IOS),
    SiteVisit("/", 16.3, OS.ANDROID)
)

// 일반적인 함수
fun List<SiteVisit>.averageDurationFor(os: OS) =
    filter { it.os == os }.map(SiteVisit::duration).average()

// 고차 함수로 더 유연하게
fun List<SiteVisit>.averageDurationFor(predicate: (SiteVisit) -> Boolean) =
    filter(predicate).map(SiteVisit::duration).average()

println(log.averageDurationFor { it.os in setOf(OS.ANDROID, OS.IOS) })
println(log.averageDurationFor { it.os == OS.IOS && it.path == "/signup" })
```

### 8-2. 인라인 함수: 람다의 부가 비용 없애기

람다가 변수를 포획하면 람다가 생성되는 시점마다 새로운 무명 클래스 객체가 생긴다. 이런 부가 비용을 없애기 위해 `inline` 변경자를 함수에 붙일 수 있다.

#### inline 함수

```kotlin
inline fun <T> synchronized(lock: Lock, action: () -> T): T {
    lock.lock()
    try {
        return action()
    } finally {
        lock.unlock()
    }
}

val l = Lock()
synchronized(l) {
    // ...
}
```

위 코드는 다음과 같이 컴파일된다:

```kotlin
l.lock()
try {
    // 람다 본문이 인라이닝됨
} finally {
    l.unlock()
}
```

#### 인라인 함수의 한계

인라인 함수에서 람다를 다른 변수에 저장하고 나중에 그 변수를 사용할 수는 없다.

```kotlin
// 컴파일 오류
inline fun <T, R> Sequence<T>.map(transform: (T) -> R): Sequence<R> {
    return TransformingSequence(this, transform) // 람다를 저장할 수 없음
}
```

#### noinline 변경자

인라인 함수에 넘기는 람다 중에서 일부를 인라이닝하고 싶지 않을 때 사용한다.

```kotlin
inline fun foo(inlined: () -> Unit, noinline notInlined: () -> Unit) {
    // ...
}
```

#### 컬렉션 연산 인라이닝

```kotlin
data class Person(val name: String, val age: Int)

val people = listOf(Person("Alice", 29), Person("Bob", 31))
println(people.filter { it.age < 30 })
```

`filter`와 `map`은 인라인 함수다. 따라서 그 바이트코드는 일반적인 명령문만큼 효율적이다.

하지만 `asSequence`를 사용하면 중간 리스트를 만들지 않는다:

```kotlin
println(people.asSequence().filter { it.age < 30 }.map { it.name }.toList())
```

#### 함수를 인라인으로 선언해야 하는 경우

- 람다를 인자로 받는 함수만 성능이 좋아질 가능성이 높다
- 일반 함수 호출의 경우 JVM이 이미 강력하게 인라이닝을 지원한다
- 코드 크기가 증가할 수 있으므로 주의해야 한다

### 8-3. 고차 함수 안에서 흐름 제어

#### 람다 안의 return문: 람다를 둘러싼 함수로부터 반환

```kotlin
data class Person(val name: String, val age: Int)
val people = listOf(Person("Alice", 29), Person("Bob", 31))

fun lookForAlice(people: List<Person>) {
    people.forEach {
        if (it.name == "Alice") {
            println("Found!")
            return // lookForAlice 함수에서 반환
        }
    }
    println("Alice is not found")
}
```

#### 람다로부터 반환: 레이블을 사용한 return

람다 식에서만 반환하고 싶다면 레이블을 사용한다:

```kotlin
fun lookForAlice(people: List<Person>) {
    people.forEach label@{
        if (it.name == "Alice") return@label
    }
    println("Alice might be somewhere") // 항상 이 줄이 출력됨
}

// 함수 이름을 레이블로 사용
fun lookForAlice(people: List<Person>) {
    people.forEach {
        if (it.name == "Alice") return@forEach
    }
    println("Alice might be somewhere")
}
```

#### 무명 함수: 기본적으로 로컬 return

무명 함수는 코드 블록을 함수에 넘길 때 사용할 수 있는 다른 방법이다:

```kotlin
fun lookForAlice(people: List<Person>) {
    people.forEach(fun (person) {
        if (person.name == "Alice") return // 무명 함수에서 반환
        println("${person.name} is not Alice")
    })
}
```

무명 함수 안에서 레이블이 붙지 않은 return 식은 무명 함수 자체를 반환시킬 뿐 무명 함수를 둘러싼 다른 함수를 반환시키지 않는다.

```kotlin
people.filter(fun (person): Boolean {
    return person.age < 30
})
```

---

## 9. 제네릭스

### 9-1. 제네릭 타입 파라미터

제네릭스를 사용하면 타입 파라미터를 받는 타입을 정의할 수 있다.

#### 제네릭 함수와 프로퍼티

```kotlin
fun <T> List<T>.slice(indices: IntRange): List<T> {
    // 구현
}

val letters = ('a'..'z').toList()
println(letters.slice<Char>(0..2)) // 타입 인자를 명시적으로 지정
println(letters.slice(10..13)) // 컴파일러가 T가 Char임을 추론
```

제네릭 확장 프로퍼티:

```kotlin
val <T> List<T>.penultimate: T // 끝에서 두 번째 원소
    get() = this[size - 2]

println(listOf(1, 2, 3, 4).penultimate) // 3
```

#### 제네릭 클래스 선언

```kotlin
interface List<T> {
    operator fun get(index: Int): T
    // ...
}

class StringList : List<String> {
    override fun get(index: Int): String = ...
}

class ArrayList<T> : List<T> {
    override fun get(index: Int): T = ...
}
```

#### 타입 파라미터 제약

타입 파라미터 제약은 클래스나 함수에 사용할 수 있는 타입 인자를 제한하는 기능이다.

```kotlin
fun <T : Number> oneHalf(value: T): Double {
    return value.toDouble() / 2.0 // Number 클래스에 정의된 메서드 호출
}

println(oneHalf(3)) // 1.5
```

드물게 타입 파라미터에 여러 제약을 가해야 하는 경우:

```kotlin
fun <T> ensureTrailingPeriod(seq: T)
        where T : CharSequence, T : Appendable {
    if (!seq.endsWith('.')) {
        seq.append('.')
    }
}

val helloWorld = StringBuilder("Hello World")
ensureTrailingPeriod(helloWorld)
println(helloWorld) // Hello World.
```

#### 타입 파라미터를 널이 될 수 없는 타입으로 한정

```kotlin
class Processor<T : Any> {
    fun process(value: T) {
        value?.hashCode() // value는 널이 될 수 없으므로 안전 호출을 쓸 필요가 없다
    }
}
```

### 9-2. 실행 시 제네릭스: 소거와 실체화

JVM의 제네릭스는 타입 소거를 사용해 구현된다. 즉, 실행 시점에 제네릭 클래스의 인스턴스에 타입 인자 정보가 들어있지 않다.

#### 실행 시점의 제네릭: 타입 검사와 캐스트

```kotlin
val list1: List<String> = listOf("a", "b")
val list2: List<Int> = listOf(1, 2, 3)
```

컴파일러는 두 리스트를 서로 다른 타입으로 인식하지만, 실행 시점에 그 둘은 완전히 같은 타입의 객체다.

타입 소거로 인해 일반적으로는 `is` 검사에서 타입 인자를 쓸 수 없다:

```kotlin
if (value is List<String>) { ... } // 컴파일 오류: Cannot check for instance of erased type
```

#### 실체화한 타입 파라미터를 사용한 함수 선언

코틀린 제네릭 타입의 타입 인자 정보는 실행 시점에 지워진다. 하지만 인라인 함수의 타입 파라미터는 실체화되므로 실행 시점에 인라인 함수의 타입 인자를 알 수 있다.

```kotlin
inline fun <reified T> isA(value: Any) = value is T

println(isA<String>("abc")) // true
println(isA<String>(123)) // false
```

#### 실체화한 타입 파라미터로 클래스 참조 대신

```kotlin
inline fun <reified T> loadService() = ServiceLoader.load(T::class.java)

val serviceImpl = loadService<ServiceInterface>()
```

#### 실체화한 타입 파라미터의 제약

실체화한 타입 파라미터를 사용할 수 있는 경우:
- 타입 검사와 캐스팅 (`is`, `!is`, `as`, `as?`)
- 코틀린 리플렉션 API (`::class`)
- 코틀린 타입에 대응하는 java.lang.Class 얻기 (`::class.java`)
- 다른 함수를 호출할 때 타입 인자로 사용

하지만 다음과 같은 일은 할 수 없다:
- 타입 파라미터 클래스의 인스턴스 생성하기
- 타입 파라미터 클래스의 동반 객체 메서드 호출하기
- 실체화한 타입 파라미터를 요구하는 함수를 호출하면서 실체화하지 않은 타입 파라미터로 받은 타입을 타입 인자로 넘기기

### 9-3. 변성: 제네릭과 하위 타입

변성 개념은 `List<String>`와 `List<Any>`와 같이 기저 타입이 같고 타입 인자가 다른 여러 타입이 서로 어떤 관계에 있는지 설명하는 개념이다.

#### 변성이 있는 이유: 인자를 함수에 넘기기

```kotlin
fun printContents(list: List<Any>) {
    println(list.joinToString())
}

printContents(listOf("abc", "bac")) // 문자열 리스트를 넘겨도 안전하다
```

이 코드가 안전한 이유는 리스트의 원소를 추가하거나 변경하지 않기 때문이다.

#### 클래스, 타입, 하위 타입

타입과 클래스는 다르다. 예를 들어:
- `String` 클래스는 `String?`, `String` 타입을 만들어낸다
- `List` 클래스는 `List<Int>`, `List<String?>`, `List<List<String>>` 등 무수히 많은 타입을 만들어낸다

하위 타입은 상위 타입이 필요한 모든 곳에 하위 타입의 값을 넘겨도 안전하다는 뜻이다.

#### 공변성: 하위 타입 관계를 유지

`Producer<T>`를 예로 들어보자. A가 B의 하위 타입일 때 `Producer<A>`가 `Producer<B>`의 하위 타입이면 `Producer`는 공변적이다.

```kotlin
interface Producer<out T> {
    fun produce(): T
}
```

타입 파라미터를 공변적으로 만들려면 `out` 키워드를 붙인다:

```kotlin
open class Animal {
    fun feed() { ... }
}

class Herd<out T : Animal> {
    val size: Int get() = ...
    operator fun get(i: Int): T { ... }
}

fun feedAll(animals: Herd<Animal>) {
    for (i in 0 until animals.size) {
        animals[i].feed()
    }
}

class Cat : Animal() {
    fun cleanLitter() { ... }
}

fun takeCareOfCats(cats: Herd<Cat>) {
    for (i in 0 until cats.size) {
        cats[i].cleanLitter()
        feedAll(cats) // 캐스팅을 할 필요가 없다
    }
}
```

#### 반공변성: 뒤바뀐 하위 타입 관계

반공변 클래스의 하위 타입 관계는 공변 클래스의 경우와 반대다.

```kotlin
interface Comparator<in T> {
    fun compare(e1: T, e2: T): Int
}
```

타입 파라미터를 반공변적으로 만들려면 `in` 키워드를 붙인다:

```kotlin
val anyComparator = Comparator<Any> { e1, e2 -> e1.hashCode() - e2.hashCode() }
val strings: List<String> = ...
strings.sortedWith(anyComparator) // 문자열과 Any 객체를 비교하는 Comparator를 사용해 문자열을 비교
```

#### 사용 지점 변성: 타입이 언급되는 지점에서 변성 지정

클래스를 선언하면서 변성을 지정하는 방식을 **선언 지점 변성**이라 한다.

자바에서는 와일드카드 타입을 사용해 **사용 지점 변성**을 표현한다:

```kotlin
fun <T> copyData(source: MutableList<out T>,
                destination: MutableList<T>) {
    for (item in source) {
        destination.add(item)
    }
}
```

#### 스타 프로젝션: 타입 인자 대신 * 사용

제네릭 타입 인자 정보가 없음을 표현하기 위해 **스타 프로젝션**을 사용한다:

```kotlin
fun printFirst(list: List<*>) { // 모든 리스트를 인자로 받을 수 있다
    if (list.isNotEmpty()) {
        println(list.first()) // first()는 Any? 반환
    }
}

printFirst(listOf("Svetlana", "Dmitry"))
```

스타 프로젝션을 사용할 때 그 타입의 값을 만들어내는 메서드만 호출할 수 있고, 그 타입의 값을 받는 메서드는 호출할 수 없다.

```kotlin
fun <T> printFirst(list: List<T>) {
    if (list.isNotEmpty()) {
        println(list.first()) // first()는 T 반환
    }
}
```

---

### 마무리

7장에서는 연산자 오버로딩을 통해 기존 자바 라이브러리를 코틀린에서 더 자연스럽게 사용할 수 있게 하는 방법을 다뤘다. 8장에서는 고차 함수를 통해 코드의 중복을 줄이고 더 나은 추상화를 구축하는 방법을 살펴봤다. 9장에서는 제네릭스를 통해 타입 안전성을 보장하면서도 재사용 가능한 코드를 작성하는 방법을 알아봤다.

이 세 장의 내용은 코틀린의 고급 기능들로, 실무에서 더 효율적이고 안전한 코드를 작성하는 데 도움이 된다.
