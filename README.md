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


