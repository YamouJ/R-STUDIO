# Predicting Renewable Energy Usage Rates with R-Studio

## Introduction

Utilizing a comprehensive dataset from Kaggle, titled “Global Data on Sustainable Energy (2000-2020),” I explored a broad array of indicators that provide insights into energy consumption patterns, economic growth, carbon emissions, and energy access. Despite significant global efforts to promote renewable energy, progress varies widely across different nations. Therefore, this project aims to understand the key determinants of renewable energy adoption, addressing the real-world problem of uneven progress. The dataset is designed to compare progress across countries and track advancements towards United Nations Sustainable Development Goal #7, which seeks to ensure access to affordable, reliable, sustainable, and modern energy for all.
The dataset includes numerous key indicators: the name of the country or region, the reporting year (ranging from 2000 to 2020), and several metrics such as access to electricity and clean fuels for cooking, which reflect the population's reliance on modern energy sources. It also encompasses renewable electricity generating capacity per person, financial flows to developing countries for clean energy projects, and the share of renewable energy in total energy consumption. Additionally, it tracks electricity generation from fossil fuels, nuclear power, and renewable sources (in terawatt-hours), as well as the percentage of electricity generated from low-carbon sources. The dataset further provides data on primary energy consumption per capita, energy intensity (energy use per GDP), carbon dioxide emissions per person, the equivalent primary energy derived from renewables, annual GDP growth rates, and GDP per capita, along with population density, total land area, and geographical coordinates (latitude and longitude).
I have selected “Renewable energy share in total final energy consumption (%)” (RenewShare) as my response variable and conducted regression, association, and gradient boosting analyses to identify the key predictors influencing its progress.

## Data Cleaning
The data was downloaded from Kaggle as a CSV file, which was then renamed "energy.csv" and uploaded into R Studio. Columns deemed unusable due to containing too many blank cells were removed. Then, the rows that still contained blank entries were eliminated. To control for time, a subset that only included observations from 2019 was defined under the name “y2019”. 2019 was presumed to be the most reliable year (for future projection) that likely best reflects contemporary economic and technological conditions since COVID-19 may have temporarily disrupted development in 2020. Columns were then renamed for clarity and conciseness. Finally, the column for the (population) “density” variable was fixed through the removal of commas to satisfy numeric form. A preview of the resulting working dataset is shown below:

<img width="641" alt="Screenshot 2024-07-09 at 1 03 06 PM" src="https://github.com/YamouJ/R-STUDIO/assets/167350506/60f0f4d5-0558-4c07-bcc3-512a5e87a91d">

## Regression
The histogram and qq plots below illustrate the distribution of observations of RenewShare, the variable I have selected as our response variable.

<img width="700" alt="Screenshot 2024-07-09 at 1 05 24 PM" src="https://github.com/YamouJ/R-STUDIO/assets/167350506/d2142f74-f085-42ea-b5fc-cc4b5722a9b1">

<img width="700" alt="Screenshot 2024-07-09 at 1 06 02 PM" src="https://github.com/YamouJ/R-STUDIO/assets/167350506/5c41fdd2-cbe6-4154-9021-d68c1e9e5377">

The median provides a better summary of the typical value of `RenewShare` (rather than mean) since the plots above demonstrate a lack of approximate Normality.


Though not shown, I constructed separate models regressing RenewShare over each other individual variable in the cleaned dataset. Taking the summary of each of these models helped determine whether each predictor variable could stand alone as significant, i.e., if the slope estimate of each model had a p-value less than 0.05. 


Predictors with p-values less than 0.05 are included in the combined model below, which had an RMSE of 17.42–significantly lower (thus, better) than that of the Naive model, which had an RMSE of 27.90. This suggests that at least one of the predictors included in the combined model is significant.

<img width="664" alt="Screenshot 2024-07-09 at 1 09 11 PM" src="https://github.com/YamouJ/R-STUDIO/assets/167350506/5c4e18c2-20b9-4d65-b814-2a942adf237f">
- 

I can also compare the RMSE values among the aforementioned individual models containing presumably significant predictors. RMSE values from the models of PopAccessElec (19.25) and PopAccessCleanFuel (18.98) is significantly less than that of naive model (27.90). This suggests that PopAccessElec and PopAccessCleanFuel may be worth including in the final model. 


