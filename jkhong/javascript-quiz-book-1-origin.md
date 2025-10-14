### 원시 타입

- 원시 타입에는 총 7가지가 존재함.
  - Number, String, Boolean, Symbol, BigInt, Null, Undefined
  - 원시 타입은 불변함.
  - Null, Undefined를 제외한 원시 타입은 래퍼 객체를 가짐.
  - 원시 값의 프로퍼티나 메서드를 사용할 수 있는 건 사용 시점에 원시 값을 래퍼 객체로 감싸는 동작을 수행하기 때문임.
- Number 타입의 경우 배정밀도 부동소수점 방식을 사용함.
  - 배정밀도 부동소수점 방식이란?
    자바스크립트 Number 타입은 배정밀도 부동소수점 방식으로 숫자를 표현함.
    배정밀도 부동소수점 방식에 대해 이해하기 위해서 컴퓨터가 소수를 표현하는 방식을 이해하면 좋음.
    - 고정 소수점
      - 32비트의 경우 1비트를 부호, 15비트를 정수부, 16비트를 소수부를 표현하기 위한 메모리로 할당한다. 정수부, 소수부를 고정해서 표현하기 때문에, 표현 가능한 수의 범위가 좁고, 메모리를 낭비할 수 있다는 단점이 있다. (ex: 20000.12 라는 실수가 있다면 .12라는 소수를 표현하기 위해 16비트 메모리를 사용해야 함.)
    - 부동 소수점
      - 고정 소수점 방식과 다르게 부호부와 지수부, 가수부로 메모리를 나누어 수를 표현한다. 가수부가 실수 전체를 표현하기 때문에 메모리 낭비가 없음.
      - 32비트 단정밀도 부동 소수점 방식의 경우 1비트 부호부, 8비트 지수부, 23비트 가수부로 나뉜다. 10진수 숫자를 1.xxxx \* 2^n 형태로 변경한 후 xxxx 부분을 가수부에 넣고, n에 bias를 더해서 지수부에 넣는다.
      - 부동 소수점 방식은 IEEE 754를 따르는데 여기서 정의한 bias 값은 127이다. bias를 더해서 지수부에 넣는 이유는 지수가 음수인 경우와 양수인 경우를 나누기 위해서이다. 127을 더하지 않는다면 음수인 지수를 표현할 수가 없다. 그렇기 때문에 127을 더해 127보다 작으면 음수, 127보다 크면 양수로 구분한다.
      - 배정밀도 부동 소수점의 경우 64비트 메모리를 사용하기 때문에 32비트의 두 배라 배정밀도라고 한다. 배정밀도의 경우 1비트 부호부, 11비트 지수부, 52비트 가수부를 가진다. 이 때 bias의 경우 1023을 사용한다.
      - 부동 소수점 방식의 단점으로 소수 표현의 한계가 있다는 점이 있다. 가수부 메모리가 한정되어 있기 때문에 무한소수 등의 경우 표현을 하지 못하면 메모리에 최대한 채우고 나머지 부분을 절삭하기 때문에 실제 숫자와 오차가 발생하게 된다. 이 때문에 실수 계산 시 오차가 발생하는 경우가 생긴다.
- 리터럴이란?
  - 타입마다 정해진 표현 방식을 사용해 자바스크립트 코드에서 직접 값을 표현하는 것
- 정수 리터럴
  - 0~9의 숫자를 사용해 표현함. 10진수, 8진수, 16진수, 2진수 표현 가능. 대소문자는 구분하지 않는다.
  - 0o77 // 8진수
  - 0xff // 16진수
  - 0b11 // 2진수
- 부동소수점 리터럴
  - 소수점을 사용해 표현합니다. (ex: 3.141592...)
  - 지수로 표현할 수도 있습니다. (ex: 3.14e+3 => 3140)
- Number 타입의 경우 정수나 부동소수점 모두 64비트 메모리에 저장되기 때문에 표현할 수 있는 최대/최소값이 정해져 있음. 자바스크립트 부동소수점은 IEEE754 표준을 따르는데, 이 때의 최댓값은 약 2^1024 임. 이 값은 Number.MAX_VALUE로 정의되어 있음. 이보다 큰 수는 Infinity라는 값으로 변환됨.
  근데 Number.MAX_VALUE에 상대적으로 작은 값을 더하거나 빼면 그대로 Number.MAX_VALUE와 동일한 값이 출력됨. 이는 부동소수점의 오차로 인해 발생함.
