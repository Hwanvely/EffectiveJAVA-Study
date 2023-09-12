```java
public class Bigram {
    private final char first;
    private final char second;

    public Bigram(char first, char second) {
        this.first  = first;
        this.second = second;
    }

    public boolean equals(Bigram b) {
        return b.first == first && b.second == second;
    }

    public int hashCode() {
        return 31 * first + second;
    }

    public static void main(String[] args) {
        Set<Bigram> s = new HashSet<>();
        for (int i = 0; i < 10; i++)
            for (char ch = 'a'; ch <= 'z'; ch++)
                s.add(new Bigram(ch, ch));
        System.out.println(s.size());
    }
}
```

main 메서드가 실행되고 Set은 중복을 허용하지 않으니 26이 출력 될 것 같지만, 실제로는 260이 출력된다.

해당 문제의 원인은, equals 메소드를 재정의(`overriding`)한 게 아니라 **다중정의(`overloading`)** 해버린 것이다. 즉 Object의 `equals()` 를 재정의하려면 매개변수 타입을 Object로 해야 하는데 그렇지 않아서 상속이 아닌 별개의 equals 메서드를 정의한 꼴이 되었다.

또한 `==` 연산자와 똑같이 객체의 식별성(identity)만 확인하고 있기 때문에, 소문자를 소유한 바이그램 10개 각각이 다른 객체로 인식되고 결국 260이 출력된 것이다.

▶️ equals 메서드 수정

```java
@Override public boolean equals(Object o) {
        if (!(o instanceof Bigram2))
            return false;
        Bigram2 b = (Bigram2) o;
        return b.first == first && b.second == second;
}
```

equals에 문제가 있음에도 컴파일에는 성공하기 때문에, `@Override` 를 달지 않게 되면 위의 잘못한 점을 컴파일러가 알려주지 못한다.

**따라서, 상위 클래스의 메서드를 재정의하려는 모든 메서드에는 @Override 애너테이션을 달자.**

### 예외 사항

**구체 클래스에서 상위 클래스의 메서드를 재정의**할 때는, 굳이 `@Override`를 달지 않아도 된다. 구체 클래스인데 구현하지 않은 추상 메서드가 남아 있다면 컴파일러가 자동으로 사실을 알려주기 때문이다.

### 🔗 인터페이스와 @Override

`@Override` 는 클래스 뿐만 아니라, 인터페이스의 메서드를 재정의 할 때도 사용할 수 있다. 디폴트 메서드를 지원하기 시작하면서, 인터페이스 메서드를 구현한 메서드에도 `@Override`를 다는 습관을 들이면 시그니처가 올바른지 재차 확신할 수 있다. 디폴트 메서드가 없다는 것을 안다면 생략해도 좋다.

단, 추상 클래스나 인터페이스에는 상위 클래스나 상위 인터페이스의 메서드를 재정의 하는 모든 메서드에 `@Override`를 다는 것이 좋다.

예컨대 Set 인터페이스는 Collection 인터페이스를 확장했지만 새로 추가한 메서드는 없기 때문에 모든 메서드 선언에 `@Override`를 달아 실수로 추가한 메서드가 없음을 보장했다.

> 정리
> 
> 
> 재정의한 모든 메서드에 @Override 애너테이션을 의식적으로 달면, 여러분이 실수했을때 컴파일러가 바로 알려줄 것이다. 예외는 한 가지 뿐이다. 구체 클래스에서 상위 클래스의 추상 메서드를 재정의한 경우에는 이 애너테이션을 달지 않아도 된다.(달아도 상관 없다)
>