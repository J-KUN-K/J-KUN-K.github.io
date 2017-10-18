---
layout: post
title:  "간단한 3층 신경망과 소프트맥스 함수"
date:   2017-09-13 17:00:13 +0800
categories: machinelearning
tags: MLwithPython
comments: 1
---

**간단한3층(출력층 포함) 신경망을 구현해 이해를 돕는다. 또한 소프트맥스함수의 특징과 구현을 한다.**

---

##### 간단한 신경망 예제

{% highlight  python linenos  %}
import numpy as np

def signoid(x):
	return 1 / (1 + np.exp(-x))

def identify_function(x):
	return x

def init_network():
	network = {}
	network['W1'] = np.array([[0.1, 0.3, 0.5], [0.1, 0.4, 0.6]])
	network['b1'] = np.array([0.1, 0.2, 0.3])
	network['W2'] = np.array([[0.1, 0.4], [0.1, 0.5], [0.3, 0.6]])
	network['b2'] = np.array([0.1, 0.2])
	network['W3'] = np.array([[0.1, 0.3], [0.2, 0.4]])
	network['b3'] = np.array([0.1, 0.2])

	return network

def forward(network, x):
	W1, W2, W3 = network['W1'], network['W2'], network['W3']
	b1, b2, b3 = network['b1'], network['b2'], network['b3']

	a1 = np.dot(x, W1) + b1
	z1 = signoid(a1)
	a2 = np.dot(z1, W2) + b2
	z2 = signoid(a2)
	a3 = np.dot(z2, W3) + b3
	y = identify_function(a3)

	return y


network = init_network()
x = np.array([1.0, 0.5])
y = forward(network, x)
print(y)

# 결괏값 [ 0.31504774  0.69111457]

{% endhighlight %}


##### 소프트맥스 함수

{% highlight  python linenos  %}
def softmax(a):
	c = np.max(a)
	exp_a = np.exp(a-c) # 지수 함수로 인한 오버플로 방지
	sum_exp_a = np.sum(exp_a)
	y = exp_a / sum_exp_a

	return y

a = np.array([0.3, 2.9, 4.0])
y = softmax(a)
print(y)
np.sum(y)

# [ 0.01821127  0.24519181  0.73659691]
# 1

{% endhighlight %}

결과 값을 살펴보면 소프트맥스 함수의 출력은 0에서 1.0 사이의 실수입니다. 또 총합은 1이 된다는 것이 소프트 맥스 함수의 중요 성질 입니다. 소프트맥스함수는 단조 증가 함수로 현업에서도 지수 함수 계산에 드는 비용을 줄이고자 출력층에 소프트맥스를 함수를 생략해도 됩니다.



