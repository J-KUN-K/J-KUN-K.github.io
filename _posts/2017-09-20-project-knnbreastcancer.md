---
layout: post
title:  "kNN algorithm을 이용한 유방암 진찰"
date:   2017-09-20 13:00:13 +0800
categories: project
tags: [R, project]
comments: 1
---
**kNN 알고리즘을 통해 유방암 진찰 시뮬레이션을 해본다.**

---

##### 진행중

###### 자료 출처는 위스콘신 대학 유방암 데이터

자료를 읽어 변수에 할당하여 줍니다. 위스콘신 대학에서 제공하는 실제 데이터이며 디지털화된 유방 덩어리 미세 침흡인물의 이미지로부터 측정치가 포함돼 있습니다. 그 값은 디지털 이미지에 나타난 세포 핵의 특징입니다.

{% highlight python linenos %}
> wdbc <- read.csv("d:/data/r/data.csv", stringsAsFactors=FALSE)
> str(wdbc)
'data.frame':	569 obs. of  33 variables:
 $ id                     : int  842302 842517 84300903 84348301 84358402 843786 844359 84458202 844981 84501001 ...
 $ diagnosis              : chr  "M" "M" "M" "M" ...
 $ radius_mean            : num  18 20.6 19.7 11.4 20.3 ...
 $ texture_mean           : num  10.4 17.8 21.2 20.4 14.3 ...
 $ perimeter_mean         : num  122.8 132.9 130 77.6 135.1 ...
 $ area_mean              : num  1001 1326 1203 386 1297 ...
 $ smoothness_mean        : num  0.1184 0.0847 0.1096 0.1425 0.1003 ...
 $ compactness_mean       : num  0.2776 0.0786 0.1599 0.2839 0.1328 ...
 $ concavity_mean         : num  0.3001 0.0869 0.1974 0.2414 0.198 ...
 $ concave.points_mean    : num  0.1471 0.0702 0.1279 0.1052 0.1043 ...
 $ symmetry_mean          : num  0.242 0.181 0.207 0.26 0.181 ...
 $ fractal_dimension_mean : num  0.0787 0.0567 0.06 0.0974 0.0588 ...
 $ radius_se              : num  1.095 0.543 0.746 0.496 0.757 ...
 $ texture_se             : num  0.905 0.734 0.787 1.156 0.781 ...
 $ perimeter_se           : num  8.59 3.4 4.58 3.44 5.44 ...
 $ area_se                : num  153.4 74.1 94 27.2 94.4 ...
 $ smoothness_se          : num  0.0064 0.00522 0.00615 0.00911 0.01149 ...
 $ compactness_se         : num  0.049 0.0131 0.0401 0.0746 0.0246 ...
 $ concavity_se           : num  0.0537 0.0186 0.0383 0.0566 0.0569 ...
 $ concave.points_se      : num  0.0159 0.0134 0.0206 0.0187 0.0188 ...
 $ symmetry_se            : num  0.03 0.0139 0.0225 0.0596 0.0176 ...
 $ fractal_dimension_se   : num  0.00619 0.00353 0.00457 0.00921 0.00511 ...
 $ radius_worst           : num  25.4 25 23.6 14.9 22.5 ...
 $ texture_worst          : num  17.3 23.4 25.5 26.5 16.7 ...
 $ perimeter_worst        : num  184.6 158.8 152.5 98.9 152.2 ...
 $ area_worst             : num  2019 1956 1709 568 1575 ...
 $ smoothness_worst       : num  0.162 0.124 0.144 0.21 0.137 ...
 $ compactness_worst      : num  0.666 0.187 0.424 0.866 0.205 ...
 $ concavity_worst        : num  0.712 0.242 0.45 0.687 0.4 ...
 $ concave.points_worst   : num  0.265 0.186 0.243 0.258 0.163 ...
 $ symmetry_worst         : num  0.46 0.275 0.361 0.664 0.236 ...
 $ fractal_dimension_worst: num  0.1189 0.089 0.0876 0.173 0.0768 ...
 $ X                      : logi  NA NA NA NA NA NA ...
{% endhighlight %}

id는 환자에 대한 유일한 식별자입니다. 유용한 정보를 제공하는 것이 아니기 때문에 모델에서 이 변수를 배제하겠습니다. 거의 대부분 머신러닝에서 아이디값은 배제됩니다.

{% highlight python linenos %}
> wdbc <- wdbc[-1]
> table(wdbc$diagnosis)

  B   M 
357 212 

{% endhighlight %}

###### 데이터는 357개의 응어리가 양성 212개가 악성

{% highlight python linenos %}
> wdbc$diagnosis <- factor(wdbc$diagnosis, levels = c("B", "M"), labels = c("Benign", "Malignant"))
> round(prop.table(table(wdbc$diagnosis)) * 100, digit = 1)

   Benign Malignant 
     62.7      37.3 

