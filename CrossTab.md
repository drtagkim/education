CrossTab
================
Taekyung Kim
2020 5 7

# 독립성 검정(independence test)

## 목적

두 범주형 변수가 서로 독립인지 검정한다.

### 독립이란

두 변수가 서로 관련이 없다.

성별(변수1)에 따라 선호하는 영화(변수2)가 다른가? 변수1과 변수2가 독립이면 성별에 따라 선호하는 영화가 다르지 않다.

### 독립성 가정의 가설

독립성 검정의 귀무가설은 서로 “독립”이다는 가정이다. 즉, 귀무가설(null hypothesis)을 기각할 수 있는 힘이 작으면
독립성 가정이 유지되고, 기각할 수 있는 힘이 커지면 독립성 가정이 깨진다.

### 범주형 변수 비교의 목적

Y의 결과에 대하여 X변수가 관계가 있는가를 질문한다.

## Titanic data 분석

1912년 4월 10일 뉴욕으로 가던 타이타닉 호가 침몰되었다. R의 학습데이터에 이 침몰선의 생존자 분포 데이터가 포함되어
있다. 이 중에서 객실 등급에 따른 생존 여부에 대한 독립성 검정을 실시해보자.

먼저 table 데이터를 불러와 Titanic.margin에 저장한다.

``` r
Titanic.margin <- margin.table(Titanic,margin=c(4,1))
Titanic.margin
```

    ##         Class
    ## Survived 1st 2nd 3rd Crew
    ##      No  122 167 528  673
    ##      Yes 203 118 178  212

경계합을 추가하자. addmarins()함수를 사용한다. 입력값은 table 객체이다.

``` r
addmargins(Titanic.margin)
```

    ##         Class
    ## Survived  1st  2nd  3rd Crew  Sum
    ##      No   122  167  528  673 1490
    ##      Yes  203  118  178  212  711
    ##      Sum  325  285  706  885 2201

총 데이터는 2201개이다. 가로로 합치나 세로로 합치나 같다. 1등석 승객은 325명이고 3등석 승객은 706명이다. 대다수는
승조원이다. 885명이나 된다. 생존자는 711명에 불과하고 1490명이 안타깝게 수장되었다.

비율로 살펴보자. 등급(class)에 따라 비율을 계산하기 위해 prop.table()함수를 사용했다. 1은 열 방향(위에서
아래로), 2는 행 방향(왼쪽에서 오른쪽으로) margin을 처리한다.

``` r
addmargins(
  prop.table(
    addmargins(Titanic.margin,2),2
  ),1
)
```

    ##         Class
    ## Survived       1st       2nd       3rd      Crew       Sum
    ##      No  0.3753846 0.5859649 0.7478754 0.7604520 0.6769650
    ##      Yes 0.6246154 0.4140351 0.2521246 0.2395480 0.3230350
    ##      Sum 1.0000000 1.0000000 1.0000000 1.0000000 1.0000000

1등석은 생존자가 62%, 2등석은 42%, 3등석은 25%로 class에 따라 생존자가 체계적으로 줄고 있다.

이제 독립성 검정을 수행하자. chisq.test()함수에 table 객체를 넣는다.

결과를 보니 p-value \< 2.2e-16으로 0.001보다 한참 작다. 따라서 독립성이 있다는 귀무가설은 지지되지
않는다(rejected). 결과는 독립성이 없어 class와 생존여부는 상당한 관련성이
있다(chi-square=190.4, degree of freedom=3).

``` r
chisq.test(Titanic.margin)
```

    ## 
    ##  Pearson's Chi-squared test
    ## 
    ## data:  Titanic.margin
    ## X-squared = 190.4, df = 3, p-value < 2.2e-16

보다 사용자가 판단하기 쉽게 cat()함수를 써서 결과를 보고하자.

``` r
result <- chisq.test(Titanic.margin)
cat("Is Class related to Survived?",
    "Judgment:",
    ifelse(result$p.value < 0.001,"Related","Not Related"),
    "\n")
```

    ## Is Class related to Survived? Judgment: Related
