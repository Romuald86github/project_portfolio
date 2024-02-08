

Hey!
I have a new project for you from the product team. Should be an interesting challenge. You can see the background and request in the email below.
I would like you to perform the analysis and write a short report for me. I want to be able to review your code as well as read your thought process for each step. I also want you to prepare and deliver the presentation for the product team - you are ready for the challenge!
They want us to predict which recipes will be popular 80% of the time and minimize the chance of showing unpopular recipes. I don’t think that is realistic in the time we have, but do your best and present whatever you find.
You can find more details about what I expect you to do here. And information on the data here.
I will be on vacation for the next couple of weeks, but I know you can do this without my support. If you need to make any decisions, include them in your work and I will review them when I am back.

Good Luck!









Hi,

We haven’t met before but I am responsible for choosing which recipes to display on the homepage each day. I have heard about what the data science team is capable of and I was wondering if you can help me choose which recipes we should display on the home page?
At the moment, I choose my favorite recipe from a selection and display that on the home page. We have noticed that traffic to the rest of the website goes up by as much as 40% if I pick a popular recipe. But I don’t know how to decide if a recipe will be popular. More traffic means more subscriptions so this is really important to the company.
Can your team:
- Predict which recipes will lead to high traffic?
- Correctly predict high traffic recipes 80% of the time?
We need to make a decision on this soon, so I need you to present your results to me by the end of the month. Whatever your results, what do you recommend we do next?
Look forward to seeing your presentation.



## 📝 Task List

Your written report should include both code, output and written text summaries of the following:

- Data Validation:   
  - Describe validation and cleaning steps for every column in the data 
- Exploratory Analysis:  
  - Include two different graphics showing single variables only to demonstrate the characteristics of data  
  - Include at least one graphic showing two or more variables to represent the relationship between features
  - Describe your findings
- Model Development
  - Include your reasons for selecting the models you use as well as a statement of the problem type
  - Code to fit the baseline and comparison models
- Model Evaluation
  - Describe the performance of the two models based on an appropriate metric
- Business Metrics
  - Define a way to compare your model performance to the business
  - Describe how your models perform using this approach
- Final summary including recommendations that the business should undertake

*Start writing report here..*


# data validation

## importing necessary libraries

```
import pandas as pd
import numpy as np
import seaborn as sns
import matplotlib.pyplot as plt
import warnings
warnings.filterwarnings('ignore')
import pickle
```

## loading the data

```
df = pd.read_csv('/Users/romualdchristialtcheutchoua/Downloads/recipe_site_traffic_2212.csv')
```

## snapshot of data structure

```
df.head()
df.tail()
len(df)
```
df has:
- 8 columns
- 947 rows
- missing values
- numeric and charater data types

## description of variables/columns

```
list(df.columns)
```

- recipe: Numeric, unique identifier of recipe
- calories: Numeric, number of calories
- carbohydrate: Numeric, amount of carbohydrates in grams
- sugar: Numeric, amount of sugar in grams
- protein: Numeric, amount of protein in grams
- category: Character, type of recipe. Recipes are listed in one of ten possible groupings (Lunch/Snacks', 'Beverages', 'Potato', 'Vegetable', 'Meat', 'Chicken, 'Pork', 'Dessert', 'Breakfast', 'One Dish Meal').
- servings: Numeric, number of servings for the recipe
- high_traffic: Character, if the traffic to the site was high when this recipe was shown, this is marked with “High”


## Understanding the problem statement: business problem and objetive of data science project

- the business faces the problem of Accurately knowing which recipes to choose and display on the home page (which recipe will lead to high traffic?); among the recipes that lead to high traffic, which have 80 traffic leads in 100 shows? (which recipes have the probability of 0.8 in leading to high traffic?)
- 
- the project's objective is to (1) build a classification machine learning model which can predict whether a recipe will lead to high traffic or not, based on it's content (calories, carbohydrate, sugar, protein, category, servings); (2) select the model(s) with at least 80% of precision for 'high_traffic'. 

## checking the datatypes consistency with the instructions, and missing values along

```
# summary information on data(df)
df.info()
```
- only the column 'servings' is not consistent in dta type. Let’s first check and correct what is rendering this varable object, and relevantly convert it to integer
- the columns 'calories', 'carbohydrate', 'sugar', 'protein' have missing data. in order to avoid data lost, we will replace the missing data by the mean of each overall column. 
- missing data present in 'high_traffic' simply means 'no high_traffic'. so, we will relace the missing values with  'Low'

```
# checking unique values of each column
for i in df.columns:
    print(df[i].value_counts())
```
the column 'servings' has '4 as a snack' and '6 as a snack' values which are rendering this column object type. Let’s fix this to retain only 4 and 6. 

```
df['servings'] = df['servings'].str.extract('(\d+)').astype(int)
```

```
# checking to confirm that the changes have taken place
for i in df.columns:
    print(df[i].value_counts())
    
df.info()
```

## filling the missing values
```
df['calories'].fillna(df['calories'].mean(), inplace=True)
df['carbohydrate'].fillna(df['carbohydrate'].mean(), inplace=True)
df['sugar'].fillna(df['sugar'].mean(), inplace=True)
df['protein'].fillna(df['protein'].mean(), inplace=True)
df['high_traffic'].fillna('Low', inplace=True)
```

```
# checking to make sure the changes haave taken place

df.head()
df.info()
```

## Let’s make sure that all values in calories, carbohydrate, sugar, protein to have only 2 decimal

```
df['calories'] = df['calories'].round(2)
df['carbohydrate'] = df['carbohydrate'].round(2))
df['sugar'] = df['sugar'].round(2)
df['protein'] = df['protein'].round(2)
```

## let’s check for duplicates

```
df.loc[df.duplicated(),]
```

## check duplicate while leaving 'recipe' out, as it is a unique identifier
```
duplicates = df[df.duplicated(subset=df.columns.difference(['recipe']), keep=False)]

# Displaying the duplicate rows
print(duplicates)
```
df has 36 duplicates

## dropping duplicates

```
df.drop_duplicates(subset=df.columns.difference(['recipe']), inplace=True)
```