- 이때문에 오차 없이 표현할 수 있는 정수의 최댓값은 Number.MAX_SAFE_INTEGER로 정의되어 있음. 이 값보다 클 경우 값의 정확성을 보장할 수 없음.
- 그래서 Number 타입으로 표현할 수 없는 값을 정확하게 표현하기 위해 bigInt 원시 타입이 추가됨.(ES2020에)
- Boolean
  - Boolean 타입은 true, false 값을 가짐.
- String
  - 문자열을 표현하기 위한 타입.
  - 자바스크립트에서 문자열을 표현하기 위해 UTF-16 인코딩을 사용함. 하나의 문자를 표현하기 위해 16비트를 사용함. 총 65536개의 문자 표현 가능함. 그러나 65536개로 전세계 모든 문자를 표현할 수 없음. 이모지 같은 것들? 기본 다국어 평면에 속하지 않는 문자는 이를 표현하기 위해 16비트 두 쌍으로 문자를 표현하는 방식을 사용함. 이를 서로게이트쌍이라고 함.
  - 헷갈리는 부분: 코드 포인트와 코드 유닛의 차이는 뭘까?
    - 내가 정리한 것: 코드 포인트는 유니코드에 정의된 코드임. 코드 유닛의 경우 실제 코드 포인트를 나타내기 위한 단위임. 이모지를 나타낼 때 2개의 코드로 표현하는데 이 때의 각 코드를 코드 유닛이라고 함.
  - 문자열 리터럴은 세 가지 방법이 있음.
    - 큰 따옴표
    - 작은 따옴표
    - 백틱
      - 백틱의 경우 템플릿 리터럴이라고 부름. 여러 줄을 적거나 문자열 사이에 표현식을 추가할 수 있음. 템플릿 리터럴은 태그드 템플릿이라는 추가 기능을 사용할 수 있음. 태그는 표현식으로 분리된 문자열을 받아 파싱한 결과를 반환하는 함수임.
      - 사용법은 아래와 같음.

```javascript
const tag = (strings, name) => {
  const modifiedName = `!${name}!`;
  return `${strings[0]}${modifiedName}${strings[1]}`;
};

const name = "홍길동";
const greetings = tag`안녕하세요 ${name}님`;

console.log(greetings); // 출력 결과: "안녕하세요 !홍길동!님"
```

