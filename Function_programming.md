
# Functional programming with Python - frhyme

### motivation?
처음 배웠던 프로그래밍 언어는 C였고(Procedural) 그 다음에 C++로 object-orientation을 중심으로 코딩하기 시작함. 프로그래밍의 패러다임이 다양하게 있지만, 지금까지 한번도 '==함수형 프로그래밍=='에 대해서 제대로 알아본 적이 없는 것 같아서, 이참에 좀 정리를 하는게 필요하지 않을까? 싶어서 이참에 정리를 좀 해보기로.
대부분 이 [참고자료](https://docs.python.org/2/howto/functional.html)에서 내용을 가져옴(😓).

### summary
- python은 multi-paradigm을 지원하는 프로그래밍 언어.
- functional programming을 지원하기 위해서는 higher-order function, lazy evaluation이 가능해야 함
    - higher-order function은 원래 가능하며, functools에서도 지원
    - iterator를 활용하여 lazy evaluation이 가능
- iterator/generator는 함수형 프로그래밍을 가능하게 하는 중요 컨셉. 이를 통해 lazy evaluation을 획득할 수 있음.
- `itertools`에는 자주 쓸 법한 iterator를 생성하는 함수가 포함되어 있음
- `functools`는 함수를 축소하는 partial function application를 지원함

---

#### Programming paradigm
프로그래밍의 패러다임은 크게 다음 네 가지로 정리할 수 있다(고 한다).
[타입에 따른 프로그래밍 언어 분류](https://en.wikipedia.org/wiki/List_of_programming_languages_by_type)에 의하면 더 많은 종류의 프로그래밍 언어들이 있어서 호기심이 발생하지만 이건 나중에 좀 정리해보기로 하고...우선은 생략한다.
1. procedural(절차형)
: 명령어를 순차적으로 나열해서 푸는 방식(C, pascal, unix shell)
2. declarative(선언형)
: 문제의 스펙을 넘기면 언어 혹은 라이브러리가 알아서 풀어주는 방식(SQL, wolfram)
3. object-oriented
: 프로그램은 객체의 집합으로 구성(C++, Java)
4. functional
: 프로그램을 함수의 집합으로 구성한다(ML 언어 계열, Ocaml, scalar)

***"Pure Functional programming is programming without assignment statements."***

최근에는 동시에 여러 패러다임을 지원하는 언어들이 많이 나오고 있는 상황이며(python에서는 procedural, object-orientation, functional 을 모두 지원), 부분적으로 서로 다른 패러다임에 의해 구현되는 일도 많음. GUI는 object-oriented로, logic 처리는 procedural 이나 functional로.

---
### Is it Proift???
'함수형 프로그래밍'은 절대선이 아니며 필요에 따라서 구분하여 쓰는 것이 적합함.
##### Formal provability
- it’s easier to construct a mathematical proof that a functional program is correct.
- 정확하게 무슨 말인지는 모르겠지만, 해당 프로그램이 제대로 작동하는지(인풋에 대해서 의도한 대로 아웃풋이 나오는지) 검증할 때 함수형 프로그래밍이 다른 프로그래밍 패러다임들에 비해 수월함. "assignment statement"가 이러한 수학적인 검증을 크게 방해함

##### modularity and composability
- 함프가 가진 실용적인 장점은 '프로그래밍을 매우 작은 조각으로 나누도록 유도한다'는 것
    - 작은 함수들을 여러 개를 묶어서 복잡한 프로그래밍을 구현하는 것이, 직관적인 이해 측면은 물론 오류가 적게 하도록 하는 측면에서도 용이함
- structured programming의 경우 '독립적인 하나의 모듈을 설계하는 것'에 강점을 가지며(programming in small), 함프의 경우 '설계된 모듈을 연결하는 glue'의 역할(programming in large)를 지원함.
- lazy evaluation과 higher-order function이 이러한 modualariy와 composability를 강화함
    1. higher-order function(함수를 input으로 받아들여서 새로운 함수를 리턴하는 함수)
        - higher-order function를 통해 개별 함수를 보다 일반적인 형태로 정의하고 또 조합할 수 있다는 것
    2. lazy evaluation(연산식의 계산을 바로 하지 않고 최후까지 늦추는 방식, 게으른 연산)
        - 함수형 프로그래밍에서 g(f input)으로 설계되어 있을 때, 만약 f의 연산에 의해서 나온 값이 return되어 컴퓨터의 임시 메모리에 저장되었다가 g의 연산으로 간다면, 해당 연산의 효율성은 크게 떨어지게 된다.
            - 특히, f의 아웃풋이 무지막지하게 클 경우, 해당 메모리에서 이를 제어할 수 없을 수도 있음.
        - 함수형 프로그래밍에서는 lazy evaluation을 지원하기 때문에, 이 두 함수를 나누어 정의하여 modularity를 높일 수 있는데, 다른 프로그래밍에서는 이것이 불가하여, 통합하여 복잡한 함수를 정의해야 한다. 이것은 modularity를 저해하게 됨
        - 따라서 lazy evaluation이 modularity를 가능하게 한다.

##### Ease of debugging and testing
- 개별 함수들이 직관적이고, 분명하게 설계되어 개별 함수에 대해서만 확인하는 식으로 디버깅을 진행하기 때문에 디버깅이 굉장히 쉬워진다.
- 또한, 함수 중심으로 unit test를 진행하면 되기 때문에 테스팅도 쉬워짐

---


### python에서 functional-style programming하기

- python에서 function-style programming하기 위해서는 lazy evaluation을 지원하는 것이 필수적
- python에서는 lazy evaluation을 가능하게 하기 위해서 iterator 사용하는 것이 필요함

#### iterators
- 데이터 스트림을 나타내는 객체이며, 한 번에 하나의 데이터를 반환한다.
- python iterator는 next()를 호출하여 해당 데이터 스트림의 다음 요소를 가져올 수 있음
- next()했을 때 없을 경우, StopIteration exception을 일으킨다.
- `iter()` 는 iterator를 지원하는 객체(iterable object: list, dict)를 iterator로 변환해준다.
- iterator는 for, max, min, in 등의 함수 및 오퍼레이터에서 사용될 수 있다.


```python
iter_lst = ["a", "b"]
print(iter_lst)
for i in iter_lst:
    print(i)
print(iter(iter_lst))
for i in iter(iter_lst):
    print(i)
a = iter(iter_lst)
print(a)
print(next(a))
print(next(a))
print("min:", min(iter(iter_lst)) )
print("max:", max(iter(iter_lst)) )
a, b = iter(iter_lst)
print(a, b)
print("a" in iter(iter_lst))
print("c" not in iter(iter_lst))
```

    ['a', 'b']
    a
    b
    <list_iterator object at 0x0000027E9CFABBE0>
    a
    b
    <list_iterator object at 0x0000027E9CFABBE0>
    a
    b
    min: a
    max: b
    a b
    True
    True

---
### iterator를 지원하는 자료 형
- list
- dictionary
- file: file의 iterator는 `readline()`의 결과를 리턴
- set


```python
dict1 = {"a":1, "b":2, "c":3}
print(dict1)
for iter_key in iter(dict1):
    print(iter_key, ":", dict1[iter_key])

f = open("test.txt", "w")
for i in range(0, 3):
    f.write(str(i)+"번째 줄입니다\n")
f.close()
f = open("test.txt", "r")

while True :
    try:
        print(next(f))
    except:
        break
f.close()
```

    {'c': 3, 'a': 1, 'b': 2}
    c : 3
    a : 1
    b : 2
    0번째 줄입니다

    1번째 줄입니다

    2번째 줄입니다

---

### Generator
- generator는 iterator를 만들기 쉽게 하는 함수
- 일반적인 함수는 값을 계산해서 반환하지만, generator는 iterator를 반환한다.
- `yield` 키워드를 포함하는 함수가 generator.
- `yield n`는 return n and stop정도로 이해하면 됨


```python
def generate_ints_less_than(n):
    for i in reversed(range(0, n)):
        yield i
        #reversed 는 iterator object를 return
a = generate_ints_less_than(1000)
print(next(a))
print(next(a))
```

    999
    998


### Generator expressions and list comprehensions
- list comprehension과 유사하지만, 대괄호[]가 아닌 소괄호()로 묶을 경우, generator가 생성됨
- generator expression은 iterator를 반환하고, listcomps는 list를 반환한다.
- listcomp와 다르게 genexp는 materialize하지 않기 때문에, 무한 iterator, 대용량의 데이터를 사용하는데 적합하다


```python
line_lst = [" a ", " b ", " c "]
stripped_iter = (line.strip() for line in line_lst)   # Generator expression
stripped_list = [line.strip() for line in line_lst]   # List comprehension
print( stripped_iter, type(stripped_iter) )
print( stripped_list, type(stripped_list) )
while True:
    try:
        print(next(stripped_iter))
    except:
        break
```

    <generator object <genexpr> at 0x0000027E9DFBB518> <class 'generator'>
    ['a', 'b', 'c'] <class 'list'>
    a
    b
    c


---
### iterator와 함께 사용할 수 있는 built-in function
python2에서는 built-in function들이 list를 return했지만, python3에서는 iterator를 return
- map: iterable한 자료구조(list, dictionary)의 각 원소가 function에 입력되었을 때의 return값을 원소로 가지는 iterator를 return
- filter: iterable한 자료구조(list, dictionary)의 각 원소가 function에 입력되었을 때 True인 원소들만 원소로 가지는 iterator를 return
- enumerate: iterable한 자료구조에 대해서 순서를 포함한 원소를 가지는 iterator를 return


```python
##map
def lower(s):
    return s.lower()
def upper(s):
    return s.upper()
lst = ["a", "b", "c"]
a = map(upper, map(lower, map(upper, lst)))
print(next(a))
print(next(a))
print(next(a))

def oper(a, b):
    return (a+a)*(b+b)
lst1 = [1,2,3]
lst2 = [2,4,6]
a = map(oper, lst1, lst2)
print(next(a))
print(next(a))
print(next(a))

####filter
def is_even(x):
    return (x%2)==0
a = filter(is_even, range(10))
print( a )
print(next(a))
print(next(a))
print(next(a))

###enumerate

a = enumerate( range(10, 15) )
print(next(a))
print(next(a))
print(next(a))
```

    A
    B
    C
    8
    32
    72
    <filter object at 0x0000027E9CFB0828>
    0
    2
    4
    (0, 10)
    (1, 11)
    (2, 12)


---

### itertools module
- The itertools module 은 자주 사용되는 iterator를 포함하고 있음


```python
import itertools
a = itertools.count()
print( [next(a) for i in range(0, 10)] )
a = itertools.count(10)
print( [next(a) for i in range(0, 10)] )
a = itertools.cycle([1,2,3,4,5])
print( [next(a) for i in range(0, 10)] )
a  = itertools.repeat("abc")
print( [next(a) for i in range(0, 10)] )
```

    [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
    [10, 11, 12, 13, 14, 15, 16, 17, 18, 19]
    [1, 2, 3, 4, 5, 1, 2, 3, 4, 5]
    ['abc', 'abc', 'abc', 'abc', 'abc', 'abc', 'abc', 'abc', 'abc', 'abc']

---

### functools 모듈
- 'Higher-order function'(함수를 인자로 받아 새로운 함수를 반환)을 제공한다.
- 가장 유용한 도구는 functools.partial()
- 함수형 스타일로 작성하다보면 기존에 있는 함수에서 인자 중 일부만 결정한 형태의 함수가 필요하게 된다.
    - 예를 들어 f(a, b, c)가 있으면, f(1, b, c) => g(b, c)가 필요하다거나, 이런 것을 "partial function application"이라고 함


```python
import functools
def multiplication(a, b):
    return a*b
def calc(a, b, oper):
    return oper(a, b)
multiply = functools.partial(calc, oper=multiplication)
print( multiply(3, 2)  )
```

    6

---
### ??????
- 솔직히 지금도 계속 헷갈리기는 함.
- 'lazy evaluation을 통한 메모리 문제 및 속도 개선'이 중요한 강점으로 보이는데, 아직 대용량 데이터를 처리해본 일이 많지 않아서 체감이 좀 잘 안되는 감이 있음.

### reference
- [functional programming HOW TO](https://docs.python.org/2/howto/functional.html)
- [파이썬을 이용한 함수형 프로그래밍](http://functionalthinking.appspot.com/파이썬_함수형_프로그래밍)
