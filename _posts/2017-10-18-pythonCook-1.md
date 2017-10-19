---
layout: post
title:  "1. 자료구조와 알고리즘(진행중)"
date:   2017-10-18 17:10:13 +0800
categories: study
tags: [python, study] 
---

**파이썬의 리스트, 세트 , 딕셔너리와 같은 구조체의 검색, 정렬, 여과 등을 배우고 collections 모듈에 제공되는 다양한 자료구조를 다룬다.** 

---

<br>

### 시퀀스를 개별 변수로 나누기

<br>

> N개의 요소를 가진 튜플이나 시퀀스가 있다. 이를 변수 N개로 나누어야 한다.

{% highlight python linenos %}

P = (4, 5)
x, y = P
print(x)
print(y)

//결과 값
4
5

data = ['ACME', 0, 91.1, (2012, 12, 21)]
name, shares, price, (year, mon, day) = data

//결과 값
ACME
0
91.1
2012
12
21
{% endhighlight %}

<br>

* 더 알아보기

언패킹은 튜플이나 리스트뿐만아니라 순환 가능한 모든 객체에 적용할 수 있다. 여기에는 문자열, 파일 이터레이터, 제너레이터가 포함된다

{% highlight python linenos %}
//문자열 언패킹
s = 'Hello'
a, b, c, d, e = s

//특정값 언패킹
data = ['ACME', 0, 91.1, (2012, 12, 21)]
_, shares, price, _ = data
{% endhighlight %}


### 임의 순환체의 요소 나누기

<br>

> 순환체를 언패킹하려는데 요소가 N개 이상 포함되어 값이 너무 많습니다. 라는 예외가 발생한다.

별 표현식을 이용한다. 특정값이 일정수 이내면 상관없지만 일정이상 이라면 별 표현식을 이용하는게 효율적이다.

{% highlight python linenos %}
//**처음과 마지막 점수를 제외한 평균을 내는 예제**//
def avg(average):
    return sum(average) / len(average)

def drop_first_last(grades):
    first, *middle, last = grades
    return avg(middle)

grades = [10, 20, 20, 20, 10]

drop_first_last(grades)

//결과 값
20.0

//**이름, 이메일, 전화번호(몇개의 전화번호가 입력되더라도 폰넘버에 들어간다. 따라서 이값이 빠졌거나 타입을 검사하는 등의 작업필요 x!)**//
record = ('kim', 'dave@exam.net', '000-123-1234', '111-234-4567')
name, email, *phone_numbers = record

//결과 값
['000-123-1234', '111-234-4567']
{% endhighlight %}

<br>

* 더 알아보기

앞에서 알아본 언패킹 방식은 길이를 알 수 없는 순환체에 안성맞춤이다. 떄로는 순환체에 들어 있는 패턴이나 구조(ex. 뒤에 1이 나오면 무조건 전화번호)에도 별 구문을 사용하면 수고를 덜 수 있다.

{% highlight python linenos %}
records = [('foo', 1, 2),('bar', 'hello'),('foo', 3, 4)]
def do_foo(x, y):
    print('foo', x, y)

def do_bar(x):
    print('bar', x)

for tag, *args in records:
    if tag == 'foo':
        do_foo(*args)
    elif tag == 'bar':
        do_bar(*args)

//결과 값
foo 1 2
bar hello
foo 3 4
{% endhighlight %}

<br>

### 마지막 N개 아이템 유지

<br>

> 순환이나 프로세싱 중 마지막으로 발견한 N 개의 아이템을 유지하고 싶다.

{% highlight python linenos %}

from collections import deque

def search(lines, patterns, history=2):
    previous_lines = deque(maxlen=history)
    print('----')
    for line in lines:
        if patterns in line:
            yield line, previous_lines
        previous_lines.append(line)


with open('income_data.txt') as f:
    for line, prevlines in search(f, 'Own-child', 5):
        for pline in prevlines:
            print(pline)
            print('*'*20)
        print('-'*20)
        print(line)
        print('-'*20)    
{% endhighlight %}

<br>


### N 아이템의 최대 혹은 최소값 찾기

<br>

> 컬렉션 내부에서 가장 크거나 작은 N개의 아이템을 찾는다

{% highlight python linenos %}
import heapq

nums = [1, 8, 2, 23, 7, -4, 18, 23, 42, 37, 2]
print(heapq.nlargest(3, nums))
print(heapq.nsmallest(3, nums))

