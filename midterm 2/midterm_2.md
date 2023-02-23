---
title: "BIS 15L Midterm 2"
output:
  html_document: 
    theme: spacelab
    keep_md: yes
---



## Instructions
Answer the following questions and complete the exercises in RMarkdown. Please embed all of your code and push your final work to your repository. Your code should be organized, clean, and run free from errors. Remember, you must remove the `#` for any included code chunks to run. Be sure to add your name to the author header above.  

After the first 50 minutes, please upload your code (5 points). During the second 50 minutes, you may get help from each other- but no copy/paste. Upload the last version at the end of this time, but be sure to indicate it as final. If you finish early, you are free to leave.

Make sure to use the formatting conventions of RMarkdown to make your report neat and clean! Use the tidyverse and pipes unless otherwise indicated. To receive full credit, all plots must have clearly labeled axes, a title, and consistent aesthetics. This exam is worth a total of 35 points. 

Please load the following libraries.

```r
library("tidyverse")
library("janitor")
library("naniar")
```

## Data
These data are from a study on surgical residents. The study was originally published by Sessier et al. “Operation Timing and 30-Day Mortality After Elective General Surgery”. Anesth Analg 2011; 113: 1423-8. The data were cleaned for instructional use by Amy S. Nowacki, “Surgery Timing Dataset”, TSHS Resources Portal (2016). Available at https://www.causeweb.org/tshs/surgery-timing/.

Descriptions of the variables and the study are included as pdf's in the data folder.  

Please run the following chunk to import the data.

```r
surgery <- read_csv("data/surgery.csv")
```

1. (2 points) Use the summary function(s) of your choice to explore the data and get an idea of its structure. Please also check for NA's.


```r
glimpse(surgery)
```

```
## Rows: 32,001
## Columns: 25
## $ ahrq_ccs            <chr> "<Other>", "<Other>", "<Other>", "<Other>", "<Othe…
## $ age                 <dbl> 67.8, 39.5, 56.5, 71.0, 56.3, 57.7, 56.6, 64.2, 66…
## $ gender              <chr> "M", "F", "F", "M", "M", "F", "M", "F", "M", "F", …
## $ race                <chr> "Caucasian", "Caucasian", "Caucasian", "Caucasian"…
## $ asa_status          <chr> "I-II", "I-II", "I-II", "III", "I-II", "I-II", "IV…
## $ bmi                 <dbl> 28.04, 37.85, 19.56, 32.22, 24.32, 40.30, 64.57, 4…
## $ baseline_cancer     <chr> "No", "No", "No", "No", "Yes", "No", "No", "No", "…
## $ baseline_cvd        <chr> "Yes", "Yes", "No", "Yes", "No", "Yes", "Yes", "Ye…
## $ baseline_dementia   <chr> "No", "No", "No", "No", "No", "No", "No", "No", "N…
## $ baseline_diabetes   <chr> "No", "No", "No", "No", "No", "No", "Yes", "No", "…
## $ baseline_digestive  <chr> "Yes", "No", "No", "No", "No", "No", "No", "No", "…
## $ baseline_osteoart   <chr> "No", "No", "No", "No", "No", "No", "No", "No", "N…
## $ baseline_psych      <chr> "No", "No", "No", "No", "No", "Yes", "No", "No", "…
## $ baseline_pulmonary  <chr> "No", "No", "No", "No", "No", "No", "No", "No", "N…
## $ baseline_charlson   <dbl> 0, 0, 0, 0, 0, 0, 2, 0, 1, 2, 0, 1, 0, 0, 0, 0, 0,…
## $ mortality_rsi       <dbl> -0.63, -0.63, -0.49, -1.38, 0.00, -0.77, -0.36, -0…
## $ complication_rsi    <dbl> -0.26, -0.26, 0.00, -1.15, 0.00, -0.84, -1.34, 0.0…
## $ ccsmort30rate       <dbl> 0.0042508, 0.0042508, 0.0042508, 0.0042508, 0.0042…
## $ ccscomplicationrate <dbl> 0.07226355, 0.07226355, 0.07226355, 0.07226355, 0.…
## $ hour                <dbl> 9.03, 18.48, 7.88, 8.80, 12.20, 7.67, 9.53, 7.52, …
## $ dow                 <chr> "Mon", "Wed", "Fri", "Wed", "Thu", "Thu", "Tue", "…
## $ month               <chr> "Nov", "Sep", "Aug", "Jun", "Aug", "Dec", "Apr", "…
## $ moonphase           <chr> "Full Moon", "New Moon", "Full Moon", "Last Quarte…
## $ mort30              <chr> "No", "No", "No", "No", "No", "No", "No", "No", "N…
## $ complication        <chr> "No", "No", "No", "No", "No", "No", "No", "Yes", "…
```


