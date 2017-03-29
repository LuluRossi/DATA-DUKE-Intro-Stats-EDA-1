# Exploring the BRFSS data
### Submission by Connor Lenio. Email: cojamalo@gmail.com
Completion Date: Mar 29, 2017

### Setup

#### Load packages


```r
library(knitr)
library(pander)
library(ggplot2)
library(colorspace)
library(tidyr)
library(dplyr)
```
<br>

#### Load data and set global knitr options


```r
opts_chunk$set(echo = TRUE,fig.align='center')
load("brfss2013.RData")
```



* * *
### Part 1: Data

#### Describe how the observations in the sample are collected, and the implications of this data collection method on the scope of inference (generalizability / causality). Note that you will need to look into documentation on the BRFSS to answer this question. See http://www.cdc.gov/brfss/ as well as the "More information on the data" section below.

<br>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;The Behavioral Risk Factor Surveillance System (BRFSS) is an observational study targeting the non-institutionalized adult population, aged 18 years or older, who reside in the United States. Specifically, the study is a retrospective observational study as asks respondents about various aspects of their health in the previous months to their contact.   The study's observations are collected by contacting respondents by phone calls. Prospective respondents are eliminated from consideration if they are not 18 years or older, at a residential or college location, or if the phone they are using is not classified as a landline. Once these conditions are met, a random adult is selected from the contacted household to interview.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;The Centers for Disease Control and Prevention manages the study, but the standardized interviews are conducted by agents in each state or territory involved in the study. The study does not involve completely random selection of respondents nor the random assignment of respondents to the factors under consideration. Thus, the study is not an experiment and, thus, involves sample bias and limitations in what conclusions can be drawn from the data collected. Moreover, no causality can be determined from this observational study, only associational relationships. Any identified associations will also be complicated by lurking variables as it will be difficult to rule out other confounding factors that may lead to such associations. 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Numerous issues with the sampling method exist and they involve differences between the sampled population and the stated target population of the study. For instance, the use of telephones as the method of contact limits the sampling frame to only those who have a landline telephone and reside in residential or college housing. Moreover, potential respondents may not answer their phones when called or otherwise may not be available, introducing nonresponse bias. Demographic differences may occur as well as more instances of certain types of people may answer the phone or have a phone at all. A cursory look at the 2013 data shows a more white, female, and wealthy sample of respondents than exists in the target population, for example. Also, efforts to normalize the number of respondents according to their state are not present and the proportion of respondents from each state is different then they would be in the target population. These examples are just some of the ways the sample population may not fully represent the target population. 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Other issues of the study revolve around its interview format. One may doubt the data due to issues such as the intelligibility of the question asked (did the respondent understand the question?) or whether memory issues are biasing results (respondents may not accurately remember their own health status). In addition, respondents may misreport their true health status due to the sensitive nature of health questions. For example, a respondent may underreport the number of times they binge drank in the past month because of concerns of sounding like they drink too much. Considering these limitations and the potential for sampling bias, one must be careful in making too strong of claims from the data. The data is still useful for learning about the health of US adults, but the scope of inference is narrow considering these and other limitations of this observational study's design.


* * *

### Part 2: Research Questions
#### Come up with at least three research questions that you want to answer using these data. You should phrase your research questions in a way that matches up with the scope of inference your dataset allows for. Make sure that at least two of these questions involve at least three variables. You are welcomed to create new variables based on existing ones. With each question include a brief discussion (1-2 sentences) as to why this question is of interest to you and/or your audience.

<br>

**Research question 1:**

For <u>those US adults who report some level of joint pain</u> and <u>limitations due to joint pain,</u> is increased <u>physical activity</u> associated with a reduction in the severity of reported joint pain?

<i>Variables:</i> lmtjoin3 (limitations due to joint pain), X_pacat1 (physical activity), and joinpain (joint pain severity score in last 30 days)

<i>Relevance:</i> Personal trainers, health providers, arthritis sufferers, etc. are interested in the benefits of exercise and any possible relationships with exercise and improving joint health or reducing pain.

<br>

**Research question 2:**

Are a having a lower reported <u>income</u> and <u>whether one has health insurance</u> associated with a <u>difficulty in affording to see a doctor</u>?

<i>Variables:</i> income2 (Income Level), hlthpln1 (Have any Health Care Coverage), and medcost (Could Not See Dr. Because Of Cost) 

<i>Relevance:</i> Healthcare is a major public policy issue in the United States and a relevant question to the debate is who needs help with medical expenses and why, and whether having health insurance makes a difference in handling medical costs. 

<br>

**Research question 3:**

Are a US adult's <u>smoking habits</u> and their <u>age group</u> associated with the <u>number of days they reported where their physical health was not good</u>?