//결과 값
[42, 37, 23]
[-4, 1, 2]

portfolio = [
    {'name': 'IBM', 'shares' : 100, 'price': 91.1},
    {'name': 'SAMSUNG', 'shares' : 50, 'price':200},
    {'name': 'LG', 'shares' : 200, 'price': 83.2},
    {'name': 'MERITS', 'shares' : 300, 'price': 120},
    {'name': 'BANK', 'shares' : 230, 'price': 140.3}
]

print(heapq.nlargest(3, portfolio, key=lambda s: s['price']))
print(heapq.nsmallest(3, portfolio, key=lambda s: s['price']))

//결과 값
[{'name': 'SAMSUNG', 'shares': 50, 'price': 200}, {'name': 'BANK', 'shares': 230, 'price': 140.3}, {'name': 'MERITS', 'shares': 300, 'price': 120}]
[{'name': 'LG', 'shares': 200, 'price': 83.2}, {'name': 'IBM', 'shares': 100, 'price': 91.1}, {'name': 'MERITS', 'shares': 300, 'price': 120}]
{% endhighlight %}


* 더 알아보기

가장 작거나 큰 n개의 아이템을 찾고 있고 N이 컬렉션 전체 크기보다 작다면 앞에 나온 함수가 더 나은 성능을 제공 한다. 하지만 최소나 최대값 하나를 찾는 경우에는 

<br>

### 우선 순위 큐 구현

<br>

> 주어진 우선 순위에 따라 아이템을 정렬하는 큐를 구현하고 항상 우선 순위가 가장 높은 아이템을 먼저 팝하도록 만들어야 한다.

{% highlight python linenos %}
import heapq

class PriorityQueue:
    def __init__(self):
        self._queue = []
        self._index = 0
        
    def push(self, item, priority):
        heapq.heappush(self._queue, (priority, self._index, item))
        self._index += 1
    
    def pop(self):
        return heapq.heappop(self._queue)[-1]

class Item:
    def __init__(self, name):
        self.name = name
    def __repr__(self):
        return 'Item({!r})'.format(self.name)
    
q = PriorityQueue()
q.push(Item('foo'), 1)
q.push(Item('bar'), 2)
q.push(Item('bee'), 1)
q.push(Item('ttt'), 3)
print(q.pop())
print(q.pop())
print(q.pop())

//결과 값
Item('foo')
Item('bee')
Item('bar')
{% endhighlight %}

<br>

### 딕셔너리의 키를 여러 값에 매핑하기

<br>

> 딕셔너리의 키를 하나 이상의 값에 매핑하고 싶다.

어렵지 않게 해결 가능하다 디펄트딕트의 장점을 이용하면 일반적인 여러값을 가진 값을 매핑하는데 더욱 간편해진다

{% highlight python linenos %}
d = {
    'a' : [1,2,3],
    'b' : [4,5]
}

from collections import defaultdict

f = defaultdict(list)
f['a'].append(1)
f['b'].append(2)
f['a'].append(3)

//**일반적인 방법**//
d = {}
for key, value in pairs:
    if key not in d:
        d[key] = []
    d[key].append(value) 

//**defaultdict 이용**//
d = defaultdict(list)
for key, value in pairs:
    d[key].append(value)
{% endhighlight %}

<br>

### 딕셔너리 순서 유지

딕셔너리 내부 아이템의 순서를 조절 하려면 collections 모듈의 OrederedDict를 사용한다. 이 모듈은 초기 삽입 순서를 그대로 기억한다.
OrderedDict는 나중에 직렬화 하거나 다른 포맷으로 인코딩할 다른 매핑을 만들 때 특히 유용하다.

<br>

> 딕셔너리를 만들고 순환이나 직렬화할 떄 순서를 조절하고 싶다.

{% highlight python linenos %}

{% endhighlight %}

<br>


### 딕셔너리 계산

<br>


### 두 딕셔너리의 유사점 찾기

<br>

> 두 딕셔너리에서 동일한 키나 값 등의 유사점을 찾고 싶다.

{% highlight python linenos %}
a = {'x':1, 'y':2, 'z':3 }
b = {'x':10, 'y':2, 'z':30 , 'v':4}
print(a.keys() & b.keys())	
print(a.keys() - b.keys())
print(b.keys() - a.keys())
print(a.items() & b.items())

//결과 값
{'x', 'z', 'y'}
set()
{'v'}
{('y', 2)}
{% endhighlight %}

<br>


