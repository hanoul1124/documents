# SOLID

​    

## 객체지향 개발 5대 원리

참조: https://www.nextree.co.kr/p6960/  


<br>


### 1. SRP (단일 책임의 원칙. Single Responsibility Principle)

- > 하나의 클래스는 하나의 책임만 가지며, 그 책임에 대해 완전히 캡슐화 해야 한다

- 작성된 클래스는 하나의 기능만을 가지며, 클래스가 제공하는 모든 서비스는 그 하나의 책임을 수행하는 데에 집중되어야 한다. 즉, 하나의 클래스는 하나의 책임만을 가져야 하며, 클래스는 그 책임을 완전히 `캡슐화`해야 한다는 것을 의미한다. 이 때, 어떤 변화에 의해 클래스를 변경해야 하는 이유는 오직 하나 뿐이어야 한다.

- 이 원칙을 적용하는 것으로 책임영역이 확실하게 만들 수 있고, 변경 시의 연쇄작용에서 비교적 자유로울 수 있다. 또한 적절한 책임 분배로 코드 가독성 향상이나 유지 보수에 도움이 되기 때문에 객체지향 원리의 기초가 된다. 이러한 단일 책임 원칙은 관계형 데이터베이스 설계에서의 정규화 개념과도 유사하다. 객체의 속성이나 메소드 중에서 서로 특성이 다른 요소 그룹이 발견되면 이들을 적절히 분리시킨다.

- 리팩토링에서 다루는 많은 영역이 SRP 원리와 관련이 있다. 여러 책임을 지닌 객체로부터 `Extract Class`, 혹은 분리된 클래스 간의 중복된 책임에 대한 `Extract Superclass` 로 클래스 관계에서의 복잡도를 줄이도록 책임을 분리한다.

- 예를 들어 ‘Guitar’라는 Class object가 존재할 때, 기타의 상세 스펙에 대한 정보를 별도의 Class로 분리하여 ‘GuitarSpec’ Class를 추출한다.  기존의 Guitar Class에는 어떤 기타를 지칭하고 있는지에 대한 SerialNumber만이 저장되며, 해당 기타의 자세한 스펙 정보는 GuitarSpec Class에 저장된다. SerialNumber는 변화요소가 아닌 고유정보이며, 기타의 디테일한 스펙 사항은 변화가 가능한 요소에 해당한다. 따라서 이들을 분리하는 것으로 기타의 일부 특성 정보를 수정할 때 Guitar Class가 아닌 GuitarSpec Class만 변경하는 형태로 동작이 구성된다.

- 단 오로지 무작정 분리하는 것으로 SRP가 적용된다는 것은 아니다. 각 개체 간의 응집력에 따라서 산발적으로 분리된 책임을 한 곳에 모아 ‘병합'하는 방식 또한 원칙을 달성하는 수단이 되기도 한다.(산탄총 수술. Shotgun surgery)  

- ```python  
    # https://github.com/doorBW/python_clean_code
    # Example 1
    class Course:
        """
        너무 많은 책임을 가진 Course 클래스.
        Course 객체의 속성 관리 및 Course DB 관리를 겸하고 있다.
        """
        
        def __init__(self, code, name, schedule, pf):
            self.code = code
            self.name = name
            self.schedule = schedule
            self.pf = pf
        
        def connect(self, con)->bool:
            """DB connecting"""
            pass
        
        def close(self)->bool:
            """DB close"""
            pass
        
        def get_course_by_pf(self, professor)->list:
            """professor로 강의 찾기 함수"""
            pass
        
        def save_course(self, Course)->bool:
            """DB에 course 저장하기"""
            pass
        
        def update_course(self, code)->bool:
            """특정 code를 가진 course를 DB update 하기"""
            pass
    
    
    class Course:
        """SRP원칙을 준수한 Course 클래스"""
        
        def __init__(self, code, name, schedule, pf):
            self.code = code
            self.name = name
            self.schedule = schedule
            self.pf = pf
     
    class CourseDB:
        """SRP원칙을 준수한 CourseDB 클래스"""
        def __init__(self, con):
            self.con = con
        
        def connect(self)->bool:
            """DB connecting"""
            pass
        
        def close(self)->bool:
            """DB closing"""
            pass
        
        def get_course_by_pf(self, professor)->list:
            """professor로 강의 찾기 함수"""
            pass
        
        def save_course(self, Course)->bool:
            """DB에 course 저장하기"""
            pass
        
        def update_course(self, code)->bool:
            """특정 code를 가진 course를 DB update 하기"""
            pass
        
    # Example 2
    class Human:
        """추상화 과정이 필요한 Human 클래스"""
        def __init__(self, name, sex):
            self.name = name
            self.sex = sex
            
        def go_restroom(self):
            """
            화장실 가는 함수.
            성별이 다른 두 케이스에 대한 책임을 동시에 가지게 된다
            """
            if(self.sex == '남자'):
                print("남자화장실로 간다.")
            elif(self.sex == '여자'):
                print("여자화장실로 간다.")
            else:
                print("성별을 지정해주세요.")
    
    
    from abc import *
     
    class HumanBase(metaclass=ABCMeta):
        def __init__(self, name):
            self.name = name
        
        @abstractmethod
        def go_restroom(self):
            pass
        
    # 성별에 따라서 분리할 수 있도록 HumanBase라는 인터페이스를 구성
    class Male(HumanBase):
        def __init__(self, name):
            super().__init__(name)
            self.sex = "남자"
        
        def go_restroom(self):
            print("남자화장실로 간다.")
            
    class Female(HumanBase):
        def __init__(self, name):
            super().__init__(name)
            self.sex = "여자"
            
        def go_restroom(self):
            print("여자화장실로 간다.")  
            
    ```

    ​    

