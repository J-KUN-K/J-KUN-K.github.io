---
layout: post
title:  "2. OddOccurrencesInArray"
date:   2017-09-26 17:10:13 +0800
categories: study
tags: algorithm
comments: 1
img: algorithm-2.png
---

 OddOccurrencesInArray 문제를 풀어봅니다. 사용언어:자바

---


![img]({{baseurl}}/assets/res/study/algorithm-2.png)

리팩토링이 필요하다... Detected time complexity: O(N) or O(N*log(N)) 많이 풀어보고 남의 코드도 많이 봐야겠다.

홀수 갯수의 element가 들어온다는 점과 upaired는 결국 유일한 수라는 점을 이용해야 할듯...


{% highlight python linenos %}
// you can also use imports, for example
import java.util.*;

// you can write to stdout for debugging purposes, e.g.
// System.out.println("this is a debug message");


class Solution {
    Map<String, Integer> map = new HashMap<String, Integer>();
    int r;
    int pass;
    String str;
    
    public void mapPuter(int a) {
            str = Integer.toString(a);  
            map.put(str, 0);
    }
        
    public int solution(int[] A) {
        // write your code in Java SE 8

        for (int a : A) {
            str = Integer.toString(a);
            if(map.get(str)==null){mapPuter(a);}
            pass = map.get(str);
            map.put(str, ++pass);
        }
        
        Set<String> keySet = map.keySet();
        Iterator<String> keyIterator = keySet.iterator();
        while(keyIterator.hasNext()) {
            String key = keyIterator.next();
            Integer value = map.get(key);
            if (value%2 == 1){
                return Integer.parseInt(key);
            }
        }
        return 0;
    }
}
{% endhighlight %}

