## 4. 클래스, 객체, 인터페이스

### 4-1. 클래스 계층 정의

코틀린의 클래스와 인터페이스는 자바와 조금 다르다. 예를 들어 인터페이스에 프로퍼티 선언이 포함될 수 있다. 자바와 달리 코틀린 선언은 기본적으로 final이며 public이다.

#### 코틀린 인터페이스

코틀린 인터페이스는 자바 8 인터페이스와 비슷하다. 인터페이스 안에는 추상 메서드뿐 아니라 구현이 있는 메서드도 정의할 수 있다.

```kotlin
interface Clickable {
    fun click()
    fun showOff() = println("I'm clickable!") // 디폴트 구현이 있는 메서드
}

class Button : Clickable {
    override fun click() = println("I was clicked")
    // showOff()는 오버라이드하지 않아도 됨
}
```

클래스 이름 뒤에 콜론(:)을 붙이고 인터페이스와 클래스 이름을 적는다. ⇒ 자바와 달리 extends와 implements 키워드를 사용하지 않고 콜론 하나만 사용한다.

인터페이스의 메서드도 디폴트 구현을 제공할 수 있다. 그런 경우 메서드 앞에 default를 붙일 필요가 없다. 그냥 메서드 본문을 메서드 시그니처 뒤에 추가하면 된다.

#### open, final, abstract 변경자

자바에서는 final로 명시적으로 상속을 금지하지 않는 모든 클래스를 다른 클래스가 상속할 수 있다. 하지만 이런 방식은 문제가 있다.

→ 코틀린의 클래스와 메서드는 기본적으로 final이다.

어떤 클래스의 상속을 허용하려면 클래스 앞에 open 변경자를 붙여야 한다. 그와 더불어 오버라이드를 허용하고 싶은 메서드나 프로퍼티의 앞에도 open 변경자를 붙여야 한다.

```kotlin
open class RichButton : Clickable { // 다른 클래스가 이 클래스를 상속할 수 있다
    fun disable() {} // 이 함수는 final이다. 하위 클래스에서 오버라이드할 수 없다
    open fun animate() {} // 이 함수는 열려있다. 하위 클래스에서 오버라이드할 수 있다
    override fun click() {} // 오버라이드한 메서드는 기본적으로 열려있다
}
```

#### 가시성 변경자: 기본적으로 공개

기본적으로 코틀린에서는 아무 변경자도 없는 경우 모든 선언이 public이다.

자바의 기본 가시성인 패키지 전용(package-private)은 코틀린에 없다. 코틀린은 패키지를 네임스페이스를 관리하기 위한 용도로만 사용한다. 그래서 패키지를 가시성 제어에 사용하지 않는다.

대신 코틀린에는 internal이라는 새로운 가시성 변경자가 있다. internal은 "모듈 내부에서만 볼 수 있음"이라는 뜻이다.

- public (기본 가시성): 어디서나 볼 수 있다
- internal: 같은 모듈 안에서만 볼 수 있다  
- protected: 하위 클래스 안에서만 볼 수 있다
- private: 같은 클래스 안에서만 볼 수 있다

### 4-2. 뻔하지 않은 생성자와 프로퍼티를 갖는 클래스 선언

코틀린은 주 생성자(primary constructor)와 부 생성자(secondary constructor)를 구분한다. 또한 코틀린에서는 초기화 블록(initializer block)을 통해 초기화 로직을 추가할 수 있다.

#### 클래스 초기화: 주 생성자와 초기화 블록

```kotlin
class User(val nickname: String)
```

이렇게 클래스 이름 뒤에 오는 괄호로 둘러싸인 코드를 주 생성자라고 부른다.

주 생성자는 생성자 파라미터를 지정하고 그 생성자 파라미터에 의해 초기화되는 프로퍼티를 정의하는 두 가지 목적에 쓰인다.

```kotlin
class User constructor(_nickname: String) { // 주 생성자
    val nickname: String
    
    init { // 초기화 블록
        nickname = _nickname
    }
}
```

constructor 키워드는 주 생성자나 부 생성자 정의를 시작할 때 사용한다. init 키워드는 초기화 블록을 시작한다.