<br>

### 2. OCP (개방폐쇄의 원칙. Open Close Principle)

- >  소프트웨어 구성요소(컴포넌트, 클래스, 모듈, 함수)는 기능 확장에는 열려있고, 기능 변경에는 닫혀있어야 한다. 

- 변경을 위한 비용은 가능한 줄이고 확장을 위한 비용은 가능한 극대화하도록 만들어야 한다. 즉, 새로운 기능을 추가할 때에는 신규 기능에 대한 소스 추가만 진행해야 하며, 기존의 코드를 수정해야 하는 일은 없어야 한다는 것이다.

- OCP를 가능하게 만드는 중요한 OOP의 메커니즘은 `추상화`와 `다형성`이다. 

- 변경(확장)될 것과 변하지 않을 것을 엄격히 구분해야 한다. 이들이 관계되는 지점에 인터페이스를 정의한다. 정의한 인터페이스에 의존하도록 코드를 작성한다. 단, 확장되는 것과 변경되지 않는 모듈을 분리하는 과정에서 필요한 기능을 적절히 예측하며 설계해야만 한다. 인터페이스는 가능하면 변경되어서는 안되므로, 이를 정의할 때 세심한 주의가 필요하다.

- 예를 들어, 위의 Guitar와 GuitarSpec class의 예시를 다시 생각해보자. 기타가 아니라 만약 바이올린에 대한 정보도 기록해야한다면 어떨까? Violin Class와 ViolinSpec Class로 분리하여 구성해야할까? 여기서 더 악기가 늘어난다면 어떻게 될 것인가? 이 경우, 악기와 악기 스펙으로 공통된 특성과 기능을 추출해내 추상화 레벨을 올려야 한다. StringInstrument, StringInstrumentSpec Class를 인터페이스로 생성하고, 각각의 악기는 이들을 확장한 Class로 만든다. JAVA의 경우 `extends(상속)` 혹은 `implements`를 통해서 확장시킬 수 있다.

- ```java  
    class Guitar extends StringInstrument(){
    	public Guitar(String serialNumber, GuitarSpec spec) {
            this.serialNumber = serialNumber;
            this.spec = spec;
        }
        private GuitarSpec spec;
    }
    
    class GuitarSpec extends StringIntrumentSpec(){
    }  
    ```

<br>      

### 3. LSP(리스코브 치환의 원칙. The Liskov Substitution Principle)

- > 만약 S가 T의 하위 타입이라면, 프로그램을 변경하지 않고 T타입의 객체를 S타입의 객체로 치환 가능해야 한다.

- 서브타입은 기반 타입이 약속한 규약(public 인터페이스)을 지켜야 한다. 상속은 구현상속(extends), 인터페이스 상속(implements)과 상관없이 다형성을 통해 확장성을 획득하도록 만든다. LSP원리는 서브클래스가 확장에 대한 인터페이스를 준수해야 함을 의미한다. 이러한 점에서 OCP는 LSP를 포함하고 있는 관계라고 할 수 있다.

