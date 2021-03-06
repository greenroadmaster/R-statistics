# Unpaired Two samples T-test

## 데이터 만들기
남자 9명, 여자 9명에 대한 몸무게를 담은 데이터셋을 만든다.


```r
women_weight <- c(38.9, 61.2, 73.3, 21.8, 63.4, 64.6, 48.4, 48.8, 48.5)
men_weight <- c(67.8, 60, 63.4, 76, 89.4, 73.3, 67.3, 61.3, 62.4)
    
(my.data <- data.frame(group = rep(c("Woman", "Man"), each = 9), weight = c(women_weight,  men_weight) ) )
```

```
##    group weight
## 1  Woman   38.9
## 2  Woman   61.2
## 3  Woman   73.3
## 4  Woman   21.8
## 5  Woman   63.4
## 6  Woman   64.6
## 7  Woman   48.4
## 8  Woman   48.8
## 9  Woman   48.5
## 10   Man   67.8
## 11   Man   60.0
## 12   Man   63.4
## 13   Man   76.0
## 14   Man   89.4
## 15   Man   73.3
## 16   Man   67.3
## 17   Man   61.3
## 18   Man   62.4
```


## Unpaired Two samples T-test

두 모집단에 대한 평균의 차이가 있는지 검정하는 방법이다.

예시)

귀무가설: 강남구에 사는 사람들의 평균 소득과 서초구 사람들의 평균 소득에 차이가 없다.

대립가설: 강남구에 사는 사람들의 평균 소득과 서초구 사람들의 평균 소득에 차이가 있다.


### 모수적 방법
모수적인 방법을 사용하기 위해서는 두 가지 전제 조건이 필요하다.

즉, 모집단이 정규성과 등분산성을 따르는지 확인해야 한다.
정규성 검정

### 여성 몸무게 정규성


```r
shapiro.test(my.data$weight[my.data$group=="Woman"])
```

```
## 
## 	Shapiro-Wilk normality test
## 
## data:  my.data$weight[my.data$group == "Woman"]
## W = 0.94266, p-value = 0.6101
```

### 남성 몸무게 정규성


```r
shapiro.test(my.data$weight[my.data$group=="Man"])
```

```
## 
## 	Shapiro-Wilk normality test
## 
## data:  my.data$weight[my.data$group == "Man"]
## W = 0.86425, p-value = 0.1066
```

둘 다 p-value값이 유의수준(0.05)보다 크기 때문에 귀무가설 채택.

따라서 정규성을 따른다.



### 등분산성 검정


```r
var.test(weight~group, data = my.data)
```

```
## 
## 	F test to compare two variances
## 
## data:  weight by group
## F = 0.36134, num df = 8, denom df = 8, p-value = 0.1714
## alternative hypothesis: true ratio of variances is not equal to 1
## 95 percent confidence interval:
##  0.08150656 1.60191315
## sample estimates:
## ratio of variances 
##          0.3613398
```


p-value값이 유의수준(0.05)보다 크기 때문에 귀무가설 채택.

따라서 등분산성을 따른다.


두 전제조건 모두 만족한다.



## t.test

```r
( res <- t.test(weight ~ group, data = my.data, var.equal = TRUE) )
```

```
## 
## 	Two Sample t-test
## 
## data:  weight by group
## t = 2.7842, df = 16, p-value = 0.01327
## alternative hypothesis: true difference in means is not equal to 0
## 95 percent confidence interval:
##   4.029759 29.748019
## sample estimates:
##   mean in group Man mean in group Woman 
##            68.98889            52.10000
```

p-value값이 유의수준(0.05)보다 작기 때문에 귀무가설 기각. 대립가설 채택.

따라서, 남자 여자 몸무게의 차이가 있다.


추가 옵션

table(my.data$group)

t.test(weight ~ group, data = my.data, var.equal = TRUE, alternative = "less")

t.test(weight ~ group, data = my.data, var.equal = TRUE, alternative = "greater")

alternative를 사용하여 대립가설을 조정할 수 있다.

여기서는 table함수를 통해 어떤 그룹이 먼저 들어갔는지 확인한다. 남자가 먼저다. 가설에서 순서가 정해지기 때문이다.

less인 경우, 남자 평균 몸무게가 여자의 평균 몸무게보다 작다.(대립가설) 귀무가설 채택이기 때문에 대립가설이 거짓이다.

greater인 경우, 남자 평균 몸무게가 여자의 평균 몸무게보다 크다.(대립가설) 대립가설 채택이기 때문에 대립가설이 참이다.


## 평균 추정 값과 신뢰구간


```r
res$estimate
```

```
##   mean in group Man mean in group Woman 
##            68.98889            52.10000
```


```r
res$conf.int
```

```
## [1]  4.029759 29.748019
## attr(,"conf.level")
## [1] 0.95
```

남자의 평균 몸무게는 68.98889kg, 여자 평균 몸무게는 52.1kg이다.

남자 여자 평균 몸무게 차이 값이 4.029759~29.748019 사이가 신뢰구간 0.95이내 이다.



## 비모수적 방법
소표본이고 정규성을 따르지 않을 때 사용하는 방법이다.

my.data는 전제조건을 통과하여 모수적 방법을 사용해야 하지만 연습으로 그냥 한다.

### wilcox.test(윌콕슨 검정)


```r
wilcox.test(weight ~ group, data = my.data, exact = FALSE)
```

```
## 
## 	Wilcoxon rank sum test with continuity correction
## 
## data:  weight by group
## W = 66, p-value = 0.02712
## alternative hypothesis: true location shift is not equal to 0
```

p-value값이 유의수준(0.05)보다 작기 때문에 귀무가설 기각. 대립가설 채택.

따라서, 남자 여자 몸무게의 차이가 있다.

## 추가 옵션


```r
table(my.data$group)
```

```
## 
##   Man Woman 
##     9     9
```

```r
wilcox.test(weight ~ group, data = my.data, exact = FALSE, alternative = "less")
```

```
## 
## 	Wilcoxon rank sum test with continuity correction
## 
## data:  weight by group
## W = 66, p-value = 0.9892
## alternative hypothesis: true location shift is less than 0
```

```r
wilcox.test(weight ~ group, data = my.data, exact = FALSE, alternative = "greater")
```

```
## 
## 	Wilcoxon rank sum test with continuity correction
## 
## data:  weight by group
## W = 66, p-value = 0.01356
## alternative hypothesis: true location shift is greater than 0
```


모수적 방법과 똑같이 해석하면 된다.


