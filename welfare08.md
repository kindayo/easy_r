종교가 있는 사람들이 이혼을 덜 할까?
================
김 다 영
July 31, 2020

## 8\. 종교 유무에 따른 이혼율

종교가 있는 사람이 종교가 없는 사람보다 이혼을 덜 하는지 알아보자\!

### 분석 절차

### 종교 변수 검토 및 전처리하기

#### 1\. 변수 검토하기

``` r
class(welfare$religion)
table(welfare$religion)
```

#### 2\. 전처리

  - 1 : 종교있음
  - 2 : 종교없음
  - 9 : 모름/무응답

<!-- end list -->

``` r
welfare$religion <- ifelse(welfare$religion == 1, "yes", "no")
table(welfare$religion)
qplot(welfare$religion)
```

![](welfare08_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

### 혼인 상태 변수 검토 및 전처리하기

#### 1\. 변수 검토하기

``` r
class(welfare$marriage)
table(welfare$marriage)
```

#### 2\. 전처리

  - 0 : 18세 미만
  - 1 : 배우자있음
  - 2 : 사별
  - 3 : 이혼
  - 4 : 별거
  - 5 : 미혼
  - 6 : 기타

<!-- end list -->

``` r
# 이혼 여부 변수 만들기
welfare$group_marriage <- ifelse(welfare$marriage == 1, "marriage",
                          ifelse(welfare$marriage == 3, "divorce", NA))

table(welfare$group_marriage)
table(is.na(welfare$group_marriage))
qplot(welfare$group_marriage)
```

![](welfare08_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

결혼 상태인 사람은 8431명, 이혼한 사람은 712명, 어디에도 속하지 않는 사람 7521명이다.

### 종교 유무에 따른 이혼율 분석하기

#### 1\. 종교 유무에 따른 이혼율 표 만들기

``` r
religion_marriage <-welfare %>% 
  filter(!is.na(group_marriage)) %>% 
  group_by(religion, group_marriage) %>% 
  summarise(n = n()) %>% 
  mutate(tot_group = sum(n)) %>% 
  mutate(pct = round(n/tot_group*100, 1))

religion_marriage
```

    ## # A tibble: 4 x 5
    ## # Groups:   religion [2]
    ##   religion group_marriage     n tot_group   pct
    ##   <chr>    <chr>          <int>     <int> <dbl>
    ## 1 no       divorce          384      4602   8.3
    ## 2 no       marriage        4218      4602  91.7
    ## 3 yes      divorce          328      4541   7.2
    ## 4 yes      marriage        4213      4541  92.8

#### `count()` 활용

``` r
religion_marriage <-welfare %>% 
  filter(!is.na(group_marriage)) %>% 
  count(religion, group_marriage) %>%
  group_by(religion) %>% 
  mutate(pct = round(n/sum(n)*100, 1))
```

#### 2\. 이혼율 표 만들기

``` r
# 이혼 추출
divorce <- religion_marriage %>% 
  filter(group_marriage == "divorce") %>% 
  select(religion, pct)

divorce
```

    ## # A tibble: 2 x 2
    ## # Groups:   religion [2]
    ##   religion   pct
    ##   <chr>    <dbl>
    ## 1 no         8.3
    ## 2 yes        7.2

#### 3\. 그래프 만들기

``` r
ggplot(data = divorce, aes(x = religion, y = pct)) + geom_col()
```

![](welfare08_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

이혼율은 종교가 있는 사람들이(7.2%) 종교가 없는 사람보다(8.3%) 적다.

### 연령대 및 종교 유무에 따른 이혼율 분석하기

#### 1\. 연령대별 이혼율 표 만들기

``` r
ageg_marriage <-welfare %>% 
  filter(!is.na(group_marriage)) %>% 
  group_by(ageg, group_marriage) %>% 
  summarise(n = n()) %>% 
  mutate(tot_group = sum(n)) %>% 
  mutate(pct = round(n/tot_group*100, 1))

ageg_marriage
```

    ## # A tibble: 6 x 5
    ## # Groups:   ageg [3]
    ##   ageg   group_marriage     n tot_group   pct
    ##   <chr>  <chr>          <int>     <int> <dbl>
    ## 1 middle divorce          437      4918   8.9
    ## 2 middle marriage        4481      4918  91.1
    ## 3 old    divorce          273      4165   6.6
    ## 4 old    marriage        3892      4165  93.4
    ## 5 young  divorce            2        60   3.3
    ## 6 young  marriage          58        60  96.7

#### `count()` 활용

``` r
ageg_marriage <-welfare %>% 
  filter(!is.na(group_marriage)) %>% 
  count(ageg, group_marriage) %>%
  group_by(ageg) %>% 
  mutate(pct = round(n/sum(n)*100, 1))
```

#### 2\. 연령대별 이혼율 그래프 만들기

``` r
# 초년 제외, 이혼 추출
ageg_divorce <- ageg_marriage %>% 
  filter(ageg != "young" & group_marriage == "divorce") %>% 
  select(ageg, pct)

ageg_divorce 

# 그래프 만들기
ggplot(data = ageg_divorce, aes(x = ageg, y = pct)) + geom_col()
```

![](welfare08_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->

#### 3\. 연령대 및 종교 유무에 따른 이혼율 표 만들기

``` r
# 연령대, 종교 유무, 결혼 상태별 비율표 만들기
ageg_religion_marriage <- welfare %>% 
  filter(!is.na(group_marriage) & ageg != "young") %>% 
  group_by(ageg, religion, group_marriage) %>% 
  summarise(n = n()) %>% 
  mutate(tot_group = sum(n)) %>% 
  mutate(pct = round(n/tot_group*100, 1))

ageg_religion_marriage
```

    ## # A tibble: 8 x 6
    ## # Groups:   ageg, religion [4]
    ##   ageg   religion group_marriage     n tot_group   pct
    ##   <chr>  <chr>    <chr>          <int>     <int> <dbl>
    ## 1 middle no       divorce          260      2681   9.7
    ## 2 middle no       marriage        2421      2681  90.3
    ## 3 middle yes      divorce          177      2237   7.9
    ## 4 middle yes      marriage        2060      2237  92.1
    ## 5 old    no       divorce          123      1884   6.5
    ## 6 old    no       marriage        1761      1884  93.5
    ## 7 old    yes      divorce          150      2281   6.6
    ## 8 old    yes      marriage        2131      2281  93.4

#### `count()` 활용

``` r
ageg_religion_marriage <- welfare %>% 
  filter(!is.na(group_marriage) & ageg != "young") %>% 
  count(ageg, religion, group_marriage) %>% 
  group_by(ageg, religion) %>% 
  mutate(pct = round(n/sum(n)*100, 1))
```

#### 연령대 및 종교 유무별 이혼율 표 만들기

``` r
df_divorce <- ageg_religion_marriage %>% 
  filter(group_marriage == "divorce") %>% 
  select(ageg, religion, pct)
```

#### 4\. 연령대 및 종교 유무에 따른 이혼율 그래프 만들기

``` r
ggplot(data = df_divorce, aes(x =ageg, y = pct, fill = religion)) +
  geom_col(position = "dodge")
```

![](welfare08_files/figure-gfm/unnamed-chunk-16-1.png)<!-- -->

노년은 종교 유무에 따른 이혼율 차이가 0.1%로 작고, 오히려 종교가 있는 사람들의 이혼율이 더 높다. 반면, 중년은 종교가
없는 사람들의 이혼율이 1.8% 더 높다.