### 순서를 깨지 않고 시퀀스의 중복 없애기

<br>

> 시퀀스에서 중복된 값을 없애고 싶지만 아이템의 순서는 유지하고 싶은 경우

일반 set 함수는 순서를 망친다. 순서를 유지하고 싶은 경우에 사용

{% highlight python linenos %}
//**일반 리스트의 경우**//
def dedupe(items):
    seen = set()
    for item in items:
        if item not in seen:
            yield item
            seen.add(item)
            
a = [1, 5, 2, 1, 9, 1, 5, 10]
print(list(dedupe(a)))

//**dict의 값의 중복을 없애는 경우**//
def dedupe_dict(items, key=None):
    seen = set()
    for item in items:
        val = item if key is None else key(item)
        if val not in seen:
            yield item
            seen.add(val)
            
a = [{'x':1,'y':2}, {'x':1,'y':3}, {'x':1,'y':2}, {'x':2,'y':4}]

print(list(dedupe_dict(a, key=lambda d: (d['x'], d['y']))))
{% endhighlight %}

<br>

* 더 알아보기

리스트나 딕셔너리가 아닌 일반적인 목적으로도 이용 가능하다.

{% highlight python linenos %}
with open('somefile.txt', 'r') as f:
    for line in dedupe(f):
        pass
{% endhighlight %}

<br>

### 슬라이스 이름 붙이기

<br>

> 프로그램 코드에 슬라이스를 지기하는 하드코딩이 너무 많아 이해하기 어려운 상황이다. 

고정된 문자열로부터 특정 데이터를 추출하는 코드가 있다고 가정해 보자.

{% highlight python linenos %}
record = '...................100  ...........513.25 ......'
cost = int(record[20:32] * float(record[40:48]))
{% endhighlight %}

<br>

이런 코드를 보고 다른 사람이 혹은 1년 후에 의미를 알아볼 수 있을까? 난 재직 초반에 나혼자 코딩하는 경우가 많아 이런식으로 했던 경우가 많았던것 같다. 반성한다..... 나은 코드로 리팩토링을 통해 의미를 줘보자...

<br>

{% highlight python linenos %}
SHARES = slice(20, 32)
PRICE = slice(40, 48)

cost = int(record[SHARES] * float(PRICE))
{% endhighlight %}

<br>

{% highlight python linenos %}
record = '...................100  ...........513.25 ......'
cost = int(record[20:32] * float(record[40:48]))
{% endhighlight %}


<br>

* 더 알아보기

indices(size) 메소드를 사용하면 특정 크기의 시퀀스에 슬라이스를 매핑할 수 있다. 이렇게 하면 튜플을 반환하는데 모든값은 경계를 넘어서지 않도록 제약이 걸려 있다. (IndexError 예외 방지)

{% highlight python linenos %}
s = 'HelloWorld'
a = slice(5, 15, 2)
a.indices(len(s))
print(a.indices(len(s)))
print(a.start)
print(a.stop)
print(a.step)

for i in range(*a.indices(len(s))):
    print(s[i])

//결과 값
(5, 10, 2)
5
15
2
W
r
d
{% endhighlight %}

<br>

### 시퀀스에 가장 많은 아이템 찾기

<br>

> 시퀀스에 가장 많이 나타난 아이템을 찾기

{% highlight python linenos %}
words = ['look', 'into', 'my', 'eyes', 'look', 'into', 'my', 'eyes', 'the', 'eyes', 'the', 'look', 'into', 'look', 'look','around', 'eyes' ]

from collections import Counter
word_counters = Counter(words)
top_three = word_counters.most_common(3)
print(top_three)

//결과 값
[('look', 5), ('eyes', 4), ('into', 3)]
{% endhighlight %}

<br>

* 더 알아보기

{% highlight python linenos %}
#카운트를 수동으로 증가 시키고 싶은 경우
for word in morewords:
    word_counters[word] += 1
top_three = word_counters.most_common(3)
print(top_three)

[('my', 6), ('look', 5), ('the', 5)]

#카운트를 수동으로 증가 by update
word_counts.update(morewords)

#Counter 함수 이용
a = Counter(words)
b = Counter(words)

# 카운트 합치기
a+b
# 카운트 빼기
a-b
{% endhighlight %}


<br>

### 일반 키로 딕셔너리 리스트 정렬

<br>

> 딕셔너리 리스트가 있고, 하나 혹은 그 이상의 딕셔너리 값으로 이를 정렬하고 싶다.

