## 7. 고급 주제
>copyright: Practical Python Programming은 크리에이티브 커먼즈 저작자표시-동일조건변경허락 4.0 국제(Attribution ShareAlike 4.0 International) 라이선스

### 7.1 가변 인자

* 위치 가변 인자(*args): ★추가적인 인자를 tuple로 전달한다!★
```python
def f(x, *args):
    ...

f(1,2,3,4,5)

def f(x, *args):
    # x -> 1
    # args -> (2,3,4,5)
```

* 키워드 가변 인자(**kwargs): ★추가적인 키워드를 딕셔너리로 전달한다!★
```python
def f(x, y, **kwargs):
    ...

f(2, 3, flag=True, mode='fast', header='debug')

def f(x, y, **kwargs):
    # x -> 2
    # y -> 3
    # kwargs -> { 'flag': True, 'mode': 'fast', 'header': 'debug' }
```

* 두가지를 혼합한 가변 인자(*args, **kwargs): 함수는 임의의 개수의 가변 인자와 키워드 없는 (non-keyword) 인자들을 받을 수 있다.
```python
def f(*args, **kwargs):
    ...

f(2, 3, flag=True, mode='fast', header='debug')

def f(*args, **kwargs):
    # args = (2, 3)
    # kwargs -> { 'flag': True, 'mode': 'fast', 'header': 'debug' }
    ...
```

* 튜플과 딕셔너리를 전달하기: 튜플을 가변 인자로 확장할 수 있으며, 마찬가지로 딕셔너리를 키워드 인자로 확장할 수 있음!
```python
numbers = (2,3,4)
f(1, *numbers)      # f(1,2,3,4)와 같음
```
```python
options = {
    'color' : 'red',
    'delimiter' : ',',
    'width' : 400
}
f(data, **options)
# f(data, color='red', delimiter=',', width=400)와 같음
```

### 7.2 익명 함수와 람다

* 리스트 정렬은 알겠는데 (sort, reverse를 옵션으로..), 딕셔너리 정렬은 어떻게 하는 걸까?: key 함수를 사용해 정렬 기준을 안내할 수 있다. 여기서 key 함수는 딕셔너리를 받아서 정렬 기준값을 반환한다!
```python
def stock_name(s):
    return s['name']

portfolio.sort(key=stock_name)  # 결과는 name key를 기준으로 정렬됨!
```

* 람다(Lambda): 익명 함수. 함수를 작성하는 대신 람다를 사용하자. 앞의 정렬 예제를 가지고 해 본다.
```python
# 이전 예제와 아래 람다 예제와 비교
portfolio.sort(key=lambda s: s['name'])
```

* 람다 사용하기
  * 람다는 매우 제한적이다.
  * ★단일 표현식만 허용한다.★
  * if, while 같은 문장은 허용하지 않는다.
  * sort()의 예와 같이 함수와 함께 사용하는 것이 일반적인 용도다.

### 7.4 함수 데코레이터

* 대표적인 구현 형식을 아래와 같이 정리해 보았다.function별로 logging을 추가해 주는 함수이다.
```python
def logged(func):
    def wrapper(*args, **kwargs):
        print('Calling', func.__name__)
        return func(*args, **kwargs)
    return wrapper

# 위처럼 구현해두면 decorator 형식으로 호출 가능함
# @{함수명}

@logged
def add(x, y):
    return x + y
```
