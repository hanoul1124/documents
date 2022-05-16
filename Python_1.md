# Python_1

  

### Duck Typing

#### 참조: https://nesoy.github.io/articles/2018-02/Duck-Typing

> 만약 어떤 새가 오리처럼 걷고, 헤엄치고, 꽥꽥거리는 소리를 낸다면 나는 그 새를 오리라고 부를 것이다.

- 사람이 오리처럼 행동하면 오리로 봐도 무방하다라는 것이 곧 덕 타이핑의 원리. 즉, 타입을 미리 정하는 것이 아니라, 실행되었을 때 해당 객체가 가진 속성과 메소드에 따라서 타입을 결정한다는 것이다. 파이썬과 같은 동적 타입의 언어에서는 근본적으로 다른 클래스더라도 객체의 적합성은 실제 객체의 유형이 아니라, **특정 메소드와 속성의 존재에 의해서 결정**된다.

    ```python  
    class Parrot:
        def fly(self):
            print("Parrot flying")
    
    class Airplane:
        def fly(self):
            print("Airplane flying")
    
    class Whale:
        def swim(self):
            print("Whale swimming")
    
    def lift_off(entity):
        entity.fly()
    
    parrot = Parrot()
    airplane = Airplane()
    whale = Whale()
    
    lift_off(parrot) # prints `Parrot flying`
    lift_off(airplane) # prints `Airplane flying`
    lift_off(whale) # Throws the error `'Whale' object has no attribute 'fly'`
    ```

      

    

### Python은 모든 것이 객체(Object)

#### 참조 : https://kukuta.tistory.com/306

다음과 같이 Python 변수가 선언될 때, 메모리 구조는 다음과 같다.

```python
n1 = 10
n2 = n1참조 : https://kukuta.tistory.com/306
n3 = 20
```

![var_memory](/home/hanoul/Projects/Docs/images/var_memory.png)

각 `n1`, `n2`, `n3`의 `PyTypeObject` 내부의 `*ob_type`은 자료형을 정의하는 객체를 가리키고 있다. 그렇다면, 자료형(Type) 정보를 저장한 객체는 어떻게 구성되어 있을까?

![var_memory2](Python+.assets/var_memory2.png)

`int `타입 객체 또한 다른 객체와 같이 `ob_refcnt(reference count)`와 `ob_type`을 기본적으로 가지고 있으며, 해당 타입을 설명하기 위한 추가 정보를 보관하고 있는 평범한 객체 중 하나일 뿐, 예약어가 아니다. 따라서, 다음과 같은 코드가 성립할 수 있다.

```python  
TEST = int # int는 키워드가 아니라 타입 객체를 가리키는 변수
n1 = TEST(10) # DWORD는 타입 객체를 가리키는 변수이므로 n1는 10을 가리키는 변수가 된다

# 함수의 인자와 리턴 값으로 타입을 사용할 수 있다
def foo(x) :
    return float
    
FLOAT = foo(int)

f = FLOAT(10.1)  
```

  

### 일급객체 (First-Class Object)

#### 출처: https://kukuta.tistory.com/323?category=984648

  일급 객체란 프로그래밍에서 아래에 나열된 특징을 가지는 모든 객체들을 가리킨다.

- 모든 요소는 "할당 명령문의 대상"이 될 수 있다.
- 모든 요소는 "동일(equal, ==) 비교의 대상"이 될 수 있다.
- 모든 요소는 "함수의 파라미터"가 될 수 있다.
- 모든 요소는 "함수의 반환 값"이 될 수 있다.

Python에서는 함수 또한 일급 객체에 해당한다. 일급 객체로 취급 된다는 것은 다른 함수의 인자로써 사용 가능하다는 것이다.