On the other hand, RMSE of the models containing gdp_per_capita (26.67), EnergyIntensity (26.56), and PrimaryConsumpCap (25.76) are not significantly less than that of the naive model. This suggests that gdp_per_capita, EnergyIntensity, and PrimaryConsumpCap may not be worth including in the final model, but this requires further analysis before making a final decision. The next step is finding the confidence intervals of the slope estimates for each of the five predictors.

### Figure 5: Confidence Intervals of Slope Estimates

<img width="500" alt="Screenshot 2024-07-09 at 1 12 35 PM" src="https://github.com/YamouJ/R-STUDIO/assets/167350506/6be4d604-31ee-4b62-9e39-189ef18d5a86">

In each of the cases above, 0 is outside the 95% confidence interval of each slope estimate. This suggests that the five regressions are statistically significant.
The following scatterplots were used to decide whether each relationship may be appropriately measured by Pearson correlation, Spearman's rank correlation, both, or neither.

### Figure 6: RenewShare vs. log10(PrimaryConsumpCap)

<img width="700" alt="Screenshot 2024-07-09 at 1 15 45 PM" src="https://github.com/YamouJ/R-STUDIO/assets/167350506/326f79ed-f9e0-4bd4-ab0c-c87600376b71">

The data cloud above maintains closer proximity to the gray line of best fit; removing two disruptive outliers beyond x=175,000 and taking the logarithm (base=10) of the x-variable improved linearity and ellipsoidal form to a considerable extent. Therefore, Pearson's correlation may be an appropriate measure. Furthermore, the relationship between RenewShare and log10(PrimaryConsumpCap) is directionally negative and moderate in strength yet still heteroscedastic (i.e., the width of the range of RenewShare values changes over the range of log10(PrimaryConsumpCap) values)--and still contains some outliers. Per the blue Loess curve, the relationship is largely monotonic, so Spearman's rank correlation may be considered too.

### Figure 7: RenewShare vs. PopAccessElec

<img width="700" alt="Screenshot 2024-07-09 at 1 17 27 PM" src="https://github.com/YamouJ/R-STUDIO/assets/167350506/fb289d7d-8f1f-49ef-9ae5-ed712b44a72b">

Based on the data cloud above, the relationship between RenewShare and PopAccessElec is directionally negative and weak (i.e., knowing the value of PopAccessElec does not sufficiently narrow down the range of possible RenewShare values). The relationship is also non-ellipsoidal and heteroscedastic (i.e., the width of the range of RenewShare values changes over the range of PopAccessElec values). Therefore, Pearson's correlation is NOT an appropriate measure, and attempts at data transformation were fruitless. Nonetheless, the relationship is marginally significantly linear and monotonic, so Spearman's rank correlation may be considered.

### Figure 8: RenewShare vs. PopAccessCleanFuel
<img width="700" alt="Screenshot 2024-07-09 at 1 21 25 PM" src="https://github.com/YamouJ/R-STUDIO/assets/167350506/554bef2c-5a76-4839-b6cf-0b4a3606b534">

Based on the data cloud above, the relationship between RenewShare and PopAccessCleanFuel is directionally negative and weak. The relationship is also non-ellipsoidal and heteroscedastic. Therefore, Pearson's correlation is NOT an appropriate measure, and attempts at data transformation were again fruitless. Nonetheless, the relationship is marginally significantly linear and monotonic, so Spearman's rank correlation may be considered.

### Figure 9: RenewShare vs. log10(EnergyIntensity)
<img width="700" alt="Screenshot 2024-07-09 at 2 36 59 PM" src="https://github.com/YamouJ/R-STUDIO/assets/167350506/d474bb0e-3c4c-43b7-b692-b307fe3913a4">

The data cloud above maintains decent proximity to the gray line of best fit; removing three disruptive outliers beyond x=12.5 and taking the logarithm of the x-variable improved linearity and ellipsoidal form to an extent. Therefore, Pearson's correlation may be an appropriate measure. Furthermore, the relationship between RenewShare and log10(EnergyIntensity) is directionally positive and moderate in strength yet still heteroscedastic (i.e., the width of the range of RenewShare values changes over the range of log10(EnergyIntensity) values). The relationship is largely monotonic, so Spearman's rank correlation may be considered too.

### Figure 10: RenewShare vs. log10(gdp_per_capita)
<img width="700" alt="Screenshot 2024-07-09 at 2 40 59 PM" src="https://github.com/YamouJ/R-STUDIO/assets/167350506/41a47712-c61e-4938-bad1-009dd6ee3693">