- 이 때, 상속을 통한 재사용은 두 클래스 사이에 **IS - A** 관계가 있을 경우로만 제한되어야 하며(단순히 현실에서의 의미만이 아니라, 공유되는 연산의 유무와 기능의 디테일한 차이점에 대해 고려해야 한다), 그 외의 경우는 구성(Composition)을 사용해야 한다. 

- **Replace with Delegation**: 다형성을 위한 상속관계가 필요 없다면, **상속을 위임으로 치환**하는 리팩토링 기법을 적용한다. 위임은 다른 인스턴스의 메소드를 호출해서 처리하는 것을 지칭하는 것으로, **HAS - A** 관계에 해당한다.

- 상속구조가 필요하다면 **Extract Subclass**, **Push Down Field**, **Push Down Method** 등의 리팩토링 기법을 이용해 LSP를 준수하는 상속 계층 구조를 만든다.

    - Push Down Method: 기반클래스(슈퍼클래스)의 어떤 메소드가 특정 서브클래스에 대해서만 관련된 경우, 해당 메소드를 그 서브클래스로 옮겨준다.
    - Push Down Field: 기반클래스(슈퍼클래스)의 어떤 필드가 특정 서브클래스에서만 사용되는 경우, 해당 필드를 그 서브클래스로 옮겨준다.

- **Design by Contract** 적용: 기반 클래스를 서브클래스로 치환 가능하게 하려면 두 클래스 간의 제약 조건이 동작에 필요한 관계성을 만족해야 한다. 기반 클래스에서 실행되던 동작이 서브 클래스에서도 원활하게 실행되어야 하고, 반대로 기반 클래스에서 실행되면 안되는 상황에서 서브 클래스가 동작을 실행하게 될 수 있다. 이는 다시 말해서, 파라미터의 타입과 같은 세부적인 조건에서도 기반 클래스의 동작이 서브 클래스로 대체되었을 때에 동일하게 적용되어야 한다는 것이다. 

    - 1. 하위 클래스는 부모 클래스에 정의된 것보다 사전조건을 엄격하게 만들면 안 된다.
    - 2. 하위 클래스는 부모 클래스에 정의된 것보다 약한 사후조건을 만들면 안된다.

- ```python
    # https://github.com/doorBW/python_clean_code
    # LSP를 지키지 못한 예제 1
    class Event:
        """Super class: Event class"""
        def __init__(self, event_data: dict):
            this.event_data = event_data
            
        def meet_condition(self, event_data: dict) -> bool: # 서브클래스와 다른 타입으로 정의됨
            return False
        
    class LoginEvent(Event):
        """Sub class: LoginEvent class"""
        def meet_condition(self, event_data: list) -> bool: # 서브클래스가 리스트 사용, 오류발생
            return event_data[0] == 'login'
    
    
    
    # LSP를 적용한 예제 1
    class Event:
        """Super class: Event class"""
        def __init__(self, event_data: dict):
            this.event_data = event_data
            
        def meet_condition(self, event_data: dict) -> bool:
            return False
        
    class LoginEvent(Event):
        """Sub class: LoginEvent class"""
        def meet_condition(self, event_data: dict) -> bool: # list to dict
            return event_data['before']["session"] == 0 and event_data['after']["session"] == 1
    
    
    
    # LSP를 지키지 못한 예제 2
    class Event:
        """Super class: Event class"""
        def __init__(self, event_data: dict):
            self.event_data = event_data
     
        @staticmethod
        def meets_condition(event_data: dict) -> bool:
            return False
        
        @staticmethod    
        def meets_condition_pre(event_data: dict) -> bool:
            assert isinstance(event_data, dict), f"{event_data!r} is not a dictionary."
            for data in ["before","after"]:
                assert data in event_data, f"{data} is not in {event_data}."
        
    class LoginEvent(Event):
        """Sub class: LoginEvent class"""
        @staticmethod
        def meets_condition(event_data: dict) -> bool:
    		# assert "session" in event_data["before"] and "session" in event_data["after"]
            return event_data['before']["session"] == 0 and event_data['after']["session"] == 1
     
            
    class UnknownEvent(Event):
        def meet_condition(self, event_data: dict) -> bool:
            return True
        
    class SystemMonitor:
        def __init__(self, event_data):
            self.event_data = event_data
            
        def identify_event(self):
            Event.meets_condition_pre(self.event_data)
            event_cls = next(
                (event_cls for event_cls in Event.__subclasses__() if event_cls.meets_condition(self.event_data)),
                UnknownEvent
            )
            return event_cls(self.event_data)
    
    
    # event_data로 들어올 session이 비어있어 오류가 발생한다.
    l3 = SystemMonitor({"before":{}, "after":{"session":0}})
    print(f"l3 is {l3.identify_event().__class__.__name__!r}")
    
    
    # LSP를 적용한 예제 2
    class Event:
        """Super class: Event class"""
        def __init__(self, event_data: dict):
            self.event_data = event_data
     
        @staticmethod
        def meets_condition(event_data: dict) -> bool:
            return False
        
        @staticmethod    
        def meets_condition_pre(event_data: dict) -> bool:
            assert isinstance(event_data, dict), f"{event_data!r} is not a dictionary."
            for data in ["before","after"]:
                assert data in event_data, f"{data} is not in {event_data}."
        
    class LoginEvent(Event):
        """Sub class: LoginEvent class"""
        @staticmethod
        def meets_condition(event_data: dict) -> bool:
            # .get() 을 사용하는 것으로 데이터에 해당 value가 없더라도 오류가 발생하지 않도록 만든다
            return event_data['before'].get("session") == 0 and event_data['after'].get("session") == 1
            
    class UnknownEvent(Event):
        def meet_condition(self, event_data: dict) -> bool:
            return True
        
    class SystemMonitor:
        def __init__(self, event_data):
            self.event_data = event_data
            
        def identify_event(self):
            Event.meets_condition_pre(self.event_data)
            event_cls = next(
                (event_cls for event_cls in Event.__subclasses__() if event_cls.meets_condition(self.event_data)),
                UnknownEvent
            )
            return event_cls(self.event_data)
    ```

       
