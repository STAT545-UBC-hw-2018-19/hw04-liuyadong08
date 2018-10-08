STAT545 Homework 04 Tidy data and joins
================

## Overview

This Rmarkdown file aims to explore data wrangling using the **tidyr**
package, and to procatice joining features. It will be used as a
cheatsheet for future data frame reshaping and data wrangling.

## Import data frame and tidyverse pacakge

Gapminder data will be used in this homework, and the dataset will be
explored using “dplyr” package.

``` r
library(gapminder)
library(tidyr)
library(tidyverse)
```

    ## ── Attaching packages ─────────────────────────────────────────────────────────────────────────── tidyverse 1.2.1 ──

    ## ✔ ggplot2 2.2.1     ✔ purrr   0.2.4
    ## ✔ tibble  1.4.1     ✔ dplyr   0.7.4
    ## ✔ readr   1.1.1     ✔ stringr 1.2.0
    ## ✔ ggplot2 2.2.1     ✔ forcats 0.2.0

    ## ── Conflicts ────────────────────────────────────────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

``` r
# use suppressMessages(library(tidyverse)) to generate a pdf file
```

## Task 1 Data reshaping prompt: making a cheatsheet as a guidence to *tidyr* package

### Overview

Two main functions that will be explored in this part will be *gather*
and *spread*. *Gather* aims to make the data frame longer, which helps
with plotting figures. *Spread* will make the data frame wider.

The arugement that *gather* takes are gather(*data frame*, *key*,
*value*, *variables*) *key* is the new vairable created that contains
the headers of the *variables* argument. *value* is the new vairable
created that contains the value of the *variables* argument.

First, get a data frame to work with. The data frame designed here is
very similar to the type of data frame that I will get as raw data.

``` r
dat0 <- data.frame(
  id = c(1,2,3,4,5),
  x_1 = sample(1:2, 5, TRUE),
  x_2 = sample(3:4, 5, TRUE)
)
```

The following chunk makes the table looks better.

``` r
knitr::kable(dat0)
```

| id | x\_1 | x\_2 |
| -: | ---: | ---: |
|  1 |    2 |    3 |
|  2 |    2 |    4 |
|  3 |    1 |    3 |
|  4 |    1 |    3 |
|  5 |    2 |    4 |

What I want to do is to copy the values of variable “x\_2” under “x\_1”,
and to rename column “x\_1” as “x\_coor” using *gather*. Also, I will
create another column called “rep” to keep track of the repetition of
the value (wheter it is from “x\_1” or “x\_2” oringinally).

``` r
dat1 <- dat0 %>%
  gather(key = "rep", value = "x_coor", x_1, x_2)
rep <- str_extract(dat1$rep, regex("(?<=_)\\d+"))
dat1$rep <- rep
```

The following chunk makes the table looks better.

``` r
knitr::kable(dat1)
```

| id | rep | x\_coor |
| -: | :-- | ------: |
|  1 | 1   |       2 |
|  2 | 1   |       2 |
|  3 | 1   |       1 |
|  4 | 1   |       1 |
|  5 | 1   |       2 |
|  1 | 2   |       3 |
|  2 | 2   |       4 |
|  3 | 2   |       3 |
|  4 | 2   |       3 |
|  5 | 2   |       4 |

Then let’s spread it back out.

``` r
dat2 <- dat0 %>%
  gather(key = "rep", value = "x_coor", x_1, x_2) %>% 
  spread(key = "rep", value = "x_coor")
```

The following chunk makes the table looks better.

``` r
knitr::kable(dat2)
```

| id | x\_1 | x\_2 |
| -: | ---: | ---: |
|  1 |    2 |    3 |
|  2 |    2 |    4 |
|  3 |    1 |    3 |
|  4 |    1 |    3 |
|  5 |    2 |    4 |

What if I have five columns as “id”, “x\_1”, “x\_2”, “y\_1”, “y\_2”, and
I want to copy the values of variable “x\_2” under “x\_1”, and the
values of variable “y\_2” under “y\_1”. Also I want to rename column
“x\_1” and “y\_1” as “x\_coor” and “y\_coor” respectively. At last, I
will create another column called “rep” to keep track of the repetition
of the value (wheter it is from “x\_1” or “x\_2” oringinally).

The following chunk will first create a data frame, and then gather
“x\_1”, “x\_2” and “y\_1”, “y\_2” seperately and combine them together
using *bind\_cols*