#### 부 생성자: 상위 클래스를 다른 방식으로 초기화

```kotlin
open class View {
    constructor(ctx: Context) {
        // 코드
    }
    
    constructor(ctx: Context, attr: AttributeSet) {
        // 코드
    }
}

class MyButton : View {
    constructor(ctx: Context) : super(ctx) {
        // ...
    }
    
    constructor(ctx: Context, attr: AttributeSet) : super(ctx, attr) {
        // ...
    }
}
```

### 4-3. 컴파일러가 생성한 메서드: 데이터 클래스와 클래스 위임

자바 플랫폼에서는 클래스가 equals, hashCode, toString 등의 메서드를 구현해야 한다. 

#### 모든 클래스가 정의해야 하는 메서드

자바와 마찬가지로 코틀린 클래스도 toString, equals, hashCode 등을 오버라이드할 수 있다.

```kotlin
class Client(val name: String, val postalCode: Int) {
    override fun toString() = "Client(name=$name, postalCode=$postalCode)"
    
    override fun equals(other: Any?): Boolean {
        if (other == null || other !is Client)
            return false
        return name == other.name && postalCode == other.postalCode
    }
    
    override fun hashCode(): Int = name.hashCode() * 31 + postalCode
}
```

#### 데이터 클래스: 모든 클래스가 정의해야 하는 메서드 자동 생성

어떤 클래스가 데이터를 저장하는 역할만을 수행한다면 toString, equals, hashCode를 반드시 오버라이드해야 한다.

→ 코틀린에서는 data라는 변경자를 클래스 앞에 붙이면 필요한 메서드를 컴파일러가 자동으로 만들어준다.

```kotlin
data class Client(val name: String, val postalCode: Int)
```

이제 Client 클래스는 자동으로 다음과 같은 메서드들을 포함한다:
- 인스턴스 간 비교를 위한 equals
- HashMap과 같은 해시 기반 컨테이너에서 키로 사용할 수 있는 hashCode
- 클래스의 각 필드를 선언 순서대로 표시하는 문자열 표현을 만들어주는 toString

데이터 클래스는 copy() 메서드도 제공한다. 객체를 복사하면서 일부 프로퍼티를 바꿀 수 있게 해주는 메서드다.

```kotlin
val lee = Client("이계영", 4122)
println(lee.copy(postalCode = 4000))
// Client(name=이계영, postalCode=4000)
```

#### 클래스 위임: by 키워드 사용

대규모 객체지향 시스템을 설계할 때 시스템을 취약하게 만드는 문제는 보통 구현 상속(implementation inheritance)에 의해 발생한다.

하위 클래스가 상위 클래스의 메서드 중 일부를 오버라이드하면 하위 클래스는 상위 클래스의 세부 구현 사항에 의존하게 된다. → 상위 클래스의 구현이 바뀌거나 상위 클래스에 새로운 메서드가 추가되면 하위 클래스의 코드가 예상과 다르게 동작할 수 있다.

코틀린은 기본적으로 클래스를 final로 취급하여 이 문제를 해결한다. 그리고 종종 상속을 사용할 때 발생하는 문제에 대한 해법으로 decorator 패턴을 사용한다.

이런 위임을 언어가 제공하는 일급 시민 기능으로 지원한다는 점이 코틀린의 장점이다.

```kotlin
class DelegatingCollection<T> : Collection<T> {
    private val innerList = arrayListOf<T>()
    
    override val size: Int get() = innerList.size
    override fun isEmpty(): Boolean = innerList.isEmpty()
    override fun contains(element: T): Boolean = innerList.contains(element)
    override fun iterator(): Iterator<T> = innerList.iterator()
    override fun containsAll(elements: Collection<T>): Boolean = 
        innerList.containsAll(elements)
}
```

이런 위임을 처리하기 위한 준비 메서드를 너무 많이 작성해야 한다는 단점이 있다.

코틀린은 이런 위임을 언어가 제공하는 일급 시민 기능으로 지원한다. 인터페이스를 구현할 때 by 키워드를 통해 그 인터페이스에 대한 구현을 다른 객체에 위임 중이라는 사실을 명시할 수 있다.