<i>Variables:</i> X_smoker3 (Four-level smoker status),X_age_g (Imputed Age In Six Groups), and physhlth (Number Of Days Physical Health Not Good)

<i>Relevance:</i> Smoking is a major public health issue, yet many people still choose to smoke, even young people who are familiar with its risks. Is smoking related to increased risk for physical illness and does the smoker's age relate to the magnitude of such relationships?  


* * *

### Part 3: Exploratory data analysis
#### Perform exploratory data analysis (EDA) that addresses each of the three research questions you outlined above. Your EDA should contain numerical summaries and visualizations. Each R output and plot should be accompanied by a brief interpretation.

<br>

**Required Data Processing Function**

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;The following function is used in the exploratory data analysis to reduce the brfss2013 data to just those observations that had a value for each of the variables under consideration - `allprep`:

```r
allprep <- function(...) {
    study <- brfss2013 %>%
        select(...) 
    
    return(study[complete.cases(study),])
}
```

* * *

**Research Question 1:**

For <u>those US adults who report some level of joint pain</u> and <u>limitations due to joint pain,</u> is increased <u>physical activity</u> associated with a reduction in the severity of reported joint pain?

<br>

**Exploratory Data Analysis for Question 1**

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Complete cases were found for the desired variables using allprep, then the data was filtered for those respondents that have limitations due to joint pain and those who reported some kind of joint pain in the last 30 days - `dat1`:


```r
dat1 <- allprep(lmtjoin3, X_pacat1, joinpain) %>%
    filter(lmtjoin3 == "Yes" & joinpain != 0) 
    
pandoc.table(head(dat1), caption = "Fig. 1-0 - Observations 1 to 6 of 65230 for output data frame `dat1`...", justify = "center")
```


-------------------------------------------
 lmtjoin3        X_pacat1         joinpain 
---------- --------------------- ----------
   Yes           Inactive            7     

   Yes           Inactive            5     

   Yes     Insufficiently active     8     

   Yes           Inactive            8     

   Yes     Insufficiently active     5     

   Yes         Highly active         7     
-------------------------------------------

Table: Fig. 1-0 - Observations 1 to 6 of 65230 for output data frame `dat1`...

<br>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;To determine if there is an association between physical activity and joint pain scores, the summary statistics for measures of center and measures of the spread were determined. The skew of the data was also determined by comparing the mean and median - `sum1`:

```r
sum1 <- dat1 %>% 
    group_by(X_pacat1) %>% 
    summarize(Q1 = quantile(joinpain, 0.25), MEAN = mean(joinpain), MEDIAN = median(joinpain),Q3 = quantile(joinpain, 0.75), IQR = IQR(joinpain), STDEV = sd(joinpain)) %>%
    mutate(SKEW = ifelse(MEAN > MEDIAN, "RIGHT", "LEFT"))
                                                                                                                                                    
pandoc.table(sum1, caption = "Fig. 1-1 - Summary Statistics for Joint Pain Grouped by Physical Activity Level", justify = "center")
```


-----------------------------------------------------------------------
      X_pacat1         Q1    MEAN    MEDIAN   Q3   IQR   STDEV    SKEW 
--------------------- ---- -------- -------- ---- ----- -------- ------
    Highly active      4   5.494909    5      7     3   2.262416 RIGHT 

       Active          4   5.624525    5      7     3   2.250173 RIGHT 

Insufficiently active  4   5.964699    6      8     4   2.267815  LEFT 

      Inactive         5   6.676478    7      8     3   2.220468  LEFT 
-----------------------------------------------------------------------

Table: Fig. 1-1 - Summary Statistics for Joint Pain Grouped by Physical Activity Level

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;These summary statistics indicate that all four categories have a similar degree of spread and variation. However, the mean, median, and 1st and 3rd quartiles show differences in the distribution of the data. For instance, the two active categories: Highly active and Active are slightly right skewed, indicating that more of their joint pain values are less than the mean of 5.5 and 5.6, respectively. The Insufficiently active category is nearly symmetric, and the Inactive category is left skewed, indicating that more of its joint pain values are greater than the mean of 6.7. Thus, there is a positive increase in the distribution of joint pain levels as activity level decreases, and there is a clear increase in joint pain level between the Inactive category and all the other categories. 

<br>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;These differences in distribution can be visualized by a density plot:


```r
ggplot(dat1, aes(x = joinpain, color = X_pacat1)) + 
    geom_density(adjust = 2) +
    scale_color_manual(values = rev(heat_hcl(6, h = c(20,-80), l = c(75,40), c =c(40,80), power = 1))) +
    scale_x_continuous(breaks = c(1:10)) +
    labs(title = "Distribution of Reported Joint Pain Levels by Physical Activity Level", y = "Density", x = "Joint Pain Level", col = "Physical Activity Level") +
    theme(plot.title = element_text(hjust = 0.5))
```

