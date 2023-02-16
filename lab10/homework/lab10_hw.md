---
title: "Lab 10 Homework"
author: "Katie Ricketts"
date: "2023-02-15"
output:
  html_document: 
    theme: spacelab
    keep_md: yes
---



## Instructions
Answer the following questions and complete the exercises in RMarkdown. Please embed all of your code and push your final work to your repository. Your final lab report should be organized, clean, and run free from errors. Remember, you must remove the `#` for the included code chunks to run. Be sure to add your name to the author header above. For any included plots, make sure they are clearly labeled. You are free to use any plot type that you feel best communicates the results of your analysis.  

Make sure to use the formatting conventions of RMarkdown to make your report neat and clean!  

## Load the libraries

```r
library(tidyverse)
library(janitor)
library(here)
library(naniar)
```

## Desert Ecology
For this assignment, we are going to use a modified data set on [desert ecology](http://esapubs.org/archive/ecol/E090/118/). The data are from: S. K. Morgan Ernest, Thomas J. Valone, and James H. Brown. 2009. Long-term monitoring and experimental manipulation of a Chihuahuan Desert ecosystem near Portal, Arizona, USA. Ecology 90:1708.

```r
deserts <- read_csv(here("lab10", "data", "surveys_complete.csv"))
```

```
## Rows: 34786 Columns: 13
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (6): species_id, sex, genus, species, taxa, plot_type
## dbl (7): record_id, month, day, year, plot_id, hindfoot_length, weight
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

1. Use the function(s) of your choice to get an idea of its structure, including how NA's are treated. Are the data tidy?  

```r
glimpse(deserts)
```

```
## Rows: 34,786
## Columns: 13
## $ record_id       <dbl> 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16,…
## $ month           <dbl> 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, …
## $ day             <dbl> 16, 16, 16, 16, 16, 16, 16, 16, 16, 16, 16, 16, 16, 16…
## $ year            <dbl> 1977, 1977, 1977, 1977, 1977, 1977, 1977, 1977, 1977, …
## $ plot_id         <dbl> 2, 3, 2, 7, 3, 1, 2, 1, 1, 6, 5, 7, 3, 8, 6, 4, 3, 2, …
## $ species_id      <chr> "NL", "NL", "DM", "DM", "DM", "PF", "PE", "DM", "DM", …
## $ sex             <chr> "M", "M", "F", "M", "M", "M", "F", "M", "F", "F", "F",…
## $ hindfoot_length <dbl> 32, 33, 37, 36, 35, 14, NA, 37, 34, 20, 53, 38, 35, NA…
## $ weight          <dbl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA…
## $ genus           <chr> "Neotoma", "Neotoma", "Dipodomys", "Dipodomys", "Dipod…
## $ species         <chr> "albigula", "albigula", "merriami", "merriami", "merri…
## $ taxa            <chr> "Rodent", "Rodent", "Rodent", "Rodent", "Rodent", "Rod…
## $ plot_type       <chr> "Control", "Long-term Krat Exclosure", "Control", "Rod…
```

```r
naniar::miss_var_summary(deserts)
```

```
## # A tibble: 13 × 3
##    variable        n_miss pct_miss
##    <chr>            <int>    <dbl>
##  1 hindfoot_length   3348     9.62
##  2 weight            2503     7.20
##  3 sex               1748     5.03
##  4 record_id            0     0   
##  5 month                0     0   
##  6 day                  0     0   
##  7 year                 0     0   
##  8 plot_id              0     0   
##  9 species_id           0     0   
## 10 genus                0     0   
## 11 species              0     0   
## 12 taxa                 0     0   
## 13 plot_type            0     0
```



2. How many genera and species are represented in the data? What are the total number of observations? Which species is most/ least frequently sampled in the study?

```r
deserts %>% 
  summarize(n_genera = n_distinct(genus),
            n_species = n_distinct(species))
```

```
## # A tibble: 1 × 2
##   n_genera n_species
##      <int>     <int>
## 1       26        40
```

```r
deserts %>% 
  nrow
```

```
## [1] 34786
```

```r
deserts %>% 
  count(species) %>% 
  slice_min(n)
```

```
## # A tibble: 6 × 2
##   species          n
##   <chr>        <int>
## 1 clarki           1
## 2 scutalatus       1
## 3 tereticaudus     1
## 4 tigris           1
## 5 uniparens        1
## 6 viridis          1
```

```r
deserts %>% 
  count(species) %>% 
  slice_max(n)
```

```
## # A tibble: 1 × 2
##   species      n
##   <chr>    <int>
## 1 merriami 10596
```


3. What is the proportion of taxa included in this study? Show a table and plot that reflects this count.

```r
deserts %>% 
  count(taxa)
```

```
## # A tibble: 4 × 2
##   taxa        n
##   <chr>   <int>
## 1 Bird      450
## 2 Rabbit     75
## 3 Reptile    14
## 4 Rodent  34247
```

```r
deserts %>% 
  count(taxa) %>% 
  ggplot(aes(x=taxa, y=n))+
  geom_col()+
  scale_y_log10()
```

![](lab10_hw_files/figure-html/unnamed-chunk-10-1.png)<!-- -->


4. For the taxa included in the study, use the fill option to show the proportion of individuals sampled by `plot_type.`

```r
deserts %>% 
  ggplot(aes(x=taxa, fill=plot_type))+
  geom_bar()+
  scale_y_log10()
```

![](lab10_hw_files/figure-html/unnamed-chunk-11-1.png)<!-- -->


5. What is the range of weight for each species included in the study? Remove any observations of weight that are NA so they do not show up in the plot.

```r
deserts %>% 
  ggplot(aes(x=species, y=weight))+
  geom_boxplot(na.rm=T)+
  coord_flip()
```

![](lab10_hw_files/figure-html/unnamed-chunk-12-1.png)<!-- -->


6. Add another layer to your answer from #5 using `geom_point` to get an idea of how many measurements were taken for each species.

```r
deserts %>% 
  ggplot(aes(x=species, y=weight))+
  geom_boxplot(na.rm=T)+
  coord_flip()+
  geom_point(na.rm=T)
```

![](lab10_hw_files/figure-html/unnamed-chunk-13-1.png)<!-- -->


7. [Dipodomys merriami](https://en.wikipedia.org/wiki/Merriam's_kangaroo_rat) is the most frequently sampled animal in the study. How have the number of observations of this species changed over the years included in the study?

```r
deserts %>% 
  filter(genus=="Dipodomys" & species=="merriami") %>% 
  group_by(year) %>% 
  ggplot(aes(x=year))+
  geom_bar()
```

![](lab10_hw_files/figure-html/unnamed-chunk-14-1.png)<!-- -->


8. What is the relationship between `weight` and `hindfoot` length? Consider whether or not over plotting is an issue.

```r
deserts %>% 
  ggplot(aes(x=weight, y=hindfoot_length))+
  geom_jitter(na.rm=T)
```

![](lab10_hw_files/figure-html/unnamed-chunk-15-1.png)<!-- -->

```r
# Over plotting is definitely an issue
```


9. Which two species have, on average, the highest weight? Once you have identified them, make a new column that is a ratio of `weight` to `hindfoot_length`. Make a plot that shows the range of this new ratio and fill by sex.

```r
deserts %>% 
  select(species, weight) %>% 
  group_by(species) %>% 
  summarize(mean_weight = mean(weight, na.rm=T)) %>% 
  arrange(desc(mean_weight)) %>% 
  head(n=2)
```

```
## # A tibble: 2 × 2
##   species     mean_weight
##   <chr>             <dbl>
## 1 albigula           159.
## 2 spectabilis        120.
```

```r
deserts %>% 
  select(species, weight, hindfoot_length) %>% 
  filter(weight!="NA" & hindfoot_length!="NA") %>% 
  filter(species=="albigula" | species=="spectabilis") %>%
  mutate(weight_to_hindfoot_length = weight/hindfoot_length)
```

```
## # A tibble: 3,072 × 4
##    species     weight hindfoot_length weight_to_hindfoot_length
##    <chr>        <dbl>           <dbl>                     <dbl>
##  1 spectabilis    117              50                      2.34
##  2 spectabilis    121              51                      2.37
##  3 spectabilis    115              51                      2.25
##  4 spectabilis    120              48                      2.5 
##  5 spectabilis    118              48                      2.46
##  6 spectabilis    126              52                      2.42
##  7 spectabilis    132              50                      2.64
##  8 spectabilis    122              53                      2.30
##  9 spectabilis    107              48                      2.23
## 10 spectabilis    115              50                      2.3 
## # … with 3,062 more rows
```

```r
deserts %>% 
  select(species, weight, hindfoot_length, sex) %>% 
  filter(weight!="NA" & hindfoot_length!="NA" & sex!="NA") %>% 
  filter(species=="albigula" | species=="spectabilis") %>% 
  mutate(weight_to_hindfoot_length = weight/hindfoot_length) %>% 
  ggplot(aes(x=species, y=weight_to_hindfoot_length, fill=sex))+
  geom_boxplot()
```

![](lab10_hw_files/figure-html/unnamed-chunk-18-1.png)<!-- -->



10. Make one plot of your choice! Make sure to include at least two of the aesthetics options you have learned.

```r
names(deserts)
```

```
##  [1] "record_id"       "month"           "day"             "year"           
##  [5] "plot_id"         "species_id"      "sex"             "hindfoot_length"
##  [9] "weight"          "genus"           "species"         "taxa"           
## [13] "plot_type"
```

```r
glimpse(deserts)
```

```
## Rows: 34,786
## Columns: 13
## $ record_id       <dbl> 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16,…
## $ month           <dbl> 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, 7, …
## $ day             <dbl> 16, 16, 16, 16, 16, 16, 16, 16, 16, 16, 16, 16, 16, 16…
## $ year            <dbl> 1977, 1977, 1977, 1977, 1977, 1977, 1977, 1977, 1977, …
## $ plot_id         <dbl> 2, 3, 2, 7, 3, 1, 2, 1, 1, 6, 5, 7, 3, 8, 6, 4, 3, 2, …
## $ species_id      <chr> "NL", "NL", "DM", "DM", "DM", "PF", "PE", "DM", "DM", …
## $ sex             <chr> "M", "M", "F", "M", "M", "M", "F", "M", "F", "F", "F",…
## $ hindfoot_length <dbl> 32, 33, 37, 36, 35, 14, NA, 37, 34, 20, 53, 38, 35, NA…
## $ weight          <dbl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA…
## $ genus           <chr> "Neotoma", "Neotoma", "Dipodomys", "Dipodomys", "Dipod…
## $ species         <chr> "albigula", "albigula", "merriami", "merriami", "merri…
## $ taxa            <chr> "Rodent", "Rodent", "Rodent", "Rodent", "Rodent", "Rod…
## $ plot_type       <chr> "Control", "Long-term Krat Exclosure", "Control", "Rod…
```

```r
deserts %>% 
  filter(taxa=="Rodent") %>% 
  group_by(plot_type) %>% 
  summarize(mean_weight = mean(weight, na.rm=T)) %>% 
  arrange(mean_weight)
```

```
## # A tibble: 5 × 2
##   plot_type                 mean_weight
##   <chr>                           <dbl>
## 1 Long-term Krat Exclosure         27.2
## 2 Rodent Exclosure                 32.5
## 3 Short-term Krat Exclosure        41.2
## 4 Control                          48.6
## 5 Spectab exclosure                51.6
```

```r
deserts %>% 
  filter(taxa=="Rodent") %>% 
  filter(plot_type=="Long-term Krat Exclosure") %>% 
  filter(sex!="NA") %>% 
  ggplot(aes(x=species, y=weight, fill=sex))+
  geom_boxplot(na.rm=T)+
  coord_flip()+
  scale_y_log10()
```

![](lab10_hw_files/figure-html/unnamed-chunk-22-1.png)<!-- -->




## Push your final code to GitHub!
Please be sure that you check the `keep md` file in the knit preferences. 
