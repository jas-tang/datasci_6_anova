# datasci_6_anova
This is a repository for Assignment 6 in HHA507. 

# Data Preparation 

I used data from this [Link](https://archive.ics.uci.edu/dataset/296/diabetes+130-us+hospitals+for+years+1999-2008). 

First, I used Python to load in my data. 

Using the prebuilt package by UCI Machine Learning Company, we can directly bring the dataset onto the platform we are using. For my instance, I'm using  google colab.
```
pip install ucimlrepo
```
```
from ucimlrepo import fetch_ucirepo

# fetch dataset
diabetes_130_us_hospitals_for_years_1999_2008 = fetch_ucirepo(id=296)

# data (as pandas dataframes)
X = diabetes_130_us_hospitals_for_years_1999_2008.data.features
y = diabetes_130_us_hospitals_for_years_1999_2008.data.targets

# metadata
print(diabetes_130_us_hospitals_for_years_1999_2008.metadata)

# variable information
print(diabetes_130_us_hospitals_for_years_1999_2008.variables)
```

I also installed pandas as pd. 

We are answering the question, how do the race of the patient (race) and their A1C test results (A1Cresult) jointly influence the duration of their hospital stay (time_in_hospital)?

Factor 1 or our indepdent variable is Race

Factor 2 or our second independent variable is A1C test result

Then, our dependent variable is the duration of hospital stay time

Due to this, we created a subset of our data. 

```
df = df1[['race', 'A1Cresult', 'time_in_hospital']]
```

# Assumption Checks 

## Shapiro-Wilks
We first did the Shaprio-Wilks test on Python. We also attempted to do the same thing on R. 
```
groups = df.groupby(['race', 'A1Cresult'])

for (race_status, A1C_status), group_df in groups:
    _, p_value = stats.shapiro(group_df['time_in_hospital'])

    print(f"Group ({race_status}, {A1C_status}):")
    print(f"P-value from Shapiro-Wilk Test: {p_value}\n")
```
This was our result for Python. 
```
Group (AfricanAmerican, >7):
P-value from Shapiro-Wilk Test: 1.8906119489850806e-16

Group (AfricanAmerican, >8):
P-value from Shapiro-Wilk Test: 6.7359146155303186e-34

Group (AfricanAmerican, None):
P-value from Shapiro-Wilk Test: 0.0

Group (AfricanAmerican, Norm):
P-value from Shapiro-Wilk Test: 2.9696700824519264e-25

Group (Asian, >7):
P-value from Shapiro-Wilk Test: 0.0023467366117984056

Group (Asian, >8):
P-value from Shapiro-Wilk Test: 2.233798113593366e-05

Group (Asian, None):
P-value from Shapiro-Wilk Test: 2.6558121925662052e-21

Group (Asian, Norm):
P-value from Shapiro-Wilk Test: 0.0007482930086553097

Group (Caucasian, >7):
P-value from Shapiro-Wilk Test: 1.7925673399754353e-38

Group (Caucasian, >8):
P-value from Shapiro-Wilk Test: 0.0

Group (Caucasian, None):
P-value from Shapiro-Wilk Test: 0.0

Group (Caucasian, Norm):
P-value from Shapiro-Wilk Test: 1.930148504761003e-41

Group (Hispanic, >7):
P-value from Shapiro-Wilk Test: 2.1721559733123286e-06

Group (Hispanic, >8):
P-value from Shapiro-Wilk Test: 1.0855431137324345e-12

Group (Hispanic, None):
P-value from Shapiro-Wilk Test: 1.793864359413239e-35

Group (Hispanic, Norm):
P-value from Shapiro-Wilk Test: 4.4957931777389604e-07

Group (Other, >7):
P-value from Shapiro-Wilk Test: 1.5921328667900525e-05

Group (Other, >8):
P-value from Shapiro-Wilk Test: 1.617614514559662e-10

Group (Other, None):
P-value from Shapiro-Wilk Test: 2.446808042197051e-30

Group (Other, Norm):
P-value from Shapiro-Wilk Test: 0.00019315516692586243
```
Out of 20 combinations, 5 are non-normal, but the other 15 are normally distributed as their P-Value is greater than 0.05.

For R, we attempted to do the same thing. We first created a list. 
```
combinations <- list(
  list(Race_status = 'Caucasian', A1C_Status = 'None'), 
  list(Race_status = 'AfricanAmerican', A1C_Status = 'None'), 
  list(Race_status = 'nan', A1C_Status = 'None'), 
  list(Race_status = 'Other', A1C_Status = 'None'), 
  list(Race_status = 'Asian', A1C_Status = 'None'), 
  list(Race_status = 'Hispanic', A1C_Status = 'None'), 

  list(Race_status = 'Caucasian', A1C_Status = '>7'), 
  list(Race_status = 'AfricanAmerican', A1C_Status = '>7'), 
  list(Race_status = 'nan', A1C_Status = '>7'), 
  list(Race_status = 'Other', A1C_Status = '>7'), 
  list(Race_status = 'Asian', A1C_Status = '>7'), 
  list(Race_status = 'Hispanic', A1C_Status = '>7'), 
  
  list(Race_status = 'Caucasian', A1C_Status = '>8'), 
  list(Race_status = 'AfricanAmerican', A1C_Status = '>8'), 
  list(Race_status = 'nan', A1C_Status = '>8'), 
  list(Race_status = 'Other', A1C_Status = '>8'), 
  list(Race_status = 'Asian', A1C_Status = '>8'), 
  list(Race_status = 'Hispanic', A1C_Status = '>8'), 

  list(Race_status = 'Caucasian', A1C_Status = 'Norm'), 
  list(Race_status = 'AfricanAmerican', A1C_Status = 'Norm'), 
  list(Race_status = 'nan', A1C_Status = 'Norm'), 
  list(Race_status = 'Other', A1C_Status = 'Norm'), 
  list(Race_status = 'Asian', A1C_Status = 'Norm'), 
  list(Race_status = 'Hispanic', A1C_Status = 'Norm')
```
Then we ran a for loop checking for means. 
```
for (combination in combinations) {
  subset <- df[df$Race_status == combination$Race_status & df$A1C_Status == combination$A1C_Status, ]
  test_result <- shapiro.test(df$time_in_hospital)
  cat("Group (", combination$Race_status, ", ", combination$A1C_Status, "):\n", sep = "")
  cat("P-value from Shapiro-Wilk Test:", test_result$p.value, "\n\n")
}
```
However, I am unable to perform this test as the sample size is too large. The test calls for 5000 or less data types. 

## Levene's Test
For the Levene's test, I also listed out every combination. 
```
statistic, p_value = stats.levene(
    df['time_in_hospital'][df['race'] == 'Caucasian'][df['A1Cresult'] == 'None'],
    df['time_in_hospital'][df['race'] == 'AfricanAmerican'][df['A1Cresult'] == 'None'],

    df['time_in_hospital'][df['race'] == 'Other'][df['A1Cresult'] == 'None'],
    df['time_in_hospital'][df['race'] == 'Asian'][df['A1Cresult'] == 'None'],
    df['time_in_hospital'][df['race'] == 'Hispanic'][df['A1Cresult'] == 'None'],

    df['time_in_hospital'][df['race'] == 'Caucasian'][df['A1Cresult'] == '>7'],
    df['time_in_hospital'][df['race'] == 'AfricanAmerican'][df['A1Cresult'] == '>7'],

    df['time_in_hospital'][df['race'] == 'Other'][df['A1Cresult'] == '>7'],
    df['time_in_hospital'][df['race'] == 'Asian'][df['A1Cresult'] == '>7'],
    df['time_in_hospital'][df['race'] == 'Hispanic'][df['A1Cresult'] == '>7'],

    df['time_in_hospital'][df['race'] == 'Caucasian'][df['A1Cresult'] == '>8'],
    df['time_in_hospital'][df['race'] == 'AfricanAmerican'][df['A1Cresult'] == '>8'],

    df['time_in_hospital'][df['race'] == 'Other'][df['A1Cresult'] == '>8'],
    df['time_in_hospital'][df['race'] == 'Asian'][df['A1Cresult'] == '>8'],
    df['time_in_hospital'][df['race'] == 'Hispanic'][df['A1Cresult'] == '>8'],

    df['time_in_hospital'][df['race'] == 'Caucasian'][df['A1Cresult'] == 'Norm'],
    df['time_in_hospital'][df['race'] == 'AfricanAmerican'][df['A1Cresult'] == 'Norm'],

    df['time_in_hospital'][df['race'] == 'Other'][df['A1Cresult'] == 'Norm'],
    df['time_in_hospital'][df['race'] == 'Asian'][df['A1Cresult'] == 'Norm'],
    df['time_in_hospital'][df['race'] == 'Hispanic'][df['A1Cresult'] == 'Norm'],

)

print(f"Levenes Test P-value: {p_value} \n")
```
However, I was forced to remove the "nan" values for race or else the test wouldn't work. This may result in inaccurate results. 
```
Levenes Test P-value: 9.580811219790571e-21
```
This says that there is significant difference, but I am unsure as to how accurate that is. 

In R, we faced similar issues.
```
install.packages("lawstat")
library(lawstat)

df_cleaned <- na.omit(df)

#Levene's Test
result <- levene.test(df_cleaned$time_in_hospital, interaction(df_cleaned$A1Cresult, df_cleaned$race, df_cleaned$time_in_hospital))
result
```
In this case, I tried to remove/clean the data entirely by removing the NaN values or NA values (R). However, I was not able to produce a result.
```
Modified robust Brown-Forsythe Levene-type test based on the absolute
	deviations from the median

data:  df_cleaned$time_in_hospital
Test Statistic = NaN, p-value = NA
```
This could be due to outliers insufficient data, or that it is not normally distributed.

# Conduct the ANOVA
## ANOVA
First, I performed the Two-Way ANOVA test on Python.
```
import statsmodels.api as sm
from statsmodels.formula.api import ols

model = ols('time_in_hospital ~ C(race) * C(A1Cresult)', data=df).fit()

anova_table = sm.stats.anova_lm(model, typ=2)
print(anova_table)
```
This was the result. 
```
]
anova_table = sm.stats.anova_lm(model, typ=2)
print(anova_table)
output
                             sum_sq       df           F        PR(>F)
C(race)                  641.735130      4.0   18.075566  7.501203e-15
C(A1Cresult)            3895.231358      3.0  146.287793  1.363867e-94
C(race):C(A1Cresult)     276.823623     12.0    2.599070  1.845652e-03
Residual              882895.150091  99473.0         NaN           NaN
```
Looking at the F-Values, they are all significantly different.

* The means of the different races are all significantly different among each other for their time spent in a hospital.
* The means of the difference A1C test for diabetes differ significantly among each other.
* The means of the different races with different A1C test results are significantly different.

Summarizing the results tells us that there is significant difference in means. 
```
import statsmodels.stats.multicomp as mc

stats.f_oneway(
    df['time_in_hospital'][df['race'] == 'Caucasian'][df['A1Cresult'] == 'None'],
    df['time_in_hospital'][df['race'] == 'AfricanAmerican'][df['A1Cresult'] == 'None'],

    df['time_in_hospital'][df['race'] == 'Other'][df['A1Cresult'] == 'None'],
    df['time_in_hospital'][df['race'] == 'Asian'][df['A1Cresult'] == 'None'],
    df['time_in_hospital'][df['race'] == 'Hispanic'][df['A1Cresult'] == 'None'],

    df['time_in_hospital'][df['race'] == 'Caucasian'][df['A1Cresult'] == '>7'],
    df['time_in_hospital'][df['race'] == 'AfricanAmerican'][df['A1Cresult'] == '>7'],

    df['time_in_hospital'][df['race'] == 'Other'][df['A1Cresult'] == '>7'],
    df['time_in_hospital'][df['race'] == 'Asian'][df['A1Cresult'] == '>7'],
    df['time_in_hospital'][df['race'] == 'Hispanic'][df['A1Cresult'] == '>7'],

    df['time_in_hospital'][df['race'] == 'Caucasian'][df['A1Cresult'] == '>8'],
    df['time_in_hospital'][df['race'] == 'AfricanAmerican'][df['A1Cresult'] == '>8'],

    df['time_in_hospital'][df['race'] == 'Other'][df['A1Cresult'] == '>8'],
    df['time_in_hospital'][df['race'] == 'Asian'][df['A1Cresult'] == '>8'],
    df['time_in_hospital'][df['race'] == 'Hispanic'][df['A1Cresult'] == '>8'],

    df['time_in_hospital'][df['race'] == 'Caucasian'][df['A1Cresult'] == 'Norm'],
    df['time_in_hospital'][df['race'] == 'AfricanAmerican'][df['A1Cresult'] == 'Norm'],

    df['time_in_hospital'][df['race'] == 'Other'][df['A1Cresult'] == 'Norm'],
    df['time_in_hospital'][df['race'] == 'Asian'][df['A1Cresult'] == 'Norm'],
    df['time_in_hospital'][df['race'] == 'Hispanic'][df['A1Cresult'] == 'Norm'],)
```
```
F_onewayResult(statistic=28.317809359621652, pvalue=1.127610700710152e-101)
```

The ANOVA test on R showed similar results.
```
model = aov(time_in_hospital ~ race * A1Cresult, data=df)
summary(model)
```
```
                  Df Sum Sq Mean Sq F value   Pr(>F)    
race                5    630   126.0  14.210 6.26e-14 ***
A1Cresult           3   4035  1345.0 151.736  < 2e-16 ***
race:A1Cresult     15    318    21.2   2.393  0.00184 ** 
Residuals      101742 901832     8.9                     
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
```
We see that the interaction of race:A1Cresult p-value of .0018 is exactly the same. The means of the different races with different A1C test results are significantly different.

While the values of the otehr two are different, they are both still less than .05. Which says that the means of the different races are all significantly different among each other for their time spent in a hospital and the means of the difference A1C test for diabetes differ significantly among each other.

## Tukey HSD
My attempt on Python wwas not fruitful. 

We wanted to run a PostHoc test
```
comp = mc.MultiComparison(df['race'], df['A1Cresult'])
post_hoc_res = comp.tukeyhsd()
post_hoc_res.summary()
```
However, the error we received says that it wants a float variable as the input.
```
Cannot cast array data from dtype('O') to dtype('float64') according to the rule 'safe'
```
Using numpy, I attempted to change the varaible types into floats.
```
numpy.array(df,dtype='float64')
```
However, this can't be allowewd as strings cant turn into floats. 

On R, I was able to run a TukeyHSD. 
```
TukeyHSD(model)
```
The result was big due to the amount of combinations and can be seen on my R Notebook. 

Groups that have significant Differences:

Races

* AfricanAmerican
* Asian-AfricanAmerican
* Caucasian-AfricanAmerican
* Hispanic-AfricanAmerican
* Other-AfricanAmerican
* Caucasian-Asian
* Hispanic-Caucasian
  
A1C Result

* None->7
* None->8
* Norm-None
