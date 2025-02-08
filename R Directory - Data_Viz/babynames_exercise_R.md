# Babynames Exercise (R Version)
Thomas Brambor

- [Introduction](#introduction)
- [Analysis](#analysis)
  - [Data Loading and Initial
    Exploration](#data-loading-and-initial-exploration)
  - [Single Name Analysis](#single-name-analysis)
  - [Top 10 Names Analysis](#top-10-names-analysis)
  - [Recent Trends (2017)](#recent-trends-2017)
- [Exercises](#exercises)

# Introduction

For each year from 1880 onwards, the data contains the number of
children of each sex given each name. All names with more than 5 uses
are given. The R package contains data up to 2017. (Source:
<http://www.ssa.gov/oact/babynames/limits.html>)

<img src="baby-names-wordcloud.jpg" width="400" />

# Analysis

## Data Loading and Initial Exploration

``` r
library(babynames)
library(tidyverse)
library(ggplot2)

str(babynames)
```

    tibble [1,924,665 × 5] (S3: tbl_df/tbl/data.frame)
     $ year: num [1:1924665] 1880 1880 1880 1880 1880 1880 1880 1880 1880 1880 ...
     $ sex : chr [1:1924665] "F" "F" "F" "F" ...
     $ name: chr [1:1924665] "Mary" "Anna" "Emma" "Elizabeth" ...
     $ n   : int [1:1924665] 7065 2604 2003 1939 1746 1578 1472 1414 1320 1288 ...
     $ prop: num [1:1924665] 0.0724 0.0267 0.0205 0.0199 0.0179 ...

``` r
head(babynames)
```

    # A tibble: 6 × 5
       year sex   name          n   prop
      <dbl> <chr> <chr>     <int>  <dbl>
    1  1880 F     Mary       7065 0.0724
    2  1880 F     Anna       2604 0.0267
    3  1880 F     Emma       2003 0.0205
    4  1880 F     Elizabeth  1939 0.0199
    5  1880 F     Minnie     1746 0.0179
    6  1880 F     Margaret   1578 0.0162

``` r
# Basic statistics
length(unique(babynames$name))  # Number of unique names
```

    [1] 97310

``` r
sum(babynames$n)/10^6          # Total number of babies (millions)
```

    [1] 348.1205

## Single Name Analysis

Looking at the popularity of the name “James” over time:

``` r
ggplot(babynames, aes(year, n)) + geom_line(data = filter(babynames, name=="James"), aes(color=sex))
```

![](babynames_exercise_R_files/figure-commonmark/unnamed-chunk-3-1.png)

## Top 10 Names Analysis

``` r
# Calculate top 10 names
top10 <- babynames %>%
  group_by(sex, name) %>%
  summarize(total = sum(n)) %>%
  arrange(desc(total)) %>%
  group_by(sex) %>%
  mutate(rank=row_number()) %>%
  filter(rank<=10) %>%
  arrange(sex, rank)
```

    `summarise()` has grouped output by 'sex'. You can override using the `.groups`
    argument.

``` r
# Show results by sex
top10 %>% filter(sex=="F")
```

    # A tibble: 10 × 4
    # Groups:   sex [1]
       sex   name        total  rank
       <chr> <chr>       <int> <int>
     1 F     Mary      4123200     1
     2 F     Elizabeth 1629679     2
     3 F     Patricia  1571692     3
     4 F     Jennifer  1466281     4
     5 F     Linda     1452249     5
     6 F     Barbara   1434060     6
     7 F     Margaret  1246649     7
     8 F     Susan     1121440     8
     9 F     Dorothy   1107096     9
    10 F     Sarah     1073895    10

``` r
top10 %>% filter(sex=="M")
```

    # A tibble: 10 × 4
    # Groups:   sex [1]
       sex   name      total  rank
       <chr> <chr>     <int> <int>
     1 M     James   5150472     1
     2 M     John    5115466     2
     3 M     Robert  4814815     3
     4 M     Michael 4350824     4
     5 M     William 4102604     5
     6 M     David   3611329     6
     7 M     Joseph  2603445     7
     8 M     Richard 2563082     8
     9 M     Charles 2386048     9
    10 M     Thomas  2304948    10

``` r
# Plot female trends
babynames %>%
  filter(sex=="F", name %in% (top10 %>% filter(sex=="F") %>% pull(name))) %>%
  ggplot(aes(year, n, color=name)) +
  geom_line() +
  ggtitle("Top 10 Female Names Over Time")
```

![](babynames_exercise_R_files/figure-commonmark/unnamed-chunk-4-1.png)

``` r
# Plot male trends
babynames %>%
  filter(sex=="M", name %in% (top10 %>% filter(sex=="M") %>% pull(name))) %>%
  ggplot(aes(year, n, color=name)) +
  geom_line() +
  ggtitle("Top 10 Male Names Over Time")
```

![](babynames_exercise_R_files/figure-commonmark/unnamed-chunk-4-2.png)

## Recent Trends (2017)

``` r
babynames %>% 
  filter(year == 2017) %>% 
  arrange(desc(prop)) %>% 
  head(10) %>% 
  ggplot(aes(reorder(name, prop), prop, fill = sex)) + 
  geom_col() +
  ylab("Proportion of Babies in 2017") + 
  xlab("") +
  coord_flip() + 
  theme_minimal()
```

![](babynames_exercise_R_files/figure-commonmark/unnamed-chunk-5-1.png)

# Exercises

1.  Plot the most common names in 2017 over the entire period.
2.  Explore which names are most often used as unisex names. For which
    names has the popularity over time changed a lot?
3.  Identify one particular pattern in the data. For example:
    - religious names are less/more common over time
    - the top 5 names capture a different portion of all names at
      different points in time
    - there are more “unique” names now
    - certain names became popular after historical events / figures
      etc.
    - some old names are making a revival after a certain time period
      (say a generation?)

Then try to capture this one pattern in a graphical display that
highlights this one point.
