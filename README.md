# Covid-Vaccine-Study

## Causal Question:
Does receiving the COVID-19 vaccine reduce the likelihood of hospitalisation due to COVID-related complications in adults aged 65 and older?

## Considering Confounders:
- Age and Health Status: The general health of an individual and their age can significantly impact their susceptibility to COVID-19 and the likelihood of hospitalisation.
- Vaccination History: Previous COVID vaccinations could influence the effectiveness of the current vaccine.
- Access to Healthcare: Socioeconomic status and access to healthcare facilities may affect both vaccination rates and the likelihood of hospitalisation.
- Comorbidities: The presence of other health conditions (e.g., heart disease, diabetes) can increase the risk of severe complications from COVID-19.
- Behavioural Factors: Hygiene practices, travel habits, and exposure to crowded places can influence COVID-19 transmission and severity.
- Geographical Location: The prevalence of the COVID-19 virus can vary by region and impact infection rates.

Correction Strategy:
Randomised Controlled Trial (RCT): Conduct a randomised study where individuals are randomly assigned to receive the COVID-19 vaccine or a placebo. This can help control for known and unknown confounders.

## Unmeasured Confounders:
- Genetic Predispositions: Certain genetic factors may affect an individual's susceptibility to COVID-19 and its severity.
- Immune System Response: Variations in the immune response to the vaccine may play a role in outcomes.
- Household Composition: Living with young children or elderly individuals could affect COVID exposure and transmission.

## Considering Colliders:

In the context of our COVID vaccine study, the potential collider identified is:

*COVID Testing Rates*

This variable is influenced by both the exposure and the outcome. Individuals who receive the COVID-19 vaccine might be more likely to get tested for COVID-19 if they experience COVID-like symptoms, thinking they are protected. On the other hand, unvaccinated individuals who develop COVID-like symptoms might not get tested as frequently because they may attribute their symptoms to a common cold.

## How this causal question can drive policy changes:

In the case of our COVID vaccine study, if it demonstrates a significant reduction in hospitalisations among vaccinated individuals, it could have several policy implications:

- Targeted Vaccination Campaigns: Public health agencies could implement targeted vaccination campaigns, especially focusing on adults aged 65 and older, to increase vaccination rates in this high-risk group.
- Subsidies and Accessibility: Governments may consider offering subsidies or free COVID-19 vaccines to encourage higher vaccination rates, particularly among older adults who may face financial barriers.
- Education and Awareness: Policy efforts could include education and awareness programmes to inform the public about the benefits of COVID-19 vaccination, especially for older adults who are more vulnerable to severe complications.
- Healthcare Provider Recommendations: Encouraging healthcare providers to strongly recommend and provide information about COVID vaccination to their older patients could be part of policy strategies.
- Surveillance and Response: Policy could also involve strengthening surveillance systems to monitor COVID cases and hospitalisations, allowing for more timely and targeted responses during COVID outbreaks.

By demonstrating a causal link between COVID-19 vaccination and reduced hospitalisations, the study could provide strong evidence to support these policy initiatives, ultimately leading to better public health outcomes for older adults.


## Abstract
Real-world evidence is critical to evaluate COVID-19 vaccine effectiveness against hospitalisation after mass vaccination campaigns. This study analysed hospitalisation rates by vaccination status in adults 65–79 years old using population-level data. Public health surveillance data on COVID-19 outcomes and vaccination were analysed for adults aged 65–79 years over 96 weeks (2021–2023). Weekly hospitalisation rates were calculated and compared between unvaccinated (n = 38,188–47,760 per week) and vaccinated groups (n = 345,132-436,039 per week) using t-tests. The unvaccinated group had a significantly higher mean hospitalisation rate (0.000477) compared to the vaccinated group (0.000188) (p 0.05), representing a 60–70% rate reduction. The restricted age range minimises confounding. Real-world evidence confirms COVID-19 vaccination substantially reduces hospitalisation risk in adults 65–79 years old, aligning with clinical trial efficacy. These findings validate the public health impact of vaccination against severe COVID-19 outcomes in this high-risk population. Continued surveillance is needed to monitor the effectiveness against emerging variants.

## Data Collection
- The data  came from a public health agency tracking COVID-19 outcomes and vaccination rates at the population level.
- Outcomes like hospitalisations and deaths were tracked from health system administrative data on COVID-19 patients admitted.
- Vaccination rates were compiled from immunisation registries and provincial/state-level databases on doses administered.
- The data were aggregated at the weekly level for analysis.
- Individual-level data were not provided, but rather population-level counts of outcomes and vaccination numbers.
- The data collection was ongoing over 96 weeks as the pandemic and vaccination campaign evolved.
- Updates were made weekly by amalgamating new COVID-19 outcomes and vaccination numbers across the population.
- Analysis was limited to adults aged 65–79 years due to their priority for early COVID-19 vaccination.
- All metrics were stratified by age group and vaccination status for the comparative analysis.


## Data Analysis
```mdx
```python
import pandas as pd
import matplotlib.pyplot as plt
import scipy.stats as stats
import numpy as np

df = pd.read_csv('COVID-19_Outcomes_by_Vaccination_Status_-_No_All_Age_Group.csv')

# Drop rows with missing values
df.dropna(inplace=True)

print(df.dtypes)

# Columns that need commas removed  
cols = ['Population Unvaccinated', 'Population Vaccinated', 'Population Boosted']

