# Multilevel Modeling with Python
## Description
This is a step-by-step demonstration on how to conduct a multilevel regression analysis using Python to investigate (1) the effects of Heat Vulnerability Index (HVI) and the median age of a given neighborhood in New York City on the percentage of adults in that neighborhood with poor physical health, and (2) whether those effects differ for each of the five boroughs.<br>
<br>
Starting from demonstrating the need to use multilevel modeling for hierarchical data, this project aims to showcase a variety of tools offered by Python libraries for data extraction, manipulation,  visualization, statistical modeling and analysis. <br> 

## Rationale
In light of climate change, some areas are more vulnerable to extreme heat than others, such as urban areas with high impervious surfaces, less green and shady surfaces, fewer socioeconomic resources, and a larger population that are disproportionately impacted by heat (e.g., older adults, outdoor workers, and with lower income).<br>
<br>
It is often observed that areas in close spatial proximity tend to share similar characteristics, thus forming "clusters/groups" and motivating researchers to conduct multilevel regressions to investigate both the within-group and between-group effects of risk factors on health outcome. <br>
<br>
In this study, the health outcome (the dependent variable *Y*) is the percentage of adults with poor physical health residing in a given zip code, while the two predictor variables—HVI and median age for each zip code—have a hierarchical structure in the sense that each zip code belongs to one of the five boroughs.  