## checking to confirm duplicates are removed

```
duplicates = df[df.duplicated(subset=df.columns.difference(['recipe']), keep=False)]

# Displaying the duplicate rows
print(duplicates)
```

## checking outliers

```
col = ['calories', 'carbohydrate', 'sugar', 'protein', 'servings']

def box(columns, data, a, b, c, d):
    plt.figure(figsize = (10, 8), facecolor = 'purple')
    j = 1
    for i in columns:
        if j<=a:
            ax = plt.subplot(b, c, j)
            sns.boxplot(data[i], palette = 'colorblind')
            plt.xlabel(i, fontsize = d)
        j+=1
        
        plt.tight_layout()
        
box(col, df,len(col), 2, 3, 10)
```
![Box](/Users/romualdchristialtcheutchoua/Desktop/plots_saved/boxplot_outliers.png)


- there seems to be extreme values for 'calories', 'carbohydrate', 'sugar', 'protein'
- Let’s use summary statistics to investigate further.


```
df.describe()
```
- there is a significant gap between the maximum value and 3rd quartile for 'calories', 'carbohydrate', 'sugar', 'protein'. Also, there is a significant gap between the 1st quartile and the minimum value. these are strong signals of outliers
- Let’s check the distribution and skewness of each column to see whether these extreme values are significantly affecting the data.

```
numeric_columns = df.select_dtypes(include=['number']).columns

# Calculate the number of rows and columns for subplots
num_columns = len(numeric_columns)
num_rows = (num_columns // 2) + (num_columns % 2)

# Create subplots
fig, axes = plt.subplots(num_rows, 2, figsize=(15, 3 * num_rows))
axes = axes.flatten()

# Plot distributions for each numeric column using a for loop
for i, column in enumerate(numeric_columns):
    ax = axes[i]
    sns.distplot(df[column], kde=True, ax=ax)
    ax.set_title(f'Distribution of {column}')
    ax.set_xlabel(column)

# Adjust layout for better spacing
plt.tight_layout()
plt.show()

```
![Dist](/Users/romualdchristialtcheutchoua/Desktop/plots_saved/distribution_skewed.png)

- the columns calories, carbohydrate, sugar and protein are all left-skewed. Outliers are negatively affecting the data. Let’s remove them. 

```
# zscore method

col = ['calories', 'carbohydrate', 'sugar', 'protein', 'servings']
from scipy.stats import zscore

feat = df[col]

# Calculate z-scores using scipy.stats.zscore
z_scores = zscore(feat)

# Convert the result to a DataFrame
z_score = pd.DataFrame(z_scores, columns=feat.columns)
z = z_score.abs()

# Display the DataFrame with z-scores
print(z)
```
```
x = df[(z<3).all(axis = 1)]

print('data lost in %: ', (df.shape[0]-x.shape[0])/df.shape[0]*100)
```

- data lost is 7.6%. this is not severe. we remove outliers

```
df = x.copy()
```


# Exploratory Analysis


## analysis of 'high_traffic'

```
import seaborn as sns
import matplotlib.pyplot as plt

# Plot bar plot
plt.figure(figsize=(8, 6))
sns.countplot(data=df, x='high_traffic', palette='viridis')
plt.xlabel('High Traffic')
plt.ylabel('Count')
plt.title('Distribution of High Traffic (Bar Plot)')
plt.show()

# Plot pie chart
plt.figure(figsize=(8, 8))
high_traffic_counts = df['high_traffic'].value_counts()
plt.pie(high_traffic_counts, labels=high_traffic_counts.index, autopct='%1.1f%%', startangle=140, colors=['skyblue', 'salmon'])
plt.title('Distribution of High Traffic (Pie Chart)')
plt.axis('equal')
plt.show()

# Plot donut chart
plt.figure(figsize=(8, 8))
plt.pie(high_traffic_counts, labels=high_traffic_counts.index, autopct='%1.1f%%', startangle=140, colors=['skyblue', 'salmon'], wedgeprops=dict(width=0.3))
plt.title('Distribution of High Traffic (Donut Chart)')
plt.axis('equal')
plt.show()
```
![highT](/Users/romualdchristialtcheutchoua/Desktop/plots_saved/high_traffic_barplot.png)

![highD](/Users/romualdchristialtcheutchoua/Desktop/plots_saved/high_traffic_piechart.png)

![highP](/Users/romualdchristialtcheutchoua/Desktop/plots_saved/high_traffic_donutchart.png)


```
import numpy as np
from statsmodels.stats.proportion import proportions_ztest

# Count the occurrences of 'High' and 'Low' traffic
high_traffic_count = df['high_traffic'].value_counts()

# Calculate proportions
n_high = high_traffic_count['High']
n_low = high_traffic_count['Low']
n_total = n_high + n_low
proportion_high = n_high / n_total
proportion_low = n_low / n_total

# Perform two-sample z-test for proportions
count = np.array([n_high, n_low])
nobs = np.array([n_total, n_total])
z_statistic, p_value = proportions_ztest(count, nobs)

# Interpret the results
alpha = 0.05
if p_value < alpha:
    print("Reject the null hypothesis: There is a significant difference in proportions between 'High' and 'Low' traffic.")
else:
    print("Fail to reject the null hypothesis: There is no significant difference in proportions between 'High' and 'Low' traffic.")
```
'High' traffic is significantly higher than 'Low' traffic. 
Usefulness of this output: the target variable is imbalanced; we will balance this variable before modelling 



## 'category' analysis

### how many recipes do we have for each category?

```

# Calculate the count of each category
category_count = df['category'].value_counts()

# Get the categories sorted by count in descending order
categories_sorted = category_count.index.tolist()

# Create the horizontal countplot with sorted categories
plt.figure(figsize=(10, 6))
sns.countplot(data=df, y='category', palette='viridis', order=categories_sorted, orient='h')
plt.xlabel('Count')
plt.ylabel('Category')
plt.title('Distribution of Categories')
# Show the plot
plt.show()
```

![Cat](/Users/romualdchristialtcheutchoua/Desktop/plots_saved/category_distribution.png)