```kotlin
class DelegatingCollection<T>(
    innerList: Collection<T> = ArrayList<T>()
) : Collection<T> by innerList {}
```

### 4-4. object 키워드: 클래스 선언과 인스턴스 생성

코틀린에서는 object 키워드를 다양한 상황에서 사용하지만 모든 경우 클래스를 정의하면서 동시에 인스턴스(객체)를 생성한다는 공통점이 있다.

object 키워드를 사용하는 여러 상황:
- 객체 선언(object declaration)은 싱글턴을 정의하는 방법 중 하나다
- 동반 객체(companion object)는 인스턴스 메서드는 아니지만 어떤 클래스와 관련 있는 메서드와 팩토리 메서드를 담을 때 쓰인다
- 객체 식은 자바의 무명 내부 클래스(anonymous inner class) 대신 쓰인다

#### 객체 선언: 싱글턴을 쉽게 만들기

객체지향 시스템을 설계하다 보면 인스턴스가 하나만 필요한 클래스가 유용한 경우가 많다.

코틀린은 객체 선언 기능을 통해 싱글턴을 언어에서 기본 지원한다.

```kotlin
object Payroll {
    val allEmployees = mutableListOf<Person>()
    
    fun calculateSalary() {
        for (person in allEmployees) {
            // ...
        }
    }
}
```

객체 선언은 object 키워드로 시작한다. 객체 선언은 클래스를 정의하고 그 클래스의 인스턴스를 만들어서 변수에 저장하는 모든 작업을 한 문장으로 처리한다.

#### 동반 객체: 팩토리 메서드와 정적 멤버가 들어갈 장소

코틀린 클래스 안에는 정적인 멤버가 없다. 코틀린은 자바의 static 키워드를 지원하지 않는다. 그 대신 코틀린에서는 패키지 수준의 최상위 함수(이들은 자바의 정적 메서드 역할을 거의 대신할 수 있다)와 객체 선언(자바의 정적 메서드 역할 중 코틀린 최상위 함수가 대신할 수 없는 역할이나 정적 필드를 대신할 수 있다)을 활용한다.

대부분의 경우 최상위 함수를 활용하는 편을 더 권장한다. 하지만 최상위 함수는 private으로 표시된 클래스 멤버에 접근할 수 없다.

그래서 클래스의 인스턴스와 관계없이 호출해야 하지만, 클래스 내부 정보에 접근해야 하는 함수가 필요할 때는 클래스에 중첩된 객체 선언의 멤버 함수로 정의해야 한다.

```kotlin
class A {
    companion object {
        fun bar() {
            println("Companion object called")
        }
    }
}

A.bar() // 동반 객체의 메서드 호출
```

## 5. 람다로 프로그래밍

### 5-1. 람다 식과 멤버 참조

#### 람다 소개: 코드 블록을 함수 인자로 넘기기

"이벤트가 발생하면 이 핸들러를 실행하자" 또는 "데이터 구조의 모든 원소에 이 연산을 적용하자"와 같은 생각을 코드로 표현하기 위해 일련의 동작을 변수에 저장하거나 다른 함수에 넘겨야 하는 경우가 자주 있다.

예전에는 무명 내부 클래스를 통해 이런 목적을 달성했다. 하지만 무명 내부 클래스를 사용하면 코드가 번잡해진다.

함수형 프로그래밍에서는 함수를 값처럼 다루는 접근 방법을 택함으로써 이 문제를 해결한다. 클래스를 선언하고 그 클래스의 인스턴스를 함수에 넘기는 대신 함수형 언어에서는 함수를 직접 다른 함수에 전달할 수 있다.

→ 람다 식을 사용하면 코드가 더욱 간결해진다. 람다 식을 사용하면 함수를 선언할 필요가 없고 코드 블록을 직접 함수의 인자로 전달할 수 있다.

```kotlin
button.setOnClickListener { /* 클릭 시 수행할 동작 */ }
```

#### 람다와 컬렉션

람다가 없다면 컬렉션을 편리하게 처리할 수 있는 좋은 라이브러리를 제공하기 힘들다.