To create a data frame

``` r
dat3 <- data.frame(
  id = c(1,2,3,4,5),
  x_1 = sample(1:2, 5, TRUE),
  x_2 = sample(3:4, 5, TRUE),
  y_1 = sample(5:6, 5, TRUE),
  y_2 = sample(7:8, 5, TRUE)
)
```

The following chunk makes the table looks better.

``` r
knitr::kable(dat3)
```

| id | x\_1 | x\_2 | y\_1 | y\_2 |
| -: | ---: | ---: | ---: | ---: |
|  1 |    1 |    3 |    5 |    8 |
|  2 |    1 |    3 |    5 |    7 |
|  3 |    2 |    4 |    6 |    8 |
|  4 |    1 |    4 |    6 |    8 |
|  5 |    2 |    3 |    6 |    7 |

To gather “x\_1” with “x\_2”, and “y\_1” with “y\_2” seperately.

``` r
dat4 <- dat3 %>%
  select(x_1, x_2) %>% 
  gather(key = "rep", value = "x_coor", x_1, x_2)
rep <- str_extract(dat4$rep, regex("(?<=_)\\d+"))
dat4$rep <- rep
dat5 <- dat3 %>%
  select(y_1, y_2) %>% 
  gather(key = "rep", value = "y_coor", y_1, y_2)
rep <- str_extract(dat5$rep, regex("(?<=_)\\d+"))
dat5$rep <- rep
dat6 <- bind_cols(dat4, dat5) %>% 
  select(-c(rep1))
```

``` r
knitr::kable(dat6)
```

| rep | x\_coor | y\_coor |
| :-- | ------: | ------: |
| 1   |       1 |       5 |
| 1   |       1 |       5 |
| 1   |       2 |       6 |
| 1   |       1 |       6 |
| 1   |       2 |       6 |
| 2   |       3 |       8 |
| 2   |       3 |       7 |
| 2   |       4 |       8 |
| 2   |       4 |       8 |
| 2   |       3 |       7 |

At last but not least, write the data frame onto a csv
file.

``` r
write_csv(dat6, "data.csv")
```

## Task 2 Create a data frame that is complementary to Grapminder and join the data frame with Gapminder

First, let’s create a data frame.

``` r
gp_comp <- data.frame(
  country = c("China", "United States", "Japan", "Korea, Rep.", "Norway", "Iceland"),
  spoken_lang = c("Mandarin", "English", "Japanese", "Korean", "Norwegian", "Icelandic"),
  NATO = c("No", "Yes", "No", "No", "Yes", "Yes")
)
```

Using
    left\_join.

