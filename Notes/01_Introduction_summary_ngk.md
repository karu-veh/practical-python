## 1. 파이썬 소개
>copyright: Practical Python Programming은 크리에이티브 커먼즈 저작자표시-동일조건변경허락 4.0 국제(Attribution ShareAlike 4.0 International) 라이선스

### 1.1 파이썬

ipython 환경에서 마지막 계산결과 사용하려면 언더바 '_' 사용함
```python
_ * 0.80 # '_' 는 마지막 계산결과를 의미함
```

### 1.2 첫 번째 프로그램

print는 default로 end에 개행 문자인 '\n'이 지정되어 있음. 개행 문자를 프린트하고 싶지 않으면 다음과 같이 한다.
```python
print('Hello', end=' ')
```

### 1.4 문자열

기타 문자열 메서드
```python
s.endswith(suffix)     # 문자열이 suffix로 끝나는지 확인
s.find(t)              # s에서 t가 처음 나타나는 곳
s.index(t)             # s에서 t가 처음 나타나는 곳
s.isalpha()            # 문자가 영문자인지 여부
s.isdigit()            # 문자가 숫자인지 여부
s.islower()            # 문자가 소문자인지 여부
s.isupper()            # 문자가 대문자인지 여부
s.join(slist)          # s를 구분자(delimiter)로 삼아 문자열의 리스트를 붙이기(join)
s.lower()              # 소문자로 변환
s.replace(old,new)     # 텍스트 교체
s.rfind(t)             # 문자열의 끝에서부터 t를 검색
s.rindex(t)            # 문자열의 끝에서부터 t를 검색
s.split([구분자])       # 문자열을 분할해 부분 문자열의 리스트를 만듦
s.startswith(prefix)   # 문자열이 prefix로 시작하는지 확인
s.strip()              # 앞뒤의 공백을 제거
s.upper()              # 대문자로 변환
```

문자열은 변경 불가능(immutable), 즉 읽기 전용이다. 한 번 생성하면 값이 바뀌지 않는다.
```python
>>> s = 'Hello World'
>>> s[1] = 'a'
Traceback (most recent call last):
File "<stdin>", line 1, in <module>
TypeError: 'str' object does not support item assignment
>>>
```

### 1.5 리스트

.index는 찾는 값의 위치 index를 반환하지만, 없을 경우 ValueError return하므로 예외 처리가 필요하다. 예외 처리 방식이 맘에 들지 않는다면, .count 로 먼저 갯수 체크 후 0이 아닌경우 index 를 찾는 방법이 있다.
```python
test_list = [1, 2, 3, 4, 'kim']

try:
    print(test_list.index('kim4'))
except ValueError:
    print("찾는 값 없음")
```

리스트의 특정 값 또는 인덱스 기준으로 element를 제거할 수 있다.
```python
# 값을 사용
names.remove('Curtis')

# 인덱스를 사용
del names[1]
```

리스트는 정렬할 수 있고 sort는 inplace=True 연산이고 sorted는 새로운 인스턴스를 생성한다.
```python
s = ['foo', 'bar', 'spam']
s.sort()                    # ['bar', 'foo', 'spam']
s.sort(reverse=True)        # ['spam', 'foo', 'bar']
t = sorted(s)               # s는 바뀌지 않으며, t는 정렬된 값을 가짐
```

### 1.6 파일 관리

f 기준으로 (resource) 파일 내 데이터를 한번에 읽거나 특정 바이트까지 읽는 방법은 아래와 같다.
```python
data = f.read()

# 'maxbytes' 바이트까지만 읽음
data = f.read([maxbytes])
```

파일을 열었으면 제대로 닫아야 하는데, 이 단계를 잊어버리기 쉽다. 따라서, 다음과 같이 with 문을 사용하면 좋다.
```python
with open(filename, 'rt') as file:
    # `file` 파일을 사용
    ...
    # 명시적으로 닫지 않아도 된다
...문장

# with 방식이 맘에 들지 않는다면!?: f = open(...), ..., f.close() 방식으로 사용함
```

파일 데이터를 읽는 일반적인 방법: 한번에 읽기와 한줄씩 읽기 2가지 경우가 있다.
```python
with open('foo.txt', 'rt') as file:
    data = file.read()
    # `data`는 `foo.txt`의 텍스트 전체로 된 문자열이다
```

```python
with open(filename, 'rt') as file:
    for line in file:
        # 행을 처리
```

문자열 데이터를 기록한다.
```python
with open('outfile', 'wt') as out:
    out.write('Hello World\n')
    ...
```

print 함수의 출력을 재지정(redirect)한다.
```python
with open('outfile', 'wt') as out:
    print('Hello World', file=out)
    ...
```

### 1.7 함수

오류와 예외: try - except 문을 사용한다. error의 확인은 cmd창에 print되니 해당 error 코드를 참조하여 except {error코드명}: 과 같은 syntax 구성을 하면 된다.
```python
for line in f:
    fields = line.split()
    try:
        shares = int(fields[1])
    except ValueError:
        print("Couldn't parse", line)
    ...

```

반대로 예외를 일으키려면, raise 문을 사용한다.
```python
# raise 문 기본 구조
raise [ExceptionClassName('예외 메시지')]
```

```python
# 사용자 정의 ExceptionClass 정의 및 사용 예. Exception 을 상속받아 구현하면 됨
class MyCustomException(Exception):
    pass

def example_function(x):
    if x < 0:
        raise MyCustomException('음수는 허용되지 않습니다.')

try:
    example_function(-5)
except MyCustomException as e:
    print("사용자 정의 예외 발생:", e)
```

```python
# 사용 예. 아래는 내장 exception class
raise RuntimeError('What a kerfuffle')
```