```python
# 함수를 일급 객체로 취급하는 map을 이용한 프로그래밍    
def square(x) :
    return x * x

m = map(square, s)    # map 객체는 함수, 리스트를 보관


s = ['banana', 'kiwi', 'apple']
s2 = sorted(s, key=lambda x : len(x)) # 함수 대신 람다 표현식 사용
print(s2)   # ['kiwi', 'apple', 'banana']
```



### Python Class: Instance vs Static

#### 참조: https://kukuta.tistory.com/331

Class의 `필드(Field)`는 클래스 내부의 데이터를 의미하며, 인스턴스 필드와 스태틱 필드가 있다. 

- 인스턴스 필드(Instance Field): 객체별로 **따로** 보관되는 데이터
- 스태틱 필드(Static Field): 모든 객체가 **공유**하는 데이터

그렇다면 인스턴스 필드와 스태틱 필드는 어디에 저장되는 것인가?

```python
class Point :
    # static field
    count = 0 # Point.count = 0 과 동일
    
    def __init__(self, x = 0, y = 0) :
        self.x = x; # instance field
        self.y = y;
        
n1 = Point(1, 2)
n2 = Point(3, 4)
```

![static_memory](Python+.assets/static_memory.png)



instance field들은 각각 해당 객체의 `__dict__` attribute에 저장된다.

static field들은 위 인스턴스들이 가리키고 있는 `Point` 라는 Class Type의 객체에 저장된다. 이전에 언급한 바와 같이, Type 객체 또한 하나의 객체로, instance field가 저장되는 것과 마찬가지로 `__dict__` attribute를 가진다. static field는 즉 Type 객체에 저장된다는 것이다. 이러한 메모리 구조를 갖기 때문에, 모든 해당 타입 클래스를 가진 객체들 사이에서 같은 값이 공유될 수 있는 것이다.

```python
# 1. 클래스 이름으로 접근 후 쓰기
Point.count = 100

# 2. 객체 이름으로 접근. 읽기
print(n1.count)    # 100

n1.count = 200
print(Point.count) # 100
print(n1.count)    # 200
print(n2.count)    # 100
```

단 Python에서 스태틱 필드에 접근하는 것에는 주의해야 한다. 단순히 읽기 동작에서는 문제가 없으나, 쓰기 동작에서는 주의를 필요로 한다. Python에서 객체를 통해서 Field를 읽으려고 시도할 경우, 가장 먼저 해당 객체의 `__dict__`에서 해당 field를 검색한다. 위 코드에서는 `count`라는 field를 찾을 것이다. 만약 `n1`과 같은 객체에서 `count`라는 필드를 발견하지 못했다면, 해당 객체의 타입 객체, 즉 `Point` 클래스의 스태틱 필드들이 저장되는 곳을 검색한다.

따라서 위 코드 실행 과정에서 첫 `n1.count`는 무사히 `100`을 리턴하는 것이다. 그러나 객체를 이용해 필드를 새로 쓰려고 할 때, 만약 `__dict__`  내에 해당 필드가 없는 경우에는 그 필드를 생성해 버린다. 즉, 인스턴스 필드로서 하나의 값이 새롭게 추가되는 것이다. 그렇기 때문에 `n1.count = 200 `이후에는 스태틱필드 값과는 다른 값이 `n1` 내에서 호출되는 것을 볼 수 있다. 

**따라서 스태틱 필드에 접근할 땐, 클래스 이름을 사용해야만 안정적인 코드를 작성할 수 있다.**  



### Python Class: Static method

#### 참조: https://kukuta.tistory.com/331

앞서 확인한 것과 마찬가지로, 필드 뿐만 아니라 메소드 또한 마찬가지의 방식으로 저장된다. 인스턴스 메소드는 객체가 인자로 있어야만 호출할 수 있으며, 스태틱 메소드는 객체 없이도 호출이 가능하다.