## Workflow
**Step 1: Understanding a Multilevel Regression Model**<br>
•	For an overview on multilevel regressions: [Multilevel Modeling: A Comprehensive Guide for Data Scientists](https://www.datacamp.com/tutorial/multilevel-modeling) <br>
•	For an example of how to conduct a multilevel regression using Python: [Advanced Statistics: Multilevel Regression](https://advstats.psychstat.org/python/multilevel/index.php) <br>
For explanation on grand mean centering vs. cluster mean centering: [Centering Options and Interpretations](https://www.learn-mlms.com/08-module-8.html) <br>
•	
<br>
**Step 2: Data Extraction / Manipulation** <br>
This analysis uses three datasets that have been extracted and cleaned: <br>
**df01** Health Outcomes by Zip Code ( 'df01_health_by_Zip.csv').<br>
**df02** Averaged Heat Vulnerability (HVI) by Zip Code ('df02_HVI_Averages.csv').<br>
**df03** Risk Factors by Zip Code ('Data_by_Zip.csv').<br>
The *pandas* library was used for: <br>
•	Merging data frames <br>
•	Dropping Na values <br>
•	Sorting values <br>
•	Querying (filtering) rows <br>
•	Grouping subsets <br>
•	Transforming subsets to find the cluster mean <br>

**Step 3: Data Visualization** <br> 
These libraries: *numpy*,  *matplotlib.pyplot*, MaxNLocator  from *matplotlib.ticker*,  and *seaborn*  were used to create the following visualizations: <br>
•	Single-color scatter plot to show the relationship between *Percentage of Residents with Poor Physical Health* (health outcome) and average *HVI* (predictor) for each zip code. <br>
<br>
•	Single-color scatter plot to show the relationship between Percentage of Residents with Poor Physical Health (health outcome) and *Median Age* (predictor) for each zip code. <br>
<br>
•	Multicolor scatter plots (one color for each borough) with a matching color regression line for each cluster of data points to show the relationship between *Percentage of Residents with Poor Physical Health* (health outcome) and average *HVI* (predictor) for each zip code. <br>
<br>
•	Multicolor scatter plots (one color for each borough) with a matching color linear regression line for each cluster of data points to show the relationship between *Percentage of Residents with Poor Physical Health* (health outcome) and *Median Age* (predictor) for each zip code. <br>
<br>
It is visually evident from the multicolor scatter plots that each borough forms a **cluster**, which supports the use of multilevel regression over regular (single level) regression. <br>
<br>
We can see that there is a **positive  relationship** between **Percentage of Residents with Poor Physical Health** and average **HVI** for each zip code. Namely, the higher the Heat Vulnerability Index, the higher the percentage of residents with poor physical health. <br>
<br>
It is worth noting that, for the multilevel regression, both predictors  (average *HVI* and *Median Age*) were **cluster-mean centered**, which means the arithmetic means of HVI and Median Age for each cluster (borough) were subtracted from each observation’s HVI and Median Age values, respectively, in the corresponding borough. <br>
<br>
Mean centering is a technique used in linear regression models when predictors do not have meaningful zero points. In this study, neither *Median Age* nor *HVI* have meaningful zero points because, without centering, the y-intercept would represent the average percentage of residents with poor physical health when the median age of those residents is 0 years old. Similarly, because Heat Vulnerability Index ranges from 1 to 5, without centering it, the y-intercept would represent the average percentage of residents with poor physical health when HVI is zero, which is out of range for the index. <br>
<br>
**Step 4: Data Analysis**<br>
Libraries used for data analysis were: *statsmodels.api* and *statsmodels.formula.api*. <br>
<br>
•	First, a **null model** was specified to see if there is significant clustering or group-level variation in the data that justifies using a multilevel model instead of a standard regression. <br>
<br>
•	Using the statistics from the null model, in can compute the intraclass correlation coefficient (**ICC**). If ICC > 0.1, one should consider the use of a multilevel model. In this study, the ICC = 0.46, which supports the use of multilevel modeling. <br>
<br>
•	Finally, a multilevel model (mixed effects linear model) is specified to investigate fixed effects and random effects of the predictor variables. <br>
<br>
**Step 5: Interpretations of Results**<br>
Given the *F*-statistics from model results, **HVI** is a **significant predictor** both in slope and intercept, while Median Age is not. <br>
A regression line plot of varying intercepts and slopes for different boroughs serves as a visual illustration that the relationship between poor physical health and HVI (while holding median age constant) is *not fixed*, but rather, borough dependent. <br>

## Further Uses
One can use the same model to investigate on two other health outcomes: percentage of adults with poor mental health (*PoorMentalHealthPercent*) and percentage of adults with high blood pressure (*HighBPPercent*) residing in the area with a given zip code. The data for these two dependent variables are included in the “df03_Heath_Outcomes.csv” dataset. <br>
<br>
Additionally, one can choose to use the following predictors included in the “df02_Data_by-Zip.csv” dataset: *PercentCollege*, *PercentMale*, *PercentMarried*, *PercentWhite*, *PercentBlack*, *PercentAsian*, and *PercentOtherRaces*. <br>
<br>
For example, by using *PercentCollege* and *PercentMarried* as predictors (*X*’s) and *PercentHighBP* as the health outcome (*Y*), one can investigate the effects of education and marital status of the residents of a given neighborhood in New York City on the percentage of adults in that neighborhood with poor mental health, and (2) whether those effects differ for each of the five boroughs.<br> 
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
Some zip codes span over multiple neighborhoods. In this case, a simple average of HVI is taken for those zip codes to create this *avgHVI* variable. <br>
<br>
**df03_Data_by-Zip.csv** contains the following columns:<br>
•	[MedianAge]( https://simplemaps.com/city/new-york/zips/age-median): The age of the median resident in the area of the zip code. <br>
•	[PercentCollege]( https://simplemaps.com/city/new-york/zips/education-college-or-above): The percentage of residents with at least a 4-year degree resident in the area of the zip code. <br>
•	[PercentMale]( https://simplemaps.com/city/new-york/zips/male): The percentage of residents who report being male resident in the area of the zip code. <br>
•	[PercentMarried]( https://simplemaps.com/city/new-york/zips/married): The percentage of residents who report being married resident in the area of the zip code. <br>
•	[PercentWhite]( https://simplemaps.com/city/new-york/zips/race-white): The percentage of residents who report their race White resident in the area of the zip code. <br>
•	[PercentBlack]( https://simplemaps.com/city/new-york/zips/race-black): The percentage of residents who report their race as Black or African American resident in the area of the zip code. <br>
•	[PercentAsian]( https://simplemaps.com/city/new-york/zips/race-asian): The percentage of residents who report their race as Asian resident in the area of the zip code. <br>
•	PercentOtherRaces: The percentage of residents who did not report their race White, Black, or Asian resident in the area of the zip code. It was calculated by subtracting PercentWhite, PercentBlack, and Percent Asian from 100 percent. <br>
<br>
**HVI_Map_NYC.png** is a map of Heat Vulnerability Index for different neighborhoods in New York City from [NYC Data Portal: Heat Vulnerability Index]( https://a816-dohbesp.nyc.gov/IndicatorPublic/data-features/hvi/) <br>
<br>
**Scatterplots_HVI_on_Poor_Health.png** is a visual juxtaposition of single-color vs. multicolor scatter plot where each color represents a borough. It shows the relationship between HVI and the percentage of residents with poor physical health. <br>
<br>
**Scatterplots_MedianAge_on_Poor_Health.png** is a visual juxtaposition of single-color vs. multicolor scatter plot where each color represents a borough. It shows the relationship between median age and the percentage of residents with poor physical health. <br>
<br>
**Linear_Regression_by_Cluster.png** shows the relationship between the health outcome (*Percentage of Residents with Poor Physical Health*) and a single predictor (either *HVI* or *Median Age*) from simple linear (ordinary least squares) models. <br>
<br>
**Regression_Lines_Multilevel.png** shows the relationship between (cluster-mean centered) *HVI* and *Percentage of Residents with Poor Physical Health* while holding *Median Age* constant. Each borough is shown in a different color to highlight the varying intercepts and slopes of the different regression lines for different boroughs. <br>
<br>
**YANG_Final_Project.ipynb** is a python notebook with step-by-step notes and Python codes for completing this project. <br>


