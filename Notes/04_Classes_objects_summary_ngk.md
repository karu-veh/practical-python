## 4. 클래스와 객체
>copyright: Practical Python Programming은 크리에이티브 커먼즈 저작자표시-동일조건변경허락 4.0 국제(Attribution ShareAlike 4.0 International) 라이선스

### 4.2 상속

* 오버라이딩(overriding): 때로는 클래스가 기존 메서드를 확장하되, 원래 구현을 재정의에 포함하고 싶을 수 있다. 이를 위해 super()를 사용한다.
```python
class Stock:
    ...
    def cost(self):
        return self.shares * self.price
    ...

class MyStock(Stock):
    def cost(self):
        # `super`에 대한 호출을 확인
        actual_cost = super().cost()
        return 1.25 * actual_cost
```

* __init__를 재정의하려면 부모를 초기화해야 한다.
```python
class Stock:
    def __init__(self, name, shares, price):
        self.name = name
        self.shares = shares
        self.price = price

class MyStock(Stock):
    def __init__(self, name, shares, price, factor):
        # `super`와 `__init__`에 대한 호출을 확인
        super().__init__(name, shares, price)
        self.factor = factor

    def cost(self):
        return self.factor * super().cost()
```

### 4.3 특수한 메서드

* python 에서 일종의 builtin 으로 정의되었거나 mangling으로 이름이 재정의되는 (언더바두개, __) 메서드를 특수한 메서드라고 부르는데, "magic method" or "special method" 라고 부른다
```python
# __init__, __str__, __repr__, __getitem__, __setitem__, __delitem__, __len__, ... 등등...
```
* mangling을 통해 이름 재정의되는 수학을 위한 특수메서드를 예로 들면, 아래와 같다!
```python
a + b       a.__add__(b)
a - b       a.__sub__(b)
a * b       a.__mul__(b)
a / b       a.__truediv__(b)
a // b      a.__floordiv__(b)
a % b       a.__mod__(b)
a << b      a.__lshift__(b)
a >> b      a.__rshift__(b)
a & b       a.__and__(b)
a | b       a.__or__(b)
a ^ b       a.__xor__(b)
a ** b      a.__pow__(b)
-a          a.__neg__()
~a          a.__invert__()
abs(a)      a.__abs__()
```

* 어트리뷰트를 통한 액세스: 어트리뷰트에 액세스, 조작, 관리하는 또 다른 방법이 있다.
```python
getattr(obj, 'name')          # obj.name과 같음
setattr(obj, 'name', value)   # obj.name = value와 같음
delattr(obj, 'name')          # del obj.name과 같음
hasattr(obj, 'name')          # 어트리뷰트가 존재하는지 테스트

# 아래는 예제 코드
if hasattr(obj, 'x'):
    x = getattr(obj, 'x'):
else:
    x = None
```