The data cloud above maintains adequate proximity to the gray line of best fit; taking the logarithm of the x-variable improved linearity and ellipsoidal form to an extent. Therefore, Pearson's correlation may be an appropriate measure. Furthermore, the relationship between RenewShare and log10(gdp_per_capita) is directionally positive and moderate in strength yet still heteroscedastic (i.e., the width of the range of RenewShare values changes over the range of log10(gdp_per_capita) values). The relationship is still nonmonotonic (according to the blue Loess smoother), however, so Spearman's rank correlation will not be considered.

### Association Analysis
Each of the following cases, which involved 500 permutations each, found with 95% confidence that the p-values of the previously approved correlation measures were between 0 and 0.007. The fact that this range is less than 0.05, suggests that the associations between RenewShare and each of the five predictors are statistically significant. Observing the appropriate plots of Chance values, the reductions in SSE (represented by the solid red line in each case) are far beyond what happens "by chance" (per the histogram of permutations), asserting that each regression is statistically significant. However, recalling the lack of ellipsoidal form as demonstrated in our graphical analysis for PopAccessElec and PopAccessCleanFuel, we must maintain that RenewShare has particularly weak linear relationships with both of those variables.

<img width="700" alt="Screenshot 2024-07-09 at 2 52 54 PM" src="https://github.com/YamouJ/R-STUDIO/assets/167350506/38b5a0bb-2237-4021-a7ce-5e79e3526a7a">

Squaring the Pearson's correlation coefficient for the regression of RenewShare vs. log10 (PrimaryConsumpCap), we found R^2 = 0.5732098, indicating a moderately strong relationship.


### Figure 13: Association between PopAccessElec and RenewShare 
<img width="651" alt="Screenshot 2024-07-09 at 2 55 37 PM" src="https://github.com/YamouJ/R-STUDIO/assets/167350506/828a2bad-686c-4ac4-90f2-ffb7a5595a7d">

### Figure 14: Chance Values of Spearman Plot for RenewShare vs. PopAccessElec

<img width="596" alt="Screenshot 2024-07-09 at 2 57 39 PM" src="https://github.com/YamouJ/R-STUDIO/assets/167350506/ee044595-fadd-471e-a336-8f11cfe1ed8c">


The data cloud above maintains decent proximity to the gray line of best fit; removing three disruptive outliers beyond x=12.5 and taking the logarithm of the x-variable improved linearity and ellipsoidal form to an extent. Therefore, Pearson's correlation may be an appropriate measure. Furthermore, the relationship between RenewShare and log10(EnergyIntensity) is directionally positive and moderate in strength yet still heteroscedastic (i.e., the width of the range of RenewShare values changes over the range of log10(EnergyIntensity) values). The relationship is largely monotonic, so Spearman's rank correlation may be considered too.

### Association Analysis
Each of the following cases, which involved 500 permutations each, found with 95% confidence that the p-values of the previously approved correlation measures were between 0 and 0.007. The fact that this range is less than 0.05, suggests that the associations between RenewShare and each of the five predictors are statistically significant. Observing the appropriate plots of Chance values, the reductions in SSE (represented by the solid red line in each case) are far beyond what happens "by chance" (per the histogram of permutations), asserting that each regression is statistically significant. However, recalling the lack of ellipsoidal form as demonstrated in our graphical analysis for PopAccessElec and PopAccessCleanFuel, we must maintain that RenewShare has particularly weak linear relationships with both of those variables.

### Figure 11: Association between log10(PrimaryConsumpCap) and RenewShare 
<img width="700" alt="Screenshot 2024-07-09 at 2 59 48 PM" src="https://github.com/YamouJ/R-STUDIO/assets/167350506/672ff8e2-a9b3-489a-a8fd-bb19f62a4bd4">


Squaring the Pearson's correlation coefficient for the regression of RenewShare vs. log10 (PrimaryConsumpCap), we found R^2 = 0.5732098, indicating a moderately strong relationship.


### Figure 13: Association between PopAccessElec and RenewShare 
<img width="700" alt="Screenshot 2024-07-09 at 3 02 36 PM" src="https://github.com/YamouJ/R-STUDIO/assets/167350506/05cc018b-53ce-4738-b0b3-3bebd5214d0a">


### Figure 14: Chance Values of Spearman Plot for RenewShare vs. PopAccessElec



### Figure 15: Association between PopAccessCleanFuel and RenewShare 
<img width="700" alt="Screenshot 2024-07-09 at 3 03 04 PM" src="https://github.com/YamouJ/R-STUDIO/assets/167350506/3a3b9497-3fdd-4bec-81c9-e6060ddb63cd">