<br>
      

- #### HAS - A 와 **IS - A** 관계, 그리고 상속

    - **HAS - A**

        - > 자동차는 배터리를 가지고 있다 # 배터리는 자동차의 일부로서 포함(belongs to)

        - 구성관계(Composition)로, 한 오브젝트가 다른 오브젝트에 “속하는” 경우를 지칭한다. 즉, 객체의 멤버 필드라고 불리는 객체를 의미한다. 한 객체에서 다른 객체의 기능을 가져와 사용하는 것으로, 상속을 사용하지 않는다. 상속에 비해서 구성요소 변경이 쉽고 유연하다.

        - ```java
            class Legs {
            
                public String howManyLegs() {
                    return "I have four legs";
                }
            }
            
            class Dog {
                Legs legs = new Legs();
            
                public void aboutMe() {
                    System.out.println("I am a Dog class and " + legs.howManyLegs());
                }
            }
            ```
    <br>

    - **IS - A**

        - > 고양이는 동물이다. # 고양이는 동물의 서브클래스

        - 상속관계(Inheritance)이며, 추상화된 객체 사이의 포함관계. “A는 B이다”와 같은 관계로 표현된다. 한 클래스 A가 다른 클래스 B의 서브클래스(파생클래스)임을 설명한다. 타입 A는 타입 B의 명세(specification)을 의미하는 타입 B의 서브타입이라고도 한다. 

        - ```java  
            class Animal {
            
                public String whatAmI() {
                    return "I belong to Animal class";
                }
            }
            
            class Dog extends Animal {
                public void aboutMe() {
                    System.out.println("I am a Dog class and " + whatAmI());
                }
            }  
            ```

 <br>         

### 4. ISP(인터페이스 분리 원칙. Interface Segregation Principle)

- > 클라이언트가 자신이 이용하지 않는 메소드에 의존하면 안된다라는 원칙이다

- 한 클래스는 자신이 사용하지 않는 인터페이스는 구현하지 말아야 한다. 즉 어떤 클래스가 다른 클래스에 종속될 때에는 가능한 최소한의 인터페이스만을 사용해야 한다. 이는 다시 말해서 하나의 범용적인 인터페이스보다는 여러 개의 구체적인 용도의 인터페이스를 구성하는 것이 낫다는 것이다.