```python
class Point :
    count = 0 
    
    def __init__(self, x = 0, y = 0) :
        self.x = x;
        self.y = y;
        
    def foo(self) : # instance method
        print('foo function')
        
    def bar() :     # static method. self가 없는 것에 주목
        print('bar function')
        
    @staticmethod
    def bar() :     # static method. self가 없는 것에 주목
        print('bar function')
        
p = Point()
p.foo()             # 일반 메소드 호출. 내부적으로 인터프리터가 Point 객체를 첫번째 인자로 넘겨 줌 
Point.foo(t)        # 일반 메소드 호출. 위와 동일한 작업, 함수의 첫번째 인자를 직접 넘겨 줌
Point.bar()         # 스태틱 메소드 호출. 클래스 이름으로 호출
```

static method는 `@staticmethod` 데코레이터를 통해서도 선언이 가능하다. 해당 wrapper는 첫번째 인자로 넘겨지는 `self`를 사용하지 않고 제외시키는 기능을 수행한다.



### Python Class: staticmethod vs classmethod 

클래스메소드는 `cls` 인자를 받는 메소드로, 해당 메소드를 호출한 객체 자체를 인자로 전달한다.

```python  
# 출처: https://nirsa.tistory.com/113
class parent_class:
    test = 'parent'
    
    @classmethod 
    def output(cls):
        return cls.test

class child_class(parent_class):
    test = 'child'
    
    
print(child_class.output()) # 결과값 : child    
```

위 코드에서, `child_class` 라는 객체에서 호출된 `output() `메소드는 호출한 객체인 `child_class` 객체가 자동으로 인자로 전달되며, 해당 객체가 `test` 필드 값에 대해서 부모 클래스와는 별개의 값을 설정하고 있기 때문에 `parent`가 아닌 `child` 값을 리턴한다. 특히 상속 관계에서 해당 method들을 잘 구분하여 사용해야 한다.  



### Python Underscore

#### 참조: https://dingrr.com/blog/post/python-%ED%8A%B9%EB%B3%84%ED%95%9C-%EC%96%B8%EB%8D%94%EC%8A%A4%EC%BD%94%EC%96%B4_

1. 인터프리터에서의 언더스코어

    파이썬 프롬프트나 인터프리터가 마지막으로 실행했던 구문의 값을 리턴한다.

    ```python  
    >>> a = 19
    >>> b =  254
    >>> _
    Traceback (most recent call last):
    File "<stdin>", line 1, in <module>
    NameError: name '_' is not defined
    
    >>> a+b
    273
    >>> _
    273
    >>> _ * 2
    546  
    ```

      

2. 값을 무시하는 언더스코어

    반환된 값을 사용할 필요가 없을 경우에 아래와 같이 표기할 수 있다.

    ```python  
    for _ in range(5):
       print("hello")  
    ```

      

3. **import를 방지**하는 언더스코어

    C++이나 JAVA와 달리 접근지정자를 설정할 수 없기 때문에, 언더스코어를 활용하여 이를 구분할 수 있다. 변수 앞에 언더스코어를 사용하는 것으로 내부에서 사용된다는 것을 인터프리터에게 알려줄 수 있고, `import *` 실행 시 해당 변수를 가지고 올 수 없게 된다. 물론 직접 호출할 경우에는 호출이 가능하다.  

    ```python  
    # 출처: https://ebbnflow.tistory.com/255
    # func.py
    def publicFunc():
        print('this is public function')
        
    def _privateFunc():
        print('this is private function')
    
        
    from func import *
    publicFunc()
    _privateFunc()
    
    """
    Traceback (most recent call last):
    File "c:\Projects\test\test.py", line 4, in <module> _privateFunc()
    NameError: name '_privateFunc' is not defined
    """
    ```

      

4. 사용제한 식별자를 회피하는 언더스코어

    True, False, def, if, raise, None, del 등 예약된 키워드들이 존재하는데, 이들과 같은 이름을 가진 객체는 존재할 수 없다. 언더스코어를 사용해 이들 키워드를 변수나 함수명으로 사용할 수 있다.

    ```python  
    data = [1, 2, 3, 4]
    del_ = data[1]
    ```

      

