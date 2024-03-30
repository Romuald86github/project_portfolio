
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


# importing necessary libraries


```import pandas as pd
import numpy as np
import seaborn as sns
import matplotlib.pyplot as plt
import warnings
warnings.filterwarnings('ignore')
import pickle 
```


# loading the data

```df = pd.read_csv('/Users/romualdchristialtcheutchoua/Downloads/product_sales.csv')
```


# Data validation

## Data types, missing values, column data's consistency

```df.info()```



```for i in df.columns:
    print(df[i].value_counts())
```
    
 ``` df.nunique()```
 

- the column 'week' doesn’t have missing value. But it is more appropriate to be a categorical data type as the numbers present are just identifying week categories. So, this column is  changed to 'object' type
- Sales_method has 5 unique values while we should have only three; we should make sure all the values belong to ['Call', 'Email', 'Email + Call'].  'email' is converted to to 'Email' and 'em + call' to 'Email + Call'
- the column 'revenue' contains 1074 missing data. removing these data points will cause data lost problem.  the missing data are replaced with the mean of overall revenue
- the column revenue has more than 2 decimals. the decimals are reduced to 2


``` df['week'] = df['week'].astype(object)```

``` df['sales_method'] = df['sales_method'].replace('email', 'Email')
df['sales_method'] = df['sales_method'].replace('em + call', 'Email + Call')
```

``` df['revenue'] = df['revenue'].fillna(df['revenue'].mean())```

``` df['revenue'] = df['revenue'].round(2)```


## outliers 

```
def box(columns, data, a, b, c, d, save_path, plot_name):
    plt.figure(figsize=(15, 7), facecolor='purple')
    j = 1
    for i in columns:
        if j <= a:
            ax = plt.subplot(b, c, j)
            sns.boxplot(data[i], palette='colorblind')
            plt.xlabel(i, fontsize=d)
        j += 1
        
    plt.suptitle('Boxplot to Check Outliers', fontsize=16)  # Set the title of the plot
    plt.tight_layout()
    plt.savefig(save_path + '/' + plot_name + '.png')
    plt.show()
    
col_num = ['nb_sold', 'revenue', 'years_as_customer', 'nb_site_visits']
box(col_num, df, len(col_num), 2, 2, 12, save_path, plot_name)
```

``` for i in df.columns:
      if df[i].dtypes in ['float', 'int']:
         print(df[i].value_counts())```


- the company was founded in 1984. So, it has 40 years of existence. any 'years_as_customer' value above 40 are removed.
- high values in 'revenue' are not outliers, as there is no limit to revenue
- high values in 'nb_sold' are not outliers, as the is not a quota for each customer
- high values in 'nb_site_visits' are not ouliers, as this is naturally feasible.

        

``` df = df[df['years_as_customer'] <= 40]```


## summary statistics of the cleaned data


``` df.describe()```


-  rows values represent ['count', 'mean', 'std', 'min', '25%', '50%', '75%', 'max']


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
Mean: The average revenue generated per transaction is approximately USD 93.94.
Standard Deviation: The revenue varies around the mean by approximately USD 45.71.
Minimum: The minimum revenue from a transaction is USD32.54.
25th Percentile (Q1): 25% of transactions generate revenue of USD53.04 or less.
Median (50th Percentile, Q2): 50% of transactions generate revenue of USD91.87 or less.
75th Percentile (Q3): 75% of transactions generate revenue of USD106.07 or less.
Maximum: The maximum revenue from a transaction is USD238.32.

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

``` 
# Columns for which histograms are to be plotted
columns = ['nb_sold', 'revenue', 'years_as_customer', 'nb_site_visits']

# Create a figure with subplots
fig, axes = plt.subplots(nrows=2, ncols=2, figsize=(12, 8))

# Flatten the axes array for easy iteration
axes = axes.flatten()

# Plot histograms for each column
for i, column in enumerate(columns):
    ax = axes[i]
    ax.hist(df[column], bins=20, color='skyblue', edgecolor='black')
    ax.set_title(f'Distribution of {column}')
    ax.set_xlabel(column)
    ax.set_ylabel('Frequency')

# Adjust layout
plt.tight_layout()

# Show the plot
plt.show()
```


``` 
col_num = ['nb_sold', 'revenue', 'years_as_customer', 'nb_site_visits']

box(col_num, df, len(col_num), 2, 2, 12)```



- correlation vs revenue



``` 
# Plot heatmap for the entire DataFrame
plt.figure(figsize=(12, 8))
sns.heatmap(df.corr(), annot=True, cmap='coolwarm', linewidths=0.5)
plt.title('Correlation Heatmap for DataFrame')



# Plot correlations with revenue
plt.figure(figsize=(8, 6))
correlation_with_revenue = df.corr()['revenue'].sort_values(ascending=False)[1:]  # Exclude revenue itself
correlation_with_revenue.plot(kind='bar', color='skyblue')
plt.title('Correlation of Features with Revenue')
plt.xlabel('Features')
plt.ylabel('Correlation with Revenue')
plt.xticks(rotation=45)
plt.tight_layout()

# Show the plots
plt.show()
```