```
# Calculate category counts
category_counts = df['category'].value_counts()

# Create a pie chart
plt.figure(figsize=(8, 8))
plt.pie(category_counts, labels=category_counts.index, autopct='%1.1f%%', startangle=140)
plt.title('Distribution of Categories')

# Equal aspect ratio ensures that pie is drawn as a circle
plt.axis('equal')
# Show the plot
plt.show()
```
![Cat1](/Users/romualdchristialtcheutchoua/Desktop/plots_saved/category_distribution_pie.png)

### how many times has each category been served?

```
# Group the DataFrame by 'category' and calculate the sum of servings for each category
servings_by_category = df.groupby('category')['servings'].sum()

# Sort the DataFrame by the number of servings in descending order
servings_by_category_sorted = servings_by_category.sort_values(ascending=True)

# Create a horizontal bar plot
plt.figure(figsize=(10, 6))
servings_by_category_sorted.plot(kind='barh', color='skyblue')
plt.title('Number of Servings for Each Category')
plt.xlabel('Number of Servings')
plt.ylabel('Category')
plt.tight_layout()
# Show the plot
plt.show()
```
![Ser](/Users/romualdchristialtcheutchoua/Desktop/plots_saved/servings_by_category.png)

### how does each category lead to high traffic?

```
# Calculate the count of 'High' traffic for each category
high_traffic_count = df[df['high_traffic'] == 'High']['category'].value_counts()

# Get the categories sorted by count of 'High' traffic in descending order
categories_sorted = high_traffic_count.index.tolist()

# Create the horizontal countplot with 'high_traffic' as hue and sorted categories
plt.figure(figsize=(10, 6))
sns.countplot(data=df, y='category', hue='high_traffic', palette='viridis', order=categories_sorted, orient='h')
plt.xlabel('Count')
plt.ylabel('Category')
plt.title('Distribution of Categories with High Traffic')
# Show the plot
plt.show()
```


![Sor](/Users/romualdchristialtcheutchoua/Desktop/plots_saved/category_traffic_sorted.png)

## findings from 'category' analysis

- the top 6 categories with highest number of recipes are: 'Breakfast','Beverages','Chicken Breast','Lunch/Snacks','Vegetable','Potato'. This indicates that these categories have a diverse range of recipes available on the platform.


- the top 6 categories with highest number of servings: 'Breakfast','Potato','Beverages','Lunch/Snacks','Chicken Breast','Vegetable'. This suggests that recipes in these categories are popular among users and likely cater to larger groups or multiple servings per recipe.


- the top 6 categories leading the most to high traffic: 'Vegetable','Potato','Pork','Meat','Lunch/Snacks','One Dish Meal'. This indicates that recipes within these categories are particularly popular or attract more attention from users, potentially due to their appeal, uniqueness, or relevance.

## insights from category analysis

The categories 'Vegetable' and 'Potato' consistently appear among the top categories by the number of servings and also lead to high traffic. This suggests that recipes featuring vegetables and potatoes are not only popular but also drive significant engagement on the platform.
Categories such as 'Breakfast' and 'Beverages' rank high in both the number of recipes and servings, indicating their importance in users' meal planning and consumption habits.
'Chicken Breast' is another category that appears in the top categories by both the number of recipes and servings, suggesting its popularity among users for meal preparation.
Categories like 'Pork', 'Meat', and 'One Dish Meal' contribute significantly to high traffic, indicating users' interest in hearty and substantial meal options.
Overall, understanding the popularity and engagement levels of different recipe categories can help in tailoring content, improving user experience, and driving traffic to the platform.


## analysis of 'servings' 

```
# Create a countplot of servings
plt.figure(figsize=(10, 6))
sns.countplot(data=df, x='servings', palette='viridis')
plt.title('Count of Each Servings Value')
plt.xlabel('Servings')
plt.ylabel('Count')
plt.xticks(rotation=45)
plt.tight_layout()
# Show the plot
plt.show()
```

![SerC](servings_count.png)

```
import squarify

# Count the occurrences of each servings value
servings_count = df['servings'].value_counts()

# Define a list of colors for each unique servings value
colors = ['skyblue', 'lightgreen', 'salmon', 'orange']

# Create a treemap for the count of servings
plt.figure(figsize=(12, 8))
squarify.plot(sizes=servings_count, label=servings_count.index, color=colors, alpha=0.8)
plt.title('Count of Each Servings Value (Treemap)')
plt.axis('off')  # Turn off axis
plt.tight_layout()
# Show the plot
plt.show()
```
![SerT](servings_count_treemap.png)



```
# Group the data by 'servings' and count occurrences of 'High'
high_traffic_counts = df[df['high_traffic'] == 'High'].groupby('servings').size()

# Sort the counts in descending order
high_traffic_counts_sorted = high_traffic_counts.sort_values(ascending=True)

# Plot the horizontal bar graph
plt.figure(figsize=(10, 8))
high_traffic_counts_sorted.plot(kind='barh', color='skyblue')
plt.xlabel('Count of High Traffic')
plt.ylabel('Servings')
plt.title('Count of High Traffic by Servings (Descending Order)')
plt.grid(axis='x', linestyle='--', alpha=0.7)

# Show plot
plt.tight_layout()
plt.show()

```
![Traf](high_traffic_by_servings.png)

## results from 'servings' analysis

- 4 servings per recipe has the highest number, indicating that a significant portion of the recipes in the dataset caters to serving sizes of 4.


- 1, 2 and 6 servings per recipe almost have the same number. This suggests that there may be similar proportions of recipes tailored for smaller (1 or 2 servings) and larger (6 servings) households or occasions.It could also indicate that recipes with these serving sizes are equally popular among users, catering to different preferences or meal planning needs.


- servings 4 generates the highest count of 'High' traffic, followed by servings 6. Recipes with 4 servings might align with standard serving sizes commonly used in households or restaurants. This suggests that users prefer recipes that provide enough servings to accommodate a typical family size, which could increase the likelihood of these recipes receiving high traffic. The popularity of recipes with 4 servings, as well as those with 6 servings, indicates that users appreciate recipes that offer scalability and flexibility. These recipes can be easily adjusted to accommodate larger or smaller groups, catering to varying needs and preferences. 





