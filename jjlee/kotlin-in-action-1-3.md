## 1. 코틀린이란 무엇이며, 왜 필요한가

### 1-1. 코틀린 소개

코틀린은 정적 타입 지정 언어이다.

즉, 모든 프로그램 구성 요소의 타입을 컴파일 시점에 알 수 있고 프로그램 안에서 객체의 필드나 메서드를 사용할 때마다 컴파일러가 타입을 검증해준다는 뜻이다.

- 예시로, 컴파일러가 문맥을 고려해 변수 타입을 결정하는 기능을 타입 추론이라고 부른다.
    - 가장 중요한 특성은 코틀린에서 널러블(Nullable)이다. 이는 널 포인터 예외가 발생할 수 있는지 여부를 검사하여 신뢰성을 더 높일 수 있다.

정적 타입 지정의 장점은 다음과 같다.

- 성능: 실행 시점에 어던 메서드를 호출할지 알아내는 과정이 필요 없다.
- 신뢰성: 컴파일러가 프로그램의 정확성을 검증하기 때문에 실행 시 프로그램이 오류로 중단될 가능성이 더 적다.
- 유지 보수성: 코드에서 다루는 객체가 어떤 타입에 속하는지 알 수 있기 때문에 처음 보는 코드를 다룰 때도 더 쉽다.
- 도구 지원: 정적 타입 지정을 활용하면 더 안전하게 리팩토링할 수 있다. + IDE

### 1-2. 코틀린의 대상 플랫폼

코틀린은 다양한 플랫폼에서 실행될 수 있다.

- 서버 사이드: 웹 애플리케이션, 백엔드 서비스, 마이크로서비스
- 안드로이드: 모바일 앱 개발
- 브라우저: JavaScript로 컴파일 가능
- 네이티브: Kotlin/Native를 통한 네이티브 바이너리 생성

특히 안드로이드에서 코틀린을 사용할 때의 장점:
- 널 안전성으로 인한 앱 안정성 향상
- Java 6과 호환되어 기존 안드로이드 앱과 함께 사용 가능
- 코틀린 컴파일러가 생성하는 코드는 자바 코드만큼 효율적으로 실행

### 1-3. 함수형 프로그래밍

함수형 프로그래밍의 핵심은 아래와 같다.

- 일급 시민인 함수: 함수를 일반 값처럼 다룰 수 있다.
- 불변성: 만들어지고 나면 내부 상태가 절대로 바뀌지 않는 불변 객체를 사용한다.
- 부수 효과 없음: 입력이 같으면 항상 같은 출력을 내놓고 다른 객체의 상태를 변경하지 않으며, 함수 외부나 다른 바깥 환경과 상호작용하지 않는 순수 함수를 사용한다.

+ 함수형 프로그래밍에서 얻을 수 있는 유익 중 하나는 다중 스레드를 사용해도 안전하다는 사실이다. 다중 스레드 프로그램에서는 적절한 동기화 없이 같은 데이터를 여러 스레드가 변경하는 경우 가장 많은 문제가 된다.

⇒ 코틀린은? 당연히 처음부터 함수형 프로그래밍 언어였다. (함수 타입, 람다 식, 데이터 클래스 등 다양한 기능을 제공하고 있다.)

### 1-4. 코틀린의 철학

> 코틀린은 자바와 상호운용성에 초점을 맞춘 실용적이고 간결하며 안전한 언어.
> 
- 기본적인 getter, setter, 생성자 파라미터 등 간결하고 편하다.
- 기본적으로 널러블을 지원하여 자바 대비 안전하다.
- 자바와의 완벽한 상호운용성을 가지고 있다.

### 1-5. 코틀린 도구와 컴파일

코틀린 소스 파일의 확장자는 `.kt`이다. 코틀린 컴파일러인 `kotlinc`는 코틀린 코드를 `.class` 파일로 컴파일한다. 

- 컴파일된 `.class` 파일은 표준 절차에 따라 패키징되고 실행된다.
- 코틀린 컴파일러로 컴파일한 코드는 코틀린 런타임 라이브러리에 의존한다.
- 런타임 라이브러리에는 코틀린 자체 표준 라이브러리 클래스와 자바 API의 확장이 들어있다.

## 2. 코틀린 기초

### 2-1. 함수, 식, 변수 기초