## comments
- 'nb_sold' and 'nb_site_visits' looks normally distributed; their means are close to medians
- 'years_as_customer' and 'revenue' are right-skewed; their means are higher than medians
- 77.27% of 'nb_sold' (neew product sold) are between 8 and 11. this can be used as an estimate interval of expected future sales per customer. 
- the distribution of 'years_as_customer' Highlights an increase of customer churn rate. Customers are ending their relationship with the company over time. 
- 80.18% of Customers visited the site 21 to 29 times
- 'nb_site_visits' is positively correlated with 'revenue'
- 'years_as_customer' is weakly negatively correlated with 'revenue'


## how many customers were there for any approach?


``` 
# Count the number of customers for each approach
approach_counts = df['sales_method'].value_counts()

# Display the counts for each approach
print(approach_counts)```


``` 
# Count the number of customers for each approach
approach_counts = df['sales_method'].value_counts()

# Display the counts for each approach
print(approach_counts)```


``` 
# Count the number of customers for each approach
approach_counts = df['sales_method'].value_counts()

# Create a bar plot
plt.figure(figsize=(10, 6))
approach_counts.plot(kind='bar', color='skyblue')
plt.title('Number of Customers for Each Sales Approach')
plt.xlabel('Sales Approach')
plt.ylabel('Number of Customers')
plt.xticks(rotation=45)  # Rotate x-axis labels for better readability
plt.grid(axis='y', linestyle='--', alpha=0.7)
plt.tight_layout()

# Show the plot
plt.show()```



``` 
import squarify

# Calculate sales method counts
sales_method_counts = df['sales_method'].value_counts()

# Define colors (3 colors)
colors = ['#ff9999', '#66b3ff', '#99ff99'] 

# Plot treemap with custom colors
plt.figure(figsize=(10, 6))
squarify.plot(sizes=sales_method_counts.values, label=sales_method_counts.index, alpha=0.8, color=colors)
plt.axis('off')
plt.title('Treemap of Sales Method')
plt.show()
```


- graph 
- table
|Email|Call|Email + Call|
|---|---|---|
|7465|4961|2572|



## what does the spread of revenue look like overall? And for each method?


``` 
# Set the style of seaborn
sns.set_style("whitegrid")

# Create a figure and axis
plt.figure(figsize=(10, 6))

# Overall revenue spread
plt.subplot(1, 2, 1)
sns.boxplot(data=df, y='revenue', color='skyblue')
plt.title('Overall Revenue Spread')

# Revenue spread for each sales method
plt.subplot(1, 2, 2)
sns.boxplot(data=df, x='sales_method', y='revenue', palette='colorblind')
plt.title('Revenue Spread by Sales Method')

# Adjust layout
plt.tight_layout()

# Show the plot
plt.show()```




``` 
# Set the style of seaborn
sns.set_style("whitegrid")

# Create a figure and axis
plt.figure(figsize=(10, 6))

# Overall revenue spread
plt.subplot(1, 2, 1)
sns.violinplot(data=df, y='revenue', color='skyblue')
plt.title('Overall Revenue Spread')

# Revenue spread for each sales method
plt.subplot(1, 2, 2)
sns.violinplot(data=df, x='sales_method', y='revenue', palette='colorblind')
plt.title('Revenue Spread by Sales Method')

# Adjust layout
plt.tight_layout()

# Show the plot
plt.show()
```




Minimum: The minimum revenue from a transaction is USD32.54.
25th Percentile (Q1): 25% of transactions generate revenue of USD53.04 or less.
Median (50th Percentile, Q2): 50% of transactions generate revenue of USD91.87 or less.
75th Percentile (Q3): 75% of transactions generate revenue of USD106.07 or less.
Maximum: The maximum revenue from a transaction is USD238.32.



The mixed method ('Email + Call') generates most revenue while 'Call' alone generates the least revenue. 


## Was there any difference in revenue over time for each of the methods?


``` 
import statsmodels.api as sm
from scipy.stats import pearsonr

# Filter the dataframe for each sales method and group revenue data by week
sales_methods = df['sales_method'].unique()
correlation_results = {}

for method in sales_methods:
    method_data = df[df['sales_method'] == method]
    grouped_method_data = method_data.groupby('week')['revenue'].mean()
    X = sm.add_constant(grouped_method_data.index)
    model = sm.OLS(grouped_method_data, X)
    results = model.fit()

    # Plot scatterplot with regression line
    plt.figure(figsize=(10, 6))
    plt.scatter(grouped_method_data.index, grouped_method_data, label='Actual Revenue')
    plt.plot(X[:, 1], results.predict(X), color='red', label='Regression Line')
    plt.title(f'Revenue Trend Over Time for "{method}" Sales Method')
    plt.xlabel('Week')
    plt.ylabel('Revenue')
    plt.legend()
    plt.grid(True)
    plt.tight_layout()

    # Calculate correlation coefficient and p-value
    correlation_coefficient, p_value = pearsonr(grouped_method_data.index, grouped_method_data)
    correlation_results[method] = {'correlation_coefficient': correlation_coefficient, 'p_value': p_value}

    # Print correlation coefficient and p-value
    plt.text(0.5, 0.02, f'Correlation Coefficient: {correlation_coefficient:.2f}\nP-value: {p_value:.4f}', horizontalalignment='center', verticalalignment='bottom', transform=plt.gca().transAxes, fontsize=10)

    # Save the plot as image
    save_path = '/Users/romualdchristialtcheutchoua/Desktop/Plots_saved'
    file_name = f'Revenue_trend_over_time_for_{method}_sales_method.png'
    plt.savefig(f"{save_path}/{file_name}", bbox_inches='tight', pad_inches=0.5)

    # Show the plot
    plt.show()

# Interpret p-values
for method, values in correlation_results.items():
    if values['p_value'] < 0.05:
        print(f"For the '{method}' sales method, there is a significant linear relationship between revenue and time.")
    else:
        print(f"For the '{method}' sales method, there is no significant linear relationship between revenue and time.")
```


