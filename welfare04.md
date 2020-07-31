어떤 연령대의 월급이 가장 많을까?
================
김 다 영
July 31, 2020

## 4\. 연령대에 따른 월급 차이

나이를 연령대 별로 분류하여 비교해보자\!

### 분석 절차

### 연령대 변수 검토 및 전처리하기

#### 1\. 파생변수 만들기 - 연령대

  - young : 30세 미만
  - middle : 30\~59세
  - old : 60세 이상

<!-- end list -->

``` r
welfare <- welfare %>% 
  mutate(ageg = ifelse(age < 30, "young", 
                       ifelse(age <= 59, "middle", "old")))
table(welfare$ageg)
```

``` r
qplot(welfare$ageg)
```

![](welfare04_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

### 연령대에 따른 월급 차이 분석하기

#### 1\. 연령대별 월급 평균표 만들기

  - young : 163만원
  - middle : 281만원
  - old : 125만원

<!-- end list -->

``` r
ageg_income <- welfare %>%
  filter(!is.na(income)) %>% 
  group_by(ageg) %>% 
  summarise (mean_income = mean(income))

ageg_income
```

#### 2\. 그래프 만들기

``` r
ggplot(data = ageg_income, aes(x =ageg, y = mean_income)) + geom_col()
```

![](welfare04_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

#### 막대 정렬 : 초년, 중년, 노년 나이 순

``` r
ggplot(data = ageg_income, aes(x =ageg, y = mean_income)) +
  geom_col() +
  scale_x_discrete(limits = c("young","middle","old"))
```

![](welfare04_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->
