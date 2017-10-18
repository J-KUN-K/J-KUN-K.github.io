---
layout: post
title:  "4. 리스트 값의 유일 문자열, 문자열 뒤집기"
date:   2017-09-27 22:10:13 +0800
categories: study
tags: algorithm 
comments: 1
img: algorithm-4.png
---

리스트 값의 유일 문자열, 문자열 뒤집기 사용언어:파이썬

---

![img]({{baseurl}}/assets/res/study/algorithm-4.png)

##### 리스트 값의 유일 문자열

[A, B, C, A]  false
[A, B, C]  true

알고리즘 사이트를 통해서 접한 코드 방식...  해시 지정해서 유일값 찾는 방식이 새롭다. ord 내장함수도 몰랐다.. 많이 배우네

{% highlight python linenos %}
def isIniqChars(compare_list):
    if len(compare_list) > 256:
        return False
    
    hash = [False] * 256
    print(hash)
    for ch in compare_list:
        if hash[ord(ch)] is True:
            print(hash)
            return False
        else:
            print(hash)
            hash[ord(ch)] = True
    return True
{% endhighlight %}

<br>

##### 문자열 뒤집기

이건 기본적인 문자열 뒤집기 파이썬에선 [::-1] 으로도 출력이 가능하다.

{% highlight python linenos %}
def reverseString(str):
    stack = []
    for i in str:
        stack.append(i)
    
    result = ""
    while len(stack) > 0:
        result += stack.pop()
        
    return result
    
{% endhighlight %}