5. **맹글링(Mangling)**을 위한 더블 언더스코어

    인터프리터에게 하위 클래스와의 충돌을 피하기 위해서 이름을 다시 쓰도록 알리고, 해당 변수로의 접근을 위해서는  **_클래스명__변수명** 형식으로 변경하여 사용할 수 있다. 이를 `맹글링(Mangling)`이라고 한다. 이로써 해당 속성을 `private`하게 만들어주고, 외부에서의 직접 접근을 방지한다.

    ```python
    # 출처: https://ebbnflow.tistory.com/255
    class Order:
        def __init__(self):
            self.coffee = 'Americano'
            self.__price = 3000 # __변수명
            
        def get_price(self):
            return self.__price
            
    print(order1.__price) # AttributeError: 'Order' object has no attribute '__price'
    print(order1._Order__price) # 3000. _CLASSNAME__FIELDNAME
    print(order1.get_price()) # 3000. 같은 클래스 내부에서는 접근 가능
    ```

      

6. **스페셜 메소드(Special Methods)**를 위한 더블 언더스코어

    `__init__`, `__add__`, `__len__`, `__repr__` 등 특수한 내장 메소드를 정의하기 위해 사용된다. 매직 메소드(Magic methods) 혹은 더블 언더스코어 메소드를 줄여서 Dunder(던더) 메소드라고도 부른다.

    ```python  
    class String:
    
       # 객체 초기화
       def __init__(self, string):
          self.string = string
    
       # 객체를 나타내는 공식적인 문자열
       # repr()로 호출
       def __repr__(self):
          return 'Object: {}'.format(self.string)
        
    print(string1) # Object: Hello
    ```

      

7. **국제화(i18) / 지역화(i10)**에서의 언더스코어

    언더스코어 그 자체가 함수로 사용된 케이스로, 문법이 아니라 컨벤션이다. 문자열 번역에 사용하는 i18, i10에서 주로 사용된다.

    ```python
    from django.http import HttpResponse
    from django.utils.translation import gettext as _
    
    def my_view(request):
        output = _("Welcome to my site.") # 번역
        return HttpResponse(output)
    ```

    

8. 자릿수 구분자 언더스코어

    Python 3.6에 추가된 문법으로, 숫자 리터럴을 편하게 읽기 위한 구분자로 사용한다.

    ```python
    # 1000 단위로 표시할때
    amount = 10_000_000.0
    
    # 16진수를 단어별로 묶을때
    addr = 0xCAFE_F00D
    
    # 2진수를 끊어 읽고 싶을때
    flags = 0b_0011_1111_0100_1110
    
    # String 2진수를 변경
    flags2 = int('0b_1111_0000', 2)
    
    print(amount)
    print(addr)
    print(flags)
    print(flags2)
    print(flags - flags2)
    
    """
    >>> 10000000.0
    >>> 3405705229
    >>> 16206
    >>> 240
    >>> 15966
    """
    ```

    

###  `__dict__` , `__slots__`

#### 출처: https://kukuta.tistory.com/332?category=984648

Python 클래스의 인스턴스 필드를 생성하면 클래스 객체의 `__dict__` Attribute에 저장된다. 앞서 static field의 사용방법에 대한 주의사항에서, `__dict__`에 필드를 저장할 경우 **외부에서 새로운 필드를 자유롭게 추가할 수 있다**는 특징이 생긴다고 언급했다. 이러한 특징은 프로그램의 안정성을 위협하는 요소가 될 수 있으며, 메모리를 어느정도 많이 사용하는 경향이 있어 보완이 필요한 요소가 존재한다.

이를 위해서 `__slots__`라는 Attribute를 사용하여 단점을 보완할 수 있다.

```python
class Bar :
    
    __slots__ = ['x', 'y']
    
    def __init__(self, x, y) :
        self.x = x
        self.y = y

bar = Bar(1, 2) 
bar.z = 10 # AttributeError: 'Bar' object has no attribute 'z'
```

