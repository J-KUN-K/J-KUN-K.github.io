---
layout: post
title:  "1. Binary gap"
date:   2017-09-19 17:10:13 +0800
categories: study
tags: [algorithm, study] 
comments: 1
img: algorithm-1.png
---

Binary gap 문제를 풀어봅니다. 사용언어:자바 

---

<br>


![img]({{baseurl}}/assets/res/study/algorithm-1.png)

All Rights Reserved. Unauthorized copying, publication or disclosure prohibited


문제의 공개나 복사 배포가 금지되어 있어서 내가 푼 답만....

코딩 시험 대비용으로 자바 보는데.... 파이썬만 쓰다가 자바 쓰니까 어휴...

자바스크립트 파이썬 자바 다 써볼까도 고민중....

{% highlight python linenos %}
// you can also use imports, for example:
 import java.util.*;

// you can write to stdout for debugging purposes, e.g.
// System.out.println("this is a debug message");

class Solution {
    public int solution(int N) {
        // write your code in Java SE 8
        List<Integer> result = new ArrayList();
        int total = 0;
        int counter = 1;
        int next = 0;
        int rr = 0;
        int max = 0;

        while(N != 0){
            
        int r = N%2;
        N = N/2;

        if (r == 1){
            result.add(counter);
            }
        counter++;
        }
        
        Collections.reverse(result);

        for (Integer i : result) { 
            if (0 == next) {
                next = i;
                continue;
            } 
            rr = next - i;
            next = i;
            if (rr >= max) {
                max = rr; 
            }
        }
        
        if(max == 0) {return 0;} else {return max-1;}
    }
}
{% endhighlight %}