``` r
left_join(gp_comp, gapminder, by = "country")
```

    ## Warning: Column `country` joining factors with different levels, coercing
    ## to character vector

    ##          country spoken_lang NATO continent year  lifeExp        pop
    ## 1          China    Mandarin   No      Asia 1952 44.00000  556263527
    ## 2          China    Mandarin   No      Asia 1957 50.54896  637408000
    ## 3          China    Mandarin   No      Asia 1962 44.50136  665770000
    ## 4          China    Mandarin   No      Asia 1967 58.38112  754550000
    ## 5          China    Mandarin   No      Asia 1972 63.11888  862030000
    ## 6          China    Mandarin   No      Asia 1977 63.96736  943455000
    ## 7          China    Mandarin   No      Asia 1982 65.52500 1000281000
    ## 8          China    Mandarin   No      Asia 1987 67.27400 1084035000
    ## 9          China    Mandarin   No      Asia 1992 68.69000 1164970000
    ## 10         China    Mandarin   No      Asia 1997 70.42600 1230075000
    ## 11         China    Mandarin   No      Asia 2002 72.02800 1280400000
    ## 12         China    Mandarin   No      Asia 2007 72.96100 1318683096
    ## 13 United States     English  Yes  Americas 1952 68.44000  157553000
    ## 14 United States     English  Yes  Americas 1957 69.49000  171984000
    ## 15 United States     English  Yes  Americas 1962 70.21000  186538000
    ## 16 United States     English  Yes  Americas 1967 70.76000  198712000
    ## 17 United States     English  Yes  Americas 1972 71.34000  209896000
    ## 18 United States     English  Yes  Americas 1977 73.38000  220239000
    ## 19 United States     English  Yes  Americas 1982 74.65000  232187835
    ## 20 United States     English  Yes  Americas 1987 75.02000  242803533
    ## 21 United States     English  Yes  Americas 1992 76.09000  256894189
    ## 22 United States     English  Yes  Americas 1997 76.81000  272911760
    ## 23 United States     English  Yes  Americas 2002 77.31000  287675526
    ## 24 United States     English  Yes  Americas 2007 78.24200  301139947
    ## 25         Japan    Japanese   No      Asia 1952 63.03000   86459025
    ## 26         Japan    Japanese   No      Asia 1957 65.50000   91563009
    ## 27         Japan    Japanese   No      Asia 1962 68.73000   95831757
    ## 28         Japan    Japanese   No      Asia 1967 71.43000  100825279
    ## 29         Japan    Japanese   No      Asia 1972 73.42000  107188273
    ## 30         Japan    Japanese   No      Asia 1977 75.38000  113872473
    ## 31         Japan    Japanese   No      Asia 1982 77.11000  118454974
    ## 32         Japan    Japanese   No      Asia 1987 78.67000  122091325
    ## 33         Japan    Japanese   No      Asia 1992 79.36000  124329269
    ## 34         Japan    Japanese   No      Asia 1997 80.69000  125956499
    ## 35         Japan    Japanese   No      Asia 2002 82.00000  127065841
    ## 36         Japan    Japanese   No      Asia 2007 82.60300  127467972
    ## 37   Korea, Rep.      Korean   No      Asia 1952 47.45300   20947571
    ## 38   Korea, Rep.      Korean   No      Asia 1957 52.68100   22611552
    ## 39   Korea, Rep.      Korean   No      Asia 1962 55.29200   26420307
    ## 40   Korea, Rep.      Korean   No      Asia 1967 57.71600   30131000
    ## 41   Korea, Rep.      Korean   No      Asia 1972 62.61200   33505000
    ## 42   Korea, Rep.      Korean   No      Asia 1977 64.76600   36436000
    ## 43   Korea, Rep.      Korean   No      Asia 1982 67.12300   39326000
    ## 44   Korea, Rep.      Korean   No      Asia 1987 69.81000   41622000
    ## 45   Korea, Rep.      Korean   No      Asia 1992 72.24400   43805450
    ## 46   Korea, Rep.      Korean   No      Asia 1997 74.64700   46173816
    ## 47   Korea, Rep.      Korean   No      Asia 2002 77.04500   47969150
    ## 48   Korea, Rep.      Korean   No      Asia 2007 78.62300   49044790
    ## 49        Norway   Norwegian  Yes    Europe 1952 72.67000    3327728
    ## 50        Norway   Norwegian  Yes    Europe 1957 73.44000    3491938
    ## 51        Norway   Norwegian  Yes    Europe 1962 73.47000    3638919
    ## 52        Norway   Norwegian  Yes    Europe 1967 74.08000    3786019
    ## 53        Norway   Norwegian  Yes    Europe 1972 74.34000    3933004
    ## 54        Norway   Norwegian  Yes    Europe 1977 75.37000    4043205
    ## 55        Norway   Norwegian  Yes    Europe 1982 75.97000    4114787
    ## 56        Norway   Norwegian  Yes    Europe 1987 75.89000    4186147
    ## 57        Norway   Norwegian  Yes    Europe 1992 77.32000    4286357
    ## 58        Norway   Norwegian  Yes    Europe 1997 78.32000    4405672
    ## 59        Norway   Norwegian  Yes    Europe 2002 79.05000    4535591
    ## 60        Norway   Norwegian  Yes    Europe 2007 80.19600    4627926
    ## 61       Iceland   Icelandic  Yes    Europe 1952 72.49000     147962
    ## 62       Iceland   Icelandic  Yes    Europe 1957 73.47000     165110
    ## 63       Iceland   Icelandic  Yes    Europe 1962 73.68000     182053
    ## 64       Iceland   Icelandic  Yes    Europe 1967 73.73000     198676
    ## 65       Iceland   Icelandic  Yes    Europe 1972 74.46000     209275
    ## 66       Iceland   Icelandic  Yes    Europe 1977 76.11000     221823
    ## 67       Iceland   Icelandic  Yes    Europe 1982 76.99000     233997
    ## 68       Iceland   Icelandic  Yes    Europe 1987 77.23000     244676
    ## 69       Iceland   Icelandic  Yes    Europe 1992 78.77000     259012
    ## 70       Iceland   Icelandic  Yes    Europe 1997 78.95000     271192
    ## 71       Iceland   Icelandic  Yes    Europe 2002 80.50000     288030
    ## 72       Iceland   Icelandic  Yes    Europe 2007 81.75700     301931
    ##     gdpPercap
    ## 1    400.4486
    ## 2    575.9870
    ## 3    487.6740
    ## 4    612.7057
    ## 5    676.9001
    ## 6    741.2375
    ## 7    962.4214
    ## 8   1378.9040
    ## 9   1655.7842
    ## 10  2289.2341
    ## 11  3119.2809
    ## 12  4959.1149
    ## 13 13990.4821
    ## 14 14847.1271
    ## 15 16173.1459
    ## 16 19530.3656
    ## 17 21806.0359
    ## 18 24072.6321
    ## 19 25009.5591
    ## 20 29884.3504
    ## 21 32003.9322
    ## 22 35767.4330
    ## 23 39097.0995
    ## 24 42951.6531
    ## 25  3216.9563
    ## 26  4317.6944
    ## 27  6576.6495
    ## 28  9847.7886
    ## 29 14778.7864
    ## 30 16610.3770
    ## 31 19384.1057
    ## 32 22375.9419
    ## 33 26824.8951
    ## 34 28816.5850
    ## 35 28604.5919
    ## 36 31656.0681
    ## 37  1030.5922
    ## 38  1487.5935
    ## 39  1536.3444
    ## 40  2029.2281
    ## 41  3030.8767
    ## 42  4657.2210
    ## 43  5622.9425
    ## 44  8533.0888
    ## 45 12104.2787
    ## 46 15993.5280
    ## 47 19233.9882
    ## 48 23348.1397
    ## 49 10095.4217
    ## 50 11653.9730
    ## 51 13450.4015
    ## 52 16361.8765
    ## 53 18965.0555
    ## 54 23311.3494
    ## 55 26298.6353
    ## 56 31540.9748
    ## 57 33965.6611
    ## 58 41283.1643
    ## 59 44683.9753
    ## 60 49357.1902
    ## 61  7267.6884
    ## 62  9244.0014
    ## 63 10350.1591
    ## 64 13319.8957
    ## 65 15798.0636
    ## 66 19654.9625
    ## 67 23269.6075
    ## 68 26923.2063
    ## 69 25144.3920
    ## 70 28061.0997
    ## 71 31163.2020
    ## 72 36180.7892

