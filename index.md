# Data analysis on admission rates of universities in the United States

## Introduction

Many colleges and universities in the United States are highly ranked all over the world with good reputation. Their great education quality attracts a lot of students and stakeholders. One important point most people care about is the admission rate of American colleges and universities. Admission rate varies a lot from one institution to another. Many factors may contribute to this such as the average cost of attendance, the work of faculty members, even the background information of the students. The aim of this study is primarily to construct a model used for description and understanding of the factors that would affect admission rates. In addition, the model built is expected to have good prediction ability and to be interpretable enough as well. 

## Data Overview

The data was collected by the College Scorecard and the data set used in this study contains 30 variables. Following is a classification and introduction to the variables. 
*	Response variable: admission rate (ADM_RATE)
*	School identifiers: unit ID, name, state postcode, region
*	School characteristics (indicator variables): control (CONTROL), tribal (TRIBAL), historically black (HBCU), predominately black (PBI), Hispanic-serving (HSI), women-only (WOMENONLY)
*	School characteristics (numerical variables): number of branches (NUMBRANCH), average cost (COSTT4_A), average faculty salary (AVGFACSAL), full-time faculty members proportion (PFTFAC)
*	Student characteristics: percentage of students receiving Pell grant (PECTPELL), percentage of students aged 25 or above (UG25ABV), percentage of aided students from lower income family (INC_PCT_LO), percentage of first-generation students (PAR_ED_PCT_1STGEN), proportion of female (FEMALE), students’ median family income (MD_FAMINC), percentage of White/Black/Asian/Hispanic/US-born people in student’s home neighbourhood (PCT_WHITE/ PCT_BLACK/ PCT_ASIAN/PCT_HISPANIC/PCT_BORN_US), percentage of the population with a bachelor’s/professional degree in student’s home neighbourhood (PCT_BA/PCT_GRAD_PROF), poverty/unemployment rate (POVERTY_RATE/ UNEMP_RATE) (according to students’ zip code)

## Statistical Methods

### Irrelevant variable removal

Before variable selection for the model, it is appropriate to perform some data cleaning, eliminating some irrelevant variables. Firstly, the school identifiers only contain some basic information of the colleges and universities such as unit ID and name, which does not contribute to the prediction of admission rate and should be removed. Secondly, in terms of the school characteristics, a few of them are indicator variables as listed above. I perform a hypothesis test to each of those indicator variables to see whether it has a significant linear relationship with the admission rate. Since the “control” variable has three values (1 for public, 2 for private non-profit, 3 for private profit), I create two separate indicator variables for it, which are “Non-profit” and “Profit” (both 0 for public). The p-values of the F statistics are listed below.



Indicator |	P value
------------ | -------------
Non-profit (private non-profit)	| 1.164659e-11
Profit (private profit)	| 0.0135966
HBCU | 0.0391931
TRIBAL	| 0.2365976
WOMENONLY |	0.2668243
PBI |	0.8529052
HIS | 0.9953614






According to the linear test, only the private non-profit indicator shows a significant linear relationship with admission rate. This means the difference in admission rate between public/private-profit and private non-profit institutions is significant, while others are not. Thus, it is appropriate to remove those non-significant indicator variables. 
In addition, for the remaining numerical variables, I also apply a linear test to them. Based on the conditional relationship among predictors, however, keeping most of them first before further selection is suggested. Therefore, I only remove several variables that show strong non-significant linear relationship with admission rate, including percentage of aided students from lower income family (with p-value 0.1433), poverty rate (with p-value 0.1363) and unemployment rate (with p-value 0.1249).



### Variable selection

After removing a few irrelevant variables, I obtain a data set with 17 potential predictors. I apply two model selection methods in this section. 
The general form of the expected final model would be a multiple regression model: 
```markdown
$y = \beta_0 + \beta_1 x_1 + \beta_2 x_2 + \cdot + \beta_k x_k$
```