<img src="Duke01_project_files/figure-html/Plot 1-1-1.png" style="display: block; margin: auto;" />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;The density plot reflects the summary statistics as the Inactive distribution is denser at higher joint pain scores compared to the other groups. The other groups are more similar in density, but Highly Active has the greatest density at lower joint pain levels compared to Active and Insufficiently Active, and the Active category is denser at lower pain scores than Insufficiently active category. 

<br>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;These differences are clarified by turning the density graph above into a proportional density plot: 


```r
ggplot(dat1, aes(x = joinpain, fill = X_pacat1)) + 
    geom_density(position = "fill", adjust =2) +
    scale_fill_manual(values = rev(heat_hcl(6, h = c(20,-80), l = c(75,40), c =c(40,80), power = 1))) +
    scale_x_continuous(breaks = c(1:10)) +
    scale_y_continuous(labels = c("0%", "25%", "50%", "75%", "100%")) +
    labs(title = "Proportional Distribution of Reported Joint Pain Levels \n by Physical Activity Level", y = "Proportional Density (%)", x = "Joint Pain Level", fill = "Physical Activity Level") +
    theme(plot.title = element_text(hjust = 0.5))
```

<img src="Duke01_project_files/figure-html/Plot 1-2-1.png" style="display: block; margin: auto;" />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;This plot supports the interpretation that the proportion of observations reporting lower levels of joint pain are greater for those who are more active. And, the less active a respondent is, the greater proportion of observations that report higher joint pain levels.

<br>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;There is another way to view the association between physical activity levels and reported joint pain. A new summary table is produced grouping by both activity and joint pain and counting the number of observations for each combination of categories - `sum2`:


```r
sum2 <- dat1 %>%
    group_by(X_pacat1, joinpain) %>%
    summarize(n = n())
pandoc.table(head(sum2), caption = "Fig. 1-2 - Observations 1 to 6 of 40 for output data frame `sum2`...", justify = "center")
```


-----------------------------
  X_pacat1     joinpain   n  
------------- ---------- ----
Highly active     1      444 

Highly active     2      1134

Highly active     3      1922

Highly active     4      2082

Highly active     5      3162

Highly active     6      2011
-----------------------------

Table: Fig. 1-2 - Observations 1 to 6 of 40 for output data frame `sum2`...

<br>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;This table is used for a barplot comparing the proportion of each physical activity category by joint pain level:

```r
cc <- scales::div_gradient_pal("red","lavender", "blue", "Lab")(seq(0,1,length.out=10))
ggplot(sum2, aes(x = factor(X_pacat1, levels =  c("Inactive", "Insufficiently active", "Active", "Highly active")), y = n, fill = factor(joinpain, levels = c(10:1)))) + geom_bar(stat="identity", position = "fill") +
    scale_fill_manual(values = cc, labels=c("10 - Worst Pain", 9:2, "1 - Least Pain")) +
    scale_y_continuous(labels = c("0%", "25%", "50%", "75%", "100%")) +
    labs(title = "Proportion of Arthritis Sufferers' Level of Reported Joint Pain in Last 30 Days \n by Physical Activity Level", y = "% Respondents in Each Joint Pain Level", x = "Respondent's Physical Activity Level", fill = "Joint Pain Level") +
    theme(plot.title = element_text(hjust = 0.5))
```

<img src="Duke01_project_files/figure-html/Plot 1-3-1.png" style="display: block; margin: auto;" />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Again, the relationship between greater physical activity and less joint pain is indicated as a higher proportion of those who report lower joint pain scores are highly active.

<br>

**Summary**

<i>Initial Question:</i> For <u>those US adults who report some level of joint pain</u> and <u>limitations due to joint pain,</u> is increased <u>physical activity</u> associated with a reduction in the severity of reported joint pain?

<i>Narrative from the Exploratory Analysis:</i> Yes, Increased physical activity is associated with a reduction in the severity of reported joint pain for those US adults who report some level of joint pain and limitations due to joint pain.

* * *

**Research Question 2:**

Are a having a lower reported <u>income</u> and <u>whether one has health insurance</u> associated with a <u>difficulty in affording to see a doctor</u>?

<br>

**Exploratory Data Analysis for Question 2**

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Complete cases were selected for the desired variables using allprep. Then, the medcost factor was revalued using plyr to change the valence of the question so that it is easier to understand while keeping the same meaning. Now, medcost represents whether a respondent could afford to see the doctor rather than if the respondent could not see the doctor due to cost - `dat2`:


```r
dat2 <- allprep(hlthpln1, medcost, income2)
dat2$medcost <- plyr::revalue(dat2$medcost, c("Yes" = "No", "No" = "Yes"))
pandoc.table(head(dat2), caption = "Fig. 2-0 - Observations 1 to 6 of 418642 for output data frame `dat2`...", justify = "center")
```


--------------------------------------
 hlthpln1   medcost       income2     
---------- --------- -----------------
   Yes        Yes    Less than $75,000

   Yes        Yes     $75,000 or more 

   Yes        Yes     $75,000 or more 

   Yes        Yes    Less than $75,000

   Yes        Yes    Less than $50,000

   Yes        Yes     $75,000 or more 
--------------------------------------

Table: Fig. 2-0 - Observations 1 to 6 of 418642 for output data frame `dat2`...

<br>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Starting with the two categorical variables, income2 and medcost, a summary table was made to summarize the number of respondents in each category. The proportion of each income group that said no to whether they could afford to see the doctor was also calculated - `sum1`:

```r
sum1 <- dat2 %>% 
    group_by(income2, medcost) %>% 
    summarize(Sum = n()) %>% 
    spread(medcost, Sum) %>% 
    mutate(Sum = Yes+No, `% No` = round(No/Sum*100, digits =1))
colnames(sum1)[1] <- "Income Level"
pandoc.table(sum1, caption = "Fig. 2-1 - Summary Statistics for If Respondent Could Afford to See Dr. Because Of Cost Grouped by Income Level", justify = "center")
```


-------------------------------------------
  Income Level     No   Yes    Sum    % No 
----------------- ---- ------ ------ ------
Less than $10,000 7249 17916  25165   28.8 

Less than $15,000 6804 19782  26586   25.6 

Less than $20,000 8047 26589  34636   23.2 

Less than $25,000 8262 33238  41500   19.9 

Less than $35,000 7118 41542  48660   14.6 

Less than $50,000 6462 54855  61317   10.5 

Less than $75,000 4587 60477  65064   7.0  

 $75,000 or more  4056 111658 115714  3.5  
-------------------------------------------

Table: Fig. 2-1 - Summary Statistics for If Respondent Could Afford to See Dr. Because Of Cost Grouped by Income Level

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;This summary table illustrates an association in the data between income level and whether one could afford to see the doctor. Since the proportion of each income level that could not afford to see the doctor decreases as income level increases, lower income is associated with a reduction in ability to afford to see a doctor.

<br>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;However, this table does not address whether health insurance also is associated with affording to see a doctor. This part of the question is explored by splitting up the data by insurance status - `sum_ins` and `sum_no_ins`:


```r
sum_ins <- dat2 %>% 
    filter(hlthpln1 == "Yes") %>%
    group_by(income2, medcost) %>% 
    summarize(Sum = n()) %>% 
    spread(medcost, Sum) %>% 
    mutate(Sum = Yes+No, `% No` = round(No/Sum*100, digits =1))
colnames(sum_ins)[1] <- "Income Level"

pandoc.table(sum_ins, caption = "Fig. 2-2a - Have Health Coverage Only - Summary Statistics for If Respondent Could Afford to See Dr. Because Of Cost Grouped by Income Level", justify = "center")
```


-------------------------------------------
  Income Level     No   Yes    Sum    % No 
----------------- ---- ------ ------ ------
Less than $10,000 3464 15178  18642   18.6 

Less than $15,000 3681 17372  21053   17.5 

Less than $20,000 3858 22750  26608   14.5 

Less than $25,000 4205 29029  33234   12.7 

Less than $35,000 4155 37502  41657   10.0 

Less than $50,000 4289 51221  55510   7.7  

Less than $75,000 3513 58154  61667   5.7  

 $75,000 or more  3457 109492 112949  3.1  
-------------------------------------------

Table: Fig. 2-2a - Have Health Coverage Only - Summary Statistics for If Respondent Could Afford to See Dr. Because Of Cost Grouped by Income Level


```r
sum_no_ins <- dat2 %>% 
    filter(hlthpln1 == "No") %>%
    group_by(income2, medcost) %>% 
    summarize(Sum = n()) %>% 
    spread(medcost, Sum) %>% 
    mutate(Sum = Yes+No, `% No` = round(No/Sum*100, digits =1))
colnames(sum_no_ins)[1] <- "Income Level"

pandoc.table(sum_no_ins, caption = "Fig. 2-2b - Do Not Have Health Coverage Only - Summary Statistics for If Respondent Could Afford to See Dr. Because Of Cost Grouped by Income Level", justify = "center")
```


-----------------------------------------
  Income Level     No   Yes   Sum   % No 