It can be observed that the chunk above joins the Gapminder data frame
to the right of the gp\_comp data frame by country. All of the countries
listed in the gp\_comp are shown in the joined dataframe, and countries
that are listed in Gapminder but not in gp\_comp are not shown. Also,
the value of gp\_comp got copied to all the blank spot.

How about the other
    order?

``` r
left_join(gapminder, gp_comp, by = "country")
```

    ## Warning: Column `country` joining factors with different levels, coercing
    ## to character vector

    ## # A tibble: 1,704 x 8
    ##    country     continent  year lifeExp      pop gdpPercap spoken_la… NATO 
    ##    <chr>       <fctr>    <int>   <dbl>    <int>     <dbl> <fctr>     <fct>
    ##  1 Afghanistan Asia       1952    28.8  8425333       779 <NA>       <NA> 
    ##  2 Afghanistan Asia       1957    30.3  9240934       821 <NA>       <NA> 
    ##  3 Afghanistan Asia       1962    32.0 10267083       853 <NA>       <NA> 
    ##  4 Afghanistan Asia       1967    34.0 11537966       836 <NA>       <NA> 
    ##  5 Afghanistan Asia       1972    36.1 13079460       740 <NA>       <NA> 
    ##  6 Afghanistan Asia       1977    38.4 14880372       786 <NA>       <NA> 
    ##  7 Afghanistan Asia       1982    39.9 12881816       978 <NA>       <NA> 
    ##  8 Afghanistan Asia       1987    40.8 13867957       852 <NA>       <NA> 
    ##  9 Afghanistan Asia       1992    41.7 16317921       649 <NA>       <NA> 
    ## 10 Afghanistan Asia       1997    41.8 22227415       635 <NA>       <NA> 
    ## # ... with 1,694 more rows