# Remove commas
for col in cols:
    df[col] = df[col].str.replace(',','')

# Convert to integers
for col in cols:
    df[col] = df[col].astype(int)
```

```
Outcome                     object
Week End                    object
Age Group                   object
Population Unvaccinated     object
Population Vaccinated       object
Population Boosted          object
Outcome Unvaccinated         int64
Outcome Vaccinated           int64
Outcome Boosted            float64
dtype: object
```
Some of the data values in the columns were stored as strings with commas (e.g., 3,000 instead of 3000), hence, data preprocessing measures were undertaken to correct for these errors.

Next, the rates of incidents for both the vaccinated and unvaccinated groups were calculated as follows:
```python
# Add Vaccinated column
df['Vaccinated'] = df['Population Vaccinated'] + df['Population Boosted']
# Calculate rates  
df['Unvax Rate'] = df['Outcome Unvaccinated'] / df['Population Unvaccinated']
df['Vax Rate'] = (df['Outcome Vaccinated'] + df['Outcome Boosted']) / df['Vaccinated'] 
```

## Statistical Test
```mdx
```python
# Statistical test
tstat, pval = stats.ttest_ind(df['Unvax Rate'], df['Vax Rate'])
print("P-value:", pval)

if pval < 0.05:
    print('Vaccinated group has significantly lower hospitalization rate')
else:
    print('No significant difference in hospitalization rates')
```

```
P-value: 8.615839793189472e-05
Vaccinated group has significantly lower hospitalization rate
```

## Visualisation
```python
# Convert columns to arrays 
x = np.array(df['Week End'])
y1 = np.array(df['Unvax Rate'])
y2 = np.array(df['Vax Rate'])

# Set figure size
plt.figure(figsize=(20, 6)) 

# Plot rates 
plt.plot(x, y1, label='Unvaccinated')
plt.plot(x, y2, label='Vaccinated')
plt.title('Covid Hospitalization Rates')
plt.xlabel('Week Ending')
plt.ylabel('Rate')
plt.legend()
plt.savefig("Covid_Hospitalization_Rates.png")
plt.show()
```
![Covid Hospitalization Rates](Covid_Hospitalization_Rates.png)

```python
# Set figure size
plt.figure(figsize=(20, 10)) 

# Boxplot
plt.boxplot([y1, y2], labels=['Unvaccinated', 'Vaccinated'])
plt.ylabel('Hospitalization Rate')
plt.title('Hospitalization Rates by Vaccination Status')
plt.savefig("Hospitalization_Rates_by_Vaccination_Status.png")
plt.show()
```
![Hospitalization Rates by Vaccination Status](Hospitalization_Rates_by_Vaccination_Status.png)

## Results
```mdx
```python
print(df['Unvax Rate'].describe())
print(df['Vax Rate'].describe())
```

```
count    96.000000
mean      0.000477
std       0.000683
min       0.000000
25%       0.000131
50%       0.000297
75%       0.000444
max       0.003878
Name: Unvax Rate, dtype: float64


count    96.000000
mean      0.000188
std       0.000178
min       0.000014
25%       0.000077
50%       0.000157
75%       0.000221
max       0.001134
Name: Vax Rate, dtype: float64
```

The data is already filtered to adults aged 65–79, so age is not a confounding factor here. The unvaccinated group has a higher mean hospitalisation rate (0.000477) compared to the vaccinated group (0.000188). The t-test shows this difference is statistically significant (p < 0.05). The vaccines were rolled out over time, so earlier weeks had more unvaccinated individuals. However, when comparing the same weeks, the unvaccinated rate is consistently higher than the vaccinated rate. The vaccines were not randomly assigned, so differences in health-seeking behaviours may play a role. Those less concerned about COVID-19 may be less likely to vaccinate and to be hospitalised. However, behavioural factors are unlikely to fully explain the 2-3x higher rates consistently seen in the unvaccinated group.

## Conclusion
The significantly lower hospitalisation rates in the vaccinated group, controlling for age and time period, show that the COVID-19 vaccines provide protection against severe disease requiring hospitalisation. Some confounding from health behaviours may exist, but it is unlikely to change the overall finding of vaccines that provide a significant protective effect in this 65-79-year-old population. The real-world data are consistent with clinical trial results showing COVID-19 vaccines are efficacious against hospitalisation.

Abstract: Real-world evidence is critical to evaluate COVID-19 vaccine effectiveness against hospitalisation after mass vaccination campaigns. This study analyzed hospitalisation rates by vaccination status in adults 65-79 years using population-level data. Public health surveillance data on COVID-19 outcomes and vaccination were analyzed for adults aged 65-79 years over 96 weeks (2021-2023). Weekly hospitalisation rates were calculated and compared between unvaccinated (n=38,188-47,760 per week) and vaccinated groups (n=345,132-436,039 per week) using t-tests. The unvaccinated group had a significantly higher mean hospitalisation rate (0.000477) compared to the vaccinated group (0.000188) (p<0.05), representing a 60-70% rate reduction. The restricted age range minimizes confounding. Real-world evidence confirms COVID-19 vaccination substantially reduces hospitalisation risk in adults 65-79 years old, aligning with clinical trial efficacy. These findings validate the public health impact of vaccination against severe COVID-19 outcomes in this high-risk population. Continued surveillance is needed to monitor effectiveness against emerging variants.