- 함수를 선언할 때 fun 키워드를 사용한다. → 실제로 코틀린에서 프로그래밍하는 수많은 것들은 fun한 일이다.
- 함수를 최상위 수준에 정의 가능하다.
- 식이 본문인 함수
    
    ```kotlin
    fun max(a: Int, b: Int) = if (a > b) a else b
    ```
    
    - 반환 타입이 생략가능한 이유 ⇒ 코틀린은 정적 타입 지정 언어이므로 컴파일 시점에 모든 식의 타입을 지정해야 하지 않는가? 라고 생각할 수 있다. 실제로 모든 변수나 모든 식에는 타입이 있으며, 모든 함수는 반환 타입이 정해져야 한다. 하지만 식이 본문인 함수의 경우 굳이 사용자가 반환 타입을 적지 않아도 컴파일러가 함수 본문 식을 분석해 결과 타입을 함수 반환 타입으로 정해준다.
- 변경 가능한 변수와 변경 불가능한 변수
    - val(value) - 변경 불가능한 참조를 저장하는 변수이고 자바로 치면 final이다.
    - var(variable) - 변경 가능한 참조이다. ⇒ 하지만 변수의 타입은 고정된다!
- 코틀린의 기본 가시성은 public이므로 생략 가능하다.

### 2-2. 문자열 템플릿

코틀린에서는 문자열 리터럴 안에 변수를 사용할 수 있다.

```kotlin
fun main(args: Array<String>) {
    val name = if (args.size > 0) args[0] else "Kotlin"
    println("Hello, $name!")
}
```

복잡한 식도 중괄호로 둘러싸서 문자열 템플릿 안에 넣을 수 있다:

```kotlin
fun main(args: Array<String>) {
    println("Hello, ${if (args.size > 0) args[0] else "someone"}")
}
```

### 2-3. 클래스 기초

클래스라는 개념의 목적은 데이터를 캡슐화하고 캡슐화한 데이터를 다루는 코드를 한 주체 아래 가두는 것이다.

```kotlin
class Person(
	val name: String, // <- 읽기 전용 프로퍼티
	var address: String // <- 쓸 수 있는 프로퍼티
) {
	val isPerson: Boolean
		get() { // <- 파라미터가 없는 함수
				return name != null
		}
}
```

자바에서는 필드와 접근자를 한데 묶어 프로퍼티라고 부르며, 프로퍼티라는 개념을 활용하는 프레임워크가 많다. 코틀린은 프로퍼티를 언어 기본 기능으로 제공하며, 코틀린 프로퍼티는 자바의 개념을 완전히 대신한다.

추가로 코틀린에서는 위 같이 선언하면 각각 공개된 게터 혹은 게터, 세터를 만들어 준다. 그리고 다른 프로퍼티 값을 표현하는 게터를 만들 수 있다.

이 때 파라미터가 없는 함수를 정의하는 방식과 커스텀 게터를 정의하는 방식 중 어느 쪽이 더나은지 궁금할 수 있다. 답은 없고 둘다 비슷하다. 구현이나 성능은 크게 차이가 없어 가독성뿐이다.

### 2-4. Enum class

enum에서도 일반적인 클래스와 마찬가지로 생성자와 프로퍼티를 선언한다. 각 enum 상수를 정의할 때는 그 상수에 해당하는 프로퍼티 값을 지정해야만 한다. 이 예제 에서는 코틀린에서 유일하게 세미콜론이 필수인 부분이다. 

```kotlin
enum class Color(
	val r: Int, val g: Int, val b: Int
) {
	RED(255, 0, 0,), ORANGE(255, 165, 0) ...
	;
}
```

### 2-5. when 표현식

코틀린의 when은 자바의 switch를 대치하되 훨씬 더 강력하다.

```kotlin
fun getMnemonic(color: Color) =
    when (color) {
        Color.RED -> "Richard"
        Color.ORANGE -> "Of"
        Color.YELLOW -> "York"
        Color.GREEN -> "Gave"
        Color.BLUE -> "Battle"
        Color.INDIGO -> "In"
        Color.VIOLET -> "Vain"
    }
```

한 분기 안에서 여러 값을 매치 패턴으로 사용할 수도 있다:

```kotlin
fun getWarmth(color: Color) = 
    when(color) {
        Color.RED, Color.ORANGE, Color.YELLOW -> "warm"
        Color.GREEN -> "neutral"
        Color.BLUE, Color.INDIGO, Color.VIOLET -> "cold"
    }
```

when은 임의의 객체와도 함께 쓸 수 있다:

```kotlin
fun mix(c1: Color, c2: Color) =
    when (setOf(c1, c2)) {
        setOf(RED, YELLOW) -> ORANGE
        setOf(YELLOW, BLUE) -> GREEN
        setOf(BLUE, VIOLET) -> INDIGO
        else -> throw Exception("Dirty color")
    }
```

인자가 없는 when도 사용할 수 있다:

