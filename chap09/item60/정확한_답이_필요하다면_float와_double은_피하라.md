# 정확한 답이 필요하다면 float와 double은 피하라

## float 와 double

float와 double 타입은 넓은 범위의 수를 빠르게 정밀한 '근사치'로 계산하도록 설계되었기 때문에, 정확한 결과가 필요할 때는 사용하면 안 된다.

특히 금융 관련 계산과는 맞지 않는다. 0.1 혹은 10의 음의 거듭제곱 수를 표현할 수 없기 때문이다.

```java
// 코드 60-1 오류 발생! 금융 계산에 부동소수 타입을 사용했다.
public static void main(String[] args) {
    double funds = 1.00;
    int itemsBought = 0;
    for (double price = 0.10; funds >= price; price += 0.10) {
        funds -= price;
        itemsBought++;
    }
    System.out.println(itemsBought + "개 구입");
    System.out.println("잔돈(달러): " + funds);
}

// 결과
// 3개 구입
// 잔돈(달러): 0.3999999999999999
```

위의 프로그램을 실행해보면 잘못된 결과가 나온다.

아이템을 3개 구입한 후 잔돈은 0.3999999999999999 달러가 남게 된다.

이를 해결하기 위해서는 `BigDecimal` , `int` , 혹은 `long` 을 사용해야 한다.

## BigDecimal → 우아한 기술 블로그에서 하는 것

```java
// 코드 60-2 BigDecimal을 사용한 해법. 속도가 느리고 쓰기 불편하다.
public static void main(String[] args) {
    final BigDecimal TEN_CENTS = new BigDecimal(".10");

    int itemsBought = 0;
    BigDecimal funds = new BigDecimal("1.00");
    for (BigDecimal price = TEN_CENTS;
         funds.compareTo(price) >= 0;
         price = price.add(TEN_CENTS)) {
        funds = funds.subtract(price);
        itemsBought++;
    }
    System.out.println(itemsBought + "개 구입");
    System.out.println("잔돈(달러): " + funds);
}

// 결과
// 4개 구입
// 잔돈(달러): 0.00
```

위와 같이 작성하면 올바른 답이 나온다.

단, BigDecimal에는 2가지 단점이 있다.

1. 기본 타입보다 쓰기가 훨씬 불편하다.
2. 기본 타입보다 훨씬 느리다.

## int, long 타입

BigDecimal의 대안으로 int 혹은 long 타입을 쓸 수 있다.

단, 다음과 같은 단점이 있다.

1. 값의 크기가 제한되고
2. 소수점을 직접 관리해야 한다.

위의 예제에서는 달러 대신 센트로 수행하면 문제를 해결할 수 있다.

```java
// 코드 60-3 정수 타입을 사용한 해법
public static void main(String[] args) {
    int itemsBought = 0;
    int funds = 100;
    for (int price = 10; funds >= price; price += 10) {
        funds -= price;
        itemsBought++;
    }
    System.out.println(itemsBought + "개 구입");
    System.out.println("잔돈(센트): " + funds);
}

// 결과
// 4개 구입
// 잔돈(센트): 0
```

📌 핵심 정리

- 정확한 답이 필요한 계산에는 float나 double은 피하라.
- 소수점 추적은 시스템에 맡기고, 코딩 시의 불편함이나 성능 저하를 신경 쓰지 않겠다면 BigDecimal을 사용하라. BigDecimal이 제공하는 여덟 가지 반올림 모드를 이용하여 반올림을 완벽히 제어할 수 있다. 법으로 정해진 반올림을 수행해야 하는 비즈니스 계산에서 아주 편리한 기능이다.
- 반면, 성능이 중요하고 소수점을 직접 추적할 수 있고 숫자가 너무 크지 않다면, int나 long을 사용하라.
- 숫자를 아홉 자리 십진수로 표현할 수 있다면 `int` 를 사용하고, 열여덟 자리 십진수로 표현할 수 있다면 `long` 을 사용하라. 열여덟 자리를 넘어가면 `BigDecimal` 을 사용해야 한다.