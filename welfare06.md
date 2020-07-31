어떤 직업이 월급을 가장 많이 받을까?
================
김 다 영
July 31, 2020

## 6\. 직업별 월급 차이

### 분석 절차

### 직업 변수 검토 및 전처리하기

#### 1\. 변수 검토하기

code\_job 변수는 직업 코드를 의미한다. 이 데이터에서는 직업을 이름이 아닌 직업분류코드로 입력하였다. 따라서
직업분류코드를 이용해 직업 명칭 변수를 만들어보자.

``` r
class(welfare$code_job)
table(welfare$code_job)
```

#### 2\. 전처리

``` r
library(readxl) 
list_job <- read_excel("Koweps_Codebook.xlsx", col_names = T, sheet = 2)

head(list_job)
dim(list_job)
```

직업분류코드 목록이 코드와 직업명 두 변수로 구성되고, 직업이 149개로 분류된다.

``` r
welfare <- left_join(welfare, list_job, id = "code_job")

welfare %>% 
  filter(!is.na(code_job)) %>% 
  select(code_job, job) %>% 
  head(10)
```

### 직업별 월급 차이 분석하기

#### 1\. 직업별 월급 평균표 만들기

``` r
job_income <- welfare %>% 
  filter(!is.na(job) & !is.na(income)) %>% 
  group_by(job) %>% 
  summarise(mean_income = mean(income))

head(job_income, 10)
```

#### 2\. 상위 10개 추출

``` r
top10 <- job_income %>% 
  arrange(desc(mean_income)) %>% 
  head(10)
top10
```

#### 3\. 그래프 만들기

``` r
ggplot(data = top10, aes(x = reorder(job, mean_income),
                         y = mean_income)) +
  geom_col() + 
  coord_flip()
```

![](welfare06_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

‘금속 재료 공학 기술자 및 시험원’이 평균 월급으로 845만원을 받고, 그 다음으로는 ’의료진료 전문가’, ’의회의원 고위공무원
및 공공단체임원’의 월급이 많다.

#### 4\. 하위 10위 추출

``` r
bottom10 <- job_income %>% 
  arrange(mean_income) %>% 
  head(10)
```

#### 5\. 그래프 만들기

``` r
ggplot(data = bottom10, aes(x = reorder(job, -mean_income), 
                            y = mean_income)) +
  geom_col() + 
  coord_flip() +
  ylim(0, 850)
```

![](welfare06_files/figure-gfm/unnamed-chunk-9-1.png)<!-- --> ‘가사 및 육아
도우미’의 월급이 평균 80만원으로 가장 적고, 그 다음으로는 ’임업관련 종사자’, ’기타 서비스 관련 단순 종사원’의
월급이 적었다.