- **SRP가 클래스의 단일책임**을 강조한다면, **ISP는 인터페이스의** **단일책임을 강조**하는 것이다. 단순히 하나의 메소드만을 갖도록 만든다는 것이 아니라, 하나의 기능을 수행할 때 필요한 것들이라면 동일한 맥락의 책임으로서 여러 개 메소드가 존재할 수 있다.

- 클래스의 상속을 이용하여 인터페이스를 나누거나, 객체 인터페이스를 통해서 분리할 수 있다. 후자의 경우는 위임을 이용하는 방식으로, 다른 클래스의 기능을 사용해야 하나 그 기능을 변경하고 싶지 않을 경우 상속 대신 위임을 사용한다.

- ```python
    # https://github.com/doorBW/python_clean_code
    # ISP를 지키지 못한 예제
    from abc import *
     
    class EventParser(metaclass=ABCMeta):
        """Interface: EventParser class"""
        @abstractmethod
        def from_json(self, event_data):
            pass
        
        """
        json과 xml을 모두 abstract method로 다루므로 이를 구현부에서 모두 구현해야 한다
        그러나 데이터 타입이 다른만큼 동시에 사용할 일이 없음에도 불구하고 둘 다 구현하게 되어버린다
        """
        @abstractmethod
        def from_xml(self, event_data): 
            pass    
    
    # ISP를 적용한 예제
    from abc import *
     
    class JsonEventParser(metaclass=ABCMeta):
        """Interface: JsonEventParser class"""
        @abstractmethod
        def from_json(self, event_data):
            pass    
        
    class XmlEventParser(metaclass=ABCMeta):
        """Interface: XmlEventParser class"""
        @abstractmethod
        def from_xml(self, event_data):
            pass  
    ```

<br>
  

### 5. DIP(의존성 역전 원칙. Dependency Inversion Principle)

- > 추상화를 통해 세부 사항(하위 클래스)에 의존하지 않도록 해야 하지만, 반대로 세부 사항(구체적인 구현)은 추상화에 의존해야 한다
    >
    > 고수준 모듈(클래스)은 보다 저수준 모듈(클래스)에 대해 의존하면 안된다

- 이 원칙을 따르면, 상위 계층(정책 결정)이 하위 계층(세부 사항)에 의존하는 전통적인 의존관계를 반전(역전)시킴으로써 상위 계층이 하위 계층의 구현으로부터 독립되도록 분리할 수 있다.

- ![DIP_layers](./images/DIP_layers.png)

- `IOC/DI`, `훅 메소드`, `확장성`이 DIP의 핵심적인 키워드다. 이들 요소를 통해 복잡한 컴포넌트 간의 관계를 단순화하고 이들 사이의 커뮤니케이션을 효율적으로 운영한다.  

  ​    

<br>

    - **IOC(Inversion of Control)**

        - 프로그래머가 작성한 프로그램이 재사용 라이브러리의 흐름 제어를 받게 되는 프로그래밍 원칙을 말한다. 즉, 프로그래머가 직접 객체의 생성과 소멸, 객체 간 의존관계 등의 제어를 수행하는 것이 아니라, 여러 프레임워크와 컨테이너가 대신 제어를 수행하게 되는 설계 방식을 의미한다. 객체에 대한 제어권이 컨테이너로 역전되기 때문에 제어의 역전이라고 부른다. DI의 상위 개념이라고 할 수 있다.  

          ​    