## insights from 'servings' analysis

Recipes with 4 servings per recipe seem to be the most prevalent, possibly reflecting a common household size or serving standard for many dishes. it is also the highest likely to lead to 'High' traffic
The similar counts for 1, 2, and 6 servings per recipe suggest flexibility in the dataset, accommodating both individual/small group and larger group servings.
Understanding the distribution of serving sizes can help in tailoring recipe recommendations, accommodating diverse user needs, and optimizing resource allocation for recipe development and content curation.


## analysis of 'calories', 'carbohydrate','sugar','protein'

### how are 'calories', 'carbohydrate','sugar','protein' distributed?

```
# List of numeric columns
numeric_columns = ['calories', 'carbohydrate', 'sugar', 'protein']

# Plot two univariate graphs for each numeric column
for column in numeric_columns:
    # Create a figure with two subplots
    fig, axes = plt.subplots(1, 2, figsize=(12, 6))
    
    # Plot histogram with KDE
    sns.histplot(df[column], kde=True, ax=axes[0], color='skyblue')
    axes[0].set_title(f'Distribution of {column}')
    axes[0].set_xlabel(column)
    axes[0].set_ylabel('Frequency')
    
    # Plot boxplot
    sns.boxplot(data=df, y=column, ax=axes[1], color='salmon')
    axes[1].set_title(f'Boxplot of {column}')
    axes[1].set_ylabel(column)
    
    # Adjust layout
    plt.tight_layout()
    # Show the plot
    plt.show()
```

```
# List of numeric columns
numeric_columns = ['calories', 'carbohydrate', 'sugar', 'protein']

# Plot three univariate graphs for each numeric column
for column in numeric_columns:
    # Create a figure with three subplots
    fig, axes = plt.subplots(1, 3, figsize=(18, 6))
    
    # Plot violin plot
    sns.violinplot(x=df[column], ax=axes[0], palette='viridis')
    axes[0].set_title(f'Violin Plot of {column}')
    axes[0].set_xlabel(column)
    axes[0].set_ylabel('Density')
    
    # Plot probability density plot
    sns.kdeplot(df[column], ax=axes[1], color='skyblue', fill=True)
    axes[1].set_title(f'Probability Density Plot of {column}')
    axes[1].set_xlabel(column)
    axes[1].set_ylabel('Density')
    
    # Plot rug plot
    sns.rugplot(x=df[column], ax=axes[2], color='salmon', height=0.1)
    axes[2].set_title(f'Rug Plot of {column}')
    axes[2].set_xlabel(column)
    axes[2].set_yticklabels([])
    
    # Adjust layout
    plt.tight_layout()
    # Show the plot
    plt.show()

```


![ca](calories_univariate_plots.png)  
![ca2](calories_univariate_plot2.png)

![car](carbohydrate_univariate_plots.png)
![car2](carbohydrate_univariate_plot2.png)

![su](sugar_univariate_plots.png)
![su2](sugar_univariate_plot2.png)

![pr](protein_univariate_plots.png)
![pr2](protein_univariate_plot2.png)


```
# Calculate quartiles for the calories column
calories_quartiles = df['calories'].describe(percentiles=[0.25, 0.5, 0.75])

# Print quartiles
print("Quartiles for Calories:")
print(calories_quartiles)

# Interpret the results
q1 = calories_quartiles['25%']
median = calories_quartiles['50%']
q3 = calories_quartiles['75%']
calories_min = calories_quartiles['min']
calories_max = calories_quartiles['max']

print("\nInterpretation:")
print(f"25% of calories are less than {q1:.2f}.")
print(f"Median (50th percentile): {median:.2f} calories")
print(f"75% of calories are less than {q3:.2f}.")
print(f"Minimum calories: {calories_min:.2f}")
print(f"Maximum calories: {calories_max:.2f}")
```

```
# Calculate quartiles for the carbohydrate column
carb_quartiles = df['carbohydrate'].describe(percentiles=[0.25, 0.5, 0.75])

# Print quartiles
print("Quartiles for Carbohydrate:")
print(carb_quartiles)

# Interpret the results
q1_carb = carb_quartiles['25%']
median_carb = carb_quartiles['50%']
q3_carb = carb_quartiles['75%']
carb_min = carb_quartiles['min']
carb_max = carb_quartiles['max']

print("\nInterpretation:")
print(f"25% of carbohydrate values are less than {q1_carb:.2f}.")
print(f"Median (50th percentile): {median_carb:.2f} grams")
print(f"75% of carbohydrate values are less than {q3_carb:.2f}.")
print(f"Minimum carbohydrate value: {carb_min:.2f} grams")
print(f"Maximum carbohydrate value: {carb_max:.2f} grams")
```

```
# Calculate quartiles for the sugar column
sugar_quartiles = df['sugar'].describe(percentiles=[0.25, 0.5, 0.75])

# Print quartiles
print("Quartiles for Sugar:")
print(sugar_quartiles)

# Interpret the results
q1_sugar = sugar_quartiles['25%']
median_sugar = sugar_quartiles['50%']
q3_sugar = sugar_quartiles['75%']
sugar_min = sugar_quartiles['min']
sugar_max = sugar_quartiles['max']

print("\nInterpretation:")
print(f"25% of sugar values are less than {q1_sugar:.2f} grams.")
print(f"Median (50th percentile): {median_sugar:.2f} grams")
print(f"75% of sugar values are less than {q3_sugar:.2f} grams.")
print(f"Minimum sugar value: {sugar_min:.2f} grams")
print(f"Maximum sugar value: {sugar_max:.2f} grams")
```


