# OOP(Object Oriented Programming)

  

## OOP의 특성

### 1. 추상화(Abstraction)

- 공통의 속성이나 기능을 묶어 이름을 붙이는 것으로, 객체 지향적 관점에서 클래스를 정의하는 것을 추상화라고 한다.

- 예를 들어 물고기, 사자, 토끼, 뱀이 있을 때, 이들을 하나의 공통된 `동물`이라는 추상적 객체로 묶어 정의할 수 있다.

- `인터페이스`와 `구현`을 분리하여 객체가 가진 특성 중 필수 속성만으로 객체를 묘사하고, 유사성만을 표현해 세부적인 디테일은 각 객체에 따라 다르게 구현되도록 할 수 있다.(`인터페이스`, `추상 클래스`, `추상 메소드`를 사용)  

    #### Python AbstractClass & JAVA interface

    JAVA는 다형성을 통해서 Interface를 사용한다. 동일한 목적 하에 동일한 기능을 수행하게끔 공통된 규격을 부여하는 것으로, 인터페이스와 구현을 분리하여 `추상화`를 실현한다. 다음 예시는 JAVA에서의 인터페이스 구현이다.

    ```java
    /* interface 키워드로 선언
    어떤 언어로 번역할 지 그 동작을 정의하는 것은 언어에 따라서 달라져야한다.
    따라서 언어별로 객체를 따로 생성, 오버라이딩을 통해 다른 결과를 내는 동일 기능을 수행하도록 한다
    인터페이스 선언 시에는 어떠한 동작도 정의하지 않고, 그러한 이름의 메소드가 있음을 선언하기만 한다
    이 때 static, default나 상수가 아닌 기본적인 메소드 타입을 추상메소드(abstract)라고 지칭한다.
    추상메소드들은 반드시 implements(구현부)에서 오버라이딩되어 재구현되어야만 한다(아니면 오류 발생)*/
    public interface Translation {
      // 한국어를 번역하다
      public String translate(String str);
      // 한국어로 번역하다
      public String translateInto(String str);
      /*만약 반드시 고정된 기능을 수행해야 하는 메소드는 static 선언을 통해서 정적 메소드를 구현,
      오버라이딩을 할 수 없게 만들 수 있다.
      interface 클래스 내에 정의되는 상수 또한 static과 마찬가지로 재구현이 불가능한 상태다.
      default method를 선언하는 것도 가능하다
      이는 인터페이스에서 기본적으로 제공해주지만, 추상메소드와 달리 선택적으로 구현할 수 있다*/
    }
    
    
    // implements로 구현. @Override를 사용해 다형성에 따라 재정의
    public class EnglishTranslation implements Translation {
      @Override
      public String translate(String str) {
        //TODO: 한국어를 번역
      }
      @Override
      public String translateInto(String str) {
        //TODO: 한국어로 번역
      }
    }
    
    public class ChineseTranslation implements Translation {
      @Override
      public String translate(String str) {
        //TODO: 한국어를 번역
      }
      @Override
      public String translateInto(String str) {
        //TODO: 한국어로 번역
      }
    }
    
    // 언어에 따라서 동일한 메소드에 대해 동일 기능/다른 동작을 수행하게 되었다
    Translation trans = new EnglishTranslation();
    String str = trans.translate(str);
    
    Translation trans = new ChineseTranslation();
    String str = trans.translate(str);
    ```

    Python은 인터페이스를 JAVA와 같은 `interface`, `implements`  대신 `abc`모듈의 `ABC` 클래스와 `abstractmethod` 데코레이터를 사용해 구현한다. 다음은 Python의 인터페이스 구현 예시다.

    - 추상 클래스는 구현 내용이 없고 메소드 목록만 가진 클래스로, 추상 클래스 자체로는 인스턴스화가 불가능하다. 추상클래스는 클래스의 메소드를 상속받는 파생 클래스에게 구현하도록 강제하기 위해서 사용된다.

    ```python
    from abc import ABC, abstractmethod
    
    class Shape(ABC) :
        @abstractmethod
        def Draw(self) :
            pass
    
    class Rect(Shape) :
        pass # 아무것도 재구현하지 않은 경우, 사용시 인스턴스화가 불가능해진다
    
    s = Shape() # TypeError: Can't instantiate abstract class Shape with abstract method Draw
    r = Rect()
    r.Draw()
    
    class Rect(Shape) :
        def Draw(self) : # 추상메소드로 선언된 메소드는 반드시 재구현되어야 한다
            print('Draw Rect')
    
    r = Rect()  
    r.Draw()     # Draw Rect
    ```

    그러나 Python에서의 Abstract method를 통한 Interface 구현은 다음과 같은 한계가 존재한다.

    - `abstract method`를 implementation하는 것은 강제하지만, subclass(상속받은 자식 클래스)에서 메소드의 parameter까지는 강제하지 않는다.
    - `abstract method`는 `@classmethod`와 `@staticmethod`를 데코레이터와 함께 사용될 수 있는데, implementing class는 method type을 바꾸는데 제약을 받지 않는다.
    - `abstract method`는 `@property` 데코레이터와 사용 가능하나 implementing class는 callable하도록 결정될 수 있다. 이러한 동작이 Python interpreter에서 제약없이 통과된다.  



### 2. 캡슐화(Encapsulation)

- `데이터 = 속성(필드)`와 `데이터를 다루는 행위 = 행위(메소드)`를 하나로 묶고, `접근 지정자`를 통해 제어하는 것. 설정된 클래스의 필드값을 바꾸기 위해서는 getter, setter와 같이 메서드(함수)를 통해서 접근해야 한다.
- `은닉화` : 객체의 세부 내용이 외부에 드러나지 않아 외부에서 객체 데이터에 직접 접근하는 것을 방지한다.(`Private`)  



### 3. 상속성(Inheritance)

- 자식 클래스가 부모 클래스의 특징과 기능을 물려받는 것으로, 클래스를 상속받은 후 이를 수정하여 사용하는 방식을 취하기 때문에 중복코드를 줄일 수 있다.
- 부모 클래스의 수정으로 모든 자식 클래스가 수정되는 효과가 있으며, 클래스에 메소드 추가 등 변경이 어려운 경우, 혹은 해당 클래스가 다양한 곳에서 상속받아 쓰이는 경우에 상속을 유용하게 활용 가능하다.
    - 자신이 아닌 남이 만든 클래스를 가져오는 경우(프레임워크 등), 클래스를 custumize할 때 상속을 사용
    - 클래스가 다양한 곳에서 사용되는 경우, 그 때 그 때 클래스를 변형시키면 여러 클래스가 영향을 받거나, 불필요한 메서드가 다른 클래스에 추가되어버린다. 따라서 클래스를 그대로 두고, 이를 상속시킨 자식 클래스를 새로 정의하면서 필요한 메서드를 따로 추가해 줄 수 있다.  



### 4. 다형성(Polymorphism)

- 메시지에 의해 객체(Class)가 연산을 수행할 때, 하나의 메시지에 대해 각 객체가 가지고 있는 고유한 방법으로 응답하는 능력. 즉, 객체의 변수나 메소드가 상황에따라 다른 결과를 내는 것을 의미한다.

- `오버라이딩(Overriding)`: 부모 클래스의 메소드를 자식 클래스에서 재정의하는 것.

- `오버로딩(Overloading)`: 한 클래스에서 메소드 이름은 같지만 파라미터 개수나 자료형을 다르게하여 서로 다르게 동작하는 것. 이름이 똑같지만 매개변수가 다른 메소드를 여러 개 정의하는 것을 의미한다.  

    