For the 'Email' sales method, there is a significant linear relationship between revenue and time.
For the 'Email + Call' sales method, there is a significant linear relationship between revenue and time.
For the 'Call' sales method, there is a significant linear relationship between revenue and time.


Based on the above analysis, we recommend to used the mixed method ('Email + Call') but to reduce the amount of calls as optimally minimal as possible and maximize the amount of emails as optimally as possible. 

* other exploratory graphs: state-wise analysis *

``` 
# Group by 'state' and 'sales_method', then count the number of occurrences
sales_method_per_state = df.groupby(['state', 'sales_method']).size().unstack(fill_value=0)

# Plot
plt.figure(figsize=(12, 8))
sales_method_per_state.plot(kind='barh', stacked=True, ax=plt.gca(), color=['#1f77b4', '#ff7f0e', '#2ca02c'])
plt.title('Number of Sales Methods per State')
plt.xlabel('Count')
plt.ylabel('State')
plt.legend(title='Sales Method')
plt.tight_layout()
plt.savefig('/Users/romualdchristialtcheutchoua/Desktop/Plots_saved_datacamp/Number_of_sales_methods_per_state.png', bbox_inches='tight')
plt.show(```



``` 
# Group the DataFrame by 'state' and calculate the sum of 'nb_sold' for each state
nb_sold_per_state = df.groupby('state')['nb_sold'].sum().sort_values(ascending=False)

# Plot
plt.figure(figsize=(12, 8))
nb_sold_per_state.plot(kind='barh', color='skyblue')
plt.title('Number of New Products Sold per State')
plt.xlabel('Number of Items Sold')
plt.ylabel('State')
plt.tight_layout()
plt.savefig('/Users/romualdchristialtcheutchoua/Desktop/Plots_saved_datacamp/Number_of_new_products_sold_per_state.png', bbox_inches='tight')
plt.show()
```



``` 
# Group the DataFrame by 'state' and calculate the sum of 'nb_site_visits' for each state
site_visits_per_state = df.groupby('state')['nb_site_visits'].sum().sort_values(ascending=False)

# Plot
plt.figure(figsize=(12, 8))
site_visits_per_state.plot(kind='barh', color='lightgreen')
plt.title('Number of Site Visits per State')
plt.xlabel('Number of Site Visits')
plt.ylabel('State')
plt.tight_layout()
plt.savefig('/Users/romualdchristialtcheutchoua/Desktop/Plots_saved_datacamp/Number_of_site_visits_per_state.png', bbox_inches='tight')
plt.show()```



``` 
# Group the DataFrame by 'state' and calculate the sum of 'revenue' for each state
revenue_per_state = df.groupby('state')['revenue'].sum().sort_values(ascending=False)

# Plot
plt.figure(figsize=(12, 8))
revenue_per_state.plot(kind='barh', color='orange')
plt.title('Revenue per State')
plt.xlabel('Revenue')
plt.ylabel('State')
plt.tight_layout()
plt.savefig('/Users/romualdchristialtcheutchoua/Desktop/Plots_saved_datacamp/Revenue_per_state.png', bbox_inches='tight')
plt.show()
```




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

``` 
# Calculate average revenue per customer visit
total_revenue = df['revenue'].sum()
total_visits = df['nb_site_visits'].sum()
average_revenue_per_visit = total_revenue / total_visits

# Display the initial metric value
print(f"Initial average revenue per customer visit: ${average_revenue_per_visit:.2f}")

# Create a plot to display the result
plt.figure(figsize=(6, 4))
plt.text(0.5, 0.5, f"Initial average revenue per customer visit: ${average_revenue_per_visit:.2f}",
         ha='center', va='center', fontsize=14)
plt.axis('off')
plt.close()
```


Initial average revenue per customer visit: $3.76. 



# Final summary including recommendations that the business should undertake

- By focusing on optimizing email communication, reducing call volume, and closely monitoring the average revenue per customer visit metric, the business can improve revenue generation while maximizing efficiency and customer engagement. Implementing these recommendations will help the business achieve its revenue goals and enhance overall performance in the long run.