```kotlin
data class Person(val name: String, val age: Int)

fun findTheOldest(people: List<Person>) {
    var maxAge = 0
    var theOldest: Person? = null
    for (person in people) {
        if (person.age > maxAge) {
            maxAge = person.age
            theOldest = person
        }
    }
    println(theOldest)
}

val people = listOf(Person("Alice", 29), Person("Bob", 31))
findTheOldest(people)
```

람다를 사용하면 이런 코드를 훨씬 간단하게 만들 수 있다:

```kotlin
val people = listOf(Person("Alice", 29), Person("Bob", 31))
println(people.maxByOrNull { it.age }) // 나이 프로퍼티를 비교해서 값이 가장 큰 원소 찾기
```

#### 람다 식의 문법

코틀린 람다 식은 항상 중괄호로 둘러싸여 있다.

```kotlin
{ x: Int, y: Int -> x + y }
```

람다 식을 변수에 저장할 수 있다:

```kotlin
val sum = { x: Int, y: Int -> x + y }
println(sum(1, 2)) // 3
```

코드의 일부분을 블록으로 둘러싸 실행할 필요가 있다면 run을 사용한다:

```kotlin
run { println(42) } // 람다 본문에 있는 코드를 실행한다
```

#### 현재 영역에 있는 변수에 접근

자바 메서드 안에서 무명 내부 클래스를 정의할 때 메서드의 로컬 변수를 무명 내부 클래스에서 사용할 수 있다. 람다 안에서도 같은 일을 할 수 있다.

```kotlin
fun printMessagesWithPrefix(messages: Collection<String>, prefix: String) {
    messages.forEach {
        println("$prefix $it") // 람다에서 함수의 prefix 파라미터 사용
    }
}
```

코틀린 람다 안에서는 파이널 변수가 아닌 변수에도 접근할 수 있다. 또한 람다 안에서 바깥의 변수를 변경해도 된다.

```kotlin
fun printProblemCounts(responses: Collection<String>) {
    var clientErrors = 0
    var serverErrors = 0
    responses.forEach {
        if (it.startsWith("4")) {
            clientErrors++ // 람다에서 람다 밖의 변수를 변경
        } else if (it.startsWith("5")) {
            serverErrors++ // 람다에서 람다 밖의 변수를 변경
        }
    }
    println("$clientErrors client errors, $serverErrors server errors")
}
```

#### 멤버 참조

넘기려는 코드가 이미 함수로 선언된 경우가 있다. 그런 경우 직접 함수를 호출하면 되지만, 람다로 감싸서 넘겨야 한다:

```kotlin
val getAge = Person::age
```

::를 사용하는 식을 멤버 참조(member reference)라고 부른다. 멤버 참조는 프로퍼티나 메서드를 단 하나만 호출하는 함수 값을 만들어준다.

```kotlin
people.maxByOrNull(Person::age)
```

이는 다음과 같은 람다 식을 더 간략하게 표현한 것이다:

```kotlin
people.maxByOrNull { person -> person.age }
```

### 5-2. 컬렉션 함수형 API

함수형 프로그래밍 스타일을 사용하면 컬렉션을 다룰 때 편리하다. 대부분의 작업에 라이브러리 함수를 활용할 수 있고 그로 인해 코드를 아주 간결하게 만들 수 있다.

#### 필수적인 함수: filter와 map

filter와 map은 컬렉션을 활용할 때 기반이 되는 함수다.

filter 함수는 컬렉션을 이터레이션하면서 주어진 람다에 각 원소를 넘겨서 람다가 true를 반환하는 원소만 모은다.

```kotlin
val list = listOf(1, 2, 3, 4)
println(list.filter { it % 2 == 0 }) // [2, 4]
```

map 함수는 주어진 람다를 컬렉션의 각 원소에 적용한 결과를 모아서 새 컬렉션을 만든다.

```kotlin
val list = listOf(1, 2, 3, 4)
println(list.map { it * it }) // [1, 4, 9, 16]
```

#### all, any, count, find: 컬렉션에 술어 적용

컬렉션에 대해 자주 수행하는 연산으로 컬렉션의 모든 원소가 어떤 조건을 만족하는지 판단하는 연산이 있다.

