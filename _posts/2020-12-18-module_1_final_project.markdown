---
layout: post
title:      "Module 1 Final Project"
date:       2020-12-18 18:54:37 -0500
permalink:  module_1_final_project
---


**The Prompt:**
Microsoft sees all the big companies creating original video content, and they want to get in on the fun. They have decided to create a new movie studio, but the problem is they don’t know anything about creating movies. They have hired you to help them better understand the movie industry. Your team is charged with doing data analysis and creating a presentation that explores what type of films are currently doing the best at the box office. You must then translate those findings into actionable insights that the CEO can use when deciding what type of films they should be creating.

### The challenges leading up to the final project

One of the most difficult portions of the first module for me is mapping and lambda in Pandas Series and DataFrames. Escentially the following:
```
import pandas as pd
df = pd.read_csv('Yelp_Reviews.csv', index_col=0)
df['text'].map(lambda x: len(x.split())).head()
# The above will return the length in characters of the first 5 reviews in the dataframe
```

Reading the above it makes perfect sense, but in practices, the `lambda` function is very confusing because it creates work behind the scenes. Up until the point where `lambda` and `mapping` was taught, everything I was learning was a more efficient way of doing operations that could also be done in multiple parts. `lambda` is an all encompassing magic word.

My difficultly with the `lambda` and `mapping` functions came to a head during **Section 05: Project - Data Cleaning**.
I spent a few days working on that project trying to understand these magic functions and how they applied to cleaning and sorting data. I tried to find a solution immediately rather than moving on and coming back to it. Eventually, at the urging of my friend who I discussed the issue with, I left the project incomplete and moved on. I'm so happy I did return to that project  at the end of **Module 1** before starting the final project because the cleaning and sorting were some of the most important parts.

**Cleaning:**
While these methods are applied to entire rows or columns, they don't include `mapping` or `lambda`, but are applied to every row or column.
```
heroes_df = pd.read_csv('heroes_information.csv')
heroes_df.drop('Unnamed: 0', axis=1, inplace=True) # Drop the column that repeated the index column
heroes_df.head()

# Tips in understanding the dataset
heroes_df.shape #(734, 10)
heroes_df.describe()
heroes_df.info()

# It looks like there are some null columns. How many?
heroes_df.isnull().sum()

# Looking at the missing data for the heroes_df - `Publisher` has 15 null values and `Weight` has 2 null values
print(heroes_df.isna().any())
print(heroes_df.isna().sum())

# Let's look at the list of Publishers
list(heroes_df['Publisher'].unique()) 

# Rather than dropping 15 rows of data, I decided to replace `null` with `Unknown`
# Checked the info about the dataset again
heroes_df['Publisher'] = heroes_df['Publisher'].fillna('Unknown')
print(heroes_df.isna().sum())
print(list(heroes_df['Publisher'].unique()))

# Deal with the 2 weight rows that are missing data
# Option 1: Replacing the missing data with the mean weight
mean_weight = heroes_df['Weight'].mean()
heroes_df['Weight'].fillna(mean_weight, inplace=True)
heroes_df.isna().sum()

# Option 2: Dropping the 2 rows with nan data since it isn't that many out of 734 rows
heroes_df.dropna(subset=['Weight'], inplace = True)
```

