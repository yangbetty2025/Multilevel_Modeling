---
layout: default
---

# What are multilevel models?
Some data have a **hierarchical** or **clustered** structure. For example, children who grew up in the same households with the same parents tend to be more alike than random individuals from the same age groups. <br>
<br>
Similarly, students from the same school or neighborhoods may perform more similarly on standardized tests. In this case, individual students' test scores are considered "nested" within institutions or geographical areas. <br>
<br>
![data_hierarchy](assets/images/data_hierarchy.png)
<br>
<br>
It is also possible to construct a three-level model where Level 1 is areas of each zip code, Level 2 is neighborhoods, and Level 3 is boroughs. The illustration below uses "UHF42 neighborhoods," which are a set of 42 defined community areas in New York City, created by the NYC Department of Health and Mental Hygiene and the United Hospital Fund (UHF) for health research and planning.<br>
<br>
![three_level_structure](assets/images/three_level_structure.png)


# Why use multilevel models?
1. Nested data **violate key assumptions** of Ordinary Least Squares (OLS) in linear regression, because: <br>
* Observations are not independent <br>
* Error terms are not independent <br>
<br>

2. Some **data structures** that do not appear to be clustered can actually be. Here is an extreme example:<br>
<br>
 ![data_structure](assets/images/data_structure.png)
 <sub> Source: "A Fun Intro to Multilevel Models in R" by Fabio Votta of University of Amsterdam </sub>
 <br>
 <br>
