---
layout: post
title:  "5. 순열 판별, 문자열 압축"
date:   2017-09-27 23:10:13 +0800
categories: study
tags: algorithm 
comments: 1
img: algorithm-5.png
---

두개의 문자열 순열 판별, 같은 문자 반복 횟수를 이용한 문자열 압축  사용언어:파이썬

---

![img]({{baseurl}}/assets/res/study/algorithm-5.png)

##### 두개의 문자열 순열 판별

[A, B, C, A] == [A, B, C]  false
[S, B, C] == [b,c,S]  true

{% highlight python linenos %}
def anagram(str1, str2):
    if ''.join(sorted(str1.lower())).strip() == ''.join(sorted(str2.lower())).strip();:
        return True
    else:
        return False
{% endhighlight %}

<br>

##### 같은 문자 반복 횟수를 이용한 문자열 압축

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