```kotlin
val canBeInClub27 = { p: Person -> p.age <= 27 }

val people = listOf(Person("Alice", 27), Person("Bob", 31))
println(people.all(canBeInClub27)) // false
println(people.any(canBeInClub27)) // true
println(people.count(canBeInClub27)) // 1
println(people.find(canBeInClub27)) // Person(name=Alice, age=27)
```

#### groupBy: 리스트를 여러 그룹으로 이뤄진 맵으로 변경

컬렉션의 모든 원소를 어떤 특성에 따라 여러 그룹으로 나누고 싶다고 하자.

```kotlin
val people = listOf(Person("Alice", 31), Person("Bob", 29), Person("Carol", 31))
println(people.groupBy { it.age })
// {31=[Person(name=Alice, age=31), Person(name=Carol, age=31)], 29=[Person(name=Bob, age=29)]}
```

### 5-3. 지연 계산(lazy) 컬렉션 연산

앞 절에서 살펴본 map이나 filter 같은 몇 가지 컬렉션 함수는 결과 컬렉션을 즉시 생성한다. 이는 컬렉션 함수를 연쇄하면 매 단계마다 계산 중간 결과를 새로운 컬렉션에 임시로 담는다는 말이다.

시퀀스(sequence)를 사용하면 중간 임시 컬렉션을 사용하지 않고도 컬렉션 연산을 연쇄할 수 있다.

```kotlin
people.asSequence() // 원본 컬렉션을 시퀀스로 변환
    .map(Person::name) // 시퀀스도 컬렉션과 똑같은 API를 제공
    .filter { it.startsWith("A") }
    .toList() // 결과 시퀀스를 다시 리스트로 변환
```

### 5-4. 자바 함수형 인터페이스 활용

코틀린 람다를 자바 API에 사용할 수도 있다. 

#### 자바 메서드에 람다를 인자로 전달

함수형 인터페이스를 인자로 원하는 자바 메서드에 코틀린 람다를 전달할 수 있다.

```kotlin
/* 자바 */
void postponeComputation(int delay, Runnable computation);
```

코틀린에서 lambda를 이 함수에 넘길 수 있다:

```kotlin
postponeComputation(1000) { println(42) }
```

컴파일러는 자동으로 람다를 Runnable 인스턴스로 변환해준다.

### 5-5. 수신 객체 지정 람다: with와 apply

자바의 람다에는 없는 코틀린 람다의 독특한 기능이 하나 있다. 그것은 수신 객체를 명시하지 않고 람다의 본문 안에서 다른 객체의 메서드를 호출할 수 있게 하는 것이다.

#### with 함수

어떤 객체의 이름을 반복하지 않고도 그 객체에 대해 다양한 연산을 수행할 수 있다면 좋을 것이다.

```kotlin
fun alphabet(): String {
    val result = StringBuilder()
    for (letter in 'A'..'Z') {
        result.append(letter)
    }
    result.append("\nNow I know the alphabet!")
    return result.toString()
}
```

이 코드에서는 result에 대해 다른 여러 메서드를 호출하면서 매번 result를 반복 사용했다.

```kotlin
fun alphabet(): String {
    val stringBuilder = StringBuilder()
    return with(stringBuilder) {
        for (letter in 'A'..'Z') {
            this.append(letter) // "this"를 명시해서 앞에서 지정한 수신 객체의 메서드를 호출
        }
        append("\nNow I know the alphabet!") // "this"를 생략하고 메서드를 호출
        this.toString() // 람다에서 값을 반환
    }
}
```

with문은 언어가 제공하는 특별한 구문처럼 보이지만 실제로는 파라미터가 2개 있는 함수다.

#### apply 함수

apply 함수는 거의 with와 같다. 유일한 차이란 apply는 항상 자신에게 전달된 객체(즉 수신 객체)를 반환한다는 점뿐이다.

```kotlin
fun alphabet() = StringBuilder().apply {
    for (letter in 'A'..'Z') {
        append(letter)
    }
    append("\nNow I know the alphabet!")
}.toString()
```

apply는 확장 함수로 정의돼 있다. apply의 수신 객체가 전달받은 람다의 수신 객체가 된다.

## 6. 코틀린 타입 시스템

