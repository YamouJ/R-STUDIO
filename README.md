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