<br>

    - **DI(Dependency Injection)**
    
        - 프로그래밍에서 구성요소간의 의존 관계가 소스코드 내부가 아닌 외부로부터 오도록 하는 것. 사용하는 객체가 아닌 외부의 독립적인 객체가 인스턴스를 생성한 후 이를 전달해서 의존성을 해결하는 방법을 의존성 주입이라고 부른다. 즉, 인스턴스화 할때 클래스의 생성자나 기타 메서드에 `object`를 인자로 주는것을 의미한다.객체 간의 강한 결합을 느슨한 결합으로 변화시켜 의존성을 약화시킨다.
    
            - 생성자 주입 / setter(메소드)를 통한 주입 / 인터페이스를 통합 주입의 유형이 존재
            - Python의 유연하며 컴파일되지 않는 동적 언어(`Duck typing`, `일급객체(First-Class object)`) 특성상 별도의 의존성 주입 프레임워크가 없이도 쉽게 이를 구현될 수 있다. 이러한 DI를 통해서, 각 컴포넌트가 느슨한 결합으로 이루어지므로 기능 변경과 확장이 쉬워진다. 컴포넌트의 실체 객체를 사용하지 않고 `Mocking(모조품화)`하여 주입하기 때문에 테스트에 용이하다. `명시적 의존성 주입(Explicit dependency)`을 통해 애플리케이션 구조를 쉽게 파악할 수 있어 유지보수 또한 쉬워진다. 명시적 의존성 주입은 의존 대상을 생성자의 인자로 전달하여 주입하는 방식을 의미한다.
    
        - ```python
            # 출처: https://velog.io/@chobe1/%ED%8C%8C%EC%9D%B4%EC%8D%AC-Dependency-Injection-%EC%9D%98%EC%A1%B4%EC%84%B1-%EC%A3%BC%EC%9E%85
                
            # 의존성 주입 이전
            class PngConversion:
                def converse(data: Data):
                	# png conversion algorithm
                	return data
            
            class JPEGConversion:
            	def converse(data: Data):
                    # jpeg conversion algorithm
                    return data
            
            class Transformer:
                def __init__(self, data: Data):
                    """
                    상위클래스(Transformer)와 하위클래스(PngConversion)의 강한 결합 방생
                    상위클래스 동작이 하위클래스 구현에 영향을 받는 형태가 된다
                    만약 상위클래스에서 conversion을 JPEGConversion 등
                    다른 객체 메서드를 사용하고 싶을 경우,
                    상위클래스 내부의 코드를 수정해야만 하는 문제가 생긴다
                    """
                    # 
                    self.converser = PngConversion()
                    self.data = data
                def converse(self):
                	self.converser.converse(self.data)
                    
            
            # 의존성 주입(Dependency Injection)을 통한 DIP 달성
            from abc import ABCMeta, abstractmethod
            
            # PngConversion, JPEGConversion을 추상화한 Conversion 인터페이스를 생성
            class Conversion(metaclass=ABCMeta):
                @abstractmethod
                def converse(data: Data):
                    pass
            
            # 하위클래스는 추상화(Conversion 인터페이스)에 의존하는 모습
            class PngConversion(Conversion):
                def converse(data: Data):
                  # png conversion algorithm
                    return data
              
            class JPEGConversion(Conversion):
                def converse(data: Data):
                  # jpeg conversion algorithm
                    return data
              
            class Transformer:
                """
                앞서 생성한 추상클래스 타입을 인자로 받도록 설정,
                해당 추상클래스를 상속받은 클래스(Png, JPEG)를 자유롭게 넘겨줄 수 있다(느슨한 결합)
                """ 
                def __init__(self, data: Data, converser: Conversion):
                    self.converser = converser
                    self.data = data
            
                def converse(self):
                    self.converser.converse(self.data)
                
            
            Transformer(data, PngConversion()).converse()
            Transformer(data, JPEGConversion()).converse()
            ```
            
        - ```python
            """
            여러 프레임워크들은 IOC와 DI를 활발하게 사용한다.
            Django는 Dictionary & Duck typing,
            DRF는 Class를 사용하여 Dependency Injection을 구현하고 있다.
            JAVA Spring은 XML을 통해 setter 인젝션을 수행한다
            """ 
            
            # settings.py in Django Dependency Injection
            CACHES = {
                'default': {
                    'BACKEND': 'django_redis.cache.RedisCache',
                    'LOCATION': REDIS_URL + '/1',
                },
                'local': {
                    'BACKEND': 'django.core.cache.backends.locmem.LocMemCache',
                    'LOCATION': 'snowflake',
                }
            }
            
            # DRF Dependency Injection
            class FooView(APIView):
                # 주입된 의존성들
                permission_classes = (IsAuthenticated, )
                throttle_classes = (ScopedRateThrottle, )
                parser_classes = (parsers.FormParser, parsers.JSONParser, parsers.MultiPartParser)
                renderer_classes = (renderers.JSONRenderer,)
            
                def get(self, request, *args, **kwargs):
                    pass
            
                def post(self, request, *args, **kwargs):
                    pass
            ```
            
        - Django의 경우에는 이러한 의존성 주입을 자체적으로 내장 구현하고 있으나, Flask나 FastAPI와 같은 타 프레임워크에서는 사용할 수 없는 방식이므로, 프레임워크에 종속적이지 않은 `Dependency injector` 라는 DI 프레임워크를 많이 사용한다.  
    
          ​    
          
  <br>
    
    - **훅 메소드(Hook method)**
    
        - 슈퍼클래스에서 디폴트 기능을 정의해두거나 비워뒀다가 서브클래스에서 선택적으로 오버라이드 할 수 있도록 만든 메소드. 서브클래스는 추상 메소드를 구현하거나, 훅 메소드를 오버라이딩 하는 방법을 사용해 기능을 확장한다. 
    
        - ```python  
            # Framework
            class UserStore():
                """
                The base class for accessing a user's information.
                The client must extend this class and implement its methods.
                """
                def get_name(self, token): # hook method
                    raise NotImplementedError 
            
            class WebFramework():
                def __init__(self, user_store: UserStore):
                    self.user_store = user_store
            
                def greet_user(self, token):
                    user_name = self.user_store.get_name(token)
                    print(f'Good day to you, {user_name}!')
                    
            
            # Client
            class AlwaysMaryUser(UserStore):
                def get_name(self, token): # override
                    return 'Mary'
            
            class SQLUserStore(UserStore):
                def __init__(self, db_params):
                    self.db_params = db_params
            
                def get_name(self, token):
                    # TODO: Implement the database lookup
                    raise NotImplementedError
            
            client = WebFramework(AlwaysMaryUser())
            client.greet_user('user_token')  
            ```

