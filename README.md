# Multilevel Modeling with Python
## Description
This is a step-by-step demonstration on how to conduct a multilevel regression analysis using Python to answer these research questions: <br> 
(1) Are the Heat Vulnerability Index (HVI) and the percentage of older population related to the physical health of adults residing in NYC?<br>
(2) Do these relationships vary by neighborhood?<br>

Starting with a demonstration of the need to use multilevel modeling for hierarchical data, this project aims to showcase a variety of tools offered by Python libraries for data extraction, manipulation,  visualization, statistical modeling, and analysis. <br> 

## Rationale
In light of climate change, some neighborhoods are more vulnerable to extreme heat than others, such as urban areas with high impervious surfaces, less green and shady surfaces, fewer socioeconomic resources, and a larger population that is disproportionately impacted by heat (e.g., older adults, outdoor workers, and those with lower income). <br>

It is often observed that areas in close spatial proximity tend to share similar characteristics, thus forming “clusters/groups” and motivating researchers to conduct multilevel regressions to investigate both the within-group and between-group effects of risk factors on health outcomes. <br>

## Health outcome, predictors, model structure, and datasets
In this study, the **health outcome** (Y) is the percentage of adults with **poor physical health** residing in a given zip code, while the two **predictors** (X’s) are the average **HVI** by zip code and the percentage of residents aged **65 and older** of each UHF42 neighborhood.<br> 

Both predictors exhibit a hierarchical data structure, in that each zip code belongs to one of the 42 UHF42 neighborhoods within the five boroughs of New York City.<br>

In multilevel models, **Level 1** predictors are those that vary within a group (in this study, averaged **HVI** by zip code), whereas **Level 2** variables are characteristics of the group (in this study, the **percentage of residents aged 65 and older** of each UHF42 neighborhood). <br>