### 6-1. 널 가능성

널 가능성(nullability)은 NullPointerException 오류(NPE라고도 부름)를 피할 수 있게 돕기 위한 코틀린 타입 시스템의 특성이다.

#### 널이 될 수 있는 타입

코틀린과 자바의 첫 번째이자 가장 중요한 차이는 코틀린 타입 시스템이 널이 될 수 있는 타입을 명시적으로 지원한다는 점이다.

어떤 변수가 널이 될 수 있다면 그 변수에 대해 메서드를 호출하면 NullPointerException이 발생할 수 있으므로 안전하지 않다. 코틀린은 그런 메서드 호출을 금지함으로써 많은 오류를 방지한다.

```kotlin
fun strLen(s: String) = s.length
```

이 함수가 널을 인자로 받을 수 있을까? 아니다. 여기서 s라는 파라미터는 String 타입이고, 코틀린에서 이는 s가 항상 String의 인스턴스여야 한다는 뜻이다. 

strLen(null) // 컴파일 오류: Null can not be a value of a non-null type String

어떤 타입이든 타입 이름 뒤에 물음표를 붙이면 그 타입의 변수나 프로퍼티에 null 참조를 저장할 수 있다는 뜻이다.

```kotlin
fun strLenSafe(s: String?) = ... // s는 null이 될 수 있다
```

String?, Int?, MyCustomType? 등은 널이 될 수 있는 타입이다. 널이 될 수 있는 타입인 변수가 있다면 그에 대해 수행할 수 있는 연산이 제한된다.

#### 타입의 의미

"타입"이란 무엇이고 "변수에 타입을 지정한다"는 것은 무엇일까? 타입은 분류(classification)로... 타입은 어떤 값들이 가능한지와 그 타입에 대해 수행할 수 있는 연산의 종류를 결정한다.

자바에서 String 타입의 변수에는 String이나 null이라는 두 종류의 값이 들어갈 수 있다. 이 두 종류의 값은 서로 완전히 다르다. 

→ 코틀린의 널이 될 수 있는 타입은 이런 문제에 대해 종합적인 해법을 제공한다.

#### 안전한 호출 연산자: ?.

?. 는 null 검사와 메서드 호출을 한 번의 연산으로 수행한다.

```kotlin
s?.toUpperCase() // s가 null이 아니면 toUpperCase 호출, null이면 null 반환
```

안전한 호출의 결과 타입도 널이 될 수 있는 타입이라는 점을 유의하자.

```kotlin
fun printAllCaps(s: String?) {
    val allCaps: String? = s?.toUpperCase() // allCaps는 null일 수 있다
    println(allCaps)
}

printAllCaps("abc") // ABC
printAllCaps(null) // null
```

#### 엘비스 연산자: ?:

코틀린은 null 대신 사용할 디폴트 값을 지정할 때 편리하게 사용할 수 있는 연산자를 제공한다. 그것은 엘비스(elvis) 연산자라고 불리는 ?: 이다.

```kotlin
fun foo(s: String?) {
    val t: String = s ?: "" // s가 null이면 ""를 결과로 한다
}
```

#### 안전한 캐스트: as?

as? 연산자는 어떤 값을 지정한 타입으로 캐스트한다. as?는 값을 대상 타입으로 변환할 수 없으면 null을 반환한다.

```kotlin
class Person(val firstName: String, val lastName: String) {
    override fun equals(o: Any?): Boolean {
        val otherPerson = o as? Person ?: return false
        return otherPerson.firstName == firstName &&
               otherPerson.lastName == lastName
    }
}
```

#### 널 아님 단언: !!

널 아님 단언(not-null assertion)은 코틀린에서 널이 될 수 있는 타입의 값을 다룰 때 사용할 수 있는 도구 중에서 가장 단순하면서도 무딘 도구다.

!!는 어떤 값이든 널이 될 수 없는 타입으로 (강제로) 바꿀 수 있다. 실제 널에 대해 !!를 적용하면 NPE가 발생한다.

```kotlin
fun ignoreNulls(s: String?) {
    val sNotNull: String = s!! // 예외는 이 지점을 가리킨다
    println(sNotNull.length)
}
```