이를 사용하면 객체가 생성될 때, `__dict__` Attribute는 사라지고, 각 x, y는 객체 자체에 데이터가 보관된다. `__slots__`에 멤버로 지정하지 않은 새로운 필드는 추가할 수 없다. 또한 메모리 사용량이 약간 줄어든다. 이 경우, `bar.z`와 같이 `z`에 접근하려고 할 경우 새로운 필드를 생성하지 못하고 어트리뷰트가 존재하지 않는다는 오류를 발생시킨다.

```python  
# __slots__ 사용 전
print(dir(foo))

['__class__', '__delattr__', '__dict__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__le__', '__lt__', '__module__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', '__weakref__', 'x', 'y', 'z']


# __slots__ 사용 후. __dict__, __weakref__ 가 사라졌다.
print(dir(bar))

['__class__', '__delattr__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__le__', '__lt__', '__module__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__slots__', '__str__', '__subclasshook__', 'x', 'y']
```

  

### Python Module

#### 출처:  https://kukuta.tistory.com/340?category=984648

파이썬의 모듈 또한 하나의 객체로서, 다양한 어트리뷰트를 갖는다. 모든 파이썬 모듈은 `__name__` 전역 변수에 이름(확장자를 제외한 파일 이름)을 저장하고 있다. 파이썬 명령으로 처음 실행 되는 경우 모듈의 이름이 `__main__`이고, 다른 모듈에 의해 임포트 된 경우 확장자를 제외한 파일의 이름이 모듈 이름이 된다.

```python
import mylib

print(__name__)       # __main__, 파이썬에 의해 실행된 모듈의 이름. 현재 실행중인 파이썬 파일을 지칭
print(mylib.__name__) # mylib, 임포트 된 모듈의 이름(from mylib.py)
```

`dir(mylib)` 을 사용해 모듈에 속한 모든 변수와 메소드를 확인할 수 있다.

```python  
dir(mylib)

"""
#  __builtins__ : <class 'dict'>
#    __cached__ : <class 'str'>
#       __doc__ : <class 'str'> # 모듈의 맨위에 멀티라인 주석을 추가하면 __doc__ 어트리뷰트에 저장
#      __file__ : <class 'str'> # 경로와 확장자를 포함한 파일 이름이 저장
#    __loader__ : <class '_frozen_importlib_external.SourceFileLoader'>
#      __name__ : <class 'str'> # 파일 이름에서 확장자를 제외한 부분이 모듈의 이름으로 저장
#   __package__ : <class 'str'>
#      __spec__ : <class '_frozen_importlib.ModuleSpec'>
#           add : <class 'function'>
"""
```

  

### Special Methods

#### 출처: https://kukuta.tistory.com/334

특정 상황에서 자동으로 호출되는 메소드(이름이 약속되어 있는 메소드)들을 일컫는 말로, 파이썬 표준 타입과 유사하게 동작하는 클래스를 설계하기 위해서는 반드시 필요하다. 약 100여개가 넘는 종류가 존재한다.

- `len()`

    문자열의 길이 또는 시퀀스의 요소 수를 조사하기 위해 사용되는 표준함수.

    ```python
    class Sentence :
        def __init__(self, s) :
            self.words = s.split()
    
        def __len__(self) : # len() 함수를 위한 스페셜 메소드
            return len(self.words)
        
    s = Sentence('Sometimes bad things happen to good people')
    
    print(len(s))      # 7
    print(s.__len__()) # 7, 이렇게도 호출 가능
    ```

- `[]`

    배열에서 인덱스 접근에 `[]`를 사용한다. 이를 위해서 `__getitem__`이라는 스페셜 메소드를 정의해야 한다.

    ```python
    class Sentence :
       def __init__(self, s) :
           self.words = s.split()
    
       def __getitem__(self, index) : # index 를 인자로 받아야 한다.
           return self.words[index]
           
    s = Sentence('Sometimes bad things happen to good people')
    
    print(s[1]) # bad 출력. s.__getitme__(1)과 동일
    ```