----------------- ---- ----- ----- ------
Less than $10,000 3785 2738  6523   58.0 

Less than $15,000 3123 2410  5533   56.4 

Less than $20,000 4189 3839  8028   52.2 

Less than $25,000 4057 4209  8266   49.1 

Less than $35,000 2963 4040  7003   42.3 

Less than $50,000 2173 3634  5807   37.4 

Less than $75,000 1074 2323  3397   31.6 

 $75,000 or more  599  2166  2765   21.7 
-----------------------------------------

Table: Fig. 2-2b - Do Not Have Health Coverage Only - Summary Statistics for If Respondent Could Afford to See Dr. Because Of Cost Grouped by Income Level

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Considering these two tables, Figures 2-2a and 2-2b, there is a substantial association between having health care coverage and whether the US adult can afford to see the doctor by income level. For instance, for the poorest income group, 18.6% of respondents with health coverage claimed they could not afford to see the doctor whereas 58.0% of the poorest respondents without health coverage claimed they could not afford to see the doctor. This wide gap in affordability based on whether one has health coverage holds for all income groups. For example, 3.1% of the richest respondents with health coverage claimed they could not afford to see the doctor whereas 21.7%% of the richest respondents without health insurance claimed they could not afford to see the doctor.

<br>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;All of the relationships identified so far using the summary tables can be visualized in one facetted proportional barplot:

```r
colnames(dat2)[1] <- "Have Any Health Care Coverage?"
ggplot(dat2, aes(x= income2, y = 1, fill = medcost)) + 
    geom_bar(stat="identity", position = "fill") + 
    facet_grid(`Have Any Health Care Coverage?`~., labeller = label_both) + 
    scale_fill_manual(values = rainbow_hcl(2)) +
    scale_y_continuous(labels = c("0%", "25%", "50%", "75%", "100%")) +
    labs(title = "Proportion of Respondents Who Could Afford to See Doctor by \n Income Category and Whether They Have Any Health Care Coverage", fill = "Could Afford Dr.?",x = "Respondent's Income Category", y = "% Respondents who Could Afford to See Doctor") +
    theme(axis.text.x = element_text(angle = 60, hjust = 1), plot.title = element_text(hjust = 0.5))
```

<img src="Duke01_project_files/figure-html/Plot 2-1-1.png" style="display: block; margin: auto;" />
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;By plotting the proportion that could afford to see the doctor and facceting by whether the respondent has health coverage, the assocaition between income and whether the respondent could afford to see the doctor is illustrated. Lower income respondents proportionally struggle more to afford to see a doctor compared to higher income respondents. In addition, the association between having health care coverage and whether one could afford to see the doctor is also shown. Having health insurance is associated with a higher proportion of respondents who could afford to see the doctor for every income group.

<br>

**Summary**

<i>Initial Question:</i> Are a having a lower reported <u>income</u> and <u>whether one has health insurance</u> associated with a <u>difficulty in affording to see a doctor</u>?

<i>Narrative from the Exploratory Analysis:</i> Yes, having a lower income is associated with an inability to afford to see the doctor. Having health insurance is associated with being better able to afford to see the doctor, so, for instance, being poor and not having health insurance is associated with a much greater difficulty in affording health care.

* * *

**Research Question 3:**

Are a US adult's <u>smoking habits</u> and their <u>age group</u> associated with the <u>number of days they reported where their physical health was not good</u>?

<br>

**Exploratory Data Analysis for Question 3**

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;The complete cases were found for the desired variables using allprep, then the smoker variable was revalued using plyr to remove unnecessary words and shorten the factor level name length- `dat3`:


```r
dat3 <- allprep(X_smoker3,X_age_g, physhlth) 
dat3$X_smoker3 <- plyr::revalue(dat3$X_smoker3, c("Current smoker - now smokes every day" = "Smokes every day", "Current smoker - now smokes some days" = "Smokes some days"))
    
pandoc.table(head(dat3), caption = "Fig. 3-0 - Observations 1 to 6 of 466130 for output data frame `dat3`...", justify = "center")
```


-------------------------------------------
   X_smoker3         X_age_g      physhlth 
---------------- --------------- ----------
 Former smoker    Age 55 to 64       30    

  Never smoked    Age 45 to 54       0     

Smokes some days  Age 55 to 64       3     

  Never smoked    Age 55 to 64       2     

 Former smoker   Age 65 or older     10    

  Never smoked    Age 45 to 54       0     
-------------------------------------------

Table: Fig. 3-0 - Observations 1 to 6 of 466130 for output data frame `dat3`...

<br>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;To determine if there is an association between smoking habits and the number of days where physical health was not good, the summary statistics for measures of center and measures of the spread were determined. The skew of the data was also determined by comparing the mean and median - `sum1`:

```r
sum1 <- dat3 %>% 
    group_by(X_age_g, X_smoker3) %>% 
    summarize(Q1 = quantile(physhlth, 0.25), MEAN = mean(physhlth), MEDIAN = median(physhlth),Q3 = quantile(physhlth, 0.75), IQR = IQR(physhlth), STDEV = sd(physhlth)) %>%
    mutate(SKEW = ifelse(MEAN > MEDIAN, "RIGHT", "LEFT"))
                                                                                                                                                    
pandoc.table(sum1, caption = "Fig. 3-1 - Summary Statistics for Joint Pain Grouped by Physical Activity Level", justify = "center")
```


-----------------------------------------------------------------------------------
    X_age_g        X_smoker3      Q1    MEAN    MEDIAN   Q3   IQR    STDEV    SKEW 
--------------- ---------------- ---- -------- -------- ---- ----- --------- ------
 Age 18 to 24   Smokes every day  0   3.271472    0      3     3   6.886953  RIGHT 

 Age 18 to 24   Smokes some days  0   2.738674    0      2     2   6.228973  RIGHT 

 Age 18 to 24    Former smoker    0   2.323664    0      2     2   5.658571  RIGHT 

 Age 18 to 24     Never smoked    0   1.888079    0      2     2   4.901121  RIGHT 

 Age 25 to 34   Smokes every day  0   3.996077    0      3     3   8.045801  RIGHT 

 Age 25 to 34   Smokes some days  0   3.330046    0      2     2   7.464691  RIGHT 

 Age 25 to 34    Former smoker    0   2.606988    0      2     2   6.405241  RIGHT 

 Age 25 to 34     Never smoked    0   1.931530    0      1     1   5.248064  RIGHT 

 Age 35 to 44   Smokes every day  0   5.385521    0      5     5   9.595131  RIGHT 

 Age 35 to 44   Smokes some days  0   5.052188    0      5     5   9.234209  RIGHT 

 Age 35 to 44    Former smoker    0   3.305841    0      2     2   7.511720  RIGHT 

 Age 35 to 44     Never smoked    0   2.410978    0      2     2   6.203203  RIGHT 

 Age 45 to 54   Smokes every day  0   7.266624    0      10   10   11.104729 RIGHT 

 Age 45 to 54   Smokes some days  0   7.583626    0      14   14   11.124207 RIGHT 

 Age 45 to 54    Former smoker    0   4.838517    0      4     4   9.332252  RIGHT 

 Age 45 to 54     Never smoked    0   3.225416    0      2     2   7.529106  RIGHT 

 Age 55 to 64   Smokes every day  0   7.889429    0      15   15   11.626661 RIGHT 

 Age 55 to 64   Smokes some days  0   8.651601    1      15   15   11.831549 RIGHT 

 Age 55 to 64    Former smoker    0   5.477805    0      5     5   9.946308  RIGHT 

 Age 55 to 64     Never smoked    0   3.979024    0      3     3   8.464195  RIGHT 

Age 65 or older Smokes every day  0   6.715234    0      10   10   10.985567 RIGHT 

Age 65 or older Smokes some days  0   6.794562    0      10   10   10.919490 RIGHT 

Age 65 or older  Former smoker    0   5.483898    0      5     5   9.933791  RIGHT 

Age 65 or older   Never smoked    0   4.675611    0      4     4   9.216045  RIGHT 
-----------------------------------------------------------------------------------

Table: Fig. 3-1 - Summary Statistics for Joint Pain Grouped by Physical Activity Level

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;These summary statistics indicate that at least 50% of the respondents of almost all of the smoking habit and age group combinations report zero days with physical health that was not good. The distribution for each category is heavily right-skewed, especially given that the possible maximum value for physhlth is 30. The mean and Q3 give a sense as to some differences between each category. The mean number of days with poor physical health for the two current smoking categories are generally higher than the mean days of poor physical health of the former smokers and the never smoked category. Moreover, the mean number of days with poor physical health for each age group increases as age increases. 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;These measures suggest some association between whether a person smokes and how many days of poor physical health they have during the month. Current smokers tend to have higher number of poor physical health days. In addition, the measures indicate some association between a respondent's age and how many days of poor physical health they may have during month. Older age groups tend to have a higher number of poor physical health days.  

<br>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;However, so many respondents reported zero days, there may be some hidden information or clearer associations if respondents who reported zero are excluded.  One can check with another table - `sum2`:


```r
sum2 <- dat3 %>% 
    group_by(physhlth) %>% 
    summarize(n = n())
total <- sum(sum2$n)
sum2 <- sum2 %>% mutate(`% of total` = round(n/total*100,digits=1))
                                                                                                                                                    
pandoc.table(sum2, caption = "Fig. 3-2 - Summary Statistics for Joint Pain Grouped by Physical Activity Level", justify = "center")
```


------------------------------
 physhlth    n     % of total 
---------- ------ ------------
    0      294532     63.2    

    1      19591      4.2     

    2      26065      5.6     

    3      15446      3.3     

    4       8149      1.7     

    5      13789      3.0     

    6       2348      0.5     

    7       8798      1.9     

    8       1500      0.3     

    9       340       0.1     

    10     10271      2.2     

    11      116       0.0     

    12      988       0.2     

    13      136       0.0     

    14      5011      1.1     

    15      9274      2.0     

    16      252       0.1     

    17      172       0.0     

    18      307       0.1     

    19       56       0.0     

    20      6033      1.3     

    21      1237      0.3     

    22      125       0.0     

    23       98       0.0     

    24      132       0.0     

    25      2435      0.5     

    26      135       0.0     

    27      237       0.1     

    28      934       0.2     

    29      433       0.1     

    30     37190      8.0     
------------------------------

Table: Fig. 3-2 - Summary Statistics for Joint Pain Grouped by Physical Activity Level

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sure enough, 63.2% of all respondents reported zero days of poor health. The data is heavily skewed with the inclusion of zero values as most people do not report being phyiscally ill. 

<br>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;One can consider the summary statistics of the data without the zero value to see if any other associations emerge - `sum3`: 


```r
sum3 <- dat3 %>% 
    filter(physhlth != 0) %>%
    group_by(X_age_g, X_smoker3) %>% 
    summarize(Q1 = quantile(physhlth, 0.25), MEAN = mean(physhlth), MEDIAN = median(physhlth),Q3 = quantile(physhlth, 0.75), IQR = IQR(physhlth), STDEV = sd(physhlth)) %>%
    mutate(SKEW = ifelse(MEAN > MEDIAN, "RIGHT", "LEFT"))

pandoc.table(sum3, caption = "Fig. 3-3 - Summary Statistics for Joint Pain Grouped by Physical Activity Level", justify = "center")
```


------------------------------------------------------------------------------------
    X_age_g        X_smoker3      Q1    MEAN     MEDIAN   Q3   IQR    STDEV    SKEW 
--------------- ---------------- ---- --------- -------- ---- ----- --------- ------
 Age 18 to 24   Smokes every day  2   8.518371     5      10    8   8.878637  RIGHT 

 Age 18 to 24   Smokes some days  2   7.236496     4      8     6   8.367796  RIGHT 

 Age 18 to 24    Former smoker    2   6.825112     3      7     5   7.960657  RIGHT 

 Age 18 to 24     Never smoked    2   5.792102     3      7     5   7.147141  RIGHT 

 Age 25 to 34   Smokes every day  2   10.248826    5      15   13   10.097335 RIGHT 

 Age 25 to 34   Smokes some days  2   9.215088     5      14   12   10.000014 RIGHT 

 Age 25 to 34    Former smoker    2   7.899963     4      10    8   9.084350  RIGHT 

 Age 25 to 34     Never smoked    2   6.489041     3      7     5   7.934681  RIGHT 

 Age 35 to 44   Smokes every day  3   12.732196    8      25   22   11.141351 RIGHT 

 Age 35 to 44   Smokes some days  3   12.129183    7      20   17   10.904305 RIGHT 

 Age 35 to 44    Former smoker    2   9.462940     5      15   13   10.161996 RIGHT 

 Age 35 to 44     Never smoked    2   7.577574     3      10    8   9.043965  RIGHT 

 Age 45 to 54   Smokes every day  3   15.294502    14     30   27   11.694803 RIGHT 

 Age 45 to 54   Smokes some days  4   15.430747    15     30   26   11.432934 RIGHT 

 Age 45 to 54    Former smoker    2   12.238213    7      25   23   11.389845 RIGHT 

 Age 45 to 54     Never smoked    2   9.684665     5      15   13   10.375837 RIGHT 

 Age 55 to 64   Smokes every day  5   16.652090    15     30   25   11.806978 RIGHT 

 Age 55 to 64   Smokes some days  5   17.021820    15     30   25   11.529917 RIGHT 

 Age 55 to 64    Former smoker    3   13.593807    10     30   27   11.626659 RIGHT 

 Age 55 to 64     Never smoked    2   11.265709    5      20   18   10.988756 RIGHT 

Age 65 or older Smokes every day  4   16.302046    15     30   26   11.691505 RIGHT 

Age 65 or older Smokes some days  4   15.757325    14     30   26   11.631876 RIGHT 

