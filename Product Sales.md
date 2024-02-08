
Hey!
I have an analysis task for you from the sales team. I think you will find it interesting, there is lots of flexibility for you to try different methods. You can see the request in the email below.
I would like you to perform the analysis and write a short report for me. I don’t need to see any code, but I do want to read your thinking and how you got to your conclusions.
I also want you to prepare and deliver the presentation for the sales rep - you are ready for the challenge!
You can find more details about what I expect you to do here. And information on the data here.
I will be on vacation for the next couple of weeks, but I know you can do this without my support. If you need to make any decisions, include them in your work and I will review them when I am back.
Good Luck!



Hi,
Really hoping you could help me out with some analysis. This is quite short notice but I have a meeting with the executive team to update them on sales approaches for the new product line so I need this information as soon as possible.
We need to know:
- How many customers were there for each approach?
- What does the spread of the revenue look like overall? And for each method?
- Was there any difference in revenue over time for each of the methods?
- Based on the data, which method would you recommend we continue to use? Some
of these methods take more time from the team so they may not be the best for us to use if the results are similar.
We don’t really know if there are other differences between the customers in each group, so anything you can tell us would be really helpful to give some context to what went well.
I need to report to the executive team in 4 weeks. You need to present to me before then so I understand what is going on and what we do next.
Look forward to seeing your presentation.



About Pens and Printers
Pens and Printers was founded in 1984 and provides high quality office products to large organizations. We are a trusted provider of everything from pens and notebooks to desk chairs and monitors. We don’t produce our own products but sell those made by other companies.
We have built long lasting relationships with our customers and they trust us to provide them with the best products for them. As the way in which consumers buy products is changing, our sales tactics have to change too. Launching a new product line is expensive and we need to make sure we are using the best techniques to sell the new product effectively. The best approach may vary for each new product so we need to learn quickly what works and what doesn’t.
New Product Sales Methods
Six weeks ago we launched a new line of office stationery. Despite the world becoming increasingly digital, there is still demand for notebooks, pens and sticky notes.
Our focus has been on selling products to enable our customers to be more creative, focused on tools for brainstorming. We have tested three different sales strategies for this, targeted email and phone calls, as well as combining the two.
Email: Customers in this group received an email when the product line was launched, and a further email three weeks later. This required very little work for the team.
Call: Customers in this group were called by a member of the sales team. On average members of the team were on the phone for around thirty minutes per customer.
Email and call: Customers in this group were first sent the product information email, then called a week later by the sales team to talk about their needs and how this new product may support their work. The email required little work from the team, the call was around ten minutes per customer.





## 📝 Task List

Your written report should include written text summaries and graphics of the following:
- Data validation:   
  - Describe validation and cleaning steps for every column in the data 
- Exploratory Analysis:  
  - Include two different graphics showing single variables only to demonstrate the characteristics of data  
  - Include at least one graphic showing two or more variables to represent the relationship between features
  - Describe your findings
- Definition of a metric for the business to monitor  
  - How should the business use the metric to monitor the business problem
  - Can you estimate initial value(s) for the metric based on the current data
- Final summary including recommendations that the business should undertake

*Start writing report here..*


# Data validation

## Data types, missing values, column data's consistency
- the column 'week' doesn’t have missing value. But it is more appropriate to be a categorical data type as the numbers present are just identifying week categories. So, this column is  changed to 'object' type
- Sales_method has 5 unique values while we should have only three; we should make sure all the values belong to ['Call', 'Email', 'Email + Call'].  'email' is converted to to 'Email' and 'em + call' to 'Email + Call'
- the column 'revenue' contains 1074 missing data. removing these data points will cause data lost problem.  the missing data are replaced with the mean of overall revenue
- the column revenue has more than 2 decimals. the decimals are reduced to 2

## outliers (see figure: boxplot_outliers)
- the company was founded in 1984. So, it has 40 years of existence. any 'years_as_customer' value above 40 are removed.
- high values in 'revenue' are not outliers, as there is no limit to revenue
- high values in 'nb_sold' are not outliers, as the is not a quota for each customer
- high values in 'nb_site_visits' are not ouliers, as this is naturally feasible.

![showing outliers](boxplot_outliers.png)