```r
miss_var_summary(surgery)
```

```
## # A tibble: 25 × 3
##    variable          n_miss pct_miss
##    <chr>              <int>    <dbl>
##  1 bmi                 3290 10.3    
##  2 race                 480  1.50   
##  3 asa_status             8  0.0250 
##  4 gender                 3  0.00937
##  5 age                    2  0.00625
##  6 ahrq_ccs               0  0      
##  7 baseline_cancer        0  0      
##  8 baseline_cvd           0  0      
##  9 baseline_dementia      0  0      
## 10 baseline_diabetes      0  0      
## # … with 15 more rows
```

2. (3 points) Let's explore the participants in the study. Show a count of participants by race AND make a plot that visually represents your output.


```r
surgery %>% 
  count(race)
```

```
## # A tibble: 4 × 2
##   race                 n
##   <chr>            <int>
## 1 African American  3790
## 2 Caucasian        26488
## 3 Other             1243
## 4 <NA>               480
```


```r
surgery %>% 
  count(race) %>% 
  ggplot(aes(x=race,y=n, fill=race))+
  geom_col()
```

![](midterm_2_files/figure-html/unnamed-chunk-6-1.png)<!-- -->


3. (2 points) What is the mean age of participants by gender? (hint: please provide a number for each) Since only three participants do not have gender indicated, remove these participants from the data.


```r
surgery %>% 
  na.omit() %>% 
  group_by(gender) %>% 
  summarise(mean_age=mean(age))
```

```
## # A tibble: 2 × 2
##   gender mean_age
##   <chr>     <dbl>
## 1 F          56.6
## 2 M          59.0
```


4. (3 points) Make a plot that shows the range of age associated with gender.


```r
surgery %>% 
  filter(!is.na(gender)) %>% 
  ggplot(aes(x=gender,y=age))+
  geom_boxplot()
```

```
## Warning: Removed 2 rows containing non-finite values (`stat_boxplot()`).
```

![](midterm_2_files/figure-html/unnamed-chunk-8-1.png)<!-- -->


5. (2 points) How healthy are the participants? The variable `asa_status` is an evaluation of patient physical status prior to surgery. Lower numbers indicate fewer comorbidities (presence of two or more diseases or medical conditions in a patient). Make a plot that compares the number of `asa_status` I-II, III, and IV-V.


```r
surgery %>% 
  count(asa_status) %>% 
  ggplot(aes(x=asa_status,y=n))+
  geom_col()
```

![](midterm_2_files/figure-html/unnamed-chunk-9-1.png)<!-- -->


6. (3 points) Create a plot that displays the distribution of body mass index for each `asa_status` as a probability distribution- not a histogram. (hint: use faceting!)


```r
surgery %>% 
  ggplot(aes(x=bmi, fill=asa_status))+ 
  geom_density(alpha=0.4) + 
  facet_wrap(~asa_status, ncol=4)+
  theme(axis.text.x = element_text(angle = 60, hjust = 1))
```

```
## Warning: Removed 3290 rows containing non-finite values (`stat_density()`).
```

![](midterm_2_files/figure-html/unnamed-chunk-10-1.png)<!-- -->



The variable `ccsmort30rate` is a measure of the overall 30-day mortality rate associated with each type of operation. The variable `ccscomplicationrate` is a measure of the 30-day in-hospital complication rate. The variable `ahrq_ccs` lists each type of operation.  