<img width="577" alt="Screenshot 2024-07-09 at 3 04 27 PM" src="https://github.com/YamouJ/R-STUDIO/assets/167350506/d3296550-89ab-4332-9356-82cf6ea5296f">

### Figure 17: Association between log10(EnergyIntensity) and RenewShare  
<img width="700" alt="Screenshot 2024-07-09 at 3 07 33 PM" src="https://github.com/YamouJ/R-STUDIO/assets/167350506/ba8cbb1f-8d35-4a6c-b054-cc44419a3634">

Squaring the Pearson's correlation coefficient for the regression of RenewShare over log10(EnergyIntensity), we find R^2 = 0.1666891, indicating a weak relationship.

### Figure 19: Association between log10(gdp_per_capita) and RenewShare 
<img width="700" alt="Screenshot 2024-07-09 at 3 10 01 PM" src="https://github.com/YamouJ/R-STUDIO/assets/167350506/7d81b4a5-16c0-43ea-8139-afbc0a15ab88">

### Figure 20: Chance Values of Pearson Plot for RenewShare vs. log10(gdp_per_capita)
<img width="700" alt="Screenshot 2024-07-09 at 3 12 19 PM" src="https://github.com/YamouJ/R-STUDIO/assets/167350506/78ed27a6-36e0-4bd7-90af-bdb45d6a5cb3">

Squaring the Pearson's correlation coefficient for the regression of RenewShare over log10(gdp_per_capita), we find R^2 = 0.3861631, indicating a fairly weak relationship.

The results of the association analysis indicate that all five presumably significant predictors are indeed statistically significant--at least when enhancing via logarithmic transformation when helpful. However, among each significant predictor's separate relationship with RenewShare, the strongest was that for log10(PrimaryConsumpCap), with an R^2 = 0.5732098. This is moderate at best, so log10(PrimaryConsumpCap) may not actually be a particularly reliable predictor of RenewShare in practice. With an R^2 of 0.3861631, log10(gdp_per_capita) is a fairly weak predictor of RenewShare. 


<img width="700" alt="Screenshot 2024-07-09 at 3 14 51 PM" src="https://github.com/YamouJ/R-STUDIO/assets/167350506/225ec365-e22a-4cb3-b2da-e7ceb853eae1">

Log10(EnergyIntensity), PopAccessElec, and PopAccessCleanFuel are even weaker predictors in practice, despite being statistically significant in theory.

### Gradient Boosting
I elected to use a gradient boosting machine (GBM), a powerful machine learning algorithm known for its ability to build robust predictive models through ensemble learning by combining the predictions of many decision trees. It iteratively improves prediction accuracy while minimizing error by incorporating the contribution of multiple weak models into a strong one. By selecting a subset of the five most important predictors from our dataset, the complexity of the model was reduced while retaining predictive power.

The GBM model was configured to optimize performance and accuracy for predicting renewable energy share. It utilizes a Gaussian distribution for the continuous response variable, with 500 trees for balanced complexity and efficiency, and an interaction depth of 3 to prevent overfitting. A learning rate of 0.0.

### Figure 21: Top Predictors Influencing RenewShare
<img width="700" alt="Screenshot 2024-07-09 at 3 27 10 PM" src="https://github.com/YamouJ/R-STUDIO/assets/167350506/3c8a8135-9d4f-4514-b428-f9ad7a0def8b">
<img width="700" alt="Screenshot 2024-07-09 at 3 36 05 PM" src="https://github.com/YamouJ/R-STUDIO/assets/167350506/f49525db-b39d-4d93-ad33-a07fb7f6f4fa">


The model above provides a ranking of the selected variables based on their relative influence on the response variable (RenewShare). It calculates these influences by evaluating how often each predictor is used in splitting the data and its impact on reducing prediction error. 

### Figure 22: Squared Error Loss vs. Number of Iterations
<img width="800" alt="Screenshot 2024-07-09 at 3 30 17 PM" src="https://github.com/YamouJ/R-STUDIO/assets/167350506/ac018b9b-045a-4933-9fea-dc82abc8c734">

-
By choosing the optimal number of iterations, the model maximizes its predictive power while keeping error to a minimum, yielding a more accurate and reliable predictive model for RenewShare.
<img width="700" alt="Screenshot 2024-07-09 at 3 34 22 PM" src="https://github.com/YamouJ/R-STUDIO/assets/167350506/37fefb3d-f952-480b-a52d-8f667b7091d2">




