## 3. 프로그램 조직화
>copyright: Practical Python Programming은 크리에이티브 커먼즈 저작자표시-동일조건변경허락 4.0 국제(Attribution ShareAlike 4.0 International) 라이선스

### 3.1 스크립팅

* docstring
  * """ wrapping 시 help 로 object로 호출하면 print되는 부분
* type hinting or type annotations: 연산이나 syntax에는 반영되지 않음. 개발자 참고용
```python
def read_prices(filename: str) -> dict:
  ...
```

### 3.2 함수의 작동

* 함수 내의 모든 할당은 로컬이다. 즉, 글로벌 변수를 함수 내에서 수정해야 한다면 함수 내에서 global 키워드를 사용하여 해당 변수가 global (함수 바깥 scope) 에 있음을 명시해야 한다.
```python
name = 'Dave'

def spam():
    global name
    name = 'Guido' # 위 글로벌 name을 변경
```

### 3.3 오류 검사

* 이미 앞장에서 설명되긴 했지만 한번더 설명을 하자면 아래와 같다.
* 예외는 오류에 대한 신호로 사용된다. 예외를 일으키고 싶으면 raise 문을 사용하라.
```python
if name not in authorized:
    raise RuntimeError(f'{name} not authorized')
```
* 예외를 붙잡으려면 try-except를 사용한다.
```python
try:
    authenticate(username)
except RuntimeError as e:
    print(e)
```
* raise 문을 일으킬때 사용하는 에러 타입은 Exception 을 상속받아 구현하면 된다
* 20개가 넘는 builtin 예외가 있다. 아래와 같으니 참조하자. 더 많은 builtin 예외가 있으니 더 알고 싶으면 문서를 참고하라.
```python
ArithmeticError
AssertionError
EnvironmentError
EOFError
ImportError
IndexError
KeyboardInterrupt
KeyError
MemoryError
NameError
ReferenceError
RuntimeError
SyntaxError
SystemError
TypeError
ValueError
```
* 처리한 다음, try-except 이후 첫 번째 문장으로 실행을 재개한다.
* 여러 except 문으로 여러 예외를 처리할 수 있다.
```python
try:
  ...
except LookupError as e:
  ...
except RuntimeError as e:
  ...
except IOError as e:
  ...
except KeyboardInterrupt as e:
  ...
```
* 또한, 처리하는 명령문이 동일하다면 아래 tuple 형식으로 그룹화 할 수 있다.
```python
try:
  ...
except (IOError,LookupError,RuntimeError) as e:
  ...
```
* 모든 오류를 한번에 붙잡으려면? 아래와 같이 except Exception: 구문을 작성하면 된다. 하지만 실패의 오류를 알 수 없으므로 추천하지 않는다. 결코 좋은 방식이 아니다!
```python
try:
    ...
except Exception:       # 위험! 아래를 참조
    print('An error occurred')
```
* 예외여부와 상관없이 반드시 실행해야 하는 문장이 있는 경우!? finally 문을 사용한다.
```python
lock = Lock()
...
lock.acquire()
try:
    ...
finally:
    lock.release()  # 이것은 항상 실행된다! 예외 발생 여부와 관계 없이.
```
* 위는 finally문을 설명하기 위한 예시이고, lock-unlock 패턴의 경우 요즘 코드에서는 with 문을 많이 사용한다. with는 리소스 사용 콘텍스트(context)를 정의한다.
```python
lock = Lock()
with lock:
    # 잠금을 획득
    ...
# 잠금 해제
```

### 3.4 모듈

* sys.modules 는 적재된 모듈 전체의 딕셔너리다.
```python
>>> import sys
>>> sys.modules.keys()
['copy_reg', '__main__', 'site', '__builtin__', 'encodings', 'encodings.encodings', 'posixpath', ...]
>>>
```

* 파이썬은 모듈을 찾을 때 경로 리스트(sys.path)를 참고한다.
```python
>>> import sys
>>> sys.path
[
  '',
  '/usr/local/lib/python36/python36.zip',
  '/usr/local/lib/python36',
  ...
]
```

### 3.5 메인 모듈

파이썬에는 메인 함수나 메서드가 없다. 그 대신 메인 모듈이 있다. 처음 실행하는 소스 파일이 메인 모듈이다.

* __main__ 확인: 모듈이 메인 스크립트로 작동하는지 if 조건문으로 알아보는 관레적인 방법이다. import 되어 모듈로서 동작할 때는 실행되지 않는 구문이 된다.
```python
# prog.py
...
if __name__ == '__main__':
    # 메인 프로그램으로서 실행 ...
    문장
    ...
```

* 위의 설명을 통해 아래와 같은 관례적인 프로그램 템플릿 작성 형식으로 모듈을 운영한다.
```python
# prog.py
# Import 문(라이브러리)
import modules

# 함수
def spam():
    ...

def blah():
    ...

# 메인 함수
def main(argv):
    # 명령행 인자, 환경 변수 등을 파싱.
    ...

if __name__ == '__main__':
    import sys
    main(sys.argv)  # sys import 하여 실행시 sys.argv 통해 argument 전달 가능하다
```

* 환경 변수(Environment Variable): os.environ 을 통해 환경변수에 접근이 가능하다.
```python
import os

name = os.environ['NAME'] # 'dave'
```

* #! 행: unix 계열 os에서 실행할 수 있는 실행문이다
```python
#!/usr/bin/env python3
# prog.py
```