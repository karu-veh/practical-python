## 6. 제너레이터
>copyright: Practical Python Programming은 크리에이티브 커먼즈 저작자표시-동일조건변경허락 4.0 국제(Attribution ShareAlike 4.0 International) 라이선스

### 6.1 이터레이션 프로토콜

이 섹션을 통해서는 이터레이션이 어떻게 작동하는지 살펴볼 수 있다.

* for 문을 생각해보자.
```python
for x in obj:
    # 문장
```
내부적으로 어떻게 작동하는가?
```python
_iter = obj.__iter__()        # 이터레이터 객체를 얻음
while True:
    try:
        x = _iter.__next__()  # 다음 항목을 얻음
    except StopIteration:     # 남은 항목이 없음
        break
    # 문장 ...
```
위를 수작업으로 저수준으로 구현하면 아래와 같다.
```python
>>> x = [1,2,3]
>>> it = x.__iter__()
>>> it
<listiterator object at 0x590b0>
>>> it.__next__()
1
>>> it.__next__()
2
>>> it.__next__()
3
>>> it.__next__()
Traceback (most recent call last):
File "<stdin>", line 1, in ? StopIteration
>>>
```

* 이터레이션의 유용성을 이해한다면 스스로 작성한 객체도 이터레이션을 지원하게 하고 싶을 것이다. 예를 들어, 다음과 같이 커스텀 컨테이너를 만들 수 있다.
```python
class Portfolio:
    def __init__(self):
        self.holdings = []

    def __iter__(self):
        return self.holdings.__iter__()
    ...

port = Portfolio()
for s in port:
    ...
```

* builtin 함수인 next()  를 사용하면 이터레이터의 __next__() 메서드를 간편하게 호출할 수 있다.
```python
>>> f = open('Data/portfolio.csv')
>>> f.__iter__()    # 참고: 이것은 파일 자체를 반환함
<_io.TextIOWrapper name='Data/portfolio.csv' mode='r' encoding='UTF-8'>
>>> next(f)
'name,shares,price\n'
>>> next(f)
'"AA",100,32.20\n'
>>> next(f)
'"IBM",50,91.10\n'
>>>
```

### 6.2 이터레이션을 커스터마이징

이 섹션은 제너레이터 함수를 사용해 이터레이션을 커스터마이즈하는 법을 살펴본다.

* ★제너레이터: 제너레이터는 이터레이션을 정의하는 함수다!★
```python
def countdown(n):
    while n > 0:
        yield n
        n -= 1

```
예:
```python
>>> for x in countdown(10):
...   print(x, end=' ')
...
10 9 8 7 6 5 4 3 2 1
>>>
```
★yield 문을 사용하는 함수는 제너레이터다.★

* ★제너레이터는 일반적인 함수와 다르게 동작한다. 제너레이터 함수를 호출하면 제너레이터 객체가 생성된다. 함수가 즉시 실행되는 것은 아니다!★
```python
def countdown(n):
    # print 문을 추가했다
    print('Counting down from', n)
    while n > 0:
        yield n
        n -= 1
```
```python
>>> x = countdown(10)
# print 문이 없다!
>>> x
# x는 generator 객체다
<generator object at 0x58490>
>>>
```
★★이 함수는 \_\_next__() 호출에 의해서만 실행된다!!!★★
```python
>>> x = countdown(10)
>>> x
<generator object at 0x58490>
>>> x.__next__()
Counting down from 10
10
>>>
```
제너레이터가 마지막으로 반환할 때, 이터레이션은 오류를 일으킨다.
```python
>>> x.__next__()
1
>>> x.__next__()
Traceback (most recent call last):
File "<stdin>", line 1, in ? StopIteration
>>>
```

* 이터레이션을 커스터마이즈하고 싶을 때에는 제너레이터 함수를 떠올려라. 작성하기 쉽다---원하는 이터레이션 로직을 함수로 구현하고, yield 문으로 값을 내보내면 된다.
```python
>>> def filematch(filename, substr):
        with open(filename, 'r') as f:
            for line in f:
                if substr in line:
                    yield line

>>> for line in open('Data/portfolio.csv'):
        print(line, end='')

name,shares,price
"AA",100,32.20
"IBM",50,91.10
"CAT",150,83.44
"MSFT",200,51.23
"GE",95,40.37
"MSFT",50,65.10
"IBM",100,70.44
>>> for line in filematch('Data/portfolio.csv', 'IBM'):
        print(line, end='')

"IBM",50,91.10
"IBM",100,70.44
>>>
```

### 6.4 제너레이터 심화

* 제너레이터 표현식: 리스트 컴프리헨션의 제너레이터 버전.
```python
>>> a = [1,2,3,4]
>>> b = (2*x for x in a)
>>> b
<generator object at 0x58760>
>>> for i in b:
...   print(i, end=' ')
...
2 4 6 8
>>>
```

* ★★리스트 컴프리헨션과의 차이점★★
  * 리스트를 생성하지 않는다.
  * 유일한 용도는 이터레이션이다.
  * 소비되고 나면 재사용할 수 없다.
* ★★일반적인 구문은 다음과 같다!★★
```python
(<expression> for i in s if <조건>)
```

* ★★제너레이터 표현식의 주용도는 시퀀스에서 계산을 수행하되 결과를 단 한 번만 사용하는 코드를 위한 것이다. 예를 들어, 파일에서 모든 주석을 제거한다.★★
```python
f = open('somefile.txt')
lines = (line for line in f if not line.startswith('#'))
for line in lines:
    ...
f.close()
```

* 제너레이터를 사용하면 코드가 더 빨리 작동하며 메모리를 적게 사용한다. 스트림에 필터를 적용한 것과 같다. 그 외에 제너레이터를 사용하는 이유를 정리하면 아래와 같다!!
  * 많은 프로그램은 이터레이션으로 깔끔하게 표현된다.
  * 항목들의 컬렉션을 루핑하며 몇 가지 연산(검색, 교체, 수정 등)을 수행한다.
  * 파이프라인을 처리하는 것은 더 넓은 범위의 데이터 처리 문제가 적용된다.
  * 메모리를 더 효율적으로 사용한다.
  * 필요할 때만 값을 생산한다.
  * 거대한 리스트를 생성하는 것과 대비된다.
  * 스트리밍 데이터를 다룰 수 있다
  * 제너레이터는 코드 재사용을 촉진한다
  * 코드에서 이터레이션과 이터레이션 사용을 분리한다.
  * 흥미로운 이터레이션 함수의 도구모음을 만들고 믹스 앤 매치할 수 있다.
* itertools 모듈: itertools는 이터레이터/제너레이터를 돕도록 설계된 다양한 함수가 있는 라이브러리 모듈이다. 향후 코드 리딩시 등장하면 참조하면 될듯!!

