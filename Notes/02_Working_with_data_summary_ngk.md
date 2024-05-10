## 2. 데이터로 작업하기
>copyright: Practical Python Programming은 크리에이티브 커먼즈 저작자표시-동일조건변경허락 4.0 국제(Attribution ShareAlike 4.0 International) 라이선스

### 2.1 자료형과 자료 구조

1. None 타입
   * 주로 누락 값 표시. 조건문에서 False와 같음
2. Tuple
   * 주로 DB 기준 1행 record를 표시하는데 사용. 리스트와 사용법 비슷함
   * 단, immutable 객체임 (str 과 마찬가지로 index로 값을 변경할 수 없음)
   * packing/unpacking
   ```python
   s = ('GoGo', 100, 490.1)
   name, shares, prices = s
   ```
3. Dictionary
   * key 값 기준으로 값을 삭제하려면 del 문 사용할 수 있다 (list와 유사)
   ```python
   >>> del s['date']
   >>>
   ```
   * 딕셔너리는 서로 다른 값이 많이 있고 그 값들을 수정 또는 조작해야 할 때 유용하다. 딕셔너리를 사용하면 코드의 가독성이 높아진다. 이는 key-value의 mapping을 통해 달성되며, key-value의 pair를 item 으로 일컫는다.
   * .items(), .keys(), .values() 를 통한 loop 구현이 일반적이나, 아래와 같이 list() 변환시 keys 에 대한 list로의 형변환이 되는 특징을 이용하여 list 유사한 loop 구현도 가능하다
   ```python
   # k가 d의 each key 로서 동작함. d대신 list(d) 로 써도 동일함
   >>> for k in d:
        print(k, '=', d[k]) 

    name = AA
    shares = 75
    price = 32.2
    date = (6, 11, 2007)
    account = 12345
    >>>
   ```
  
### 2.2 컨테이너

python에서 컨테이너는 list, dictionary, set 를 일컫는다.
* set은 list와 비교하여 "고유 항목의 모음" 이라는 점과, "순서를 유지하지 않는다"는 특징이 있다. 각종 tree 구현의 초석이 된다. 중복 제거에도 사용될 수 있는데, 가령 list -> set -> list 의 형변환 방식을 예로 들 수 있다.
* dictionary에서 존재하지 않는 key를 통해 찾으려 할 경우, get method에서 default 값을 제공할 수 있다.
```python
# name = d.get(key, default)
>>> prices.get('IBM', 0.0)
93.37
>>> prices.get('SCOX', 0.0)
0.0
>>>
```

파이썬에서는 어떤 타입의 값이든 딕셔너리의 키로 사용할 수 있는데, 단, 딕셔너리 키는 반드시 변경불가능한 타입이어야 한다. (immutable). 리스트, 세트, 다른 딕셔너리는 변경 가능  (mutable)하므로 딕셔너리의 키로 사용할 수 없다.

```python
holidays = {
  (1, 1) : 'New Years',
  (3, 14) : 'Pi day',
  (9, 13) : "Programmer's day",
}

>>> holidays[3, 14]
'Pi day'
>>>
```

큰 딕셔너리와 리스트를 print를 통해 조회하는 것은 지저분할 수 있는데, 이때에는 pprint module의 pprint function을 사용하면 도움이 된다. pprint의 의미는 "pretty print"의 뜻이다.
```python
>>> from pprint import pprint
>>> pprint(portfolio)
[{'name': 'AA', 'price': 32.2, 'shares': 100},
    {'name': 'IBM', 'price': 91.1, 'shares': 50},
    {'name': 'CAT', 'price': 83.44, 'shares': 150},
    {'name': 'MSFT', 'price': 51.23, 'shares': 200},
    {'name': 'GE', 'price': 40.37, 'shares': 95},
    {'name': 'MSFT', 'price': 65.1, 'shares': 50},
    {'name': 'IBM', 'price': 70.44, 'shares': 100}]
>>>
```

pandas를 사용하지 않더라도 내장 csv 모듈을 사용해도 충분히 효과적으로 csv를 읽어들일 수 있다. 각 line 별로 delimiter가 자동으로 인식되어 split된 list의 list로 return이 되는데 아래 예제코드를 참조하자
```python
>>> import csv
>>> f = open('Data/prices.csv', 'r')
>>> rows = csv.reader(f)
>>> for row in rows:
        print(row)


['AA', '9.22']
['AXP', '24.85']
...
[]
>>>
```

### 2.3 포매팅