7. (4 points) What are the 5 procedures associated with highest risk of 30-day mortality AND how do they compare with the 5 procedures with highest risk of complication? (hint: no need for a plot here)


```r
surgery %>% 
  select(ahrq_ccs,ccsmort30rate) %>% 
  group_by(ahrq_ccs) %>% 
  summarise(highest_ccsmort=max(ccsmort30rate)) %>% 
  arrange(desc(highest_ccsmort))
```

```
## # A tibble: 23 × 2
##    ahrq_ccs                                             highest_ccsmort
##    <chr>                                                          <dbl>
##  1 Colorectal resection                                         0.0167 
##  2 Small bowel resection                                        0.0129 
##  3 Gastrectomy; partial and total                               0.0127 
##  4 Endoscopy and endoscopic biopsy of the urinary tract         0.00811
##  5 Spinal fusion                                                0.00742
##  6 Hip replacement; total and partial                           0.00740
##  7 Genitourinary incontinence procedures                        0.00433
##  8 <Other>                                                      0.00425
##  9 Other hernia repair                                          0.00403
## 10 Arthroplasty knee                                            0.00296
## # … with 13 more rows
```



```r
surgery %>% 
  select(ahrq_ccs,ccscomplicationrate) %>% 
  group_by(ahrq_ccs) %>% 
  summarise(highest_ccscom=max(ccscomplicationrate)) %>% 
  arrange(desc(highest_ccscom))
```

```
## # A tibble: 23 × 2
##    ahrq_ccs                                  highest_ccscom
##    <chr>                                              <dbl>
##  1 Small bowel resection                              0.466
##  2 Colorectal resection                               0.312
##  3 Nephrectomy; partial or complete                   0.197
##  4 Gastrectomy; partial and total                     0.190
##  5 Spinal fusion                                      0.183
##  6 Hysterectomy; abdominal and vaginal                0.151
##  7 Other hernia repair                                0.143
##  8 Oophorectomy; unilateral and bilateral             0.135
##  9 Open prostatectomy                                 0.109
## 10 Laminectomy; excision intervertebral disc          0.106
## # … with 13 more rows
```

8. (3 points) Make a plot that compares the `ccsmort30rate` for all listed `ahrq_ccs` procedures.


```r
surgery %>% 
  select(ahrq_ccs,ccsmort30rate) %>% 
  ggplot(aes(x=ahrq_ccs,y=ccsmort30rate))+
  geom_col()+
  coord_flip()
```

![](midterm_2_files/figure-html/unnamed-chunk-13-1.png)<!-- -->



9. (4 points) When is the best month to have surgery? Make a chart that shows the 30-day mortality and complications for the patients by month. `mort30` is the variable that shows whether or not a patient survived 30 days post-operation.


```r
surgery %>% 
  group_by(month) %>%
  count(mort30) %>% 
  arrange((n)) %>% 
  filter(mort30=="Yes")
```

```
## # A tibble: 12 × 3
## # Groups:   month [12]
##    month mort30     n
##    <chr> <chr>  <int>
##  1 Dec   Yes        4
##  2 Nov   Yes        5
##  3 Oct   Yes        8
##  4 Aug   Yes        9
##  5 May   Yes       10
##  6 Apr   Yes       12
##  7 Jul   Yes       12
##  8 Mar   Yes       12
##  9 Jun   Yes       14
## 10 Sep   Yes       16
## 11 Feb   Yes       17
## 12 Jan   Yes       19
```


10. (4 points) Make a plot that visualizes the chart from question #9. Make sure that the months are on the x-axis. Do a search online and figure out how to order the months Jan-Dec.


```r
surgery %>% 
  group_by(month) %>%
  count(mort30) %>% 
  ggplot(aes(x=month,y=n, fill=mort30))+
  geom_col(position = "dodge")+
  xlab("Month") +
  scale_y_log10()
```

![](midterm_2_files/figure-html/unnamed-chunk-15-1.png)<!-- -->


Please provide the names of the students you have worked with with during the exam:

Please be 100% sure your exam is saved, knitted, and pushed to your github repository. No need to submit a link on canvas, we will find your exam in your repository.