This analysis will use five datasets that have been extracted from their linked sources and preprocessed:<br> 
•	[Health Outcomes by Zip Code](https://datacommons.org/place/geoId/3651000?category=Health)<br>
•	[Averaged Heat Vulnerability (HVI) by Zip Code](https://a816-dohbesp.nyc.gov/IndicatorPublic/data-features/hvi/) <br>
•	[Risk Factors by Zip Code](https://simplemaps.com/city/new-york/zips/age-median)<br>
•	[Risk Factors by Borough](https://furmancenter.org/stateofthecity/view/citywide-and-borough-data)<br>
•	[UHF42 Neighborhood Codes](https://www.nyc.gov/assets/doh/downloads/pdf/ah/zipcodetable.pdf)<br>

## Workflow
**Step 1: Understanding a Multilevel Regression Model**<br>
•	For an overview on multilevel regressions: [Multilevel Modeling: A Comprehensive Guide for Data Scientists](https://www.datacamp.com/tutorial/multilevel-modeling) <br>
•	For an illustrated introduction to multilevel models using R: [A Fun Intro to Multilevel Models in R](https://favstats.github.io/intro_multilevel/slides/#1)<br>
•	For an example of how to conduct a multilevel regression using Python: [Advanced Statistics: Multilevel Regression](https://advstats.psychstat.org/python/multilevel/index.php) <br>
•	For explanation on grand mean centering vs. cluster mean centering: [Centering Options and Interpretations](https://www.learn-mlms.com/08-module-8.html) <br>
<br>

**Step 2: Data Extraction / Manipulation** <br>
This analysis uses three datasets that have been extracted and cleaned: <br>

**df01** Health Outcomes by Zip Code ( 'df01_health_by_Zip.csv').<br>
**df02** Averaged Heat Vulnerability (HVI) by Zip Code ('df02_HVI_Averages.csv').<br>
**df03** Risk Factors by Zip Code ('Data_by_Zip.csv').<br>
**df04** Risk Factors by Borough ('Data_by_Borough.csv').<br>
**df05** UHF42 Neighborhoods ('UHF42_Zip.csv').<br>

The ***pandas*** library was used for: <br>
•	Merging data frames <br>
•	Dropping Na values <br>
•	Sorting values <br>
•	Querying (filtering) rows <br>
•	Grouping subsets <br>
•	Transforming subsets to find the cluster mean <br>

**Step 3: Data Visualization** <br> 
These libraries: ***numpy***, ***matplotlib.pyplot***, ***MaxNLocator*** from ***matplotlib.ticker***, and ***seaborn*** were used to create the following visualizations: <br>

•	Unicolor scatter plot to show the relationship between the *Percentage of Residents with Poor Physical Health* (health outcome) and the average *HVI* (predictor) for all zip codes. <br>

•	Unicolor scatter plot to show the relationship between the *Percentage of Residents with Poor Physical Health* (health outcome) and the *Percentage of residents aged 65 years and older* (predictor) for each zip code. <br>

•	Multicolor scatter plots (one color for each borough) with a matching color regression line for each cluster of data points to show the relationship between *Percentage of Residents with Poor Physical Health* (health outcome) and the average *HVI* (predictor) for each borough. <br>

•	Multicolor scatter plots (one color for each borough) for each cluster of data points to show the relationship between *Percentage of Residents with Poor Physical Health* (health outcome) and *Percentage of Residents with Poor Physical Health* (predictor) for each zip code. <br>

•	Unicolor line plot to show the varying slopes and intercepts for each of the 42 UHF neighborhoods.<br>

It is visually evident from the multicolor scatter plots that each borough forms a **cluster**, supporting the use of multilevel regression over Ordinary Least Squares (single-level) regression. <br>

We can see a **positive  relationship** between the **Percentage of Residents with Poor Physical Health** and the average **HVI** for each zip code. Namely, the higher the Heat Vulnerability Index, the higher the percentage of residents with poor physical health. <br>

It is worth noting that, for this multilevel regression, the average *HVI* (a predictor) was **cluster-mean-centered**, which means the arithmetic mean of HVI for each cluster (UHF42 neighborhoods) was subtracted from each observation’s HVI values in the corresponding neighborhood. <br>

**Mean centering** is a technique used in linear regression models when predictors do not have meaningful zero points. In this study, HVI has no meaningful zero because the Heat Vulnerability Index ranges from 1 to 5. Without centering it, the y-intercept would represent the average percentage of residents with poor physical health when HVI is zero, which is out of range for the index.<br>

Other reasons for centering are to reduce multicollinearity and to simplify interpretations when interaction terms are included in the model.<br>

There are two options for centering in multilevel models: **grand-mean centering** or **cluster-mean centering**. Since we are interested in the Level 2 predictor, cluster-mean centering is chosen because it provides an unbiased estimate of the within-cluster effect and produces better estimates of the slope variance.<br>

**Step 4: Data Analysis** <br>
Libraries used for data analysis were: *statsmodels.api* and *statsmodels.formula.api*. <br>

•	First, a **null model** was specified to see if there is significant clustering or group-level variation in the data that justifies using a multilevel model instead of a standard regression. <br>

•	Using the statistics from the null model, one can compute the intraclass correlation coefficient (**ICC**). If ICC > 0.1, one should consider using a multilevel model. In this study, the ICC = 0.78, which supports the use of multilevel modeling. <br>

•	Finally, a multilevel model (mixed effects linear model) is specified to investigate the fixed effects and random effects of the predictor variables. <br>

**Step 5: Interpretations of Results**<br>
Given the *p*-value from the fixed-effects model results, both **HVI** and the percentage of residents aged *65 and older** are **significant** predictors both in slope and intercept. <br>

A regression line plot with varying intercepts and slopes across different neighborhoods illustrates that the relationship between poor physical health and HVI (while holding the percentage of older residents constant) is *not fixed* but rather cluster-dependent. <br>

## Further Uses
One can use the same model to investigate two other **health outcomes**: the percentage of adults with poor mental health (PoorMentalHealthPercent) and the percentage of adults with high blood pressure (HighBPPercent) residing in the area with a given zip code.<br>

To investigate alternative **predictors**, one can choose from MedianAge, MedianIncome, PercentCollege, PercentMale, PercentMarried, PercentWhite, PercentBlack, PercentAsian, PercentOtherRaces, ForeignBornPercent, RacialDiversityIndex, MedianHouseholdIncome, PovertyRate, UnemployRate, BAdegreePercent, and HomeownershipPercent.<br>

For example, by using BAdegreePercent (percentage of residents with a BA degree) and PercentMarried as predictors (X’s) and PercentHighBP as the health outcome (Y), one can investigate the effects of education level and marital status on the percentage of adults with high blood pressure in NYC, and (2) whether those effects differ for each of the 42 UHF neighborhoods.<br>

Here is the [MLM_dataframe]( https://yangbetty2025.github.io/Multilevel_Modeling/assets/images/MLM_dataframe.csv) with all the variables mentioned above.<br>
<br>

## Files List
**df01_Health_by_Zip.csv** (extracted from [Data Commons](https://datacommons.org/place/geoId/3651000?category=Health)) contains the following columns as health outcomes:<br>
•	*Zip*: five-digit zip codes used in NYC. <br>
•	*PoorPhysicalHealthPercent*: percentage of adult population with poor physical health residing in the area of the zip code. <br>
•	*PoorMentalHealthPercent*: percentage of adult population with poor mental health residing in the area of the zip code. <br>
•	*HighBPPercent*: percentage of adult population with high blood pressure residing in the area of the zip code. <br>
<br>
**df02_HVI_Averages.csv** (extracted from [NYC Data Portal: Heat Vulnerability Index]( https://a816-dohbesp.nyc.gov/IndicatorPublic/data-features/hvi/)) contains the following columns:<br>
•	*Zip*: five-digit zip codes used in NYC <br>
•	*Borough*: BX (Bronx), BK (Brooklyn), MN (Manhattan), QN (Queens), and SI (Station Island). <br>
•	*avgHVI*: Heat Vulnerability Index ranking from 1 to 5, with 1 indicating the associated neighborhood being least vulnerable and 5 the most vulnerable. Four factors were used in calculating the HVI for each neighborhood: (i) daytime summer surface *temperature*, (ii) availability of *air conditioning*, (iii) amount of *green space*, and (iv) *median income* as a proxy for the likelihood of being able to afford air conditioning.  <br>

Some zip codes span multiple neighborhoods. In this case, a simple average of HVI is taken for those zip codes to create the *avgHVI* variable. <br>
<br>
**df03_Data_by-Zip.csv** contains the following columns:<br>
•	*[MedianAge]( https://simplemaps.com/city/new-york/zips/age-median)*: The age of the median resident in the area of the zip code. <br>
•	*[PercentCollege]( https://simplemaps.com/city/new-york/zips/education-college-or-above)*: The percentage of residents with at least a 4-year degree resident in the area of the zip code. <br>
•	*[PercentMale]( https://simplemaps.com/city/new-york/zips/male)*: The percentage of residents who report being male in the area of the zip code. <br>
•	*[PercentMarried]( https://simplemaps.com/city/new-york/zips/married)*: The percentage of residents who report being married in the area of the zip code. <br>
•	*[PercentWhite]( https://simplemaps.com/city/new-york/zips/race-white)*: The percentage of residents who report their race as White in the area of the zip code. <br>
•	*[PercentBlack]( https://simplemaps.com/city/new-york/zips/race-black)*: The percentage of residents who report their race as Black or African American in the area of the zip code. <br>
•	*[PercentAsian]( https://simplemaps.com/city/new-york/zips/race-asian)*: The percentage of residents who report their race as Asian in the area of the zip code. <br>
•	*PercentOtherRaces*: The percentage of residents who did not report their race as White, Black, or Asian in the area of the zip code. It was calculated by subtracting *PercentWhite*, *PercentBlack*, and *PercentAsian* from 100 percent. <br>

**df04_Data_by_Borough.csv** (extracted from [NYU Furman Center City and Borough Data](https://furmancenter.org/stateofthecity/view/citywide-and-borough-data)) contains the following columns:<br>
•	*Percent65plus*: The percentage of residents aged 65 years and older. <br>
•	*ForeignBornPercent*: The percentage of foreign-born residents. <br>
•	*RacialDiversityIndex*: The probability that two randomly chosen people in a given geographic area will be of a different race.<br>
•	*MedianHouseholdIncome*: The median household income in 2019 dollars.<br>
•	*PovertyRate*: The number of people below the poverty threshold divided by the number of people for whom poverty status was determined.<br>
•	*UnemployRate*: The percentage of residents who are unemployed. <br>
•	*BAdegreePercent*: The percentage of residents with a bachelor’s degree.<br>
•	*HomeownershipPercent*: The number of owner-occupied units divided by the total number of occupied housing units. <br>

**df05_UHF42_Zip.csv** (extracted from [UHF CODES](https://www.nyc.gov/assets/doh/downloads/pdf/ah/zipcodetable.pdf)) contains the following columns:<br>
•	*Borough*: BX (Bronx), BK (Brooklyn), MN (Manhattan), QN (Queens), and SI (Station Island). <br>
•	*UHF42*: The code for each of the 42 defined community areas in New York City, created by the NYC Department of Health and Mental Hygiene and the United Hospital Fund (UHF) for health research and planning. <br>
•	*Zip*: five-digit zip codes used in NYC <br>

**MLM_datafram.csv** is file created by merging the above five files and removing all null values as well as one outlier (zip code 11005). 

**YANG_Final_Project.ipynb** is a Python notebook with step-by-step notes and Python code for completing this project. <br>