```kotlin
fun mixOptimized(c1: Color, c2: Color) =
    when {
        (c1 == RED && c2 == YELLOW) || (c1 == YELLOW && c2 == RED) -> ORANGE
        (c1 == YELLOW && c2 == BLUE) || (c1 == BLUE && c2 == YELLOW) -> GREEN
        (c1 == BLUE && c2 == VIOLET) || (c1 == VIOLET && c2 == BLUE) -> INDIGO
        else -> throw Exception("Dirty color")
    }
```

### 2-6. 스마트 캐스트, 익셉션 캐치

자바에서 어떤 변수의 타입은 instanceof로 확인한 다음에 그 타입에 속한 멤버에 접근하기 위해서는 명시적으로 변수 타입을 캐스팅해야 한다.

→ 코틀린에서는 컴파일러가 캐스팅을 해준다. 어떤 변수가 원하는 타입인지 일단 is로 검사하고 나면 굳이 변수를 원하는 타입으로 캐스팅하지 않아도 마치 처음부터 그 변수가 원하는 타입으로 선언된 것처럼 사용할 수 있다. 이를 스마틐 캐스트라고 한다.

자바에서는 함수를 작성할 때 함수 선언 뒤에 throws IOException을 붙여야한다.

이유는 IOException이 체크예외이기 때문이다. 자바에서는 체크 예외를 명시적으로 처리해야 한다. 어떤 함수가 던질 가능성이 있는 예외나 그 함수가 호출한 다른 함수에서 발생할 수 있는 예외를 모두 catch로 처리해야 하며, 처리하지 않은 예외는 throws 절에 명시 해야한다.

코틀린에서는 언체크와 체크를 구별하지 않는다. 함수가 던지는 예외를 지정하지 않고 발생한 예외를 잡아내도 되고 잡아내지 않아도 된다. 실제 자바 체크 예외를 사용하는 방식을 고려해 코틀린 예외가 설계 되었다. 그냥 무시하거나 의미 없는 예외때문에 발생하는 문제를 해결할 수 있다.

try를 식으로 사용하기:

```kotlin
fun readNumber(reader: BufferedReader): Int? {
    val number = try { 
        Integer.parseInt(reader.readLine())
    } catch (e: NumberFormatException) {
        null
    }
    println(number)
}
```

### 2-7. 범위와 반복

코틀린에는 자바의 for (int i = 0; i < 10; i++) 같은 구문이 없다. 대신 범위(range)를 사용한다.

```kotlin
val oneToTen = 1..10
```

범위는 양끝을 포함하는 구간이다 (inclusive).

for 루프에서 범위 사용하기:

```kotlin
for (i in 1..100) {
    print(fizzBuzz(i))
}

// 역방향 범위와 스텝
for (i in 100 downTo 1 step 2) print(fizzBuzz(i))
```

맵 이터레이션:

```kotlin
val binaryReps = TreeMap<Char, String>()

for (c in 'A'..'F') {
    val binary = Integer.toBinaryString(c.toInt())
    binaryReps[c] = binary
}

for ((letter, binary) in binaryReps) {
    println("$letter = $binary")
}
```

in 연산자로 컬렉션이나 범위의 원소 검사:

```kotlin
fun isLetter(c: Char) = c in 'a'..'z' || c in 'A'..'Z'
fun isNotDigit(c: Char) = c !in '0'..'9'
```

## 3. 함수 정의와 호출

### 3-1. 코틀린의 컬렉션

```kotlin
println(set.javaClass)
=> java.util.HashSet
```

바로 위처럼 코틀린은 자신만의 컬렉션 기능을 제공하지 않는다. 코틀린이 자체 컬렉션을 제공하지 않는 이유는 표준 자바 컬렉션을 활용하면 자바 코드와 상호작용 하기가 훨씬 더 쉽다. 서로 호출할 때 변환할 필요가 없다.

컬렉션 생성하기:

```kotlin
val set = setOf(1, 7, 53)
val list = listOf(1, 7, 53)
val map = mapOf(1 to "one", 7 to "seven", 53 to "fifty-three")
```

### 3-2. 이름 붙인 인자와 디폴트 파라미터

함수를 살펴보자.

```kotlin
joinToString(collection, separator= " ", prefix="", postfix=".")

// 디폴트 파라미터
joinToString(collection)
```

위 함수의 변천사를 이제 지켜볼거다. 코틀린은 위 처럼 name 파라미터를 이용하여 각각 어떤 값이 들어갈지 함수단에서 지정해줄 수 있다. 자바에서는 이에 대한 순서가 헷갈려 오류가 난적도 있다. 특히 bool 값이면 더 위험하다. 좀 더 다듬은게 디폴트 파라미터를 활용한 방식이다. 디폴트 파라미터를 함수에 지정하여 값이 지정되지 않아도 기본으로 그 값이 들어갈 수 있게 할 수 있다. 