- Symbol - ES6에 새롭게 추가된 원시타입 - 리터럴이 없고, String, Number, Boolean과 다르게 new 생성자 키워드를 사용하지 않고 Symbol() 함수로 생성함. - Symbol() 함수의 인자로 문자열을 전달할 수 있음. 다만 같은 문자열이라도 매번 고유한 Symbol 값을 생성함. Symbol은 고유하고 불변함.
  `javascript
Symbol("same string") === Symbol("same string") // 결과: false
	` - 인자로 전달한 문자열은 Symbol.prototype.description 로 참조 가능함. 다만 이는 디버깅을 위한 값임. - 기본적으로는 유일하고 불변한 값을 사용하기 위해 Symbol을 사용하는데, 이 외에 자바스크립트 객체의 기본 동작을 위해 사용하기도 함. - Symbol은 미리 정의된 정적 프로퍼티를 가지고 있는데 이를 Well-known Symbols라고 한다. 예를 들어, 원시 타입으로 변경하기 위한 Symbol.toPrimitive나 이터러블 프로토콜에서 사용되는 Symbol.iterator가 있음. 객체에 해당 키가 포함되어 있으면 [ecmascript 명세에 정의된 대로](https://tc39.es/ecma262/#sec-well-known-symbols) 동작하게 됨. - 자바스크립트 전역에 걸쳐 공통으로 사용하고 싶은 Symbol이 있다면 Symbol.for() 정적 메서드를 사용하면 됨.
  `javascript
		Symbol.for('shared symbol') === Symbol.for('shared symbol') // 결과: true
		` - 전역 심볼의 키 혹은 설명(description)을 조회하기 위해 Symbol.keyFor() 메서드를 사용함. - 단순 궁금증: Symbol.keyFor()는 언제 사용할까? - 스스로 정리한 답변: 디버깅할 때 사용함. Symbol.keyFor() 메서드에 전역 symbol 값을 인자로 전달하면 해당 전역 Symbol을 생성할 때 사용한 description 값을 출력한다. - 객체 프로퍼티 열거 메서드 사용 시 Symbol 키는 문자열 키와 달리 포함되지 않음. (ex: for ... in, Object.keys()) - Symbol 키를 조회하기 위해서는 Object.getOwnPropertySymbols() 또는 Reflect.ownKeys() 메서드를 사용해야 함.
- Null
  - Null 타입의 값은 null 하나만 존재. 값이 없음을 나타냄
  - 반환할 값이 없을 때 null 값을 반환하기도 함.
    - ex: document.querySelector()
- Undefined
  - undefined 값 하나만 존재. 아래와 같은 상황일 때 undefined가 반환됨.
    - 변수에 초깃값을 지정하지 않을 때
    - 객체에 속성이 없을 때
    - return 문에 값이 없을 때
    - 함수 인자에 값이 없을 때
    - void 연산자를 사용할 때 >> 이건 처음 봄
      - 표현식의 결과를 의도적으로 undefined로 반환해서, 결과값으로 인해 발생할 수 있는 부작용을 방지하는 레거시 기술로 이해하면 됨.

---

### 객체 타입

객체 타입은 수정 가능한 다양한 속성을 가진 자료구조.
각 속성은 키와 값으로 이루어져 있음. 객체의 키는 문자열 또는 심볼만 허용됨.
객체는 키와 값들을 중괄호로 감싼 객체 리터럴로 생성할 수 있음.
객체는 원시값과 다르게 변경이 가능하다.
자바스크립트의 원시 타입을 제외한 모든 타입은 객체 타입이다. 정규 표현식, Date, 함수와 배열 등도 객체임.

---

### typeof 연산자로 타입 확인하기

값의 타입을 확인하고 싶을 때 typeof 연산자를 사용하면 됨.
Undefined -> "undefined"
Null -> "object"
Boolean -> "boolean"
Bigint -> "bigint"
Number -> "number"
String -> "string"
Symbol -> "symbol"
Function -> "function"
Object -> "object"

- 함수 타입은 객체지만 typeof 연산자로 평가될 경우 "function" 값을 반환함
- null은 Null 타입이지만 "object"를 반환함
  - 이유: 초기 자바스크립트는 값이 32비트 메모리에 저장됨. 이 때 3비트는 타입의 종류를 나타내는 타입 태그를 저장하고, 나머지 29비트는 실제 값을 저장했음. 이 때 객체를 나타내는 타입 태그는 000이고, null 값은 32비트에 모두 0을 저장하는 걸로 나타냈음. typeof 연산자는 타입 태그의 값을 비교함. 그렇기 때문에 객체 타입의 타입 태그인 000과 null 값의 타입 태그가 동일한 것으로 판단해서 null 값을 객체 타입으로 평가함.

---

### 타입 변환

자바스크립트는 할당된 값에 따라 변수의 타입이 변경되는 동적 타입 언어임.
그렇기 때문에 다른 타입 간 연산을 수행해야 하는 경우가 생김. 이 경우 자바스크립트는 타입 강제 변환으로 타입을 맞춰 처리 함

- 명시적 타입 변환: 개발자가 의도적으로 타입을 변경
- 암시적 타입 변환: 개발자의 의도와 상관 없이 자동으로 타입 변환

#### Number 타입으로 변환

| 타입      | 변환규칙                                                                                                                     |
| --------- | ---------------------------------------------------------------------------------------------------------------------------- |
| Null      | 0으로 변환                                                                                                                   |
| Undefined | NaN으로 변환                                                                                                                 |
| Boolean   | true는 1, false는 0으로 변환                                                                                                 |
| String    | Number 리터럴로 파싱되면 Number로 변환, 그렇지 않으면 NaN으로 변환                                                           |
| Bigint    | 암시적 변환의 경우 정확성을 잃을 수 있기 때문에 타입 에러 반환 (Number 함수를 통해 명시적으로 변환하는 경우는 Number로 변환) |
| Symbol    | 타입 에러                                                                                                                    |

##### Object 타입을 Number 타입으로 변환하는 규칙

- Symbol.toPrimitive

  - 객체를 원시값으로 변환해주는 메서드. 첫 번째 인자로 "string", "number" 등 타입 힌트를 전달할 수 있음. 만약 + 연산자와 같이 String 타입과 Number 타입을 모두 받을 수 있는 경우라면 "default" 값을 전달함.

  ```javascript
  const obj = {
    [Symbol.toPrimitive](hint) {
      switch (hint) {
        case "string":
          return "문자열";
        case "number":
          return 10;
        case "default":
          return "기본값";
        default:
          break;
      }
    },
  };

  console.log(+obj); // 10
  console.log(`${obj}`); // "문자열"
  console.log("" + obj); // "기본값"
  ```

- valueOf
  - valueOf 메서드는 기본적으로 객체 자기 자신(this)을 반환함. 타입 변환을 위해 원시값을 반환하는 메서드로 재정의 할 수 있음.
- toString - 객체를 문자열로 변환하기 위한 메서드
  세 가지 메서드는 타입 강제 변환 시 자바스크립트에 의해 자동으로 호출됨. Number 타입으로 변환할 경우 Symbol.toPrimitive, valueOf, toString 순서로 원시값을 반환 받을 때까지 호출함.

##### Bigint 타입에 단항 연산자 사용하기

Bigint 타입에 단항 - 연산자를 사용할 경우 에러가 발생하지 않지만, 단항 + 연산자를 사용할 경우 Bigint를 number로 변환할 수 없다는 타입 에러가 발생한다. 단항 + 연산자는 피연산자를 Number 타입으로 강제 형변환하지만 단항 - 연산자는 단순 부호만 변경하기 때문에 그렇다.

##### 비트 연산과 TypedArray

여기는 이해를 못했음. 추가로 공부가 필요함.

#### String 타입으로 변환

| 타입      | 변환 규칙                                       |
| --------- | ----------------------------------------------- |
| Undefined | "undefined"로 변환                              |
| Null      | "null"로 변환                                   |
| Boolean   | true일 경우 "true", false일 경우 "false"로 변환 |
| Number    | 10진수 형식의 문자열로 변환                     |
| Bigint    | 10진수 형식의 문자열로 변환                     |
| Symbol    | 타입 에러                                       |

##### Object 타입을 String 타입으로 변환하는 규칙

Number의 경우와 비슷하게 Symbol.toPrimitive, valueOf, toString 메서드가 사용됨. Number와 다른 점은 호출 순서가 Symbol.toPrimitive, toString, valueOf 순서임.

##### String 타입으로 강제 변환하는 방법

대표적인 연산은 문자열 연결임. 문자열 연결 방법에는 + 연산자 사용, concat 메서드 사용, 템플릿 리터럴 사용하는 방법이 있음.
\+ 연산자는 숫자와 문자열 모두 피연산자로 사용하기 때문에 객체의 경우 원시값으로 변경 시 Symbol.toPrimitive(타입 힌트는 "default"), valueOf, toString 순서로 호출됨.
concat, 템플릿 리터럴의 경우 문자열로 타입 변환되는 게 확실하기 때문에 객체의 경우 Symbol.toPrimitive(타입 힌트는 "string"), toString, valueOf 순서로 호출된다는 차이점이 있음.
그렇기 때문에 문자열 연결 시 +연산자 대신 concat이나 템플릿 리터럴을 사용하는 게 의도치 않은 버그를 줄일 수 있음.

#### Boolean 타입으로 변환

| 타입      | 변환 규칙                                       |
| --------- | ----------------------------------------------- |
| Undefined | false로 변환                                    |
| Null      | false로 변환                                    |
| Number    | 0, -0, NaN은 false로 변환 ,나머지는 true로 변환 |
| Bigint    | 0n은 false로 변환, 나머지는 true로 변환         |
| String    | 빈 문자열은 false로 변환, 나머지는 true로 변환  |
| Symbol    | true로 변환                                     |
| Object    | true로 변환                                     |

false로 변환되는 경우는 undefined, null, 0, -0, NaN, 0n, 빈 문자열인데 이 값을 Falsy 값이라고 부름. 그 외 나머지는 Truthy 값이라고 함.
Boolean 타입으로 강제 변환시키는 방법에는 이중 NOT 연산자(!!), Boolean 함수, if 문 등이 있음.

#### 객체 타입으로 변환

객체 타입을 피연산자로 기대하는 연산자의 경우 래퍼 객체를 생성해 원시값을 감싸게 됨. undefined와 null 값의 경우 래퍼 객체가 존재하지 않기 때문에 에러를 반환함.
객체 타입으로 강제 변환하는 방법에는 Object.prototype.valueOf, Object 함수 등이 있음.
Object.prototype.valueOf 메서드의 경우 객체 자기 자신인 this를 반환하기 때문에 call 메서드를 사용해 this로 원시값을 전달하면 해당 원시값을 래퍼 객체로 감싸고 래퍼 객체를 반환하게 됨.

```javascript
const seven = Object.prototype.valueOf.call(7);

console.log(seven); // Number (7). 래퍼 객체를 반환
console.log(typeof seven); // Object
console.log(seven.valueOf); // 7. 래퍼 객체의 valueOf 메서드는 원시값을 반환함.
console.log(seven + 3); // 10. seven은 Object 타입이기 때문에 valueOf 메서드를 통해 원시값 7로 변경됨.
```

Object 함수도 비슷하게 원시값을 객체로 변환하지만 null과 undefined를 빈 객체로 변환한다는 차이점이 있음.

#### 값과 타입의 비교

값을 비교하는 방법은 크게 3가지가 있음

1. 일치 연산자(혹은 엄격한 동등 연산자 \=\=\=)
   피연산자의 값과 타입을 모두 비교함. 피연산자의 타입이 다를 경우 false를 반환함. 피연산자의 타입이 같을 때 값이 다르면 false를 반환하는데, +0, -0, NaN에 대해 예외가 존재함.
   ```javascript
   +0 === -0; // true
   NaN === NaN; // false
   ```
   +0과 -0의 경우 IEEE 754 부동소수점 표준에서 0값의 비교 시 부호는 고려하지 않는다는 정의에 따라 같은 값으로 평가한다.
   NaN의 경우 다른 숫자와의 연산은 모두 NaN이 되고, 비교 연산은 false로 평가된다. NaN 값인지 확인하기 위해 Number.isNaN이나 Object.is 연산자를 사용한다. isNaN 전역함수도 존재하지만 인수를 Number 타입으로 강제 변환하기 때문에 의도치 않은 결과를 얻을 수 있기 때문에 사용하지 않는 게 좋음.
2. 동등 연산자(\=\=)
   피연산자의 타입이 같다면 일치 연산자와 동일하게 동작하지만, 타입이 다를 경우 피연산자에 대한 암시적 타입 변환을 수행한다.
   - 한쪽 피연산자가 null 또는 undefined일 때 다른쪽 피연산자도 null 또는 undefined라면 true를 반환, 그렇지 않다면 false 반환
   - 한쪽 피연산자가 객체 타입이고 다른쪽 피연산자는 원시 타입이라면 객체 타입을 원시 타입으로 변환 후 다시 비교
   - 한쪽 피연산자가 Symbol이라면 false 반환
   - 한쪽 피연산자만 Boolean이라면 true를 1, false를 0으로 변환 후 다시 비교
   - 한쪽 피연산자가 String 타입이고 다른쪽 피연산자는 Number 타입이라면 String 타입을 Number 타입으로 변환 후 다시 비교
   - 한쪽 피연산자가 Bigint 타입이고 다른쪽 피연산자가 Number 타입이라면 두 수를 비교. Number 타입의 값이 +Infinity, -Infinity, NaN이라면 false를 반환.
   - 한쪽 피연산자가 String 타입이고 다른쪽 피연산자가 Bigint 타입이라면 String 타입의 값을 Bigint 함수를 사용해 변환 후 다시 비교. 변환에 실패하면 false를 반환.
3. Object.is 메서드
   Object.is 정적메서드는 일치 연산자와 비슷하게 동작하지만 +0, -0, NaN 값에 예외를 두지 않음.
   ```javascript
   Object.is(+0, -0); // false
   Object.is(NaN, NaN); // true
   ```