{% highlight python linenos %}
rows = [
    {'fname':'Brian', 'lname':'Jones', 'uid':1003 },
    {'fname':'David', 'lname':'Beazley', 'uid':1002 },
    {'fname':'Jhon', 'lname':'Cleese', 'uid':1001 },
    {'fname':'Big', 'lname':'Jones', 'uid':1004 },
    {'fname':'Small', 'lname':'Jones', 'uid':1002 }
]

from operator import itemgetter

rows_by_fname = sorted(rows, key=itemgetter('fname'))
rows_by_uid = sorted(rows, key=itemgetter('uid'))
rows_by_lfname = sorted(rows, key=itemgetter('lname', 'uid'))


print(rows_by_fname)
print(rows_by_uid)
print(rows_by_lfname)

# 결과값
[{'fname': 'Big', 'lname': 'Jones', 'uid': 1004}, {'fname': 'Brian', 'lname': 'Jones', 'uid': 1003}, {'fname': 'David', 'lname': 'Beazley', 'uid': 1002}, {'fname': 'Jhon', 'lname': 'Cleese', 'uid': 1001}, {'fname': 'Small', 'lname': 'Jones', 'uid': 1002}]
[{'fname': 'Jhon', 'lname': 'Cleese', 'uid': 1001}, {'fname': 'David', 'lname': 'Beazley', 'uid': 1002}, {'fname': 'Small', 'lname': 'Jones', 'uid': 1002}, {'fname': 'Brian', 'lname': 'Jones', 'uid': 1003}, {'fname': 'Big', 'lname': 'Jones', 'uid': 1004}]
[{'fname': 'David', 'lname': 'Beazley', 'uid': 1002}, {'fname': 'Jhon', 'lname': 'Cleese', 'uid': 1001}, {'fname': 'Small', 'lname': 'Jones', 'uid': 1002}, {'fname': 'Brian', 'lname': 'Jones', 'uid': 1003}, {'fname': 'Big', 'lname': 'Jones', 'uid': 1004}]
{% endhighlight %}

<br>

* 더 알아보기

{% highlight python linenos %}

{% endhighlight %}


<br>


### 기본 비교 기능 없이 객체 정렬

<br>



### 필드에 따라 레코드 묶기

<br>

> 일련의 딕셔너리나 인스턴스가 있고 특정 필드 값에 기반한 그룹의 데이터를 순환하고 싶다.


{% highlight python linenos %}
from operator import itemgetter
from itertools import groupby

rows =[
    {'address':'5412 N CLARK', 'date':'07/01/2012'},
    {'address':'5148 N CLARK', 'date':'07/04/2012'},
    {'address':'5800 E 58TH', 'date':'07/02/2012'},
    {'address':'2122 N CLARK', 'date':'07/01/2012'},
    {'address':'5645 N RAVENWOOD', 'date':'07/02/2012'},
    {'address':'1060 W ADDISON', 'date':'07/02/2012'},
    {'address':'1039 W GRACE', 'date':'07/04/2012'},
]

rows.sort(key=itemgetter('date'))

for date, items in groupby(rows, key=itemgetter('date')):
    print(date)
    for i in items:
        print('         ', i)

//결과 값
07/01/2012
          {'address': '5412 N CLARK', 'date': '07/01/2012'}
          {'address': '2122 N CLARK', 'date': '07/01/2012'}
07/02/2012
          {'address': '5800 E 58TH', 'date': '07/02/2012'}
          {'address': '5645 N RAVENWOOD', 'date': '07/02/2012'}
          {'address': '1060 W ADDISON', 'date': '07/02/2012'}
07/04/2012
          {'address': '5148 N CLARK', 'date': '07/04/2012'}
          {'address': '1039 W GRACE', 'date': '07/04/2012'}
{% endhighlight %}

<br>


### 시퀀스 필터링

<br>

> 임시


{% highlight python linenos %}

{% endhighlight %}

<br>

### 딕셔너리 부분 추출

<br>

> 임시


{% highlight python linenos %}

{% endhighlight %}



<br>


### 시퀀스 요소에 이름 매핑

<br>

> 임시

{% highlight python linenos %}

{% endhighlight %}

<br>

### 데이터를 변환하면서 줄이기

<br>

> 임시

{% highlight python linenos %}

{% endhighlight %}

<br>

### 여러 매핑을 단일 매핑으로 합치기

<br>

> 임시

{% highlight python linenos %}

{% endhighlight %}

<br>
