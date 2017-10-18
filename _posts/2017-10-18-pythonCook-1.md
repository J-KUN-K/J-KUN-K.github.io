---
layout: post
title:  "1. 자료구조와 알고리즘(진행중)"
date:   2017-10-18 17:10:13 +0800
categories: study
tags: [python, study] 
---

**파이썬의 리스트, 세트 , 딕셔너리와 같은 구조체의 검색, 정렬, 여과 등을 배우고 cleections 모듈에 제공되는 다양한 자료구조를 다룬다.** 

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

* 임시

{% highlight python linenos %}

{% endhighlight %}

<br>


### 우선 순위 큐 구현

<br>

> 임시

* 임시

{% highlight python linenos %}

{% endhighlight %}

<br>


### 딕셔너리의 키를 여러 값에 매핑하기

<br>

> 임시

* 임시

{% highlight python linenos %}

{% endhighlight %}

<br>


### 딕셔너리 순서 유지

<br>

> 임시

* 임시

{% highlight python linenos %}

{% endhighlight %}

<br>


### 딕셔너리 계산

<br>

> 임시


{% highlight python linenos %}

{% endhighlight %}

<br>


### 두 딕셔너리의 유사점 찾기

<br>

> 두 딕셔너리에서 동일한 키나 값 등의 유사점을 찾고 싶다.

{% highlight python linenos %}

{% endhighlight %}

<br>


### 순서를 깨지 않고 시퀀스의 중복 없애기

<br>

> 임시


{% highlight python linenos %}

{% endhighlight %}

<br>


### 슬라이스 이름 붙이기

<br>

> 임시


{% highlight python linenos %}

{% endhighlight %}


<br>


### 시퀀스에 가장 많은 아이템 찾기

<br>

> 임시


{% highlight python linenos %}

{% endhighlight %}


<br>


### 일반 키로 딕셔너리 리스트 정렬

<br>

> 임시


{% highlight python linenos %}

{% endhighlight %}

<br>


### 기본 비교 기능 없이 객체 정렬

<br>

> 임시


{% highlight python linenos %}

{% endhighlight %}


<br>


### 필드에 따라 레코드 묶기

<br>

> 임시


{% highlight python linenos %}

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