<br>

- **DIP Example: 통신 프로그래밍**
    - 일반적으로 통신 프로그램은 클라이언트가 서버에게 요청을 send()하고, 서버로부터 결과를 receive()하는 동작을 수행한다. 멀티스레드 프로그래밍에서 이 send(), recv()를 수행하는 경우, recv() 함수는 Block되기 때문에, recv()를 기다리는 동안 스레드는 서버의 응답까지 계속해서 대기하게 된다. 따라서 서버의 응답을 대기하는 동안 recv()를 호출한 스레드는 다른 작업을 할 수 없기 때문에 스레드 자원이 낭비된다.
    - 이 방식의 대안으로서 `Polling` 모델이 제시된다. 클라이언트 스레드는 서버에게 메세지를 보내고 recv()를 전담하는 스레드에게 recv()를 맡긴다. 그 후 전담 스레드가 아닌 클라이언트 스레드들은 다른 작업을 실행하면서 계속 일한다. 서버로부터 응답을 확인하고 싶은 시점에서 접수된 서버의 메세지를 가져온다. 이 경우, 클라이언트 스레드는 응답 대기시간을 유효하게 활용할 수 있게 된다. 그러나 이 모델에서는 어느 순간 클라이언트 스레드는 서버의 응답을 직접 확인해야 한다. 그 응답 확인 시점을 자유롭게 선택할 수 있는 기회가 있을 뿐이며, 서버의 응답이 실제로 이루어졌는지와는 별개의 문제다. 만약 서버의 응답이 예상보다 지연될 경우, 클라이언트 스레드는 서버의 응답이 올 때까지 여러 번 응답 큐를 확인하는 비용이 따른다. 결국 서버의 응답에 대한 처리가 비동기적이어도 될 때, 그리고 클라이언트 스레드가 서버의 응답 확인 시도가 여러 번 발생할 때 폴링 모델에서 오버헤드가 발생하게 된다.
    - 이를 해결하기 위해서 DIP를 적용할 수 있다. 클라이언트 스레드는 메세지를 send()한 후 recv()하는 대신에 서버의 응답을 처리하는 훅 메소드를 등록한다. recv()를 담당하는 스레드는 서버로부터 응답을 접수하면 대응하는 훅 메소드를 찾아 그를 실행한다. 따라서 recv() 스레드는 서버의 응답 접수와 훅 메소드 실행을 담당하게 된다. 이 모델은 비동기 소켓 모델로서 DIP 원칙을 그대로 따른다. 이로써 클라이언트 스레드의 불필요한 응답 확인 요청을 없앨 수 있으며, 클라이언트 스레드 또한 응답을 확인해야 하는 반복 동작에서 벗어나 다른 작업에 집중할 수 있게 단순화된다. 이와 같은 구조는 통제권이 클라이언트 스레드가 아니라 커맨드 오브젝트(클라이언트가 전달하는 파라미터 데이터를 주입 받기 위해 사용하는 객체. 훅 메소드를 등록하기 위해 적용)로  역전되는 IOC가 발생하는 것이 전제가 된다.