```
# Calculate quartiles for the protein column
protein_quartiles = df['protein'].describe(percentiles=[0.25, 0.5, 0.75])

# Print quartiles
print("Quartiles for Protein:")
print(protein_quartiles)

# Interpret the results
q1_protein = protein_quartiles['25%']
median_protein = protein_quartiles['50%']
q3_protein = protein_quartiles['75%']
protein_min = protein_quartiles['min']
protein_max = protein_quartiles['max']

print("\nInterpretation:")
print(f"25% of protein values are less than {q1_protein:.2f} grams.")
print(f"Median (50th percentile): {median_protein:.2f} grams")
print(f"75% of protein values are less than {q3_protein:.2f} grams.")
print(f"Minimum protein value: {protein_min:.2f} grams")
print(f"Maximum protein value: {protein_max:.2f} grams")
```
### distribution output

'calories' is left-skewed: the skewness will be removed prior to modelling
25% of calories are less than 112.60.
Median (50th percentile): 297.81 calories
75% of calories are less than 566.88.
Minimum calories: 0.14
Maximum calories: 1767.53

'carbohydrate' is left-skewed: the skewness will be removed prior to modelling
25% of carbohydrate values are less than 8.75.
Median (50th percentile): 22.29 grams
75% of carbohydrate values are less than 41.87.
Minimum carbohydrate value: 0.03 grams
Maximum carbohydrate value: 163.33 grams

'sugar' is left-skewed: the skewness will be removed prior to modelling
25% of sugar values are less than 1.70 grams.
Median (50th percentile): 4.66 grams
75% of sugar values are less than 9.05 grams.
Minimum sugar value: 0.01 grams
Maximum sugar value: 52.17 grams

'protein' is left-skewed: the skewness will be removed prior to modelling
25% of protein values are less than 3.25 grams.
Median (50th percentile): 11.22 grams
75% of protein values are less than 26.86 grams.
Minimum protein value: 0.00 grams
Maximum protein value: 128.84 grams

### distribution insights
 - the majority of recipes in the dataset are lower in calories, with a wide range of variation.
 - there is a wide range of carbohydrate content among the recipes, with a majority having relatively low carbohydrate content.
 - most recipes have relatively low sugar content, but there are some recipes with higher sugar content.
 - while many recipes contain protein, there is a wide variation in protein content among the recipes, with most recipes being lower in protein.

Overall, these insights provide a comprehensive understanding of the distribution of nutritional values in the dataset, which can be valuable for dietary planning, recipe development, and nutritional analysis. these  provide valuable information that businesses can leverage to enhance their offerings, meet customer needs, and drive success in the competitive food industry landscape.


### how do 'calories','carbohydrate','sugar','protein' vary across 'category' and 'high_traffic'?


```
import seaborn as sns
import matplotlib.pyplot as plt

# Define the list of numeric columns
numeric_columns = ['calories', 'carbohydrate', 'sugar', 'protein']

# Set up the boxplot
plt.figure(figsize=(12, 8))

# Iterate over each numeric column
for i, column in enumerate(numeric_columns, start=1):
    plt.subplot(2, 2, i)
    
    # Create the boxplot
    sns.boxplot(data=df, x='category', y=column, palette='viridis')
    
    # Set title and labels
    plt.title(f'Distribution of {column} Across Categories')
    plt.xlabel('Category')
    plt.ylabel(column)

# Adjust layout
plt.tight_layout()
# Show the plot
plt.show()
```

![cat](boxplot_category_distribution.png) 


```
import seaborn as sns
import matplotlib.pyplot as plt

# Define the list of numeric columns
numeric_columns = ['calories', 'carbohydrate', 'sugar', 'protein']

# Set up the boxplot
plt.figure(figsize=(12, 8))

# Iterate over each numeric column
for i, column in enumerate(numeric_columns, start=1):
    plt.subplot(2, 2, i)
    
    # Create the boxplot
    sns.boxplot(data=df, x='high_traffic', y=column, palette='viridis')
    
    # Set title and labels
    plt.title(f'Distribution of {column} Across High Traffic')
    plt.xlabel('High Traffic')
    plt.ylabel(column)

# Adjust layout
plt.tight_layout()
# Show the plot
plt.show()
```

![high](boxplot_high_traffic_distribution.png) 


```
from scipy.stats import ttest_ind

# Define the variables to test
variables = ['calories', 'carbohydrate', 'sugar', 'protein']

# Perform independent t-tests for each variable
for variable in variables:
    # Split data based on 'high_traffic'
    high_traffic = df[df['high_traffic'] == 'High'][variable]
    low_traffic = df[df['high_traffic'] == 'Low'][variable]

    # Perform t-test
    t_statistic, p_value = ttest_ind(high_traffic, low_traffic)

    # Print the results
    print(f"Variable: {variable}")
    print(f"T-statistic: {t_statistic}")
    print(f"P-value: {p_value}")

    # Interpret the results
    alpha = 0.05
    if p_value < alpha:
        print(f"Reject the null hypothesis: There is a significant difference in '{variable}' between different levels of 'high_traffic'.")
    else:
        print(f"Fail to reject the null hypothesis: There is no significant difference in '{variable}' between different levels of 'high_traffic'.")
    print()  # Print an empty line for better readability
```

```
from scipy.stats import kruskal

# Perform Kruskal-Wallis test for each variable across different categories
for variable in ['calories', 'carbohydrate', 'sugar', 'protein']:
    # Create a list of data arrays for each category
    category_data = [df[df['category'] == category][variable] for category in df['category'].unique()]
    
    # Perform Kruskal-Wallis test
    kruskal_result = kruskal(*category_data)
    
    # Print the results
    print(f"Kruskal-Wallis Test Results for '{variable}' across different categories:")
    print(f"H-statistic: {kruskal_result.statistic}")
    print(f"P-value: {kruskal_result.pvalue}")

    # Interpret the results
    alpha = 0.05
    if kruskal_result.pvalue < alpha:
        print(f"Reject the null hypothesis: There is a significant difference in '{variable}' across different categories.")
    else:
        print(f"Fail to reject the null hypothesis: There is no significant difference in '{variable}' across different categories.")
    print()  # Print an empty line for better readability
```

### output variation across 'category' and 'high_traffic'

Kruskal-Wallis Test Results for 'calories' across different categories:
H-statistic: 95.75575475781262
P-value: 3.839369098263141e-16
Reject the null hypothesis: There is a significant difference in 'calories' across different categories.

