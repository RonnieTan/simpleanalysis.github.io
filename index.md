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
The general form of the expected final model would be a multiple regression model.


### Method 1. Automated stepwise selection starting with the full model
This method begins with the full model with all the predictors and uses Bayesian Information Criterion (BIC) as the selection standard, which has a relatively strong penalty term for additional predictors. It considers adding or deleting one predictor at a time in a way that the resulting model has the lowest BIC in each intermediate step. 

The model obtained by this method is: 
ADM_RATE ~ NUMBRANCH + COSTT4_A + AVGFACSAL + PFTFAC + PAR_ED_PCT_1STGEN + FEMALE + MD_FAMINC + PCT_BLACK + PCT_HISPANIC + PCT_BA + PCT_GRAD_PROF + Non-profit

### Method 2. Automated stepwise selection starting with no predictor
I also apply the same selection method starting with no predictor in an opposite direction to see if there is any difference in the resulting model. BIC is still the information criterion in the selection process. 

The model obtained starting with no predictor is:
ADM_RATE ~ AVGFACSAL + Non-profit + FEMALE + PCT_HISPANIC + PFTFAC + NUMBRANCH + PCT_BLACK + COSTT4_A + MD_FAMINC

Compared with the previous model, the model obtained by method 2 has three predictors dropped, which are PAR_ED_PCT_1STGEN, PCT_BA and PCT_GRAD_PROF. Reviewing the statistics in the last step of method 1 below, it is worth to notice that the increase in BIC when dropping those three variables are quite small (only 0.7, 2.5 and 0.6 correspondingly). Therefore, it is reasonable to choose the reduced model for simplicity. 

![](https://ronnietan.github.io/simpleanalysis.github.io/1.png)

In addition, this model is also the optimal model with 9 predictors obtained by All Possible Subsets method, which selects the model with the highest R square for each subset size. 


### Box-Cox transformation
In order to correct the non-linearity between the predictors and admission rate, I apply the Box-Cox power transformation to the model obtained above. 

As calculated by R, it is suggested to apply a transformation with power lambda equals -6 to variable NUMBRANCH and apply a log transformation to variable PCT_BLACK.  Thus, the model after the power transformation is:

ADM_RATE ~ AVGFACSAL + Non-profit + FEMALE + PCT_HISPANIC + PFTFAC + (NUMBRANCH)^(-6) + log(PCT_BLACK) + COSTT4_A + MD_FAMINC

The transformation increases the significance of those two transformed variables as well as the R square of the model to some extent. 

### Model validation
I divide the entire dataset into one training dataset (75%) and one test dataset (25%) for model validation. After constructing the model using the training dataset, I validate the model using the test dataset. Most of the variables still show high significance (with p-value < 0.05) when fitting the model using test data, except for one variable COSTT4_A (with p-value 0.168). 

I additionally check the relationship between COSTT4_A and ADM_RATE (response). The difference between the fitted lines of training data and test data is not distinct enough, so the loss of significance in this variable may due to randomly splitting of the dataset. 

![](https://ronnietan.github.io/simpleanalysis.github.io/3.png)

### Model assumptions check
In order to make sure that the constructed model satisfies the necessary properties to make good predictions, there are four model assumptions required to check. 

I plot the standard residual plot to check the first three assumptions: linearity between the response and predictors, independence of errors, constant variance of errors. Based on the plot, we can see there is no systematic pattern or obvious cluster of residuals. And the variance of residuals for each fitted value is basically constant. Therefore, these three model assumptions are satisfied.

![](https://ronnietan.github.io/simpleanalysis.github.io/4.png)



For the last model assumption, which is normality of errors, I plot the normal Q-Q plot (Appendix Figure 1). The model fits the identical line well, so normality of errors is satisfied.

I also check the additional condition that mean response is a single function of fitted value to make sure the residual analysis is reliable (Appendix Figure 2). The points are randomly scattered around the identical line, so the condition is satisfied.  

### Model diagnostics

i. multicollinearity

I calculate the Variance Inflation Factor of each predictor to check the multicollinearity among predictors. None of them is greater than 5, so the predictors are weakly correlated.

![](https://ronnietan.github.io/simpleanalysis.github.io/5.png)

ii. leverage point

There are 106 observations (7% of the dataset) having a leverage greater than two times of the average 2(p+1)/n, which are identified as leverage points.

iii. outlier

There are 52 observations (3.4% of the dataset) having an absolute value of standard residual greater than 2, and only 1 of them has an absolute value of standard residual greater than 4. This outlier is Saint Elizabeth College of Nursing, which has an abnormal admission rate of 0. This might be due to an error of data record since an estimated admission rate on its official website is around 60%. 

iv. influential point

Using Cook’s Distance as the measure of influence, there is no observation identified as an influential point, which is desired. 

## Results
### Final model summary

![](https://ronnietan.github.io/simpleanalysis.github.io/6.png)

There are nine predictors in the final model, including one indicator variable “Non-profit” and two transformed variables. The p-values of both the T-test for each predictor and the F test are small enough, which means that all the predictors are significantly related to the response. The column of “Estimate” represents the estimates of β parameters in the general form.

The R square of the model is approximate 0.23, which reflects the proportion of the variance of admission rate explained by the model. And the value is normal for this large dataset. 

### Conclusion on the process of obtaining the model
Starting with the original 30 variables, the model is reduced step by step to a final model with 9 variables. The process involves:
* Eliminate school identifiers
*	Represent CONTROL as two indicator variables “Non-profit” and “Profit”
*	Eliminate non-significant indicator variables and 3 numerical variables
*	Apply stepwise selection method starting with the full model / no predictor model
*	Compare the BIC of the resulting models and make appropriate trade off
*	Apply Box-Cox transformation to correct non-linearity

The reasoning of choices and decisions made in each step is explained in the previous section. 

### Conclusion on the goodness of the model

Based on the analysis in 3.4 – 3.6, the model satisfies necessary properties for good predictions. The goodness involves:
*	Most predictors remain significant when using test dataset to validate model
*	Four necessary model assumptions and the additional condition are satisfied
*	Problematic observations are identified and diagnosed

## Discussion
### Model interpretation

In general, the constructed model contains 9 factors/variables that could explain the variation observed in admission rates very well, including AVGFACSAL, Non-profit, FEMALE, PCT_HISPANIC, PFTFAC, NUMBRANCH, PCT_BLACK, COSTT4_A, MD_FAMINC. The estimates of β parameters in the final model could be interpreted as the expected change in admission rate for one-unit increase of the corresponding factor (after transformation) when all other factors are held fixed. For example, the estimated coefficient for “Non-profit” means the average difference in admission rate between private non-profit institutions and public/private for-profit institutions when all other factors are the same.  

Based on the analysis in the model selection process, the model is not only good enough at describing how the factors affect admission rate, but also avoids unnecessary complexity. The model is one of the models with lowest BIC and has the highest R square among 9-predictors models. Therefore, the model is appropriate to answer the research question.

### Limitations
*	The number of predictors in the final model is still not small and the Box-Cox transformation makes the model less interpretable. 
*	The stepwise selection method generally does not consider all the possible models containing subsets of the 30 variables, so the resulting model might not have the lowest BIC among all models. 
*	The assumptions of constant variance and normality of errors are not completely satisfied for extreme values from the two ends, so the prediction for institutions with admission rate lower than 40% or higher than 90% might not be accurate. 
*	Although most of the leverage points are not outliers, there are still a few might have bad influence on the estimation, which would affect the accuracy of the model.


