# 한줄정리
> 피연산자(연산할 대상)을 연산하기 위해 사용하는 기호
## 이게 뭔가?
>Operators are special symbols that perform specific operations on one, two, or three operands, and then return a result. 
>
>연산자는 하나, 둘또는 세개의 피연산자에 대해 특별한 연산을 수행을 진행후 결과를 반환하는 특별한 기호입니다.- Oracle Java Tutorials

위와 같이 피연산자에 대해 어떠한 결과를 반환하도록 하는 기호를 칭한다.
종류는 아래와 같이 있다.

| 연산자                  | 우선순위                                    |
| -------------------- | --------------------------------------- |
| postfix              | _expr_++ _expr_--                       |
| unary                | ++_expr_ --_expr_ +_expr_ -_expr_ ~ !   |
| multiplicative       | * / %                                   |
| additive             | + -                                     |
| shift                | << >> >>>                               |
| relational           | < > <= >= instanceof                    |
| equality             | == !=                                   |
| bitwise AND          | &                                       |
| bitwise exclusive OR | ^                                       |
| bitwise inclusive OR | \|                                      |
| logical AND          | &&                                      |
| logical OR           | \|                                      |
| ternary              | ? :                                     |
| assignment           | = += -= *= /= %= &= ^= \|= <<= >>= >>>= |
### JVM 작동 원리
<img width="341" height="250" alt="Pasted image 20260706011926" src="https://github.com/user-attachments/assets/86bb42c6-1f85-42c5-b953-314e60ab8874" />

JVM의 JVM Stack이 위와 같은 것들이 담겨있는 Stack Frame을 Pop, Push하는 등의 연산을 통해 연산을 하게된다. 또한, 이 Stack Frame에는 Operand Stack이 포함된다. 

Operand Stack(LIFO)의 크기는 컴파일 시점에 최대 크기가 정해지며, 한 칸은 32bit(4byte)를 차지한다.
처음에는 비어있으며 상수/값을 스택에 로드할수 있고, 또한 연산후 다시 스택에 해당 값이 담기게 된다.
또한 메서드에 전달할 매개변수도 여기 담기고 사용되기도 함.

예를 들자면 `iadd`연산은 operand stack에서 2개의 int값을 pop한후 +연산하여 다시 push하는 명령어이다. 비슷한 연산으로 `ladd`(long +), `fadd`(float +) 등이 있다.
여기서 long과 같이 64bit값을 넣고 32bit 2개로 취급하여 계산하지 못한다. 즉 타입을 쪼갤수 없다.

또한, 자료형이 다른 두 값은 바로 계산하는것이 아닌, `i2l`, `i2f`등으로 타입이 변환되어 사용된다.
마지막으로, 위에서 언급했듯이 한 칸은 32bit로, 이보다 작은 `byte`, `char`, `short`는 자동으로 내부적으로 int로 변환된다.
따라서 아래와 같은 코드는 작동하지 않는다.
```java
byte x = 10;
byte y = 20;
byte z = x + y; // 에러
```

## 왜 필요한가?
기본적으로 우리는 ALU, CPU의 연산장치에 직접적으로 접근할수 없다. 따라서 다른 언어가 없다는 가정하에, 연산자가 없다면 연산 자체가 불가능할것이다.
어셈블리를 활용한다고 해도 레지스터, CPU 주소를 하나하나 지정해야한다.
위와 같이 연산자가 없다면 가독성 뿐만아니라 매우 복잡해질것이다.
이런 부분을 추상화해주고 간단히 만들어주기때문에 필요하다.
## 어디에 쓰이나?
크게 세 상황에 쓰인다.
#### 조건
if, while문과 같이 어떠한 상황을 체크해야할때 사용한다
`>`, `<`, `instanceof` 등이 있다.
#### 연산
값을 일시적으로 연산하여 바꿀 시에 필요하다.
+, -, ++, >>> 등이 있다.
#### 저장
=, += 등으로 값을 저장할때 쓰인다.

## 실무에서 자주 터지는 문제
#### 오버플로우
int형과 같은 경우 최대 값이 2147483647로, 만약 이를 넘어선다면 오버플로우가 일어나
값이 음수로 넘어갈 수 있다
값이 커질 가능성이 있다면 long과 같은 큰 자료형을 사용하거나
`Math.addExact(a, b)`과 같은 함수를 사용하여 예외를 터트린다 <- 잘 안쓰임
#### 부동소수점 오차
부동소수점의 경우 0, 1로만 모든 걸 저장하기 떄문에 0.1과 같은 값은 내부적으로 무한소수가 된다
이를 더할경우 매우 조금의 오차가 발생한다.
소수점자리까지 정확해야한다면 BigDecimal의 add, multiply를 활용하자.
#### 단락평가
&&, ||는 앞, 뒤의 조건을 모두보는것이 아닌
조건이 무조건 false, true인 경우에는 자원을 절약하기 위해 뒤의 조건을 확인하지않는다.
따라서 원하는 동작이 아니라면 미리 계산후 조건 계산을 하자
## 예제 코드
```java
int a = 5;
int b = 10;

long c = a + b;
c = (long)(c - 3.4);
```
# 면접/리뷰 질문
### 자동 int형 변환
```java
byte x = 10;
byte y = 20;
byte z = x + y; // 에러
```
위 코드와 같은 코드가 작동하지 않는 이유는?
**답변**
operand stack의 경우 한 칸이 32bit로, 이보다 작은 byte는 int형으로 변환되어 담기게 됩니다.
따라서 위 x+y부분에서 int + int로 변환되어 계산후 int형이 반환되는데, 이 값을 byte에 억지로 담으려고해 오류가 납니다.
### \==와 .equals의 차이
\==와 equals의 차이는 무엇일까요?
**답변**
\==는 기본자료형일때는 값을, 객체일때는 주소를 참조합니다.
equals()는 내부의 실제 데이터를 비교합니다.
# 참고 링크
https://docs.oracle.com/javase/tutorial/java/nutsandbolts/operators.html  
https://d2.naver.com/helloworld/1230  
https://docs.oracle.com/javase/specs/jvms/se21/html/jvms-2.html
