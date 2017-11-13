# Typical Filipino

# Introduction
Filipinos are cheerful, resourceful and practical people. It is in our nature 
to make the best out a of any situation, no matter how hard it is. We get this 
from our strong family values and religious beliefs. However, our ideals are 
not always represented in  our actions especially when it comes to our 
financial priorities. I would hear Filipinos complaining about low salaries but
are spending so much on things they do not need. 

## Objectives
In this article, I would like to find out if the spending habits and financial 
priorities change as their income increases of a typical Filipino family.

## Dataset Source
This dataset is from The Philippine Statistics Authority who conducts the 
Family Income and Expediture Survey (FEIS) every 3 years nationwide. This is 
from the 2015 most recent survey. The raw data was cleaned by Francis Paul 
Flores from his [kaggle.com/grosvenpaul](https://www.kaggle.com/grosvenpaul/family-income-and-expenditure/downloads/Family%20Income%20and%20Expenditure.csv).

# Getting a Feel of the Data
The Dataset contains 41,544 observations of Filipino Households from every 
Region of the country. It is comprised of 60 variables describing each family 
on their income, family description and expenditure.


```r
library(readr)

fies <- read_csv("fies.csv")

# Rename Lables for easier reference.
names(fies) <- c("income", "region", "expense", "source", 
                 "agri_house", "exp_bread", "exp_rice", "exp_meat", 
                 "exp_seafood", "exp_fruit", "exp_veg", "exp_resto_hotel", 
                 "exp_alcoh", "exp_taba", "exp_clothe", "exp_house_water", 
                 "exp_rent", "exp_med", "exp_trans", "exp_comms", 
                 "exp_edu", "exp_misc", "exp_spec", "exp_farm",
                 "inc_entrep", "head_gender", "head_age", "head_stat", 
                 "head_educ", "head_job_bus", "head_occup", "head_workclass", 
                 "family_t", "family_n", "baby_n", "kid_n", 
                 "employed_n", "house_t", "roof_t", "wall_t", 
                 "house_area", "house_age", "bed_n", "house_tenure",
                 "toilet", "electric", "water_t", "tv_n", 
                 "DVD_n", "sterio_n", "ref_n", "wash_n", 
                 "aircon_n", "car_n", "tel_n", "cell_n",
                 "pc_n", "stove_n", "mboat_n", "mbike_n")
```

There are 60 variables in this dataset and are not code-friendly. First thing 
to do is rename each column key. A detailed desciption of each column is 
documented on a separate txt file for your reference.


```r
names(fies)
```

```
##  [1] "income"          "region"          "expense"        
##  [4] "source"          "agri_house"      "exp_bread"      
##  [7] "exp_rice"        "exp_meat"        "exp_seafood"    
## [10] "exp_fruit"       "exp_veg"         "exp_resto_hotel"
## [13] "exp_alcoh"       "exp_taba"        "exp_clothe"     
## [16] "exp_house_water" "exp_rent"        "exp_med"        
## [19] "exp_trans"       "exp_comms"       "exp_edu"        
## [22] "exp_misc"        "exp_spec"        "exp_farm"       
## [25] "inc_entrep"      "head_gender"     "head_age"       
## [28] "head_stat"       "head_educ"       "head_job_bus"   
## [31] "head_occup"      "head_workclass"  "family_t"       
## [34] "family_n"        "baby_n"          "kid_n"          
## [37] "employed_n"      "house_t"         "roof_t"         
## [40] "wall_t"          "house_area"      "house_age"      
## [43] "bed_n"           "house_tenure"    "toilet"         
## [46] "electric"        "water_t"         "tv_n"           
## [49] "DVD_n"           "sterio_n"        "ref_n"          
## [52] "wash_n"          "aircon_n"        "car_n"          
## [55] "tel_n"           "cell_n"          "pc_n"           
## [58] "stove_n"         "mboat_n"         "mbike_n"
```

## Income Distribution

![](eda-filipino-family_files/figure-html/Income_distribution-1.png)<!-- -->

It's no surprise that income per household is skewed to the left, where there is
more poor families and few rich people with dramatic outlying incomes.


```r
summary(fies$income)
```

```
##     Min.  1st Qu.   Median     Mean  3rd Qu.     Max. 
##    11285   104895   164080   247556   291138 11815988
```

Doing a quick summary of the income, we see that the average income of each
household is 247,556 Php annually. But this is skewed because of the maximum
income of 11 mill Php of the super rich.

## Averaging Everything is just Lazy

![](eda-filipino-family_files/figure-html/Income_log10-1.png)<!-- -->

We transform the histogram to adjust the power of magnitude of each income and
as a result we can see a beautiful normal distribution. To understand why we use
log10, imagine that we made the x axis points closer together so that we can see
see the distribution more clearly.

The problem with relying on averages is that it is heavily influenced by 
outliers. Php 247,556 in the graph is far from the center, which makes it a poor 
representative of the distribution.

The Rich are really rich at 11 mill Php  and the Poor are really poor with only 
11,285 Php of annual income.

In addition, this average is of the whole population. We can't do that because,
for some people, 247k is small while other it is very big. So, we facet the 
graph according to different groups or work classes.

## Grouped by Working Class according to Survey

![](eda-filipino-family_files/figure-html/GroupBy_workclass-1.png)<!-- -->

In this graph, we use the work class determined by the survey. But this is not 
really telling us anything especially that the categories are vague and are not 
familiar. The needs a little bit of cleaning.

## We make our own Groups


```r
library(stringr)
library(dplyr)

pro_class <- paste(c("managers", "medical", "dentist", "lawyer",
                     "architect", "supervisor", "trade", "education",
                     "chemist", "professionals", "scientist",
                     "justice", "Mechanical engineers", "director",
                     "accountant", "Chemical engineers", 
                     "Electrical Engineers", "Industrial engineers",
                     "designer", "singers", "television", "dancer",
                     "fashion", "marketing", "Civil engineers", "air",
                     "Computer programmers", "nurse", "Librarians",
                     "science", "therapist", "opticians", "Veter",
                     "pilots", "estate agents", "plant operators", 
                     "legislative"),
                   collapse = "|")

farm_class <- paste(c("farm", "fish", "growers", "gather", "duck",
                      "cultivat", "plant", "hunter", "village",
                      "practitioners", "healers", "diary", "animal"), 
                    collapse = "|")

work_class <- paste(c("workers", "carpenters", "welder", "helper", "clean",
                      "launder", "labor", "driver", "freight", "mechanics",
                      "mason", "porter", "waiter", "cook", "foremen",
                      "caretaker", "lineman", "varnish", "conductor",
                      "salesperson", "bookkeeper", "inspector", "undertaker", 
                      "loggers", "wood", "roofers", "cutter", "electricians",
                      "assembler", "builder", "metal",  "tanners", "garbage",
                      "repair", "prepare", "rigger", "vendors", "valuers",
                      "setters", "guides", "tasters", "potters", "preservers",
                      "textile", "fitters", "valets", "blasters", 
                      "humanitarian", "staff officers"), 
                    collapse = "|")

off_class <- paste(c("pawn", "buy", "baker", "maker", "tailor", "assistant",
                     "clerk", "engineering technicians", "operators",
                     "artists", "managers/managing proprietors", "insurance",
                     "educ", "principals", "secretaries", "general", 
                     "pharmacists", "commercial", "communications engineers",
                     "draftsmen", "instructors", "travel consultants",
                     "enlisted personnel"), 
                    collapse = "|")

fies$head_class <- with(fies,
                        ifelse(str_detect(head_workclass, "government"), 
                               "gov",
                        ifelse(grepl(work_class, head_occup, ignore.case = T),
                               "work",
                        ifelse(grepl(off_class, head_occup, ignore.case = T),
                               "off",
                        ifelse(grepl(farm_class, head_occup, ignore.case = T),
                               "farm",
                        ifelse(grepl(pro_class, head_occup, ignore.case = T),
                               "pro",
                               NA))))))
```

## We got to Catch them All


```r
fies.occup <- fies %>%
  filter(is.na(fies$head_class)) %>%
  group_by(head_occup) %>%
  summarise(n= n(),
            income_mean = mean(income),
            income_median = median(income)) %>%
  arrange(desc(n))

fies.occup
```

```
## # A tibble: 1 x 4
##   head_occup     n income_mean income_median
##       <fctr> <int>       <dbl>         <dbl>
## 1       <NA>  7536    285650.6        201883
```

I decided to evaluate each occupation of the head of the family and manually 
group each household according to the following working class:

- *Professionals.* Jobs that usually requires higher education.
- *Office* Usually found in shops and offices of goods.
- *Government.* Jobs in government.
- *Workers.* Jobs that usually pays low and does not require a degree.
- *Farmers.* Jobs that pertains to rural work. Agriculture, Fishing, etc.

I am basing the categories based on my own classifications of how I see the
working class and their overall mean income. I am ignoring the households
that did not specify the occupation of the head of the family.


```r
tapply(fies$income, fies$head_class, summary)
```

```
## $farm
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##   12911   83998  119494  160549  178824 4810822 
## 
## $gov
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##   24886  174310  323632  426544  558545 3805717 
## 
## $off
##     Min.  1st Qu.   Median     Mean  3rd Qu.     Max. 
##    21136   134373   216450   317292   369152 11815988 
## 
## $pro
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##   25133  321260  530172  668838  843932 4208400 
## 
## $work
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##   11285  104172  151013  193944  232346 7082152
```

## A Millioniare Waiter

I review each working class and see what I can further refine. My goal is to
segment as much as I can to emphasize the division among the working classes
and at the same time being fair with my evaluation of the different jobs.


```r
fies.workers <- fies %>%
  filter(fies$head_class == "work") %>%
  select(head_occup, income, inc_entrep) %>%
  arrange(desc(income), head_occup)

fies.workers
```

```
## # A tibble: 14,459 x 3
##                                                                     head_occup
##                                                                         <fctr>
##  1                                                     Street ambulant vendors
##  2                                           Market and sidewalk stall vendors
##  3                                          Waiters, waitresses and bartenders
##  4 General managers/managing proprietors in personal care, cleaning and relati
##  5                                         Shop salespersons and demonstrators
##  6                                           Market and sidewalk stall vendors
##  7                                           Market and sidewalk stall vendors
##  8                                                         Building caretakers
##  9                                  Production supervisors and general foremen
## 10                                         Shop salespersons and demonstrators
## # ... with 14,449 more rows, and 2 more variables: income <int>,
## #   inc_entrep <int>
```


```r
subset(fies.workers, income == "5652261")
```

```
## # A tibble: 1 x 3
##                           head_occup  income inc_entrep
##                               <fctr>   <int>      <int>
## 1 Waiters, waitresses and bartenders 5652261    5107451
```

I then checked the worker class and noticed some interesting outliers in the
group. The one that stood out was a Waiter job who is earning Php5,652,261
annually. This does not seem right to me so I took a deeper look and found out
that income is the sum of their income from their jobs and their business.

Subtracting the income from business from the total income, the Waiter was 
actually earning Php544,810 annually from his job and Php5,107,451 from his 
business. For Analysis, we would like to see the income from their employment.

## We just want the Income from their Jobs


```r
fies$inc_work <- with(fies, income - inc_entrep)

# Check if there are no negative income from work.
head(table(sort(fies$inc_work)))
```

```
## 
##   0 137 150 540 550 667 
##  22   1   1   1   1   1
```


```r
tapply(fies$inc_work, fies$head_class, summary)
```

```
## $farm
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##    1212   26991   54926   89381  103539 3034500 
## 
## $gov
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##   18370  147436  294358  387665  518604 3210933 
## 
## $off
##     Min.  1st Qu.   Median     Mean  3rd Qu.     Max. 
##        0    52901   120242   194908   246241 11639365 
## 
## $pro
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##    4339  295431  490000  602606  761157 3310612 
## 
## $work
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##       0   85926  130510  166069  203122 1796730
```

## Reveiwing the Professionals


```r
fies.pro <- fies %>%
  filter(fies$head_class == "pro") %>%
  group_by(head_occup) %>%
  summarise(income_mean = mean(inc_work),
            income_median = median(inc_work),
            n= n()) %>%
  arrange(desc(income_mean))

fies.pro
```

```
## # A tibble: 74 x 4
##                                                                     head_occup
##                                                                         <fctr>
##  1                                          Agronomists and related scientists
##  2                            Aircraft pilots, navigators and flight engineers
##  3                                                                     Lawyers
##  4                              Directors and chief executives of corporations
##  5                                                          Chemical engineers
##  6                                                                    Justices
##  7                                                             Medical doctors
##  8 Production and operations managers in transport, storage and communications
##  9                                      Maritime transport service supervisors
## 10                                                        Mechanical engineers
## # ... with 64 more rows, and 3 more variables: income_mean <dbl>,
## #   income_median <dbl>, n <int>
```

## Reviewing the Office Employees


```r
fies.office <- fies %>%
  filter(fies$head_class == "off") %>%
  group_by(head_occup) %>%
  summarise(income_mean = mean(inc_work),
            income_median = median(inc_work),
            n= n()) %>%
  arrange(desc(income_mean))

fies.office
```

```
## # A tibble: 95 x 4
##                                                         head_occup
##                                                             <fctr>
##  1                                Power production plant operators
##  2        Incinerator, water treatment and related plant operators
##  3           Glass and ceramics kiln and related machine operators
##  4                                               School principals
##  5                  Technical and commercial sales representatives
##  6                                       Insurance representatives
##  7 College, university and higher education teaching professionals
##  8                                       Pharmaceutical assistants
##  9                                                          Buyers
## 10                        Electronics and communications engineers
## # ... with 85 more rows, and 3 more variables: income_mean <dbl>,
## #   income_median <dbl>, n <int>
```

## Reviewing the Farmers


```r
fies.farm <- fies %>%
  filter(fies$head_class == "farm") %>%
  group_by(head_occup) %>%
  summarise(income_mean = mean(inc_work),
            income_median = median(inc_work),
            n= n()) %>%
  arrange(desc(income_mean))

fies.farm
```

```
## # A tibble: 35 x 4
##                                                                     head_occup
##                                                                         <fctr>
##  1                                                        Other animal raisers
##  2 Production and operations managers in agriculture, hunting, forestry and fi
##  3                                                            Tree nut farmers
##  4                                                        Hunters and trappers
##  5                                                       Other poultry farmers
##  6                                    Fish-farm cultivators (excluding prawns)
##  7                                                         Hog raising farmers
##  8                                                             Chicken farmers
##  9                                                    Ornamental plant growers
## 10                                                     Other livestock farmers
## # ... with 25 more rows, and 3 more variables: income_mean <dbl>,
## #   income_median <dbl>, n <int>
```

## See what happens when you just Average


```r
ggplot(subset(fies, income != 0 & 
                !is.na(head_class)), 
       aes(income)) +
  geom_histogram(col=I("white")) +
  scale_x_continuous(trans = log10_trans(), 
                     breaks = c(10000, 25000, 70000,
                                247556, 
                                500000, 2000000),
                     limits = c(2000, 2500000)) +
  facet_wrap( ~ head_class, ncol = 2)  +
  geom_vline(data = subset(fies, !is.na(head_class)),
             aes(xintercept = mean(fies$income)),
             color = I("red"))
```

![](eda-filipino-family_files/figure-html/incomeDistributionbyCustomClass-1.png)<!-- -->

We go back to the overall income distribution but this time using our custom
work class. We add the average income Php247k and we see that some groups
are below the average line and other groups are above the average line.

## Income from Salaries


```r
# We remove the households that does not specify occupation.
ggplot(subset(fies, inc_work != 0 & 
                !is.na(head_class)), 
       aes(inc_work)) +
  geom_histogram(col=I("white"), fill = I("#00CCFF")) +
  scale_x_continuous(trans = log10_trans(), 
                     breaks = c(10000, 25000, 70000,
                                247556, 
                                500000, 2000000),
                     limits = c(2000, 2500000)) +
  scale_y_continuous(breaks = c(0, 500, 1000, 1500,
                               2000, 2500),
                     limits = c(0, 2500)) +
  facet_wrap( ~ head_class, ncol = 2)  +
  geom_vline(data = subset(fies, !is.na(head_class)),
             aes(xintercept = mean(fies$income)),
             color = I("red"))
```

![](eda-filipino-family_files/figure-html/incomeFromSalaries-1.png)<!-- -->

If people relied from their salaries, the picture is the same. Farmers, Office
and Workers are below the average line while Gov Employees and Professionals
are above the average line.

## Income from Business


```r
ggplot(subset(fies, inc_entrep != 0 & 
                !is.na(head_class)), 
       aes(inc_entrep)) +
  geom_histogram(col=I("white"), fill = I("#82AF4C")) +
  scale_x_continuous(trans = log10_trans(), 
                     breaks = c(10000, 25000, 70000,
                                247556, 
                                500000, 2000000),
                     limits = c(2000, 2500000)) +
  scale_y_continuous(breaks = c(0, 500, 1000, 1500,
                               2000, 2500),
                     limits = c(0, 2500)) +
  facet_wrap( ~ head_class, ncol = 2)  +
  geom_vline(data = subset(fies, !is.na(head_class)),
             aes(xintercept = mean(fies$income)),
             color = I("red"))
```

![](eda-filipino-family_files/figure-html/expenseByCustomClass-1.png)<!-- -->

If people relied solely on income from their business, all of them are below
the Average line of the overall income population. This goes to show that
majority of Filipinos have higher income from Salaries than from business.

## View from BoxPlots


```r
ggplot(subset(fies, !is.na(head_class)), aes(head_class, inc_work)) +
  geom_boxplot() +
  coord_cartesian(ylim = c(0, quantile(fies$inc_work, .98)))
```

![](eda-filipino-family_files/figure-html/boxplotsIncomeSalaries-1.png)<!-- -->


```r
ggplot(subset(fies, !is.na(head_class)), aes(head_class, inc_entrep)) +
  geom_boxplot() +
  coord_cartesian(ylim = c(0, quantile(fies$inc_entrep, .92)))
```

![](eda-filipino-family_files/figure-html/boxplotsIncomeBusiness-1.png)<!-- -->

Both graphs shows that professionals are heavily dependent on the salaries from
their professions as their main source of income. While most groups would treat
treat their business as a secondary source of income, office employees and
farmers would seem to have more profitable business than the rest of the groups.

But this is not clear because we are mixing households that rely on salaries
and business alone with people who are soley relient on either their salaries 
or their business.

## Comparing Source of Income in One Graph


```r
library(tidyr)

fies$id <- seq_len(nrow(fies))

fies.n <- fies %>%
  filter(!is.na(fies$head_class)) %>%
  select(id, head_class, income, inc_entrep, inc_work, expense) %>%
  gather(inc_type, inc, c(inc_work, inc_entrep)) %>%
  gather(cashflow, cash, c(income, expense)) %>%
  arrange(id)

fies.n
```

```
## # A tibble: 136,032 x 6
##       id head_class   inc_type    inc cashflow   cash
##    <int>      <chr>      <chr>  <int>    <chr>  <int>
##  1     1        gov   inc_work 435962   income 480332
##  2     1        gov inc_entrep  44370   income 480332
##  3     1        gov   inc_work 435962  expense 117848
##  4     1        gov inc_entrep  44370  expense 117848
##  5     2       work   inc_work 198235   income 198235
##  6     2       work inc_entrep      0   income 198235
##  7     2       work   inc_work 198235  expense  67766
##  8     2       work inc_entrep      0  expense  67766
##  9     3       work   inc_work  82785   income  82785
## 10     3       work inc_entrep      0   income  82785
## # ... with 136,022 more rows
```

We would like to compare work income from business income to find out the
relationship. We transform the databale from wide format to long format so that
we can graph the data in a frequency polygon.


```r
ggplot(fies.n, aes(inc, color = inc_type)) +
  geom_freqpoly(alpha =.5) +
  scale_x_continuous(trans = log10_trans(), 
                     breaks = c(10000, 25000, 70000,
                                247556, 
                                500000, 2000000),
                     limits = c(2000, 2500000)) +
  facet_wrap(~ head_class, ncol = 2)
```

![](eda-filipino-family_files/figure-html/unnamed-chunk-1-1.png)<!-- -->

Placing the graphs on a frequency polygon, we see that farmers have both their
salaries from business income at almost similar distribution. Office Employees
have income from Salaries higher than their business income. While workers
would have a distribution that have a higher income from Salaries and income
from business more spread out. Government employees and Professionals does not
have a clear relationship between income from salaries business.

## Let's Look at Ratios

At this stage, I am just graphing everythin up and their is still this
underlying question that how much of this distribution have only salaries, 
only business or both as their source of income. Let's look at the ratios and
see.


```r
fies.w <- fies %>%
  filter(!is.na(head_class)) %>%
  filter(inc_entrep != 0 & inc_work != 0) %>%
  select(id, head_age, head_class, income, inc_entrep, inc_work, expense) %>%
  mutate(inc_ratio = inc_work / inc_entrep,
         incExp_ratio = income / expense) %>%
  arrange(desc(inc_ratio))

summary(fies.w$inc_ratio)
```

```
##      Min.   1st Qu.    Median      Mean   3rd Qu.      Max. 
##     0.001     0.656     1.948    11.601     5.645 11327.933
```