When the order of the first two arguments of the *left\_join* function
is switched, the joined data frame looks different. The Gapminder
columns are now on the left and the entire Gapminder data frame shows up
in the joined data frame. If a country exists in Gapminder but not in
gp\_comp, its value of “spoken\_lang” and “NATO” will get *NA*.

Let’s try
    *right\_join*.

``` r
right_join(gapminder, gp_comp, by = "country")
```

    ## Warning: Column `country` joining factors with different levels, coercing
    ## to character vector

    ## # A tibble: 72 x 8
    ##    country continent  year lifeExp        pop gdpPercap spoken_lang NATO  
    ##    <chr>   <fctr>    <int>   <dbl>      <int>     <dbl> <fctr>      <fctr>
    ##  1 China   Asia       1952    44.0  556263527       400 Mandarin    No    
    ##  2 China   Asia       1957    50.5  637408000       576 Mandarin    No    
    ##  3 China   Asia       1962    44.5  665770000       488 Mandarin    No    
    ##  4 China   Asia       1967    58.4  754550000       613 Mandarin    No    
    ##  5 China   Asia       1972    63.1  862030000       677 Mandarin    No    
    ##  6 China   Asia       1977    64.0  943455000       741 Mandarin    No    
    ##  7 China   Asia       1982    65.5 1000281000       962 Mandarin    No    
    ##  8 China   Asia       1987    67.3 1084035000      1379 Mandarin    No    
    ##  9 China   Asia       1992    68.7 1164970000      1656 Mandarin    No    
    ## 10 China   Asia       1997    70.4 1230075000      2289 Mandarin    No    
    ## # ... with 62 more rows

The joined data frame using *right\_join(gapminder, gp\_comp, by =
“country”)* looks similar to the joined data frame using
*left\_join(gp\_comp, gapminder, by = “country”)*. Both of them only
shows the countries listed in gp\_comp, but the former one has colomns
from Gapminder on the left whereas the later one has the gp\_comp
columns from gp\_comp on the left. This indicate the the left\_join will
only show items that exist in the first arguemnt(data frame), and the
right\_join will only show items that exist in the second
arguement(data\_frame). Both of them will put the columns from the first
arguement on the left.

Let’s try
    inner\_join.

``` r
inner_join(gapminder, gp_comp, by ="country")
```

    ## Warning: Column `country` joining factors with different levels, coercing
    ## to character vector

    ## # A tibble: 72 x 8
    ##    country continent  year lifeExp        pop gdpPercap spoken_lang NATO  
    ##    <chr>   <fctr>    <int>   <dbl>      <int>     <dbl> <fctr>      <fctr>
    ##  1 China   Asia       1952    44.0  556263527       400 Mandarin    No    
    ##  2 China   Asia       1957    50.5  637408000       576 Mandarin    No    
    ##  3 China   Asia       1962    44.5  665770000       488 Mandarin    No    
    ##  4 China   Asia       1967    58.4  754550000       613 Mandarin    No    
    ##  5 China   Asia       1972    63.1  862030000       677 Mandarin    No    
    ##  6 China   Asia       1977    64.0  943455000       741 Mandarin    No    
    ##  7 China   Asia       1982    65.5 1000281000       962 Mandarin    No    
    ##  8 China   Asia       1987    67.3 1084035000      1379 Mandarin    No    
    ##  9 China   Asia       1992    68.7 1164970000      1656 Mandarin    No    
    ## 10 China   Asia       1997    70.4 1230075000      2289 Mandarin    No    
    ## # ... with 62 more rows

The joined table in this part looks the same as the joined table using
right\_join(gapminder, gp\_comp, by = “country”). The reason is that the
countries listed in gp\_comp is a subset of the countries listed in
Gapminder.

And
    full\_join.