문자열 포매팅은 .format이나 f-포매팅을 사용할 수 있는데 (version >= 3.6), 자리수 정렬은 아래와 같은 의미를 갖는다.
```python
f'{name:>10s} {shares:>10d} {price:>10.2f}'

:>10d   정수를 10자리 필드에 오른쪽 정렬
:<10d   정수를 10자리 필드에 왼쪽 정렬
:^10d   정수를 10자리 필드에 가운데 정렬
:0.2f   부동소수점수를 두 자리 정밀도로 나타냄

d       십진 정수
b       이진 정수
x       16진 정수
f       부동소수점수 [-]m.dddddd
e       부동소수점수 [-]m.dddddde+-xx
s       문자열
```

문자열 포매팅 시, 아래와 같이 채울 문자를 지정할 수 있다.
```python
>>> print(f'{value:*>16,.2f}')
*******42,863.10
>>> print(f'{value:*>16.2f}')
********42863.10
```

### 2.4 시퀀스

파이썬에는 세 가지 시퀀스(sequence) 자료형이 있다.
(문자열, 리스트, 튜플)
* enumerate() 함수는 유용하게 쓰자. iteration에 counter idx를 추가하는 기능이고, 시퀀스 자료형에 대한 접근 외에도 좋은 예는, 파일을 읽으면서 행 번호를 추적하는 경우이다.
```python
with open(filename) as f:
    for lineno, line in enumerate(f, start=1):  # start는 선택적이다.
        ...
```

* zip() 함수는 여러 시퀀스를 결합해 iterator를 만든다.
```python
columns = ['name', 'shares', 'price']
values = ['GOOG', 100, 490.1 ]
pairs = zip(columns, values)
# ('name','GOOG'), ('shares',100), ('price',490.1)

for column, value in pairs: # 결과를 얻으려면, 반드시 이터레이션을 해야한다.
    ...
```

* zip의 일반적인 용도는 딕셔너리를 생성하기 위한 키/값을 생성하는 것이다.
```python
d = dict(zip(columns, values))  # 호!
```

### 2.5 collections 모듈

collections 모듈에는 데이터 처리를 위한 유용한 객체가 많이 있다. 그중 몇 가지를 간략히 소개받는다. 예제는 건너 뛰도록 하고, 언제 사용하는지만 알고 가자.

* Counter: Counter를 사용한다.
* 일대다 매핑 (one to many): defaultdict를 사용한다
* 이력을 유지하기 (N history): deque를 사용한다.

### 2.6 list comprehension

새 리스트 생성하기
```python
# 일반적인 구문1: [ <표현식> for <변수명> in <시퀀스> ]
# 일반적인 구문2: [ <표현식> for <변수명> in <시퀀스> if <조건>]
>>> a = [1, 2, 3, 4, 5]
>>> b = [2*x for x in a ]
>>> b
[2, 4, 6, 8, 10]
>>>
```

필터링 (cond)
```python
>>> a = [1, -5, 4, 2, -2, 10]
>>> b = [2*x for x in a if x > 0 ]
>>> b
[2, 8, 4, 20]
>>>
```

리스트 컴프리헨션은 엄청나게 유용하다. 예를 들어, 특정 딕셔너리 필드의 값을 수집할 수 있다. 시퀀스에 데이터베이스 질의와 비슷한 것을 수행할 수 있다. 리스트 컴프리헨션과 시퀀스 축소를 조합할 수도 있다.
```python
stocknames = [s['name'] for s in stocks]
a = [s for s in stocks if s['price'] > 100 and s['shares'] > 50 ]
cost = sum([s['shares']*s['price'] for s in stocks])
```

### 2.7 객체

* is, id 와 reference
```python
# validation of a and b ptr for equal object
>>> a = [1,2,3]
>>> b = a
>>> a is b
True
>>>
>>> id(a)
3588944
>>> id(b)
3588944
>>>
```

* 얕은 복사 (shallow copy)
  * object를 리스트의 elemnent로 갖을 때 조심해야 함
  * 아래는 리스트가 새로 만들어지긴 했지만 리스트 elelment가 공유되는 예제이다.
  ```python
  >>> a = [2,3,[100,101],4]
  >>> b = list(a) # 사본을 만듦
  >>> a is b
  False

  >>> a[2].append(102)
  >>> b[2]
  [100,101,102]
  >>>
  >>> a[2] is b[2]
  True
  >>>
  ```

  * 이때는 copy.deepcopy를 이용하면 됨
  ```python
  >>> import copy
  >>> b = copy.deepcopy(a)
  ```

* zip, dict, types list를 이용한 트릭! 꽤 유용한 예제다. 패턴을 기억해두자
```python
>>> types = [str, float, str, str, float, float, float, float, int]
>>> converted = [func(val) for func, val in zip(types, row)]
>>> record = dict(zip(headers, converted))
>>> record
{'volume': 181800, 'name': 'AA', 'price': 39.48, 'high': 39.69,
'low': 39.45, 'time': '9:36am', 'date': '6/11/2007', 'open': 39.67,
'change': -0.18}
>>> record['name']
'AA'
>>> record['price']
39.48
>>>
```