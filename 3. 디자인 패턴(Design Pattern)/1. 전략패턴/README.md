# 전략 패턴
- 전략 패턴은 알고리즘군을 정의하고 캡슐화하여 각각의 알고리즘군을 수정해서 쓸 수 있게 해 줍니다. 
- 전략 패턴을 사용하면 클라이언트로부터 알고리즘을 분리하여 독립적으로 변경할 수 있습니다.

* * * 
### 객체 지향 기초
- 추상화
- 캡슐화
- 다형성 
- 상속

### 객체 지향 원칙 
- 바뀌는 부분은 캡슐화한다.
- 상속보다는 구성을 활용한다.
- 구현보다는 인터페이스에 맞춰 프로그래밍 한다.


#### Duck.java

```java 
package strategy;

public abstract class Duck {
    FlyBehavior flyBehavior;
    QuackBehavior quackBehavior;

    public Duck() {}

    public void setFlyBehavior(FlyBehavior flyBehavior) {
        this.flyBehavior = flyBehavior;
    }

    public void setQuackBehavior(QuackBehavior quackBehavior) {
        this.quackBehavior = quackBehavior;
    }

    abstract void display();

    public void performFly() {
        flyBehavior.fly();
    }

    public void performQuack() {
        quackBehavior.quack();
    }

    public void swim() {
        System.out.println("All ducks float, even decoys");
    }
}
```

#### FlyBehavior.java

```java 
package strategy;

public interface FlyBehavior {
    public void fly();
}
```

### FlyWithWings.java
```java
package strategy;

public class FlyWithWings implements FlyBehavior {
    public void fly() {
        System.out.println("I'm flying");
    }
}
```

#### FlyNoWay.java
```java
package strategy;

public class FlyNoWay implements FlyBehavior {
    public void fly() {
        System.out.println("I can't fly");
    }
}
```

#### FlyRocketPowered.java
```java
package strategy;

public class FlyRocketPowered implements FlyBehavior {
    public void fly() {
        System.out.println("I'm flying with a rocket");
    }
}
```

#### QuackBehavior.java

```java
package strategy;

public interface QuackBehavior {
    public void quack();
}
```

#### Quack.java 
```java 
package strategy;

public class Quack implements QuackBehavior {
    public void quack() {
        System.out.println("Quack");
    }
}
```

#### MuteQuack.java
```java
package strategy;

public class MuteQuack implements QuackBehavior {
    public void quack() {
        System.out.println("<< Silence >>");
    }
}
```

#### FakeQuack.java
```java
package strategy;

public class FakeQuack implements QuackBehavior {
    public void quack() {
        System.out.println("Qwak");
    }
}
```

#### Squeak.java
```java
package strategy;

public class Squeak implements QuackBehavior {
    public void quack() {
        System.out.println("Squeak");
    }
}
```

#### MallardDuck.java
```java
package strategy;

public class MallardDuck extends Duck {
    public MallardDuck() {
        quackBehavior = new Quack();
        flyBehavior = new FlyWithWings();
    }

    public void display() {
        System.out.println("I'm a real Mallard duck");
    }
}
```

#### RedHeadDuck.java
```java
package strategy;

public class RedHeadDuck extends Duck {
    public RedHeadDuck() {
        flyBehavior = new FlyWithWings();
        quackBehavior = new Quack();
    }

    public void display() {
        System.out.println("I'm a real Red Headed duck");
    }
}
```

#### ModelDuck.java
```java
package strategy;

public class ModelDuck extends Duck {
    public ModelDuck() {
        flyBehavior = new FlyNoWay();
        quackBehavior = new Quack();
    }

    public void display() {
        System.out.println("I'm a model duck");
    }
}
```

#### DecoyDuck.java
```java
package strategy;

public class DecoyDuck extends Duck {
    public DecoyDuck() {
        setFlyBehavior(new FlyNoWay());
        setQuackBehavior(new MuteQuack());
    }

    public void display() {
        System.out.println("I'm a duck Decoy");
    }
}
```

#### RubberDuck.java
```java 
package strategy;

public class RubberDuck extends Duck {
    public RubberDuck() {
        flyBehavior = new FlyNoWay();
        quackBehavior = () -> System.out.println("Squeak");
    }

    public RubberDuck(FlyBehavior flyBehavior, QuackBehavior quackBehavior) {
        this.flyBehavior = flyBehavior;
        this.quackBehavior = quackBehavior;
    }

    public void display() {
        System.out.println("I'm a rubber duckie");
    }
}
```

#### MiniDuckSimulator.java
```java 
package strategy;

public class MiniDuckSimulator {
    public static void main(String[] args) {
        MallardDuck mallard = new MallardDuck();
        FlyBehavior cantFly = () -> System.out.println("I can't fly");
        QuackBehavior squeak = () -> System.out.println("Squeak");
        RubberDuck rubberDuckie = new RubberDuck(cantFly, squeak);

        DecoyDuck decoy = new DecoyDuck();

        Duck model = new ModelDuck();

        mallard.performQuack();
        rubberDuckie.performQuack();
        decoy.performQuack();

        model.performFly();
        model.setFlyBehavior(new FlyRocketPowered());
        model.performFly();
    }
}

- 실행 결과
Quack
Squeak
<< Silence >>
I can't fly
I'm flying with a rocket
```

#### MiniDuckSimulator1.java
```java
package strategy;

public class MiniDuckSimulator1 {
    public static void main(String[] args) {
        Duck mallard = new MallardDuck();
        mallard.performQuack();
        mallard.performFly();

        Duck model = new ModelDuck();
        model.performFly();
        model.setFlyBehavior(new FlyRocketPowered());
        model.performFly();
    }
}

- 실행 결과
Quack
I'm flying
I can't fly
I'm flying with a rocket
```