# Marketing Campaign Analysis

## Data Source

[Kaggle - Fast Food Marketing Campaign A/B Test](https://www.kaggle.com/datasets/chebotinaa/fast-food-marketing-campaign-ab-test)

## About
A fast-food chain is planning to add a new item to the menu, but before fully rolling out the item, the company wants to experiment with three different campaigns promoting the new item at restaurants in randomly selected markets. The idea is to see which promotion works best at bringing in sales, which is reported on a weekly basis over a 4 week period.

## Hypothesis

With three different promotions being tested, the goal is to measure the sales behind each promotion and determine whether there is a statistically significant difference in sales between the three promotions. That being said, the null and alternate hypothesis are as follows:

**Null**: There is **no** statistically significant difference in sales between the the three promotions.

**Alternate**: There is a statistically significant difference in sales between the three promotions.

## Quick Stats:
- Duration of campaign: **4 weeks**
- Number of participating restaurant locations: **137 locations**
- Number of participating markets: **10 markets**
  
  ![image](https://user-images.githubusercontent.com/100224330/177631817-5672bfc6-54cd-40c4-991e-9267aa52ffbc.png)
- Number of participating locations by market size:
  
  ![image](https://user-images.githubusercontent.com/100224330/177646947-bace64a8-19be-4c6d-a189-4a5436b837bd.png)

## Checking for Pretest Bias

**Question**: Are the promotions being evenly distributed across different market sizes?

  ![image](https://user-images.githubusercontent.com/100224330/177635927-f4582950-f2ec-405a-9a81-94a310f37ea6.png)
  
While Promotion 2 is more commonly rolled out than the other two, each promotion is most commonly being tested in medium sized markets.

**Question**: Are the promotions being tested at stores with the same average age?

  ![image](https://user-images.githubusercontent.com/100224330/177644648-5ff166a6-d396-40ea-ae21-aec9c09f2292.png)
  
The average store age appears to be between 8-9 years old, so no real imbalance here. We can also check if there is any relationship between age of the store and sales.

![image](https://user-images.githubusercontent.com/100224330/177645689-41943893-ecc9-45c3-953e-709566022c98.png)<br>
There appears to be no relationship between the age of the store and sales, so even an average difference of 1 year shouldn't make an impact.

## Visualizng Sales by Promotion

![image](https://user-images.githubusercontent.com/100224330/177646282-d0fc46bb-f024-476b-a03b-652f1a1d170b.png)

Based on the visual of sales over the 4 week period, it's clear that promotions 3 and 1 have the most sales, with promotion 2 having the least sales. Just comparing the sales figures is not enough however, as we need to determine whether these differences are statistically significant. We can start exploring this through a one-way ANOVA test.

## One-Way ANOVA Test

Creating a series for sales of each promotion
```
promo_1 = df[df['Promotion'] == 1]['SalesInThousands']
promo_2 = df[df['Promotion'] == 2]['SalesInThousands']
promo_3 = df[df['Promotion'] == 3]['SalesInThousands']
```
Performing the ANOVA test
```
stats.f_oneway(promo_1, promo_2, promo_3)
```
Which yields the result below
```
F_onewayResult(statistic=21.953485793080677, pvalue=6.765849261408714e-10)
```
#### Analyzing the Results
With a p-value that is much lower than an alpha value of 0.05 or 0.01, indicating that there is an statistically significant difference in the mean sales between the three promotions. However, which promotion's sales is causing the statistically significant difference in sales? We can use the **Tukey** test to tell us which differences are significant.

## Post-Hoc Testing
```
from statsmodels.stats.multicomp import pairwise_tukeyhsd

tukey = pairwise_tukeyhsd(endog=df['SalesInThousands'],
                         groups = df['Promotion'],
                         alpha = 0.05)

tukey.plot_simultaneous()
plt.title('Sales Confidence Intervals for Promotions', fontsize=14, fontweight='bold')
tukey.summary()
```
  ![image](https://user-images.githubusercontent.com/100224330/177649516-fbde3106-4d48-4468-bb38-cf07381a0261.png)
  ![image](https://user-images.githubusercontent.com/100224330/177649534-777eb20c-9071-4b49-8036-f60b87d89844.png)

**Analyzing the Results**

The Tukey test summary table show us the mean difference in sales, the p-values, and whether we can accept (True) or reject (False) the null hypothesis. The results show that there is a statistically significant difference between promotions 1 and 2 and between 2 and 3, while there is not a statistically significant difference between promotions 1 and 3.

## Recommendations
Promotions 1 and 3 both outperform promotion 2, so we wouldn't recommend promotion 2. Because the mean sales for promotions 1 and 3 are so close together, we can't confidently recommend one over the other. Below are some recommendations to consider:

- Roll out both promotion 2 and 3.
- Compare and contrast the elements of promotion 2 and 3 to devise a new promotion that combines the best elements of both into one.
- Make this a true A/B test. Measure sales at restaurants where the new item has not been added to the menu as part of a control group, and then rollout just one promotion at select restaurants. Sales can be measured pre-test and during the test to determine the impact on sales and whether there is a statistically significant difference or not.

## Ideas for Additional Data
- Demographic info on the markets these restaurants are in
- More granular sales reporting
    - Sales for both the new item introduced and overall restaurant sales
    - What percentage of sales for the new item came in-store vs. drive-thru?
    - What other food/drink items were commonly purchased along with the new item?
- What other food/drink items were commonly purchased along with the new item?
- An indication of whether there are any other ongoing promotions running concurrently at these restaurants.
