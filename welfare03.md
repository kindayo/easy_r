몇 살 때 월급을 가장 많이 받을까?
================
김다영
July 30, 2020

## 3\. 나이와 월급의 관계

비정규직이 많아지면서 안정된 직장에 취업하는 것도 어려워졌지만, 젊은 세대를 더욱 힘들게 하는 것은 세대 간 소득 격차가 심해서
사회가 불평등하게 느껴진다는 점이다. 나이에 따라 월급이 어떻게 다른지 데이터 분석을 통해 알아보자.

### 분석 절차

#### 1\. 변수 검토하기

``` r
class(welfare$birth)
```

    ## [1] "numeric"

``` r
summary(welfare$birth)
```

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##    1907    1946    1966    1968    1988    2014

``` r
qplot(welfare$birth)
```

![](welfare03_files/figure-gfm/unnamed-chunk-2-1.png)<!-- --> \#\#\#\#
2. 전처리

태어난 연도는 1900\~2014사이의 값을 지니고, 모름/무응답은 9999로 코딩된다.

``` r
#이상치 확인
summary(welfare$birth)

#결측치 확인
table(is.na(welfare$birth))

#이상치 결측 처리
welfare$birth <-ifelse(welfare$birth == 9999, NA, welfare$birth)
table(is.na(welfare$birth))
```

#### 3\. 파생변수 만들기 - 나이

``` r
welfare$age <- 2015 - welfare$birth + 1
summary(welfare$age)
```

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##    2.00   28.00   50.00   48.43   70.00  109.00

``` r
qplot(welfare$age)
```

![](welfare03_files/figure-gfm/unnamed-chunk-4-1.png)<!-- --> \#\#\# 나이와
월급의 관계 분석하기

#### 1\. 나이에 따른 월급 평균표 만들기

``` r
age_income <- welfare %>% 
  filter(!is.na(income)) %>% 
  group_by(age) %>% 
  summarise(mean_income = mean(income))
```

    ## `summarise()` ungrouping output (override with `.groups` argument)

``` r
head(age_income)
```

    ## # A tibble: 6 x 2
    ##     age mean_income
    ##   <dbl>       <dbl>
    ## 1    20        121.
    ## 2    21        106.
    ## 3    22        130.
    ## 4    23        142.
    ## 5    24        134.
    ## 6    25        145.

#### 2\. 그래프 만들기

``` r
ggplot(data = age_income, aes(x = age, y = mean_income)) + geom_line()
```

![](welfare03_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->