### 3-3. 최상위 함수와 프로퍼티

자바에서는 모든 코드를 클래스의 메서드로 작성해야 한다. 하지만 실제로는 어느 한 클래스에 포함시키기 어려운 코드가 많이 있다. 코틀린에서는 함수를 소스 파일의 최상위 수준, 모든 클래스 밖에 위치시킬 수 있다.

```kotlin
// strings.kt
package strings

fun joinToString(...): String { ... }
```

이 함수는 어떻게 실행될 수 있을까? JVM이 클래스 안에 들어있는 코드만을 실행할 수 있으므로 컴파일러는 이 파일을 컴파일할 때 새로운 클래스를 정의해준다.

### 3-4. 확장 함수

자바 api를 재작성하지 않고도 코틀린이 제공하는 여러 편리한 기능을 사용할 수 있다면 어떨까? 바로 확장 함수가 그런 역할을 해준다.

```kotlin
fun String.lastChar(): this.get(this.length - 1)
// String이 수신 객체 타입, this가 수신 객체
```

확장 함수를 만들려면 추가하려는 함수 이름 앞에 그 함수가 확장할 클래스의 이름을 덧붙이기만 하면 된다. 클래스 이름을 수신 객체 타입이라 하고, 호출되는 대상이 되는 값을 수신 객체라고 부른다!

그러므로 코틀린에서 제공되는 last(), max() 같은 함수 모두 확장함수이다!!

확장 함수는 오버라이드할 수 없다는 점을 기억해야 한다. 확장 함수는 클래스 밖에 선언되므로 확장 함수와 멤버 함수의 시그니처가 같다면 멤버 함수가 호출된다.

### 3-5. 가변 인자 함수: varargs

리스트를 생성하는 함수를 호출할 때 원하는 만큼 많이 원소를 전달할 수 있다.

```kotlin
val list = listOf(2, 3, 5, 7, 11)
```

이런 기능을 위해 varargs 키워드를 사용한다:

```kotlin
fun listOf<T>(vararg values: T): List<T> { ... }
```

이미 배열에 들어있는 원소를 가변 길이 인자로 넘길 때는 스프레드 연산자(*)를 배열 앞에 붙여야 한다:

```kotlin
fun main(args: Array<String>) {
    val list = listOf("args: ", *args)
    println(list)
}
```

### 3-6. 구조 분해 선언과 중위 호출

mapOf를 사용해서 코틀린에서 맵을 만들면 to라는 중위 호출 방식을 사용한다. 이는 일반 메서드이다.

```kotlin
val map = mapOf(1 to "one", 7 to "seven", 53 to "fifty-three")
```

중위 호출은 인자가 하나뿐인 메서드에 대해 사용할 수 있다:

```kotlin
1.to("one")  // 일반적인 방법
1 to "one"   // 중위 호출
```

실제로 이를 확인해보면 1 to "one"이라는 것을 할 때 Pair가 만들어지고 이를 val (a=1, b="one")으로 할당 되는 것이다.

구조 분해 선언(destructuring declaration)을 사용하면 복합적인 값을 분해해서 여러 변수에 나눠 담을 수 있다:

```kotlin
val (number, name) = 1 to "one"
```

이는 루프에서도 유용하다:

```kotlin
for ((index, element) in collection.withIndex()) {
    println("$index: $element")
}
```

### 3-7. 문자열과 정규 표현식

코틀린 문자열은 자바 문자열과 같다. 하지만 코틀린은 다양한 확장 함수를 제공해 표준 자바 문자열을 더 즐겁게 다룰 수 있게 해준다.

문자열 나누기:

```kotlin
// 자바의 split은 정규식이므로 "."을 쓰면 문제가 있다
println("12.345-6.A".split("\\.|-".toRegex()))

// 코틀린에서는 여러 구분 문자열을 지정할 수 있다
println("12.345-6.A".split(".", "-"))
```

### 3-8. 지역 함수와 확장

코틀린에서는 함수에서 추출한 함수를 원 함수 내부에 중첩시킬 수 있다. 이렇게 하면 문법적인 부가 비용을 들이지 않고도 깔끔하게 코드를 조직할 수 있다.

```kotlin
class User(val id: Int, val name: String, val address: String)

fun saveUser(user: User) {
    fun validate(user: User, value: String, fieldName: String) {
        if (value.isEmpty()) {
            throw IllegalArgumentException(
                "Can't save user ${user.id}: empty $fieldName")
        }
    }
    
    validate(user, user.name, "Name")
    validate(user, user.address, "Address")
    
    // user를 데이터베이스에 저장한다
}
```

지역 함수는 자신이 속한 바깥 함수의 모든 파라미터와 변수를 사용할 수 있다.