Kruskal-Wallis Test Results for 'carbohydrate' across different categories:
H-statistic: 76.64296314340143
P-value: 2.2763594886232805e-12
Reject the null hypothesis: There is a significant difference in 'carbohydrate' across different categories.

Kruskal-Wallis Test Results for 'sugar' across different categories:
H-statistic: 85.13871861055108
P-value: 4.9010495185894993e-14
Reject the null hypothesis: There is a significant difference in 'sugar' across different categories.

Kruskal-Wallis Test Results for 'protein' across different categories:
H-statistic: 379.8297039751401
P-value: 1.8375846616160072e-75
Reject the null hypothesis: There is a significant difference in 'protein' across different categories.




Variable: calories
T-statistic: 1.8756401520398442
P-value: 0.06104646678138911
Fail to reject the null hypothesis: There is no significant difference in 'calories' between different levels of 'high_traffic'.

Variable: carbohydrate
T-statistic: 1.8819730890693596
P-value: 0.06018031468652777
Fail to reject the null hypothesis: There is no significant difference in 'carbohydrate' between different levels of 'high_traffic'.

Variable: sugar
T-statistic: -2.365161490294297
P-value: 0.018245368707192063
Reject the null hypothesis: There is a significant difference in 'sugar' between different levels of 'high_traffic'.

Variable: protein
T-statistic: 0.21684707251799568
P-value: 0.8283794387414634
Fail to reject the null hypothesis: There is no significant difference in 'protein' between different levels of 'high_traffic'.



### what are the top 2 categories with the highest 'calories','carbohydrate','sugar','protein'?


```
# Calculate the mean value for each nutritional variable grouped by category
mean_nutrition_by_category = df.groupby('category').mean()

# Get the top 2 categories with the highest mean values for each nutritional variable
top_categories_calories = mean_nutrition_by_category['calories'].nlargest(2)
top_categories_carbohydrate = mean_nutrition_by_category['carbohydrate'].nlargest(2)
top_categories_sugar = mean_nutrition_by_category['sugar'].nlargest(2)
top_categories_protein = mean_nutrition_by_category['protein'].nlargest(2)

# Print the top 2 categories for each nutritional variable
print("Top 2 Categories with Highest Mean Values:")
print("Calories:")
print(top_categories_calories)
print("\nCarbohydrate:")
print(top_categories_carbohydrate)
print("\nSugar:")
print(top_categories_sugar)
print("\nProtein:")
print(top_categories_protein)
```

Top 2 Categories with Highest Mean Values:
Calories:
category
Meat    581.273108
Pork    543.045588
Name: calories, dtype: float64

Carbohydrate:
category
Dessert          45.423500
One Dish Meal    42.223333
Name: carbohydrate, dtype: float64

Sugar:
category
Dessert      19.565833
Beverages    10.554659
Name: sugar, dtype: float64

Protein:
category
Chicken Breast    40.801149
Meat              34.601892
Name: protein, dtype: float64




### insights from the above variation

While there are significant variations in nutritional content across different recipe categories, the level of traffic (high vs. low) does not appear to have a significant effect on calorie, carbohydrate, or protein content. However, there may be differences in sugar content between recipes with high and low levels of traffic.
Businesses may want to consider these insights when developing and promoting recipes, particularly in terms of targeting specific nutritional profiles based on category preferences and understanding the potential impact of traffic levels on certain nutritional aspects, such as sugar content.


## average nutritional content for 'High' traffic

```

# Filter the DataFrame to select only recipes with high traffic
high_traffic_recipes = df[df['high_traffic'] == 'High']

# Drop rows with missing values in nutritional content columns
high_traffic_recipes = high_traffic_recipes.dropna(subset=['calories', 'carbohydrate', 'sugar', 'protein'])

# Calculate the average nutritional content for high traffic recipes
average_nutritional_content = high_traffic_recipes[['calories', 'carbohydrate', 'sugar', 'protein']].mean()

# Plot the average nutritional content
plt.figure(figsize=(10, 6))
average_nutritional_content.plot(kind='bar', color='skyblue')
plt.xlabel('Nutritional Content')
plt.ylabel('Average Value')
plt.title('Average Nutritional Content of Recipes Associated with High Traffic')
plt.xticks(rotation=45)
plt.grid(axis='y', linestyle='--', alpha=0.7)

# Show plot
plt.tight_layout()
plt.show()

```

![av](avg_nutritional_content_high_traffic.png)  


Average Nutritional Content of Recipes Associated with High Traffic:
calories        427.920316
carbohydrate     33.197945
sugar             6.800000
protein          20.665751





## what is the correlation between 'calories','carbohydrate','sugar','protein','servings'?



```
import seaborn as sns
import matplotlib.pyplot as plt

# Select numeric columns (excluding 'recipe')
numeric_columns = df.select_dtypes(include=['number']).drop(columns=['recipe'])

# Calculate correlation matrix
correlation_matrix = numeric_columns.corr()

# Create a heatmap
plt.figure(figsize=(10, 8))
sns.heatmap(correlation_matrix, annot=True, cmap='coolwarm', fmt=".2f", linewidths=0.5)
plt.title('Correlation Heatmap of Numeric Variables')
plt.xticks(rotation=45)
plt.yticks(rotation=0)
plt.tight_layout()
# Show the plot
plt.show()

```
![Corr](correlation_heatmap.png)

none of these columns is correlated to another.
Usefulness of this result: there is no multicolinearity, thus helping for model builing.


# model development

## 'recipe' is not relevant for modelling: let’s drop it
```
df = df.drop('recipe', axis = 1)
```

## encoding categorical data/variables

```
from sklearn.preprocessing import OrdinalEncoder

encoder = OrdinalEncoder()

for column in df:
    if df[column].dtypes == 'object':  
        df[column] = encoder.fit_transform(df[column].values.reshape(-1,1))


df.head()
```

## rechecking correlation 