- 객체를 문자열로 변환

    객체를 문자열로 변환하기 위해서는 `__str__`이나 `__repr__` 메소드를 제공해야 한다.

    - `__str__`: 주로 사용자에게 보여주기 위한 용도의 문자열 변환
    - `__repr__`: 디버깅 등 내부 사용을 위한 용도

    ```python
    class Point :
        def __init__(self, x, y) :
            self.x = x
            self.y = y
           
        def __str__(self) :
            return f'x={self.x}, y={self.y}'
            
        def __repr__(self) :
            return f'Point({self.x}, {self.y})'
    
    point = Point(1,2)
    
    # point.__str__(). 클래스에 __str__() 스페셜 메소드가 없으면 없으면 __repr__() 호출
    print(point) 
    # point.__str__()
    print(str(point))
    # point.__repr__(). 
    print(repr(point))  
    ```

- `callable object`

    `__call__` 스페셜 메소드를 사용하면 객체를 함수처럼 호출하여 사용`()`할 수 있다. 이를 통해서 callable object는 **객체의 상태를 가질 수 있는 함수**를 만들 수 있게 된다.

    ```python  
    class Plus :
    
        def __init__(self) :
            self.count = 0  # 함수 호출 회수를 기록
            
        def __call__(self, x, y) :
            self.count += 1
            return x + y
    
    plus = Plus()
    
    n = plus(1, 2) # plus.__call__(1, 2)
    print(n)       # 3
    print(plus.count) # 1
    ```

      

### Getter & Setter, @property

객체의 필드(멤버 변수)에 직접적인 접근 대신, 함수 통해 접근하도록하는 방법으로, 객체 지향적 프로그래밍에서 자주 사용된다. 그러나 필드를 외부에서 직접 접근할 경우 객체가 **잘못된 상태**를 가질 수 있다.

```python
class Person :
    
    def __init__(self, name : str, age : int) :
        self.name = name
        self.age = age

person = Person('Jason', '24')
print(person.name, person.age) # Jason 24

person.name = 10               # 문자열로 쓰고 싶었지만 정수를 저장함
person.age = -100              # 나이는 마이너스가 없지만 마이너스 값을 저장함
```

따라서 다음과 같이 `getter`, `setter` 함수를 만들어 저장 및 참조, 유효성 검사 등을 수행하도록 만드는 것이다.

```python
class Person :
    
    def __init__(self, name : str, age : int) :
        self.__name = name
        self.__age = age

    def get_name(self) :        # getter of name
        return self.__name
    
    def set_name(self, name) :  # setter of name
        if True != isinstance(name, str) :
            raise ValueError('name should be str type')
        self.__name = name
        
    def get_age(self) :         # getter of age
        return self.__age
    
    def set_age(self, age) :    # setter of age
        if True != isinstance(age, int) or 0 > age :
            raise ValueError('age should be int type and not be negative')
        self.__age = age
```

하지만 이러한 과정을 파이썬에서는 좀 더 간편하게, 필드를 직접 접근하는 것과 유사한 방식으로 이용할 수 있게 만들어주는 것이 있다. 바로 `@property` 다. `property`, `classmethod`, `staticmethod` 등 이들은 디스크립터를 사용한 것으로, 디스크립터(descriptor)를 간단하게 한 줄로 요약하면, "**__get__, __set__ 또는 __delete__ 스페셜 메소드 중 한개 이상 구현 되어 있는 객체**"를 말한다. 이렇게 구현된 디스크립터는 다른 객체의 속성으로 정의 될 수 있으며, 그 속성에 대한 읽기, 쓰기, 삭제 연산을 할 때 동작에 따라 각각 구현된 스페셜 메소드가 호출 된다.

