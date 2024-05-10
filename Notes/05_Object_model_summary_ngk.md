## 5. 파이썬 객체의 내부 작동
>copyright: Practical Python Programming은 크리에이티브 커먼즈 저작자표시-동일조건변경허락 4.0 국제(Attribution ShareAlike 4.0 International) 라이선스

### 5.1 딕셔너리 돌아보기

파이썬 객체 시스템은 딕셔너리와 관련된 구현에 많은 부분 기반을 둔다. 이 섹션에서 그에 대해 논의한다.

* mro(): method resolution order (이거 외에 5.1 챕터는 몰라도 되는 내용으로 이해된다)
```python
>>> class NewStock(Stock):
        def yow(self):
            print('Yow!')
>>>
>>> NewStock.mro()  # 상속순서를 살펴볼 수 있다.
(<class '__main__.NewStock'>, <class 'stock.Stock'>, <class 'object'>)
>>>
```

### 5.2 클래스와 캡슐화

* 프라이빗 어트리뷰트: 이름이 '_' 로 시작하는 어트리뷰트는 private 으로 간주한다
  * 단, 프로그래밍 스타일일 뿐이고 액세스하고 변경할 수 있다. (그저 개발자간 약속)
  * 단, '__' 로 시작하면 진짜로 액세스가 안된다. 다만 이후 mangling 된 변수명으로는 접근이 가능하다. (즉, 진정한 의미의 private 아님)
* 데코레이터를 써서 getter, setter 를 구현하는 방법이 있다.
  * @property: getter에 사용
  * @{변수명}.setter: setter에 사용
  ```python
  class Stock:
    def __init__(self, name, shares, price):
        self.name = name
        self.shares = shares
        self.price = price

    @property
    def shares(self):
        return self._shares

    @shares.setter
    def shares(self, value):
        if not isinstance(value, int):
            raise TypeError('Expected int')
        self._shares = value
  ```
  위와같이 정의하면, 이제 평범한 프로퍼티 액세스는 @property와 @shares.setter 하에서 getter와 setter 메서드를 트리거한다.
  ```python
  >>> s = Stock('IBM', 50, 91.1)
  >>> s.shares         # @property를 트리거
  50
  >>> s.shares = 75    # @shares.setter를 트리거
  >>>
  ```
  이 패턴을 적용하면 소스 코드를 바꿀 필요가 없다. 새로운 setter도 클래스 내에 할당되면 내부 __init__() 메서드를 포함해 호출된다.
  ```python
  class Stock:
      def __init__(self, name, shares, price):
          ...
          # 이 할당은 아래 setter를 호출한다
          self.shares = shares
          ...

      ...
      @shares.setter
      def shares(self, value):
          if not isinstance(value, int):
              raise TypeError('Expected int')
          self._shares = value
  ```
* \_\_slots__ 어트리뷰트: 어트리뷰트 이름의 집합을 제한할 수 있다.
```python
class Stock:
    __slots__ = ('name','_shares','price')
    def __init__(self, name, shares, price):
        self.name = name
        ...
```
그 외의 어트리뷰트에 대해서는 오류가 일어난다.
```python
>>> s.price = 385.15
>>> s.prices = 410.2
Traceback (most recent call last):
File "<stdin>", line 1, in ?
AttributeError: 'Stock' object has no attribute 'prices'
```
파이썬 클래스가 가지는 딕셔너리 형태의 속성은 런타임에도 임의의 새로운 속성을 추가할 수 있다는 유연성을 제공해주지만 더 많은 메모리를 사용한다는 단점도 가지고 있다. 클래스 속성을 딕셔너리로 관리하는 대신에 slots을 사용하여 고정된 속성 집합을 사용하게 함으로써 메모리 사용량을 줄이고 최적화 할 수 있다.
정말 많은 객체를 생성할 거라고 예상되는 클래스는 slots으로 속성을 관리하는 것을 추천한다!!