``` r
full_join(gapminder, gp_comp, by ="country")
```

    ## Warning: Column `country` joining factors with different levels, coercing
    ## to character vector

    ## # A tibble: 1,704 x 8
    ##    country     continent  year lifeExp      pop gdpPercap spoken_la… NATO 
    ##    <chr>       <fctr>    <int>   <dbl>    <int>     <dbl> <fctr>     <fct>
    ##  1 Afghanistan Asia       1952    28.8  8425333       779 <NA>       <NA> 
    ##  2 Afghanistan Asia       1957    30.3  9240934       821 <NA>       <NA> 
    ##  3 Afghanistan Asia       1962    32.0 10267083       853 <NA>       <NA> 
    ##  4 Afghanistan Asia       1967    34.0 11537966       836 <NA>       <NA> 
    ##  5 Afghanistan Asia       1972    36.1 13079460       740 <NA>       <NA> 
    ##  6 Afghanistan Asia       1977    38.4 14880372       786 <NA>       <NA> 
    ##  7 Afghanistan Asia       1982    39.9 12881816       978 <NA>       <NA> 
    ##  8 Afghanistan Asia       1987    40.8 13867957       852 <NA>       <NA> 
    ##  9 Afghanistan Asia       1992    41.7 16317921       649 <NA>       <NA> 
    ## 10 Afghanistan Asia       1997    41.8 22227415       635 <NA>       <NA> 
    ## # ... with 1,694 more rows

The joined table in this part looks the same as the joined table using
left\_join(gapminder, gp\_comp, by = “country”). The reason is also that
the countries listed in gp\_comp is a subset of the countries listed in
Gapminder.

And some filtering joins.

Let’s first look at
    semi\_join.

``` r
semi_join(gapminder, gp_comp, by ="country")
```

    ## Warning: Column `country` joining factors with different levels, coercing
    ## to character vector

    ## # A tibble: 72 x 6
    ##    country continent  year lifeExp        pop gdpPercap
    ##    <fctr>  <fctr>    <int>   <dbl>      <int>     <dbl>
    ##  1 China   Asia       1952    44.0  556263527       400
    ##  2 China   Asia       1957    50.5  637408000       576
    ##  3 China   Asia       1962    44.5  665770000       488
    ##  4 China   Asia       1967    58.4  754550000       613
    ##  5 China   Asia       1972    63.1  862030000       677
    ##  6 China   Asia       1977    64.0  943455000       741
    ##  7 China   Asia       1982    65.5 1000281000       962
    ##  8 China   Asia       1987    67.3 1084035000      1379
    ##  9 China   Asia       1992    68.7 1164970000      1656
    ## 10 China   Asia       1997    70.4 1230075000      2289
    ## # ... with 62 more rows

The joined data frame shows all rows in Gapminder that has a match in
gp\_comp. But it only shows the Gapminder data frame. To show the
gp\_comp data frame only, the following chunk can be
    used.

``` r
semi_join(gp_comp, gapminder, by ="country")
```

    ## Warning: Column `country` joining factors with different levels, coercing
    ## to character vector

    ##         country spoken_lang NATO
    ## 1         China    Mandarin   No
    ## 2 United States     English  Yes
    ## 3         Japan    Japanese   No
    ## 4   Korea, Rep.      Korean   No
    ## 5        Norway   Norwegian  Yes
    ## 6       Iceland   Icelandic  Yes

Last, let’s look at
    anti\_join.

``` r
anti_join(gp_comp, gapminder, by ="country")
```

    ## Warning: Column `country` joining factors with different levels, coercing
    ## to character vector

    ## [1] country     spoken_lang NATO       
    ## <0 rows> (or 0-length row.names)

The joined data frame has nothing because all items under “country”
column in gp\_comp has a match in Gapminder.  
What if the order of the arguements were
    swapped?

``` r
anti_join(gapminder, gp_comp, by ="country")
```

    ## Warning: Column `country` joining factors with different levels, coercing
    ## to character vector

    ## # A tibble: 1,632 x 6
    ##    country     continent  year lifeExp      pop gdpPercap
    ##    <fctr>      <fctr>    <int>   <dbl>    <int>     <dbl>
    ##  1 Afghanistan Asia       1952    28.8  8425333       779
    ##  2 Afghanistan Asia       1957    30.3  9240934       821
    ##  3 Afghanistan Asia       1962    32.0 10267083       853
    ##  4 Afghanistan Asia       1967    34.0 11537966       836
    ##  5 Afghanistan Asia       1972    36.1 13079460       740
    ##  6 Afghanistan Asia       1977    38.4 14880372       786
    ##  7 Afghanistan Asia       1982    39.9 12881816       978
    ##  8 Afghanistan Asia       1987    40.8 13867957       852
    ##  9 Afghanistan Asia       1992    41.7 16317921       649
    ## 10 Afghanistan Asia       1997    41.8 22227415       635
    ## # ... with 1,622 more rows

The joined data frame is not empty beacuse many countries listed in
Gapminder do not exist in gp\_comp.