**Getting most common entry:**
These methods are being applied to every row and column in the dataset and DO use `lambda` and `mapping`. The below is after joining the `heroes_df` to the `powers_df`. The `powers_df` included the name of all the superheroes and a column for every superpower. Each entry under the superpowers was boolean: either `True` or `False`.
```
# Make a list of the powers
list_powers = heroes_and_powers_df.columns.values
list_powers = list(list_powers[11:])
list_powers

# make it a dictionary
powers_dict = {}
for x in list(heroes_and_powers_df.columns)[11:]:
    powers_dict[x] = heroes_and_powers_df[x].sum()
powers_dict

# Sort the dictionary and check out the top 5
sorted(powers_dict.items(), key=lambda kv: kv[1], reverse=True)[:5]

# The top 5 powers are... (grab the first column in a list of tuples)
top_5 = sorted(powers_dict.items(), key=lambda kv: kv[1], reverse=True)[:5]
powers = [x[0] for x in top_5]
counts = [x[1] for x in top_5]
print(powers) #['Super Strength', 'Stamina', 'Durability', 'Super Speed', 'Agility']
print(counts) #[350, 281, 251, 241, 235]

# Sort by the top most common publishers
marvel_df = heroes_and_powers_df[heroes_and_powers_df['Publisher'] == 'Marvel Comics']
dc_df = heroes_and_powers_df[heroes_and_powers_df['Publisher'] == 'DC Comics']

# Marvel top 5 powers are...
marvel_powers_dict = {}
for x in list(marvel_df.columns)[11:]:
    marvel_powers_dict[x] = marvel_df[x].sum()
    
marvel_top_5 = sorted(marvel_powers_dict.items(), key=lambda kv: kv[1], reverse=True)[:5]
marvel_powers = [x[0] for x in marvel_top_5]
marvel_counts = [x[1] for x in marvel_top_5]
marvel_powers #['Super Strength', 'Durability', 'Stamina', 'Super Speed', 'Agility']

# DC top 5 powers are...
dc_powers_dict = {}
for x in list(dc_df.columns)[11:]:
    dc_powers_dict[x] = dc_df[x].sum()
    
dc_top_5 = sorted(dc_powers_dict.items(), key=lambda kv: kv[1], reverse=True)[:5]
dc_powers = [x[0] for x in dc_top_5]
dc_counts = [x[1] for x in dc_top_5]
dc_powers #['Super Strength', 'Flight', 'Stamina', 'Super Speed', 'Agility']

# Plotting findings
fig = plt.figure(figsize = (12,10))
fig.subplots_adjust(hspace=0.3)

ax1 = plt.subplot(211)
ax1.barh(y = marvel_powers, width = marvel_counts)
ax1.set_title('Marvel Top 5 Superpowers')
ax1.set_ylabel('Top 5 Powers')
ax1.set_xlabel('Frequency')
ax1.set_xlim(0,200)

ax2 = plt.subplot(212)
ax2.barh(y = dc_powers, width = dc_counts)
ax2.set_title('DC Top 5 Superpowers')
ax2.set_ylabel('Top 5 Powers')
ax2.set_xlabel('Frequency')
ax2.set_xlim(0,200);
```


## The ACTUAL Final Project
Having the knowledge of Data Cleaning and Sorting top of mind, I was excited to actually address the prompt.

After checking out the first steps:
```
import pandas as pd
import numpy as np
import seaborn as sns
import matplotlib.pyplot as plt
%matplotlib inline
```

And checking out the files both in `Microsoft Excel` and in `Python`:
```
ls Data

# Check out the fist file
box_gross_df = pd.read_csv('Data/bom.movie_gross.csv')
print(list(box_gross_df.columns))
box_gross_df.head(2) #['title', 'studio', 'domestic_gross', 'foreign_gross', 'year']
```

I made a diagram of how all the files were connected to refer back to:
!([img]https://i.imgur.com/4a0Br8N.png[/img])

Then I chose which data I wanted to work with to get the answer to the main question: **What are details of the movies Microsoft should produce at its new studio?**

* Movies that have an average rating of at least 7.0
* Movies that have at least 100 votes
* After some merging and cleaning, I changed the `production_budget`, `domestic_gross`, and `worldwide_gross` columns to `int64` so additional limitations of what a successful movie is can be made.
* Movies that has a budget of less than $100M
* Domestic Gross was 50% higher than the Production Budget. Since the production budget doesn't include things like marketing, there needs to be a fair amount of money left over. With a new studio, focusing on the domestic market first is smart.
* Release year after 2000

Now were on to the part of acutally analyzing the data using `lambda` and `mapping` to see which genres and contributers have been a part of the top 200 successful movies by my parameters.

```
genre_strings = list(successful_movies['genres'])

# The genre_strings list is actually a list of tuples since the majority of movies have 2-3 genres
genres_tuples = []
for i in range(0,len(genre_strings)):
    genres_tuples.append(successful_movies['genres'][i].split(','))
genres_tuples

import itertools
genres_list = list(itertools.chain(*genres_tuples))
print(len(genres_list)) #502
genres_list

genres = list(set(genres_list))
print(len(genres)) #21
genres

#There are 502 genres listed if we list out every genre for each of the 200 movies in our dataset. However, there are only 21 unique genres

genres_dict = {}
for genre in genres_list:
    if genre not in genres_dict:
        genres_dict[genre] = 0
    genres_dict[genre] += 1
genres_dict

# Sort the dictionary
genres_sorted = sorted(genres_dict.items(), key=lambda kv: kv[1])
genres_sorted

# Plot the results and save the image
genres_plot = [x[0] for x in genres_sorted]
genres_count_plot = [x[1] for x in genres_sorted]

fig = plt.figure(figsize = (20,20))

ax1 = plt.subplot(111)
ax1.barh(y = genres_plot, width = genres_count_plot, color='lightblue')
for index, value in enumerate(genres_count_plot):
    ax1.text(value, index, str(value))
ax1.set_title('Top 200 Movies Genre Frequencey', fontsize=30)
ax1.set_xlabel('Frequency')
ax1.set_ylabel('Genres');

fig.savefig("images/genre_freq.png", bbox_inches='tight', dpi=600)
```

So that's how I became more comfortable working with a large amount of data as well as `lambda` and `mapping`.