```python  
class Person :
    
    def __init__(self, name : str, age : int) :
        self.__name = name
        self.__age = age

    @property
    def name(self) :
        return self.__name
    
    @name.setter
    def name(self, name) :
        if True != isinstance(name, str) :
            raise ValueError('name should be str type')
        self.__name = name
       
    @property
    def age(self) :
        return self.__age
    
    @age.setter
    def age(self, age) :
        if True != isinstance(age, int) or 0 > age :
            raise ValueError('age should be int type and not be negative')
        self.__age = age
        
person = Person('Jason', '24')

print(person.name, person.age)   # Jason 24

person.name = 10                 # ValueError: name should be str type
person.age = -100                # ValueError: age should be int type and not be negative
```

  

### @dataclass

클래스를 사용해 데이터를 저장할 경우, type-safe해지기 때문에 프로그램 실행 중 발생할 수 있는 오류가 적어진다는 장점이 있다. Python 3.7에서 dataclass라는 모듈이 표준 라이브러리에 추가되었는데, 이를 통해 내장 자료구조처럼 편리하면서도 클래스처럼 안정적인 데이터 모델을 구현할 수 있게 되었다.

```python
# 기존의 Class를 사용한 데이터 모델 정의
from datetime import date


class User:
    def __init__(
        self, id: int, name: str, birthdate: date, admin: bool = False
    ) -> None:
        self.id = id
        self.name = name # 각 변수들이 반복해서 나타나게 된다(보일러 플레이트)
        self.birthdate = birthdate
        self.admin = admin
```

반복적으로 같은 변수명이 코드에 나타나는 것 뿐만 아니라, `__repr__()`, `__eq__()` 를 사용하는 등 추가적인 기능을 확장하기 위해서는 많은 양의 코드가 필요하다. 데이터클래스를 통해서 이러한 불편함을 해소할 수 있다.

```python
from dataclasses import dataclass, field
from datetime import date
from typing import List


# __init__(), __repr__(), __eq__()와 같은 메소드들은 자동으로 생성해준다
@dataclass
class User:
    id: int
    name: str
    birthdate: date
    admin: bool = False
    
"""
데이터의 불변성(Immutability)을 보장하고자 할 경우 frozen 옵션을 사용한다
데이터클래스 간의 대소비교를 적용하고 싶다면 order 옵션을 사용해 정렬할 수 있다
기본적으로 데이터클래스 인스턴스는 hashable하지 않아 딕셔너리나 세트에서 사용할 수 없다
데이터클래스 인스턴스를 hashable하게 만들고 싶다면 unsafe_hash 옵션을 사용하면 된다
"""
@dataclass(frozen=True, order=True, unsafe_hash=True)
class User:
    id: int
    name: str
    birthdate: date
    admin: bool = False
    """
    friends: List[int] = [] 와 같이 선언할 경우, 해당 기본값은 인스턴스 간 공유되기 때문에
    모든 인스턴스가 하나의 리스트 주소를 가리키게 되므로 이와 같은 방식의 할당은 허용되지 않는다.
    이 경우 dataclasses.filed 함수의 default_factory를 사용해 매번 새로운 리스트가 생성되게 한다
    """
    friends: List[int] = field(default_factory=list)
    
    
"""
>>> user1 = User(id=1, name="Steve Jobs", birthdate=date(1955, 2, 24))
>>> user2 = User(id=2, name="Bill Gates", birthdate=date(1955, 10, 28))
>>> user3 = User(id=1, name="Steve Jobs", birthdate=date(1955, 2, 24))
>>> user4 = User(id=2, name="Bill Gates", birthdate=date(1955, 10, 28))

# frozen
>>> user1.admin = True
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<string>", line 4, in __setattr__
dataclasses.FrozenInstanceError: cannot assign to field 'admin'

# order
>>> sorted([user2, user1])

# unsafe_hash
>>> set([user1, user2, user3, user4])
"""
```

  