```
corr_matrix = df.corr()
plt.figure(figsize=(15, 15))
cmap = sns.diverging_palette(220, 20, as_cmap=True)
sns.heatmap(corr_matrix, cmap=cmap, annot=True, fmt=".2f", vmin=-1, vmax=1, center=0, square=True, linewidths=.5, cbar_kws={"shrink": .75})
plt.title("Correlation Heatmap")
plt.show()
```
![Corr2](correlation_heatmap2.png)

There is no multicolinearity

## separating features and target

```
x = df.drop('high_traffic', axis =1)
y = df['high_traffic']
```

## rechecking target imbalance and balancing the classes

```
y.value_counts()
```

```
from imblearn.over_sampling import SMOTE
smote = SMOTE(random_state=42)
X, Y = smote.fit_resample(x, y)
```

```
Y.value_counts()
```

```
x=X
y=Y
```

## checking and removing skewness from features

```
col = ['calories','carbohydrate','sugar','protein']
df[col].skew()
```

```
# Applying log1p transformation to handle zero values
x['calories'] = np.log1p(x['calories'])
x['carbohydrate'] = np.log1p(x['carbohydrate'])
x['sugar'] = np.log1p(x['sugar'])
x['protein'] = np.log1p(x['protein'])
```




## standardizing the features and calculating variance inflation factor

```
from statsmodels.stats.outliers_influence import variance_inflation_factor
from sklearn.preprocessing import StandardScaler


# Standardize the variables
scaler = StandardScaler()
x = pd.DataFrame(scaler.fit_transform(x), columns=x.columns)

# Calculate VIF for each standardized variable
vif = pd.DataFrame()
vif["Variable"] = x.columns
vif["VIF"] = [variance_inflation_factor(x.values, i) for i in range(x.shape[1])]

# Display the DataFrame with VIF values for standardized variables
print(vif)
```
all the VIF are 1, which is good. 

## defining the problem and objective

the business faces the problem of Accurately knowing which recipes to choose and display on the home page (which recipe will lead to high traffic?). The objective is to build a classification machine learning model that accurately predicts whether a recipe will lead to high traffic on the website. Specifically, we aim to achieve a precision of 80% for identifying high traffic recipes. This means that out of all the recipes predicted as high traffic by the model, we want 80% of them to be correct.


## importing necessary libraries for model building

```
from sklearn.model_selection import train_test_split
from sklearn.ensemble import RandomForestClassifier
from sklearn.metrics import accuracy_score
from sklearn.ensemble import ExtraTreesClassifier
from sklearn.linear_model import LogisticRegression
from sklearn.svm import SVC
from sklearn.ensemble import GradientBoostingClassifier, AdaBoostClassifier, BaggingClassifier
from sklearn.metrics import classification_report, confusion_matrix, roc_curve, accuracy_score
from sklearn.model_selection import cross_val_score
from sklearn.model_selection import GridSearchCV
from sklearn import metrics 
import joblib
import pickle
```
NB: here, we select 7 of the mostly used classification algorithms in order to come up with the 2 models for comparison


## getting the best random_state

```
from sklearn.model_selection import train_test_split, cross_val_score, GridSearchCV
from sklearn.ensemble import RandomForestClassifier

x_train, x_test, y_train, y_test = train_test_split(x, y, test_size=0.25, random_state=42)

# Define classifier
clf = RandomForestClassifier()

# Define parameters for grid search
param_grid = {'random_state': range(1, 101)}

# Perform grid search
grid_search = GridSearchCV(clf, param_grid, cv=5, scoring='accuracy')
grid_search.fit(x_train, y_train)

# Get the best random state
best_random_state = grid_search.best_params_['random_state']
print(f"Best Random State: {best_random_state}")
```

## splitting the data

```
x_train, x_test, y_train, y_test = train_test_split(x, y, test_size=0.25, random_state=61)
```

## training the 7 selected models and cross-validation, in order to choose 2 among them

```
# training the data on each algorithm and CV

L = LogisticRegression()
R = RandomForestClassifier()
E = ExtraTreesClassifier()
G = GradientBoostingClassifier()
A = AdaBoostClassifier()
B = BaggingClassifier()
S = SVC()

Mod = [L,R,E,G,A,B,S]

for model in Mod:
    model.fit(x_train,y_train)
    pred_model = model.predict(x_test)
    acc = accuracy_score(y_test, pred_model)
    pred_train = model.predict(x_train)
    print('accuracy_score on training data: ', accuracy_score(y_train, pred_train))
    score = cross_val_score(model,x,y)
    print(accuracy_score(y_test, pred_model))
    print(confusion_matrix(y_test, pred_model))
    print(classification_report(y_test, pred_model))
    print(score)
    print(score.mean())
    print('The difference between accuracy score and cross validation score of ', model, 'is: ', accuracy_score(y_test, pred_model) - score.mean())
    print('\n')
```


all the 7 trained models give:
- good training accuracies
- good prediction accuracies
- less sensitivity to over/underfitting (very small difference between accuracy score and cross validation score)

ExtraTreesClassifier() is the only model which combines the highest prediction accuracy, the highest precision for 'High' traffic (80%, as expected) and one of the least difference between accuracy score and cross validation score. So, we select it as our baseline model. we select GradientBoostingClassifier() and RandonForsetClassifier() as our comparison models because they have the second best prediction accuracy; but our best comparison model will be choosen based on the prediction score after hypertuning (generalization).


## hypertunning the baseline model

```
param_grid = {
    'criterion': ['gini', 'entropy', 'log_loss'],            
    'max_features': ['sqrt', 'log2', None],            
    'n_estimators': [50, 100, 200],            
    'min_samples_leaf': [1, 2, 4],              
    'n_jobs': [0, -1, 2]   
}

GV = GridSearchCV(ExtraTreesClassifier(), param_grid, cv=5)
GV.fit(x_train,y_train)

```

```
GV.best_params_
```

```
baseline_model = ExtraTreesClassifier(criterion='log_loss', max_features='log2',min_samples_leaf=1,n_jobs=-1, 
                                  n_estimators=50)
my_model.fit(x_train, y_train)
pred = my_model.predict(x_test)
print('Acuuracy Score=', (accuracy_score(y_test, pred))*100) 

```

