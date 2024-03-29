# item38

# 아이템 38. 확장할 수 있는 열거 타입이 필요하면 인터페이스를 사용하라

- 열거 타입은 확장할 수 없다. 그러나 연산 코드(operation code 혹은 opcode)에는 확장할 수 있는 열거 타입이 어울린다.
    - 연산 코드의 각 원소는 특정 기계가 수행하는 연산을 뜻한다.
- 이때 열거 타입이 임의의 인터페이스를 구현할 수 있다는 사실을 이용하여 확장과 비슷한 효과를 낼 수 있다.
- 연산 코드용 인터페이스를 정의하고 열거 타입이 이 인터페이스를 구현하게 하면 된다.
- 열거 타입이 해당 인터페이스의 표준 구현체 역할을 한다.

### 확장 가능 열거 타입

```java
public enum ExtendedOperation implements Operation {
    EXP("^") {
        @Override
        public double apply(double x, double y) {
            return Math.pow(x, y);
        }
    },
    REMAINDER("%") {
        @Override
        public double apply(double x, double y) {
            return x % y;
        }
    };

    private final String symbol;

    ExtendedOperation(String symbol) {
        this.symbol = symbol;
    }

    @Override
    public String toString() {
        return symbol;
    }
}
```

- 위는 지수 연산(EXP)과 나머지 연산(REMAINDER)을 추가한 것이다. Operation 인터페이스를 구현한 열거 타입을 작성하기만 하면 된다.
- 새로 작성한 연산은 기존 연산을 쓰던 곳이면 어디든 쓸 수 있다. Operation 인터페이스를 사용하도록 작성되어 있기만 하면 된다.
- apply가 인터페이스(Operation)에 선언되어 있으니 열거 타입에 따로 추상 메서드로 선언하지 않아도 된다. 이 부분이 상수별 메서드 구현과 다른 점이다.
- 이 패턴을 사용하는 자바 라이브러리의 java.nio.file.LinkOption 열거 타입은 CopyOption과 OpenOption 인터페이스를 구현했다.

## 인터페이스를 이용한 확장된 열거 타입의 문제점

- 열거 타입끼리는 구현을 상속할 수 없다는 문제가 있다.
- 만약 아무 상태에도 의존하지 않는다면, 디폴트 구현을 이용해 인터페이스에 추가하는 방법이 있다.
    - 반면 여기서의 Operation 예는 연산 기호를 저장하고 찾는 로직이 BasicOperation과 ExtendedOperation 모두에 들어가야만 한다.
    - 이 경우에는 중복량이 적으니 문제되지 않지만, 공유하는 기능이 많다면 그 부분을 별도의 도우미 클래스나 정적 도우미 메서드로 분리하는 방식으로 코드 중복을 없앨 수 있을 것이다.

## 핵심 정리

- 열거 타입 자체는 확장할 수 없지만, **인터페이스와 그 인터페이스를 구현하는 기본 열거 타입을 함께 사용해 같은 효과를 낼 수 있다.**
    - 이렇게 하면 클라이언트는 이 인터페이스를 구현해 자신만의 열거 타입 또는 다른 타입을 만들 수 있다.
- 만약 API가 기본 열거 타입을 직접 명시하지 않고 인터페이스 기반으로 작성되었다면 기본 열거 타입의 인스턴스가 쓰이는 모든 곳을 새로 확장한 열거 타입의 인스턴스로 대체해 사용할 수 있다.