## summary statistics of the cleaned data
-  rows values represent ['count', 'mean', 'std', 'min', '25%', '50%', '75%', 'max']

![sum stat](summary_statistics_of_cleaned_data.png)

## comments:

- nb_sold (Number of new products Sold):

Count: There are 14,998 observations in the dataset.
Mean: On average, approximately 10 items are sold per transaction.
Standard Deviation: The number of items sold varies around the mean by approximately 1.81.
Minimum: The minimum number of items sold in a transaction is 7.
25th Percentile (Q1): 25% of transactions involve 9 or fewer items.
Median (50th Percentile, Q2): 50% of transactions involve 10 items.
75th Percentile (Q3): 75% of transactions involve 11 or fewer items.
Maximum: The maximum number of items sold in a transaction is 16.

- revenue (Revenue from Sales):

Count: There are 14,998 observations in the dataset.
Mean: The average revenue generated per transaction is approximately $93.94.
Standard Deviation: The revenue varies around the mean by approximately $45.71.
Minimum: The minimum revenue from a transaction is $32.54.
25th Percentile (Q1): 25% of transactions generate revenue of $53.04 or less.
Median (50th Percentile, Q2): 50% of transactions generate revenue of $91.87 or less.
75th Percentile (Q3): 75% of transactions generate revenue of $106.07 or less.
Maximum: The maximum revenue from a transaction is $238.32.

- years_as_customer (Years as a Customer):

Count: There are 14,998 observations in the dataset.
Mean: On average, customers have been with the company for approximately 4.96 years.
Standard Deviation: The duration of customer relationships varies around the mean by approximately 5.01 years.
Minimum: The shortest duration as a customer is 0 years, indicating new customers.
25th Percentile (Q1): 25% of customers have been with the company for 1 year or less.
Median (50th Percentile, Q2): 50% of customers have been with the company for 3 years or less.
75th Percentile (Q3): 75% of customers have been with the company for 7 years or less.
Maximum: The longest duration as a customer is 39 years.

- nb_site_visits (Number of Site Visits):

Count: There are 14,998 observations in the dataset.
Mean: On average, customers visit the company's website approximately 25 times.
Standard Deviation: The number of site visits varies around the mean by approximately 3.50 visits.
Minimum: The minimum number of site visits is 12.
25th Percentile (Q1): 25% of customers visit the website 23 times or fewer.
Median (50th Percentile, Q2): 50% of customers visit the website 25 times or fewer.
75th Percentile (Q3): 75% of customers visit the website 27 times or fewer.
Maximum: The maximum number of site visits is 41.



# Exploratory analysis

## distribution plots and Correlation 
- Distribution of 'nb_sold', 'revenue', 'years_as_customer', 'nb_site_visits': histogram 
- Distribution of 'nb_sold', 'revenue', 'years_as_customer', 'nb_site_visits': boxplot 

![Dist](Distribution_of_nb_sold_revenue_years_as_customer_nb_site_visits.png)

![Box](boxplot_outliers_removed.png)

- correlation vs revenue

![Heat](Correlation_heatmap.png)

![Corr](Correlation_vs_revenue.png)

## comments
- 'nb_sold' and 'nb_site_visits' looks normally distributed; their means are close to medians
- 'years_as_customer' and 'revenue' are right-skewed; their means are higher than medians
- 77.27% of 'nb_sold' (neew product sold) are between 8 and 11. this can be used as an estimate interval of expected future sales per customer. 
- the distribution of 'years_as_customer' Highlights an increase of customer churn rate. Customers are ending their relationship with the company over time. 
- 80.18% of Customers visited the site 21 to 29 times
- 'nb_site_visits' is positively correlated with 'revenue'
- 'years_as_customer' is weakly negatively correlated with 'revenue'


## how many customers were there for any approach?
- graph 
- table
|Email|Call|Email + Call|
|---|---|---|
|7465|4961|2572|

![Ap](approach_counts.png)

![Bar](Number_of_Customers_for_Each_Sales_Approach.png)


![Tree](Sales_method_treemap.png)

## what does the spread of revenue look like overall? And for each method?