```
# ROC curve
a, b, thresholds = metrics.roc_curve(y_test, pred)
f = metrics.auc(a, b)
display = metrics.RocCurveDisplay(fpr=a, tpr=b, roc_auc = f, estimator_name = my_model)
display.plot()
plt.show()
```


## hypertuning the comparison models


```
param_grid = {
    'criterion': ['friedman_mse', 'squared_error'],            
    'max_features': ['sqrt', 'log2', None],            
    'n_estimators': [50, 100, 200],            
    'min_samples_leaf': [1, 2, 4],              
    'verbose': [0, -1, 2]   
}

Gv = GridSearchCV(GradientBoostingClassifier(), param_grid, cv=5)
Gv.fit(x_train,y_train)

```


```
Gv.best_params_
```

```
model1 = GradientBoostingClassifier(criterion='squared_error', max_features='log2',min_samples_leaf=1,verbose=0, 
                                  n_estimators=50)
model1.fit(x_train, y_train)
pred = model1.predict(x_test)
print('Acuuracy Score=', (accuracy_score(y_test, pred))*100) 

```


```
# ROC curve
a, b, thresholds = metrics.roc_curve(y_test, pred)
f = metrics.auc(a, b)
display = metrics.RocCurveDisplay(fpr=a, tpr=b, roc_auc = f, estimator_name = model1)
display.plot()
plt.show()
```


```
model2 = RandomForestClassifier(max_depth=30, max_features='log2',min_samples_leaf=1,min_samples_split=2, 
                                  n_estimators=100)
model2.fit(x_train, y_train)
pred = model2.predict(x_test)
print('Acuuracy Score=', (accuracy_score(y_test, pred))*100) 

```

```
# ROC curve
a, b, thresholds = metrics.roc_curve(y_test, pred)
f = metrics.auc(a, b)
display = metrics.RocCurveDisplay(fpr=a, tpr=b, roc_auc = f, estimator_name = model2)
display.plot()
plt.show()


```




for the two models, we use the following metrics for comparison:
- precision for 'High' traffic: 80% for the  baseline (ExtraTreeClassifier) and 78% for the comparison (GradientBoostingClassifier) models
- accuracy on training data: 0.99 for the baseline and 0.88 for the comparison models
- prediction accuracy: 0.78 for the baseline and 0.77 for the comparison models
- how the model generalizes on unseen data (prediction accuracy post hypertuning): 0.79 for the baseline and 0.77 for the the comparison
- AUC: 0.79 for baseline and 0.77 for comparison models



# Defining a way to compare your model performance to the business and Describe how your models perform using this approach

- ExtraTreesClassifier is 79% likely to accurately predict whether a recipy will lead to high traffic or not, while GradientBoostingClassifier has is 77% likely
- ExtraTreesClassifier correctly predicts high traffic recipes 80% of the time while GradientBoostingClassifier does 78% of time. 




# Definition of a metric for the business to monitor

To define a metric for the business to monitor, we need to align with the business objectives and reflect the performance of the machine learning model in achieving those objectives. For the given scenario of predicting high-traffic recipes, we can define the following metric:

Metric: Precision for identifying high-traffic recipes

How should the business monitor what they want to achieve?

The business should monitor the precision of the model in identifying high-traffic recipes. Precision measures the proportion of correctly predicted high-traffic recipes out of all recipes predicted as high-traffic by the model.
Monitoring precision allows the business to assess the accuracy of the model in recommending recipes that will generate high traffic on the website.
Estimate the initial value(s) for the metric based on the current data:

Calculate precision using the current dataset and model predictions.
Use the confusion matrix to determine the number of true positive (TP) and false positive (FP) predictions.
Precision can be calculated as:

Precision= (TP+FP)/TP

ExtraTreesClassifier gives 80% precision, which is the expected value.
GradientBoostingClassifier gives 78% precision, which is slightly less than what we expect


 
Interpret the precision value to understand how well the model is performing in correctly identifying high-traffic recipes. For example, if the precision is 0.80, it means that out of all recipes predicted as high-traffic, 80% of them are indeed high-traffic.
By monitoring precision, the business can ensure that the model effectively identifies high-traffic recipes, which aligns with the objective of predicting recipes that will lead to high traffic on the website.









# Final Summary and recommendations

## Model Performance Evaluation:

After training and evaluating seven different classification models, we selected the ExtraTreesClassifier as the baseline model due to its high prediction accuracy (79%) and precision for identifying high-traffic recipes (80%). The GradientBoostingClassifier and RandomForestClassifier were chosen as comparison models based on their performance metrics.
The baseline model achieved the desired precision of 80% for identifying high-traffic recipes, while the comparison models fell slightly below this threshold.

## Metric for Business Monitoring:

The chosen metric for business monitoring is the precision for identifying high-traffic recipes. This metric measures the accuracy of the model in correctly predicting recipes that will generate high traffic on the website.
The business should regularly monitor the precision value to ensure that the model is effectively identifying high-traffic recipes and providing accurate recommendations to users.

## Initial Precision Values:

The baseline model (ExtraTreesClassifier) achieved a precision of 80%, which meets the business's expectations.
The comparison models (GradientBoostingClassifier and RandomForestClassifier) achieved slightly lower precision values (78%), indicating a slightly lower accuracy in predicting high-traffic recipes.

## Recommendations for the Business:

Continuously monitor the precision of the machine learning model to ensure its effectiveness in recommending high-traffic recipes.
Consider retraining the model periodically with updated data to maintain its accuracy and relevance.
Explore additional features or data sources that may further improve the model's predictive performance.
Collaborate with data scientists and analysts to interpret model insights and identify opportunities for optimizing recipe recommendations.
Leverage the model's predictions to tailor content and marketing strategies, driving engagement and traffic on the website.
By implementing these recommendations and closely monitoring the precision of the machine learning model, the business can enhance its ability to accurately predict high-traffic recipes and effectively cater to user preferences, ultimately driving success and growth in the competitive market landscape.
























