> summary(wdbc[c("radius_mean", "area_mean", "smoothness_mean")])

  radius_mean       area_mean      smoothness_mean  
 Min.   : 6.981   Min.   : 143.5   Min.   :0.05263  
 1st Qu.:11.700   1st Qu.: 420.3   1st Qu.:0.08637  
 Median :13.370   Median : 551.1   Median :0.09587  
 Mean   :14.127   Mean   : 654.9   Mean   :0.09636  
 3rd Qu.:15.780   3rd Qu.: 782.7   3rd Qu.:0.10530  
 Max.   :28.110   Max.   :2
{% endhighlight %}

각각의 속성을 보면 kNN의 거리 계산은 입력 속성의 측정 범위에 크게 의존합니다. smooth_mean의 범위는 0.05에서 0.16까지이며, area_mean의 범위는 143.5에서 2501.0까지입니다. 결과적으로 거리계산에서 area의 영향은 smooth보다 훨씬 크게 됩니다. 이는 분류기가 잠재적으로 문제를 유발할 수 있으므로 속성의 값의 정규 범위로 재조정하기 위해 정규화를 적용해야 합니다.


##### 정규화

{% highlight python linenos %}
> normalize <- function(x) { return ((x - min(x)) / (max(x) -min(x))) }
> normalize(c(1, 2, 3, 4, 5))
[1] 0.00 0.25 0.50 0.75 1.00
> normalize(c(10, 20, 30, 40, 50))
[1] 0.00 0.25 0.50 0.75 1.00
{% endhighlight %}

정규화함수가 잘 작동합니다. 절대값 크기가 10배크지만 상관없이 정규화 값은 같습니다.

{% highlight python linenos %}
> wbcd_n <- as.data.frame(lapply(wdbc[2:31], normalize))
> summary(wdbc_n$area_mean)
> summary(wbcd_n$area_mean)
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
 0.0000  0.1174  0.1729  0.2169  0.2711  1.0000 
 {% endhighlight %}

 결괏값에 보이듯이 area_mean 범위는 143.5~2501.0 이었지만 지금은 0~1로 변경되었습니다.

 {% highlight python linenos %}
> wbcd_n <- as.data.frame(lapply(wdbc[2:31], normalize))
> summary(wdbc_n$area_mean)
> summary(wbcd_n$area_mean)
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
 0.0000  0.1174  0.1729  0.2169  0.2711  1.0000 
 {% endhighlight %}

 ##### 데이터 전처리 : 훈련, 테스트 데이터

훈련데이터 479개, 테스트 데이터 100개 생성

목표 변수 인 diagnosis를 배제하기 위해 팩터 백터로 이런 범주 라벨을 저장할 필요가 있다.

{% highlight python linenos %}
> wbcd_train <- wbcd_n[1:469, ]
> wbcd_test <- wbcd_n[470:569, ]
> wbcd_train_labels <- wbcd[1:469, 1]
> wbcd_test_labels <- wbcd[470:569, 1]
{% endhighlight %}

 ##### 데이터 전처리 : 훈련, 테스트 데이터

 {% highlight python linenos %}
> package("class")
> library("class")
> wbcd_test_pred <- knn(train = wbcd_train, test = wbcd_test, cl=wbcd_train_labels, k =21)
{% endhighlight %}

 ##### 모델 성능 평가

 {% highlight python linenos %}
> package("gmodels")
> library("gmodels")
> wbcd_test_pred <- knn(train = wbcd_train, test = wbcd_test, cl=wbcd_train_labels, k =21)
> CrossTable(x = wbcd_test_labels, y = wbcd_test_pred, prop.chisq=FALSE)
{% endhighlight %}

##### 결괏값

 {% highlight python linenos %}

                 | wbcd_test_pred 
wbcd_test_labels |    Benign | Malignant | Row Total | 
-----------------|-----------|-----------|-----------|
          Benign |        77 |         0 |        77 | 
                 |     1.000 |     0.000 |     0.770 | 
                 |     0.975 |     0.000 |           | 
                 |     0.770 |     0.000 |           | 
-----------------|-----------|-----------|-----------|
       Malignant |         2 |        21 |        23 | 
                 |     0.087 |     0.913 |     0.230 | 
                 |     0.025 |     1.000 |           | 
                 |     0.020 |     0.210 |           | 
-----------------|-----------|-----------|-----------|
    Column Total |        79 |        21 |       100 | 
                 |     0.790 |     0.210 |           | 
-----------------|-----------|-----------|-----------|

{% endhighlight %}

흔히 말하는 1종 오류가 발생했습니다. 악성인 환자에게 양성 판단2건  2종 오류는 발생하지 않았습니다. 

##### 모델성능 높이기

 {% highlight python linenos %}

> wbcd_z <- as.data.frame(scale(wdbc[-1]))
> summary(wbcd_z$area_mean)
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
-1.4532 -0.6666 -0.2949  0.0000  0.3632  5.2459 

{% endhighlight %}

돌렸는데 결측치 에러 발생. 확인 중