3. Below is a list of helpful resources for getting a basic understanding on multilevel modeling: <br>
* [Multilevel Modeling: A Comprehensive Guide for Data Scientists](https://www.datacamp.com/tutorial/multilevel-modeling)<br>
* [A Fun Intro to Multilevel Models in R](https://favstats.github.io/intro_multilevel/slides/#1)<br>
* [Advanced Statistics: Multilevel Regression](https://advstats.psychstat.org/python/multilevel/index.php)
* [Centering Options and Interpretations](https://www.learn-mlms.com/08-module-8.html)<br>
<br>
<br>

# The research question and rationale 
Are Heat Vulnerability Index (HVI) and poor mental health related to the physical health of adults residing in New York City? Do those relationships differ depending on the neiborhood? <br>

In light of climate change, some neighborhoods are more vulnerable to extreme heat than others, such as urban areas with high impervious surfaces, less green and shady surfaces, fewer socioeconomic resources, and a larger population that are disproportionately impacted by heat (e.g., older adults, outdoor workers, and those with lower income). <br>

It is often observed that areas in close spatial proximity tend to share similar characteristics, thus forming "clusters/groups" and motivating researchers to conduct multilevel regressions to investigate both the within-group and between-group effects of risk factors on health outcome. <br>
<br>
<br>

# Health outcome, predictors, model structure, and datasets
In this study, the **health outcome** (Y) is the percentage of adults with **poor physical health** residing in a given zip code, while the two **predictors** (X's) are averaged **HVI** by zip code and the percentage of residents aged **65 and older** of each UHF neighborhood, both of which exhibit a hierarchical data structure in the sense that each zip code belongs to one of the five boroughs.<br>
<br>
In multilevel models, **Level 1** predictors are those that vary within a group (in this study, averaged **HVI** by zip code), whereas **Level 2** variables are characteristics of the group (in this study, the **percentage of residents aged 65 and older** of each UHF42 neighborhood).<br>
<br>
This analysis will use five datasets that have been extracted from their linked sources and preprocessed: <br>
* [Health Outcomes by Zip Code](https://datacommons.org/place/geoId/3651000?category=Health)<br>
* [Averaged Heat Vulnerability (HVI) by Zip Cod](https://a816-dohbesp.nyc.gov/IndicatorPublic/data-features/hvi/)<br>
* [Risk Factors by Zip Code](https://simplemaps.com/city/new-york/zips/age-median)<br>
* [Risk Factors by Borough](https://furmancenter.org/stateofthecity/view/citywide-and-borough-data)<br> 
* [UHF42 Neighborhood Codes](https://www.nyc.gov/assets/doh/downloads/pdf/ah/zipcodetable.pdf)
<br>
<br>

# Data visualization: scatter plots & regression lines
It is always a good idea to visualize the data before modeling to identify patterns, trends, and outliers that are not apparent in raw numbers.<br> 
<br>
In this study, an **outlier** (zip code 11005) was detected because of its extremely high median age of residents (80.6 years old). Upon further investigation, zip code 11005 contains only one senior center. This data point was removed from the analysis dataset as a result.<br>
<br>
The following unicolor scatter plot indicates a **positive** relationship between averaged **HVI** and **poor physical health**, while the multicolor scatter plot further reveals **clusters** of data points at the borough level. <br>
<br>

 ![HVI_scatterplots](assets/images/HVI_scatterplots.png)
<br>
<br>
Interestingly, the unicolor scatter plot below shows a negative relationship between percentage of **residents aged 65 and older** and **poor physical health**, which is contrary to what one might expect. There is a "clustering effect" by default, as all zip codes from the same borough share the percentage of residents aged 65 and older.<br>
<br>
![percent65plus](assets/images/percent65plus_scatterplots.png)
<br>
<br>
Conducting an OLS linear regression on the entire dataset, one would estimate a single y-intercept and slope for the effect of HVI on poor physical health for *all* residents of NYC, regardless of their borough of residence. However, when the same regression is conducted separately for each borough, five district y-intercepts and slopes emerge, supporting the use of a multilevel model.<br>
<br>
![HVI_regression_lines](assets/images/HVI_regression_lines.png)
<br>
<br>

# Data Analysis
Visualization is an intuitive way to get the big picture (pun intended) of our data, but there is a standardized measurement we can use to justify using a multilevel model. The measurement is called "intraclass correlation coefficient," or **ICC** for short. <br>
<br>

## Intraclass correlation coefficient (ICC) 
Intraclass correlation coefficient (ICC) is a statistic that measures how strongly units in the same group are related. It is calculated as:<br>
<br>
**ICC = Between-Group Variance / Total Variance**
= Between-Group Variance / (Between-Group Variance + Within-Group Variance)<br>
<br>
- When ICC = 0, the varaiance between groups is zero, indicating "all groups are similar," which means the dataset contains no distinct grouping.<br>
<br>
- When ICC = 1, there is no variance within the groups, meaning all variance comes from between groups.<br>
<br>
- When 0 < ICC < 1, it tells the degree of between-group differences. For example, an ICC of 0.78 means that 78% of total variance is due to differences between the groups. <br>
<br>
The higher the ICC, the more justification there is to use a multilevel model to investigate the between-group differences. An ICC of 0.78 is considered very high and it is definitely beneficial to use a multilevel model in this case.<br>

## The null model
To calculate the ICC, we must first run a **null model**, which is a baseline model that estimates the between-group variance and the within-group variance.<br>
<br>
In regression, a null model is usually the simplest possible model involving only an intercept (the mean value of Y) with no predictor variables. It represents a case where the predictors have no effect on the outcome (i.e., the risk factors studied do not impact the health outcome). If the variances are large under the null assumption, it means a more complex model involving predictors should be evaluated.<br> 
<br> 
First load the necessary libraries--the model we want to use is **mixed linear model**, or ***mixedlm***, from the ***statsmodel*** library.<br>
<br>
Specify the null model, fit the model, and then print out the results. For our study, we will be conducting a two-level mixed linear regression, where the Level 2 groups are **UHF42** neighborhoods.<br>
<br>
```python
import statsmodels.api as sm
import statsmodels.formula.api as smf 

null_model = smf.mixedlm("PoorPhysicalHealthPercent ~ 1", data=df, groups=df["UHF42"])

results_null = null_model.fit()
print(results_null.summary())

```

<br>

![null_model_results](assets/images/null_model_results.png)
<br>
<br>

Using the **Between-Group Variance** and the **Within-Group Variance** estimated by the model, we can now calculate the ICC: <br>

**ICC** = Between-Group Variance / Total Variance<br>
= Between-Group Variance / (Between-Group Variance + Within-Group Variance)<br>
= 7.958 / (7.958 + 2.2614) = **0.78**<br>
<br>
<br>

## Cluster-mean centering
It is worth noting that, for this multilevel regression, the HVI predictor was cluster-mean centered (also known as group-mean centered). In other words, the arithmetic means of HVI for each cluster (UHF42 neighborhood) were subtracted from each observation’s HVI value in the corresponding neighborhood.<br>
<br>

Mean centering is a technique used in linear regression models when predictors do not have meaningful zero points. In this study, HVI has no meaningful zero  because Heat Vulnerability Index ranges from 1 to 5. Without centering it, the y-intercept would represent the average percentage of residents with poor physical health when HVI is zero, which is out of range for the index.<br>
<br>

Other reasons for centering are to reduce multicollinearity and to simplify interpretations when interaction terms are included in the model.<br>
<br>


There are two options for centering in multilevel models, namely, grand-mean centering and cluster-mean centering. Since we are interested in the Level 2 predictor, **cluster-mean centering** is chosen because it gives an unbiased estimate of the within-cluster effect and produces better estimates of the slope variance.<br>
<br>
<br>

## Fixed effects vs. random effects
In multilevel models, **fixed effects** focus on generalizable patterns and relationships (y-intercept and slope) that are assumed to be consistent across all groups of analysis.<br> 
<br>
In contrast, **random effects** capture group-specific variations by allowing for variability between groups and estimating parameters that can vary across the groups or clusters. There are two parameters for random effects:<br>
* **Random intercept** estimate the variation in the baseline (intercept) between groups. For example, in this study, a random intercept would allow each neighborhood to have a unique average percentage of residents with poor health, reflecting differences in baseline performance across different neighborhoods. <br>
<br>
* **Random slope** capture the relationship between a predictor and the outcome across groups or clusters. If the effect of HVI on poor health varies from one neighborhood to another, this can be modeled with random slopes where each neighborhood will have its unique relationship (slope) between HVI and poor physical health. <br>
<br>
<br>

## Fixed-effects model specification
As mentioned earlier, the averaged HVI predictor is cluster-centered. The cluster means are represented by *mHVI<sub>j</sub>* for neighborhood *j*. <br>

Conceptually, to get the **fixed-effects parameters**we specify the model as:<br>

**Poor Health** = 𝛽<sub>0</sub> + 𝛽<sub>1</sub> ***mHVI<sub>j</sub>*** + 𝛽<sub>2</sub>***Age65<sub>j</sub>*** + *v<sub>j</sub>* + *e<sub>ij</sub>* <br>
where *i* represents individual zip code and *j* UHF neighborhood.<br>
<br>
𝛽<sub>0</sub>, 𝛽<sub>1</sub>, and 𝛽<sub>2</sub> are fixed-effects parameters whose estimates can be extracted from the output table.
```python
model_fixedE = smf.mixedlm("PoorPhysicalHealthPercent ~ mHVI + Percent65plus", data=df, groups=df["UHF42"])

results_fixedE = model_fixedE.fit()

print(results_fixedE.summary())
```
<br>
![fixed_effects_results](assets/images/fixed_effects_results.png)<br>
<br>

Based on the output of the analysis, both **mHVI** and **Percent65plus** are significant given the Z-values in the results table.
<br>
<br>

## Two-level model specification
Conceptually, the two-level model can be specified as:<br>
**Poor Health** = 𝛽<sub>0j</sub> + 𝛽<sub>1j</sub> ***mHVI<sub>ij</sub>*** + *e<sub>ij</sub>* <br>
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
= γ<sub>0</sub> + γ<sub>1</sub> ***mHVI<sub>j</sub>*** + γ<sub>2</sub>***Age65<sub>j</sub>*** + *v<sub>0j</sub>* <br>
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
    + (γ<sub>3</sub> + γ<sub>4</sub>***mHVI<sub>j</sub>*** + γ<sub>5</sub>***Age65<sub>j</sub>*** + *v<sub>1j</sub>) * ***HVI<sub>ij</sub>*** + *e<sub>ij</sub>* <br>

where **γ**s are **fixed-effects** and ***v<sub>0j</sub>*** and ***v<sub>1j</sub>*** are **random effects**<br>
<br>
In python, each term in the mixed-effects model needs to be specified except for *e<sub>ij</sub>*, which means there will be two interaction terms.<br> 
<br>
The random effects are specified using the option **re_formula = "~1 + HVI"** where 1 and HVI specify that the coefficients vary for the grouping variable (i.e., UHF42 neighborhoods).<br>
<br>
As a reminder, the HVI variable used for this model is cluster-mean centered, or HVI_CMC.<br>  

```python

model_2level = smf.mixedlm("PoorPhysicalHealthPercent ~ mHVI + Percent65plus + HVI_CMC + mHVI*HVI_CMC + Percent65plus*HVI_CMC", data=df, groups=df["UHF42"], re_formula="~1 + HVI_CMC")

results_2level = model_2level.fit()

print(results_2level.summary())
```
<br>
![two_level_results](assets/images/two_level_results.png)<br>
<br>

The parameters in the table of **fixed effects** give the estimates for **γ**s that can be plugged back into the equations to calculate 𝛽<sub>0j</sub> and 𝛽<sub>1j</sub>, as follows: <br>

𝛽<sub>0j</sub> = 21.018 + 1.285 ***mHVI<sub>j</sub>*** - 1.03 ***Age65<sub>j</sub>*** <br>
𝛽<sub>1j</sub> = 10.182 - 0.44 ***mHVI<sub>j</sub>*** - 0.6 ***Age65<sub>j</sub>***  <br>
<br>
As 𝛽<sub>0j</sub> is the y-intercept for neighborhood *j*, the first equation show that neighborhoods with higher HVI suffer a higher percentage of average percentage of adults with poor physical health compared to neighborhoods with lower HVI. <br>
<br>
Similarly, as 𝛽<sub>1j</sub> is the slope for neighborhood *j*, the second equation show that neighborhoods with a HVI that is already high will experience a smaller increase in the percentage of adults with poor physical health compared to neighborhoods whose current HVI is low. <br>
<br>
The random-effects parameters are also shown and marked on the output and the individual values for each neighborhood can be obtained by the using the ***random_effects*** attribute of the fitted model. With these values, the individual regression line can be plotted as follows:<br>
<br>
![two_level_results](assets/images/multilevel_regression_lines.png)
<br>
<br>

# Results interpretations and implications
The results show a clear positive relationship between HVI and percentage of adult residents with poor physical health for all neighborhoods.<br>
<br>
Neighborhoods with higher HVI have a larger portion of their adult residents in poor physical health. However, it is the neighborhoods with low HVI that will see a relatively larger increase in the porportion of their adult residents with poor physical health.<br>
<br>
Therefore, public health **mitigation** measures should target neighborhoods with a high HVI, while **prevention** measures should 
focus on neighborhoods whose currently lower HVI is likely to rise considerably due to climate change. <br>
<br>
<br>
# Future uses
One can use the same model to investigate on two other **health outcomes**: percentage of adults with poor mental health (PoorMentalHealthPercent) and percentage of adults with high blood pressure (HighBPPercent) residing in the area with a given zip code.<br> 
<br>
To investigate alternative **predictors**, one can choose from MedianAge, MedianIncome, PercentCollege, PercentMale, PercentMarried, PercentWhite, PercentBlack, PercentAsian, PercentOtherRaces, ForeignBornPercent, RacialDiversityIndex, MedianHouseholdIncome, PovertyRate, BAdegreePercent, HomeownershipPercent. <br>
<br>
For example, by using BAdegreePercent (percentage of residents with a BA degree) and PercentMarried as predictors (X’s) and PercentHighBP as the health outcome (Y), one can investigate the effects of education level and marital status of the residents of a given neighborhood in New York City on the percentage of adults in that neighborhood with high blood pressure, and (2) whether those effects differ for each of the five boroughs. <br>
<br>
Here is the [MLM_dataframe](assets/images/MLM_dataframe.csv) with all the variables mentioned above.
<br>


