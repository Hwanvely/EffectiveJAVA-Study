## **추상 클래스 vs 인터페이스**

자바 8부터 인터페이스에 디폴트 메서드default method를 제공하기 때문에, 두 방식 모두 인스턴스 메서드를 제공할 수 있다. 차이라면 추상 클래스는 좀 더 상세한 구현과 필드를 가질 수 있고, 인터페이스와 달리 다중상속은 불가능 하다는 점이다.

비슷한 역할이지만, 인터페이스는 추상 클래스에 비해 다양한 장점이 있다.

1. 기존 클래스에 손쉽게 새로운 인터페이스를 구현해넣을 수 있다.
    
    Comparable, Iterable, AutoCloseable 인터페이스가 새롭게 추가 되었을 때 표준 라이브러리의 기존 클래스가 이 인터페이스를 구현한 채로 릴리즈 됐다.
    
2. 인터페이스는 믹스인mixin 정의에 안성맞춤이다.
    
    혼합이라는 뜻을 가지고 있는 믹스인은, 믹스인을 구현할 클래스에 원래의 ‘주 타입’ 외에 특정 선택적 행위를 제공한다고 선언하는 효과를 줄 수 있다.
    
3. 계층구조 없는 타입 프레임워크를 만들 수 있다.
    
    물론 계층이 적절한 개념도 존재하지만, 현실에는 계층을 엄격히 구분하기 어려운 개념도 있다. 책에 등장하는 Singer, Songwriter 인터페이스를 생각해보자.
    

```java
public interface Singer {
	AudioClip sing(Song s);
}

public interface Songwriter {
	Song compose(int chartPosition);
}
```

현실에는 싱어송라이터도 있으므로, 해당 개념을 구현하려면 다음처럼 새로운 계층을 만들면 된다.

```java
public interface SingerSongwriter extends Singer, Songwriter {
	AudioClip strum();
	void actSensitive();
}
```

위의 세 사례 모두 추상클래스에선 구현하기 어려운 부분이다.

## **디폴트 메서드**

인터페이스의 메서드 중 구현 방법이 명백한 것이 있다면, 그 구현을 디폴트 메서드로 제공할 수 있다. 디폴트 메서드는 상속하려는 사람을 위해 @implSpec 자바독 태그를 활용하면 좋다.

인스턴스 필드를 활용할 수 없고, private하지 않은 정적 멤버도 가질 수 없다는 한계가 있다.

## **추상 골격 구현 클래스**

디폴트 메서드가 가지고 있는 단점을 극복하기 위해, 인터페이스와 *추상 골격 구현* 클래스를 함께 제공하는 방식으로 인터페이스와 추상 클래스의 장점을 모두 취할 수 도 있다. 인터페이스로는 타입을 정의하고, 골격 구현 클래스는 나머지 메서드를 구현한다. 이렇게 해두면 골격 구현 클래스를 확장하는 것 만으로 인터페이스를 구현하는 데 필요한 일이 대부분 완료된다. 이를 **템플릿 메서드 패턴** 이라 부른다.

구조상 추상 골격 구현 클래스를 활용 못하는 경우 (다른 클래스 상속)에도 인터페이스를 사용하면 그만이므로, 기존 추상 클래스가 가지고 있단 제약에서도 벗어난다.

****구현하기****

골격 구현 작성은 다음과 같은 과정을 거쳐 만들면 된다.

1. 인터페이스를 살펴 다른 메서드들의 구현에 사용되는 기반 메서드를 선정한다.
2. 골격 구현에서 기반메서드가 추상메서드가 된다.
3. 기반 메서드들을 사용해 직접 구현할 수 있는 메서드는 모두 디폴트 메서드로 제공한다. equals, hashcode와 같은 Object의 메서드는 디폴트 메서드로 제공하면 안 된다는 걸 유념하자.
4. 만약 인터페이스의 메서드 모두가 기반 메서드나 디폴트 메서드가 된다면, 굳이 골격 구현 클래스를 만들 필요가 없다. 기반 메서드나 디폴트 메서드로 만들지 못한 메서드가 남아있다면, 이 인터페이스를 구현하는 골격 구현 클래스를 하나 만들어 남은 메서드를 작성해 넣는다.

****주의사항****

추상 골격 구현 또한 상속을 사용하는 걸 가정하므로 item19에서 강조한 설계 및 문서화 지침에 따라야 한다.