Age 65 or older  Former smoker    3   14.041458    10     30   27   11.511313 RIGHT 

Age 65 or older   Never smoked    3   13.120730    8      30   27   11.293449 RIGHT 
------------------------------------------------------------------------------------

Table: Fig. 3-3 - Summary Statistics for Joint Pain Grouped by Physical Activity Level

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Excluding the respondents who had zero days of poor physical health, a similar association pattern appears as before, but, now, unique median values are present. The two categories of current smokers have higher mean and median values than for former smokers and those who never smoked. The large IQR values for many of the categories indicate there is a great spread of values considering both this version of the data and the data that includes zero values. This large spread of values means there is a lot of overlap between the densities of each category, which limits the strength of any association drawn from the data. 

<br>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Both the associations and the large spread and overlap in the data can be visualized with multiple boxplots:


```r
no0_dat3 <- dat3 %>% filter(physhlth != 0)
ggplot(no0_dat3, aes(x = X_age_g, y = physhlth, fill = X_smoker3)) + 
    geom_boxplot() +
    scale_fill_manual(values = heat_hcl(6, h = c(20,-80), l = c(75,40), c =c(40,80), power = 1)) +
    labs(title = "Boxplot of Days Respondent's Physical Health Was Not Good \n by Smoking Habit and Age ", fill = "Respondent's Smoking Habit",x = "Age Group", y = "Number Of Days Physical Health Was Not Good") +
    theme(plot.title = element_text(hjust = 0.5))
```

<img src="Duke01_project_files/figure-html/Plot 3-1-1.png" style="display: block; margin: auto;" />
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;These boxplots display a version of the same information as the table above, except the mean and sd values are not plotted. The boxplots give a sense of the large IQR or spread in the data, especially for the older age groups indicating that the distribution of days with poor physical health ranges over almost all the possible options from 1-30 in these cases. The IQR also shows how some of these categories have large overlaps in their distribution, which reduces the strength of any association in the data as this means some of the distributions are not significantly distinct. In addition, the medians generally show the decreasing number of poor physical health days both as one is younger and does not smoke.



<br>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;The final plot of the data shows the averages of each category:

```r
ggplot(sum3, aes(x = X_smoker3, y = MEAN, col = X_age_g, group = X_age_g)) + 
    geom_line(lwd=2) +
    scale_y_continuous(breaks = c(5:20)) +
    scale_color_manual(values = heat_hcl(6, h = c(20,-80), l = c(75,40), c =c(40,80), power = 1)) +
    labs(title = "Average Number Of Days Respondent's Physical Health Was Not Good \n by Smoking Habit and Age ", color = "Age",x = "Respondent's Smoking Habit", y = "Average Number Of Days Physical Health Was Not Good") +
    theme(plot.title = element_text(hjust = 0.5))
```

<img src="Duke01_project_files/figure-html/Plot 3-2-1.png" style="display: block; margin: auto;" />

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;The boxplot analysis supports an association between increased smoking and an increase in the number of days where physical health was not good. In addition, this association is magnified when considering the respondent's age as greater age is related with a greater number of days with poor physical health. However, there is much overlap in the data and many members of each group report poor physical health days that are shared with many other individuals in other categories. This overlap weakens the association drawn between smoking habits and poor physical health days. 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;However, one may accept this limitation and focus instead on the upper half of the distribution of reported days for each category. Since the mean is sensitive to outlying values, it also picks up on the differences in the upper end of the number of days reported for each category. One may phrase this association as, if a respondent reports a higher number of days were their physical health is not good and they are older there is an association with much higher numbers of poor physical health days if they smoke. This relationship is a version of if it rains it pours. One may expect that if they do smoke, yes, they may fall in that lower half of possible values for their group and not have many poor physical health days, but, if they fall in the upper half, they are much more likely to have more days of poor physical days if they smoke and are older when compared to other smoking habits and ages.

<br>

**Summary**

<i>Initial Question:</i> Are a US adult's <u>smoking habits</u> and their <u>age group</u> associated with the <u>number of days they reported where their physical health was not good</u>?

<i>Narrative from the Exploratory Analysis:</i> Yes, smoking and older age are associated with a greater number of days where physical health was not good. This association is relatively weak due to the great overlap in the data. However, especially in the upper half of the data for each category, smoking and old age is clearly associated with more days of poor physical health, and, thus, there is evidence for an association between high amounts of poor health days, smoking, and age. Thus, an older smoker who has a high number of days where physical health is not good is expected to have a much greater number of poor health days than a young individual who never smoked and has a high number of poor physical health days relative to their group.


* * *

### Submission by Connor Lenio. Email: cojamalo@gmail.com
Completion Date: Mar 29, 2017
