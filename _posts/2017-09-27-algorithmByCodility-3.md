---
layout: post
title:  "3. CyclicRotation"
date:   2017-09-27 17:10:13 +0800
categories: study
tags: algorithm 
comments: 1
---

CyclicRotation  문제를 풀어봅니다. 사용언어:자바

---

87%의 정답률이 나왔다. extream_empty case를 통과 못했다.

Assume that:

that, given a zero-indexed array A consisting of N integers and an integer K, returns the array A rotated K times.

..........

-N and K are integers within the range [0..100];
-each element of array A is an integer within the range [−1,000..1,000].

근데 이 조건에 empty가 어디 포함된거지... 알고리즘 풀때는 문제 이해부터 항상 문제다.


{% highlight python linenos %}
// you can also use imports, for example:
// import java.util.*;

// you can write to stdout for debugging purposes, e.g.
// System.out.println("this is a debug message");

class Solution {
    public int[] solution(int[] A, int K) {
        // write your code in Java SE 8

        int[] newA = new int[A.length];  
        int length = A.length;
        int rotation;
        if (K%length != 0) { rotation = (K%length);} else {return A;}

        System.out.println(length);
        System.out.println(rotation);


        for (int i=0;i<length;i++){
            newA[rotation++] = A[i];
 
            if (rotation == length){rotation=0;}
        }

        return newA;
    }
}
{% endhighlight %}