### 6-2. 코틀린의 원시 타입

코틀린은 원시 타입과 래퍼 타입을 구분하지 않으므로 항상 같은 타입을 사용한다.

#### 원시 타입: Int, Boolean 등

자바는 원시 타입과 참조 타입을 구분한다. 원시 타입(int, boolean 등)의 변수에는 그 값이 직접 들어가지만, 참조 타입(String 등)의 변수에는 메모리상의 객체 위치가 들어간다.

코틀린은 원시 타입과 래퍼 타입을 구분하지 않으므로 항상 같은 타입을 사용한다.

```kotlin
val i: Int = 1
val list: List<Int> = listOf(1, 2, 3)
```

원시 타입과 참조 타입이 같다면 코틀린이 그들을 항상 객체로 표현하는 걸까? 그렇다면 비효율적일 것이다.

실제로는 코틀린 컴파일러가 가능한 한 가장 효율적인 방식으로 표현한다. 대부분의 경우(변수, 프로퍼티, 파라미터, 반환 타입 등) 코틀린의 Int 타입은 자바 int 타입으로 컴파일된다.

#### 널이 될 수 있는 원시 타입: Int?, Boolean? 등

null 참조를 자바의 참조 타입의 변수에만 대입할 수 있기 때문에 널이 될 수 있는 코틀린 타입은 자바 원시 타입으로 표현할 수 없다. 따라서 코틀린에서 널이 될 수 있는 원시 타입을 사용하면 그 타입은 자바의 래퍼 타입으로 컴파일된다.

```kotlin
data class Person(val name: String, val age: Int?)

val person = Person("Sam", null)
println(person.age) // null
```

### 6-3. 컬렉션과 배열

#### 널 가능성과 컬렉션

컬렉션 안에 널 값을 넣을 수 있는지 여부는 어떤 변수의 값이 널이 될 수 있는지 여부와 마찬가지로 중요하다.

```kotlin
fun readNumbers(reader: BufferedReader): List<Int?> {
    val result = ArrayList<Int?>() // 널이 될 수 있는 Int 값으로 이루어진 리스트를 만든다
    for (line in reader.lineSequence()) {
        try {
            val number = line.toInt()
            result.add(number) // 정수(널이 아닌 값)를 리스트에 추가한다
        }
        catch(e: NumberFormatException) {
            result.add(null) // 현재 줄을 파싱할 수 없으므로 리스트에 널을 추가한다
        }
    }
    return result
}
```

List<Int?>는 Int?타입의 값을 저장할 수 있다. 즉, Int 값이나 null을 저장할 수 있다.

다른 경우를 생각해보자. 리스트 자체가 널이 될 수 있다면 어떨까?

```kotlin
fun addValidNumbers(numbers: List<Int?>) {
    var sumOfValidNumbers = 0
    var invalidNumbers = 0
    for (number in numbers) {
        if (number != null) {
            sumOfValidNumbers += number
        } else {
            invalidNumbers++
        }
    }
    println("Sum of valid numbers: $sumOfValidNumbers")
    println("Invalid numbers: $invalidNumbers")
}
```

### 6-4. 요약

- 코틀린의 타입 시스템은 코드에서 NullPointerException을 제거하려고 노력한다.
- 코틀린에서는 타입 시스템이 널이 될 수 있는 타입과 될 수 없는 타입을 구분한다.
- 널이 될 수 있는 타입의 값에 대해 메서드를 직접 호출할 수는 없다. 그런 값에 대해 널 검사를 먼저 수행해야 한다.
- ?. (안전한 호출), ?: (엘비스 연산자), !! (널 아님 단언), as? (안전한 캐스트) 등을 사용하면 널이 될 수 있는 타입의 값을 간결한 코드로 다룰 수 있다.
- 코틀린에서는 수를 표현하는 타입(Int 등)이 일반 클래스와 똑같이 생겼고 일반 클래스와 똑같이 동작한다.
- 코틀린 컬렉션은 자바 라이브러리를 바탕으로 만들어졌지만, 코틀린에서는 읽기 전용 컬렉션과 변경 가능 컬렉션을 구별해 제공한다.