### 다중 상속

Python은 여러 개의 클래스로부터 동시에 상속받는 **다중 상속**을 지원한다. 이는 여러 개의 기반 클래스로부터 동시에 모든 속성들을 물려받는 것이다.

```python
class A :
    def foo(self) :
        print('A foo')

class B :
    def foo(self) :
        print('B foo')
        
class C(A, B) :
    pass
        
test = C()
test.foo() # 어느 클래스의 foo가 호출 될까?

"""
정답은 이름이 겹칠 땐, 먼저 상속 받은 클래스의 것을 사용한다
위의 경우에는 A클래스의 foo() 메소드를 사용한다
만일 C(B, A)와 같은 순서로 상속 받았다면 B의 foo를 사용하게 된다
"""
```

위와 같이 다중 상속 상황에서 어떤 메소드를 사용할 지 순서를 결정하는 것을 **MRO(Method Resolution Order)**라고 한다. 클래스의 MRO 순서를 알기 위해서는 `CLASS_NAME.mro()`을 실행하여 확인할 수 있다.

```python
print(C.mro())
# C의 MRO는 C -> A -> B -> object(시스템에서 지정하는 최상위 클래스) 순서
>>> [<class '__main__.C'>, <class '__main__.A'>, <class '__main__.B'>, <class 'object'>]

"""
super(Type, Object).method()
super함수는 MRO 순서에서 타입으로 지정된 클래스의 '다음 순서'의 클래스를 리턴한다.
"""
super(A, test).foo() # B의 foo 호출
```

#### 다이아몬드 상속과 super()

그렇다면 다음과 같은 경우는 어떻게 생성하게 될까?

![diamond_super](Python+.assets/diamond_super.png)

```python
# 다이아몬드 상속 구조
class A :
    def __init__(self) :
        print('A __init__')

class B(A) :
    def __init__(self) :
        print('B __init__')
        A.__init__(self)

class C(A) :
    def __init__(self) :
        print('C __init__')
        A.__init__(self)
        
class D(B, C) :
    def __init__(self) :
        print('D __init__')
        B.__init__(self) # 다중 상속이라 각 상위 클래스의 생성자를 따로 불러준다.
        C.__init__(self)
        
d = D()
print(D.mro()) # [<class '__main__.D'>, <class '__main__.B'>, 
               # <class '__main__.C'>, <class '__main__.A'>, <class 'object'>]
```

위 경우 클래스 D 객체를 생성하면 A 클래스가 두 번 중복하여 호출되는 현상이 발생한다. 이를 `super()`를 적절히 사용하여 해결할 수 있다. `super()`는 어디까지나 MRO 상에서 바로 뒤를 호출한다는 점을 기억해야 한다. super()에 아무런 인자를 전달하지 않을 경우, 다음과 같은 default 인자가 사용된다.

```python
# 자신의 클래스 이름과 self 객체를 인자로 전달
super(self.__class__, self)


# 실제 적용
class A :
    def __init__(self) :
        print('A __init__')

class B(A) :
    def __init__(self) :
        print('B __init__')
        super().__init__()  # super(self.__class__, self).__init__()

class C(A) :
    def __init__(self) :
        print('C __init__')
        super().__init__()  # super(self.__class__, self).__init__()
        
class D(B, C) :
    def __init__(self) :
        print('D __init__')
        super().__init__()  # super(self.__class__, self).__init__()
        
d = D()
print(D.mro()) # [<class '__main__.D'>, <class '__main__.B'>, 
               # <class '__main__.C'>, <class '__main__.A'>, <class 'object'>]
               
"""
D() --> super(D) = B() --> super(B) = C() --> super(C) = A()
각자 MRO 바로 뒤를 선택하여 init하게 되므로, 모두 한 번 씩만 호출된다
"""
```

이와 같이 상위 클래스의 메소드를 호출할 경우 꼭 `super()`를 활용하는 것이 좋다.