Minimum: The minimum revenue from a transaction is $32.54.
25th Percentile (Q1): 25% of transactions generate revenue of $53.04 or less.
Median (50th Percentile, Q2): 50% of transactions generate revenue of $91.87 or less.
75th Percentile (Q3): 75% of transactions generate revenue of $106.07 or less.
Maximum: The maximum revenue from a transaction is $238.32.

![Rev](Spread_of_revenue.png)

![Vio](Revenue_distribution_violin_plots.png)


The mixed method ('Email + Call') generates most revenue while 'Call' alone generates the least revenue. 


## Was there any difference in revenue over time for each of the methods?

For the 'Email' sales method, there is a significant linear relationship between revenue and time.
For the 'Email + Call' sales method, there is a significant linear relationship between revenue and time.
For the 'Call' sales method, there is a significant linear relationship between revenue and time.

![Em](Revenue_trend_over_time_for_Email_sales_method.png)

![EmC](Revenue_trend_over_time_for_Email_Call_sales_method.png)

![Call](Revenue_trend_over_time_for_call_sales_method.png)

Based on the above analysis, we recommend to used the mixed method ('Email + Call') but to reduce the amount of calls as optimally minimal as possible and maximize the amount of emails as optimally as possible. 

* other exploratory graphs: state-wise analysis *

![RS](Revenue_per_state.png)

![NS](Number_of_site_visits_per_state.png)

![PS](Number_of_sales_methods_per_state.png)

![N](Number_of_new_products_sold_per_state.png)




# Definition of a metric for the business to monitor

Based on the analysis performed above, a metric that could be valuable for the business to monitor is the average revenue per customer visit. This metric combines insights from various analyses conducted:

- Correlation Analysis: We've examined the correlation between revenue and various factors such as the number of site visits, years as a customer, and other features. By focusing on revenue per visit, the business can track how changes in these factors affect revenue generation.

- Sales Method Analysis: We've analyzed revenue trends over time for different sales methods. Monitoring revenue per visit can help evaluate the effectiveness of each sales approach in converting customer visits into revenue.

- Customer Behavior Analysis: We've explored customer behavior metrics such as site visits and years as a customer. Tracking revenue per visit can provide insights into customer spending patterns and preferences, allowing the business to tailor marketing strategies and improve customer engagement.

By monitoring the average revenue per customer visit over time, the business can assess the effectiveness of its marketing campaigns, sales strategies, and customer engagement efforts. This metric serves as a key performance indicator (KPI) for evaluating business performance and making data-driven decisions to drive revenue growth.

## How should the business use the metric to monitor the business problem

To effectively use the average revenue per customer visit metric to monitor the business problem, the business can follow these steps:

- Set Clear Goals: Define specific goals or targets for the average revenue per customer visit metric. These goals should align with the overall business objectives, such as increasing revenue, improving customer engagement, or optimizing marketing strategies.

- Regular Monitoring: Continuously monitor the average revenue per customer visit metric over time. This can be done on a daily, weekly, or monthly basis, depending on the business's preference and the frequency of customer interactions.

- Identify Trends and Patterns: Analyze trends and patterns in the average revenue per customer visit metric. Look for any significant changes or deviations from the expected values. For example, sudden drops in the metric could indicate issues with customer satisfaction or changes in market conditions.

- Root Cause Analysis: Investigate the underlying factors driving changes in the metric. This may involve analyzing customer behavior, marketing campaign performance, sales strategies, or external factors impacting consumer spending.

- Implement Strategies: Based on the insights gained from monitoring the metric, implement targeted strategies to address any issues or capitalize on opportunities. This could involve adjusting pricing strategies, launching targeted marketing campaigns, improving customer service, or optimizing product offerings.

- Track Impact: Monitor the impact of implemented strategies on the average revenue per customer visit metric. Evaluate whether the changes lead to improvements in revenue generation and customer engagement. Adjust strategies as needed based on ongoing performance monitoring.


## Can you estimate initial value(s) for the metric based on the current data

Initial average revenue per customer visit: $3.76. 

![IT](Initial_average_revenue_per_customer_visit.png)

# Final summary including recommendations that the business should undertake

- By focusing on optimizing email communication, reducing call volume, and closely monitoring the average revenue per customer visit metric, the business can improve revenue generation while maximizing efficiency and customer engagement. Implementing these recommendations will help the business achieve its revenue goals and enhance overall performance in the long run.









